# Blazor Cloud Run samples

This repository contains a few sample applications that illustrate how to add Docker support to Blazor web applications and deploy them on GCP Cloud Run.

Currently, the following Blazor models are supported:
- Blazor Server
- Blazor WebAssembly (ASP.NET core hosted)
- Blazor WebAssembly (standalone)

## Deploy to Cloud Run
To deploy one of the applications, open a terminal on the root folder of the selected application and run the following command:

```console
gcloud builds submit --config=cloudbuild.yaml --substitutions="_LOCATION=<your_region>,_REPOSITORY=<your_repository>,_IMAGE=<your_image>,_SERVICE_NAME=<your_service>" .
```

where you need to replace the placeholders with your actual values:
- `<your_region>`: the region of your Cloud Run service and Artifact Registry repository
- `<your_repository>`: the name of your Artifact Registry repository
- `<your_image>`: the name of the Docker image
- `<your_service>`: the name of the Cloud Run service