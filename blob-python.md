# Blob storage in python
 
Tutorial: https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-python

### 1.1 Create resource group

> az group create --name "holi" --location "east us"

### 1.2 Create storage account

> az storage account create \
  --kind StorageV2 \
  --resource-group learn-9d4821ca-a044-42b1-ad77-2ec4b3cc978a \
  --location centralus \
  --name [your-unique-storage-account-name]

### 1.3 Install python blob library and start a new venv

>python3 -m venv venv
 
> source venv/bin/activate
 
> pip install azure-storage-blob
 
> pip install --upgrade pip


### 1.4 Get connection string

> az storage account show-connection-string --name $the_name_of_the_storage_account

### 1.5 Set connection string for the storage account in your console.
> export AZURE_STORAGE_CONNECTION_STRING="DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=mimiragusto;AccountKey=etyuXHDNOnsfHgC5zn5cBrmAsdi1KZQvNX02E+qXcIET8pbA02vt6qxACIKAWU2AqDmYd1Ijqz2BarDOGmb3uQ=="

### 2. Write python code

        import os, uuid
        from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

        try:
            print("Azure Blob storage v12 - Python quickstart sample")
            # Quick start code goes here
            connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
            
            # Create the BlobServiceClient object which will be used to create a container client
            blob_service_client = BlobServiceClient.from_connection_string(connect_str)

            # Create a unique name for the container
            container_name = "quickstart" + str(uuid.uuid4())

            # Create the container
            container_client = blob_service_client.create_container(container_name)

            # Create a file in local data directory to upload and download
            local_path = "./data"
            # this could be another already existing file.
            local_file_name = "myfile" + str(uuid.uuid4()) + ".txt"
            upload_file_path = os.path.join(local_path, local_file_name)

            # Write text to the file
            file = open(upload_file_path, 'w')
            file.write("Hello, World!")
            file.close()

            # Create a blob client using the local file name as the name for the blob
            blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

            print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

            # Upload the created file
            with open(upload_file_path, "rb") as data:
                blob_client.upload_blob(data)

            print("\nListing blobs...")

            # List the blobs in the container
            blob_list = container_client.list_blobs()
            for blob in blob_list:
                print("\t" + blob.name)

            # Clean up
            print("\nPress the Enter key to begin clean up")
            input()

            print("Deleting blob container...")
            container_client.delete_container()

            print("Deleting the local source and downloaded files...")
            os.remove(upload_file_path)
            #os.remove(download_file_path)

            print("Done")
            
        except Exception as ex:
            print('Exception:')
            print(ex)


        # Download the blob to a local file
        # Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
        # download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
        # print("\nDownloading blob to \n\t" + download_file_path)

        # with open(download_file_path, "wb") as download_file:
        #     download_file.write(blob_client.download_blob().readall())