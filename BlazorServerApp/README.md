# Blazor Server

This folder contains a sample Blazor Server web app, that has been modified to include all files required to deploy and run the application on Google Cloud Run.

The following are the main steps required to add support for Cloud Run.

## 1. Adding Docker support

Cloud Run is a serverless compute platform that lets you run containerized applications. One way to containerize a Blazor app is to add a Dockerfile.

The [Dockerfile](BlazorServerApp/Dockerfile) contains the steps required to build and publish the Blazor Server application. 

Visual Studio can automatically add a Dockerfile while creating a new Blazor Server project, or at a later stage by right-clicking on the project file and selecting *Add > Docker support*.

## 2. Creating a build definition

The [cloudbuild.yaml](cloudbuild.yaml) file contains the build configuration for Cloud Build. It defines the steps required to build and push the container image to Artifact Registry, and then deploy it to a Cloud Run service.

The cloudbuild.yaml definition is required in this case to customize the build steps and correctly identify the position of the Dockerfile inside the application folder.

## 3. Deploying to Cloud Run

To submit the build and deploy the application, open a terminal on the root folder of the application and run the following command:

```console
gcloud builds submit --config=cloudbuild.yaml --substitutions="_LOCATION=<your_region>,_REPOSITORY=<your_repository>,_IMAGE=<your_image>,_SERVICE_NAME=<your_service>" .
```

where you need to replace the placeholders with your actual values:
- `<your_region>`: the region of your Cloud Run service and Artifact Registry repository.
- `<your_repository>`: the name of your Artifact Registry repository.
- `<your_image>`: the name of the Docker image.
- `<your_service>`: the name of the Cloud Run service.

Alternatively, the same cloudbuild.yaml definition can be used to create a Cloud Build trigger and set up continuous deployment.