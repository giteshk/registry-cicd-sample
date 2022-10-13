# Registry Cloud Build sample

This sample repo demonstrates how to use [Cloud Build](https://cloud.google.com/build) 
and the [registry tool](https://github.com/apigee/registry/tree/main/cmd/registry) to 
publish information to API hub

1. `demoapi.yaml` is a kubernetes style description of the API entry in the hub.
   - Initial version of this file can be created using `registry export yaml` command. 
      See section [below](#exporting-api-information-from-api-hub)
2. `specs` directory contains the different versions of the API spec that are referenced in the demoapi.yaml

## Steps to trigger the build
1. Define environment variables
    ```
    PROJECT=<REGISTRY-PROJECT-NAME>
    SERVICE_ACCOUNT_NAME=registry-cloudbuild-account
    SERVICE_ACCOUNT=${SERVICE_ACCOUNT_NAME}@${PROJECT}.iam.gserviceaccount.com
    ```

2. Create the service account to use with cloud build and add required roles
    ```
    gcloud iam service-accounts create ${SERVICE_ACCOUNT_NAME} \
        --description="${SERVICE_ACCOUNT_NAME}" \
        --display-name="${SERVICE_ACCOUNT_NAME}" \
        --project=$PROJECT

    gcloud projects add-iam-policy-binding $PROJECT \
        --member="serviceAccount:${SERVICE_ACCOUNT}" \
        --role="roles/apigeeregistry.admin" \
        --project=$PROJECT

   gcloud projects add-iam-policy-binding $PROJECT \
        --member="serviceAccount:${SERVICE_ACCOUNT}" \
        --role="roles/logging.logWriter" \
        --project=$PROJECT

    gcloud projects add-iam-policy-binding $PROJECT \
        --member="serviceAccount:${SERVICE_ACCOUNT}" \
        --role="roles/storage.objectAdmin" \
        --project=$PROJECT
    ```
3. Note: Service account roles take a few minutes to propogate

4. Trigger building the project using the following command:
    ```
    gcloud builds submit . \
        --substitutions=_SERVICE_ACCOUNT=${SERVICE_ACCOUNT} \
        --project=$PROJECT
    ```

## Exporting API information from API hub
If you have created the API from the API hub ui. 
You can export the configuration using the following command
```
   registry config configurations create config-<gcp-project-name> \
      --registry.address=apigeeregistry.googleapis.com:443 \
      --registry.insecure=0 \
      --registry.project=<gcp-project-name> \
      --registry.location=global
   
   registry config set token-source 'gcloud auth print-access-token'
   
   registry export yaml apis/demoapi > demoapi.yaml
```
