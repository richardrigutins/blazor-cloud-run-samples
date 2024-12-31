# Blazor WebAssembly (standalone)

This folder contains a sample Blazor WebAssembly web app, that has been modified to include all files required to deploy and run the application on Google Cloud Run.

The following are the main steps required to add support for Cloud Run.

## 1. Adding Docker support

Cloud Run is a serverless compute platform that lets you run containerized applications. One way to containerize a Blazor app is to add a Dockerfile.

The [Dockerfile](BlazorWasmStandaloneApp/Dockerfile) contains the steps required to build and publish the Blazor WebAssembly application.

Currently, there isn't an option to automatically generate a Dockerfile for a Blazor WASM standalone project, so it must be created manually.

The Dockerfile must use a base image that acts as a static web server to host the actual Blazor app. This sample uses **nginx**.

The static web server must also be configured to correctly handle server-side routing: instead of returning a 404 error each time a user tries to manually open a specific page directly, the web server should always deliver the *index.html* and let routing be handled by Blazor on the client side.
With nginx, this configuration can be done using the [nginx.conf](nginx.conf) file; the Dockerfile must then include the steps to copy the nginx.conf into the final image:

```docker
COPY nginx.conf /etc/nginx/nginx.conf
```

Additionally, the nginx.conf file can be used to switch between different environment-specific application configurations (i.e. appsettings.json or appsettings.Development.json). This can be achieved by setting the *Blazor-Environment* header with the name of the desired environment (i.e. Production, Development):

```nginx
add_header Blazor-Environment "Production";
```

Using this value, Blazor will be able to read the correct configuration environment.

To reduce the amount of data transferred and speed up load time, you can also configure nginx to serve compressed versions of the files. These files are contained in the `_framework` folder of the published Blazor application.
To configure compression, add this content to the nginx.conf file:

```nginx
location /_framework/ {
 gzip_static on;
}
```

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
