# 👨🏼‍💻Migrating Data From Azure Blob Storage To GCS👨🏼‍💻
![MigrateFromAWSandAzureToGCP!](https://lucid.app/publicSegments/view/124eabd7-7d3d-4ee7-ada8-887b4b9e655e/image.png)

## Step 1: Create Your Azure Resource Group, Storage Account and Container
1. Create an Azure Resurce Group: https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups
2. Create an Azure Storage Account: https://learn.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account-1
3. Create an Azure Container Resource: https://learn.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container
4. Upload Data to The Azure Container Resource: https://learn.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal#upload-a-block-blob
5. Generate An Azure Shared Access Token (SAS): https://improvado.io/docs/how-to-generate-an-azure-sas-token#:~:text=Azure%20SAS%20Token-,How%20to%20Generate%20an%20Azure%20SAS%20Token,-The%20simplest%20way

## Step 2: Setup Your GCP Cloud Console and Create a Project
1. Sign Up For Free: https://cloud.google.com/free
    - Click on `Go to Console` to sign up
2. You will be prompted to configure the following 
    - Use the following Link: https://github.com/awanmbandi/google-cloud-projects/blob/migrate-azure-to-gcp/signup-to-gcp.md
    - Or Click on the `signup-to-gcp.md` and follow the instructions mentioned

## Step 3: Create the Migration Job Using "Storage Transfer Service"
### Runbook: 
- https://github.com/awanmbandi/google-cloud-projects/blob/migrate-azure-to-gcp/migration-job.md
1. Create Job 
2. Define Source Environment (Azure Data Science Blob Container)
3. Define Target Environment (GCP Data Science GCS Storage Bucket)
