# Registry Cloud Build sample

This sample repo demonstrates how to use cloudbuild and the registry tool to 
manage publish API information to API Hub

1. demoapi.yaml is a kubernetes style description of the API entry in the API hub.
2. specs directory contains the different versions of the API that are referenced in the api-config.yaml


If you have created the API from the API hub ui. You can export the configuration using the following command

registry config configurations create config-<gcp-project-name> \
--registry.address=apigeeregistry.googleapis.com:443 \
--registry.insecure=0 \
--registry.project=<gcp-project-name> \
--registry.location=global

registry config set token-source 'gcloud auth print-access-token'

registry export yaml apis/demoapi > demoapi.yaml

