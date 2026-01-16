# Deploying your own well-tested code


**NB! Build first. We don't have a CI-job yet, so you need to manually push images as showed below**. On the plus-side, nobody knows what images you push :) 

## 1. Get Credentials


You can retrieve the username and password using the following Azure CLI command:

```bash
az acr credential show --name variantcoursetesttemporaryacr --query "{username:username, password:passwords[0].value}"
```

This will output a JSON object with the username and password.

## 2. Build, Tag, and Push the Image

Navigate to this directory and run the following commands:
Please use a tag with your name so we avoid collisions 

```bash
# 1. Build the Docker image
docker build -t hello-world-api .

# 2. Log in to your container registry
# Use the username from the previous step
docker login variantcoursetesttemporaryacr.azurecr.io -u variantcoursetesttemporaryacr

# When prompted for a password, use the password from the previous step.

# 3. Tag the image for your registry
docker tag hello-world-api variantcoursetesttemporaryacr.azurecr.io/<yourname>-hello-world-api:v1

# 4. Push the image to the registry
docker push variantcoursetesttemporaryacr.azurecr.io/<yourname>-hello-world-api:v1
```

## 3. Edit the deployment to use whatever tag you chose
See deployment.yml 