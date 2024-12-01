# AzureVM-StorageIntegration

Here’s an outline for a project showcasing **Azure VM with Azure Storage Account integration**. The project demonstrates how to use Azure Storage (Blob or File Share) to store and manage files for an application running on an Azure VM.

---

### **Project Overview**
- **Objective**: Deploy an application on Azure VM that uses Azure Storage Account to handle data (e.g., file uploads, backups, or logs).
- **Components**:
  1. Azure Virtual Machine (VM)
  2. Azure Storage Account (Blob Storage or File Share)
  3. Application accessing the storage

---

### **Steps to Set Up the Project**

#### **1. Create an Azure VM**
1. In the [Azure Portal](https://portal.azure.com):
   - Navigate to **Virtual Machines** > **Create** > **Azure Virtual Machine**.
   - Select OS (e.g., Ubuntu 20.04 or Windows Server).
   - Configure networking to allow SSH/RDP and HTTP traffic.
2. Connect to the VM using SSH or RDP.

---

#### **2. Create an Azure Storage Account**
1. Navigate to **Storage Accounts** in the Azure Portal.
2. Click **+ Create**:
   - Select the same region as the VM for lower latency.
   - Choose a unique name and storage options (e.g., Standard or Premium).
3. Enable **Blob Storage** and/or **File Shares** based on your use case:
   - **Blob Storage**: For unstructured data (e.g., images, videos, logs).
   - **File Share**: For shared access to files.

---

#### **3. Configure Storage on the VM**
- **Option 1: Azure Blob Storage**
  - Install the Azure CLI:
    ```bash
    curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
    ```
  - Authenticate with Azure:
    ```bash
    az login
    ```
  - Create a container:
    ```bash
    az storage container create --account-name <storage_account_name> --name <container_name> --auth-mode login
    ```
  - Upload/download files using Azure CLI:
    ```bash
    az storage blob upload --account-name <storage_account_name> --container-name <container_name> --file <local_file_path> --name <blob_name>
    az storage blob download --account-name <storage_account_name> --container-name <container_name> --name <blob_name> --file <local_file_path>
    ```

- **Option 2: Azure File Share**
  - Create a file share in the storage account.
  - Mount the share on the VM:
    - For **Linux**:
      ```bash
      sudo apt-get install cifs-utils
      sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> /mnt/storage \
      -o vers=3.0,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,sec=ntlmssp
      ```
    - For **Windows**:
      Use the `net use` command:
      ```cmd
      net use Z: \\<storage_account_name>.file.core.windows.net\<share_name> /user:<storage_account_name> <storage_account_key>
      ```

---

#### **4. Deploy an Application on the VM**
1. Install a web server (e.g., Apache or Nginx).
   ```bash
   sudo apt update
   sudo apt install apache2 -y
   ```
2. Deploy a sample application that integrates storage (e.g., file upload/download to/from Blob or File Share).
   - Modify the application code to use the Azure SDK for accessing Blob Storage.
     ```bash
     pip install azure-storage-blob  # For Python
     ```

   - Example Code for Blob Upload (Python):
     ```python
     from azure.storage.blob import BlobServiceClient

     connection_string = "<your_connection_string>"
     blob_service_client = BlobServiceClient.from_connection_string(connection_string)

     container_name = "<container_name>"
     blob_name = "example.txt"
     file_path = "/path/to/local/example.txt"

     blob_client = blob_service_client.get_blob_client(container=container_name, blob=blob_name)
     with open(file_path, "rb") as data:
         blob_client.upload_blob(data)

     print("File uploaded successfully!")
     ```

---

#### **5. Test the Setup**
1. Verify the application is running on the VM using its public IP address.
   ```bash
   curl http://<VM_Public_IP>
   ```
2. Test the storage integration by uploading or retrieving files.

---

Let me know if you need more specific code examples or deployment guidance!
