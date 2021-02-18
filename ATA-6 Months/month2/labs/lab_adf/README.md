# Azure Trailblazer Academy Azure Data Factory (ADF) Lab
## Overview
Azure Data Factory is a PaaS cloud-based ETL & data integration tool allows you to create data driven workflows in the cloud for orchestrating and automating data movement and data transformation. 

It is a great tool to orchestrate big data and operationalize processes to refine these enormous stores of raw data into actionable business insights.

## Lab Overview
This lab will help you gain the experience to ingest data from on-premises databases such as Oracle, SAP, Teradata, Hortonworks, DB2, SQL Server and Cloudera to Azure Data storage, databases and data warehouses services. 
It will showcase the steps to build a pipeline using ADF to ingest the data into ADLS GEN2 storage and secure the PII data using data transformation functions using the Data Flow activity and finally store the data in Synapse SQL Pool (Data warehouse) for building BI dashboards. 

<img src="./images/ata-adf-lab-architecture.png" alt="Data factory lab architecture diagram" width="600">

## Pre-requisites
- Write Access to Azure Data Lake Storage Account (ADLS Gen2)
- Read Access to Sample HR schema in Oracle Database
- Write Access to Synapse SQL Pool Data warehouse  

## Automated Deployment
This automated deployment script will create thre required Azure srevices for this lab. Press the "*Deploy to Azure*" button below.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2FAzureTrailblazerAcademy%2Fmaster%2Fmonth2%2Flabs%2Flab_adf%2Fscripts%2Flab2_data_deployment.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Enter the following information
- Subscription: Enter your subscription.
- Resource group: Select 'Create new' under Resource group. Enter 'ata-adf-lab-\<YourName\>-rg'
- Region: Select 'East US'.
- SQL_Server Name:Enter 'ata-adf-lab-sql-\<YourName\>'
- Server_location:Enter 'eastus'

<img src="./images/adf-custom-deployment.png" alt="Enter the required info to run the custom deployment" width="600">

- AdministratorLogin:'azureadmin'
- Administrator Login Password:'Ataadf123!'
- Database Name:'ataadflabsqldb'
- Storage_Accounts_Data Lake Gen2:'ataadflabstorage\<YourName\>/
- Click on 'Review and Create'
- Click on 'Create'

Please check if it created the following services after successful deploypment.

<img src="./images/adf-custom-deployed-services.png" alt="Customer deployed Azure Services" Width="600">

Temporary access to an Oracle database for completing this lab will be provided in the class.

## Task List
- [Task-1: Create Azure Data Factory Service](#task-1-create-azure-data-factory-service)
- [Task-2: Create linked services](#task-2-create-linked-services)
- [Task-3: Create Copy Activity to ingest Oracle HR Emplyee data to Azure Storage](#task-3-create-copy-activity-to-ingest-oracle-hr-employee-data-to-azure-storage) 
- [Task-4: Create Data Flows to Transofrm Employee data and store it in Synapse SQL Analytics](#task-4-create-data-flows-to-transofrm-employee-data-and-store-it-in-synapse-sql-analytics)
- [Task-5: Build a pipeline to connect Copy and Dataflow actvities and Test the Execution](#task-5-build-a-pipeline-to-connect-copy-and-dataflow-activities-and-test-the-execution) 

### Task-1: Create Azure Data Factory Service
1. Provision an ADF service
- Type 'Data factories' in the search bar. 
- Select 'Data factories' and select 'add' to create a new service

Provide the following info: 
- Name: 'ata-adf-lab-\<YourName\>'
- Subscription: Make sure to select your subscription
- Select 'ata-adf-lab-\<YourName\>', the resource group you have created with custom deployment. 
- Location: select 'East US'
- Enable GIT: uncheck the box

Click on 'Create' button  

<img src="./images/adf-main-service-create.png" alt="Create ADF Service" width="600">

2. Open ADF Author Tool
- Select 'Go to resource' when it completes the deployment.
- Select 'Author & Monitor' in the middle of the screen.

<img src="./images/adf-select-author.png" alt="Select Author & Monitor" width="600">

Opens up a new tab introducing the drag and drop interface to build pipelines.

<img src="./images/adf-select-create-pipeline.png" alt="select create pipeline" width="600">

### Task-2: Create linked services
You will be creating connection linked services to sync and source systems such as Oracle, ADLS Gen2 and Synapse in this task. 
1. Create Oracle Linked Service
- select Management hub (ToolBox) icon on the left and select 'Linked Services' under Connections section.  
- Select 'New' under Linked Services
- search for 'Oracle' under 'Data Store' and select 'Oracle Database' and click on 'Continue'

Enter the following Oracle Connect Info:
- Name: Enter 'OracleDB12cHR'
- Leave the default 'Connection string' option
- Host: Enter Instructor provided server IP address
- Port: 1521 (Default Oracle Port)
- Connection type: Select 'Oracle Service Name'
- Service name: Enter Instructor provided Oracle Service name
- User name: hr
- Password: 'hr'

<img src="./images/adf-oracle-connect-info.png" alt="Enter Oracle Connect Info" width="600">

- Select 'test connection' to verify the successful connection

Click on 'Create' button to create the Oracle linked service.

2. Create ADLS Gen2 Storage Linked Service
- Select 'New' under Linked Services
- Search for 'Gen2' under data store and select ADLS Gen2 
Click on 'Continue' and enter the following information:

- Name: Enter 'Gen2HRStorage'
- Authentication Method: Leave the default 'Account Key' selection
- Account Selection method: Leave the default 'From Azure subscription'
- Storage account name: select 'ataadflabstorage/<yourname/>'
- Click on 'Test connection' 
Click on 'Create' after successful connection to create the ADLS Gen2 storage linked service

<img src="./images/adf-gen2-storage-connect-info.png" alt="Enter Gen2 Storage connect info" width="600" >

3. Create Synapse Analytics Linked Service
- Select 'New' under Linked Services
- Search for 'Synapse' under data store and select 'Azure Synapse Analytics' 
- Click on 'Continue'

Enter the folowing info:
- Name: Enter 'SynapseDBHR'
- Server name: select 'ata-adf-lab-sql-\<yourname\>'
- Database name: select 'ataadflabsqldb'
- User name: enter 'azureadmin'
- Password: enter the default 'ataadf123!'
- Click on 'test connect' to test the connection.

Click on 'Create' after the successful connection to create the Synapse linked service

<img src="./images/adf-synapse-sink-db-connect-info.png" alt="Enter Synapse sink db connect info" Width="600">

You have successfully created connection linked services to Oracle, ADLS Gen2 and Synapse SQL Pool.

### Task-3: Create Copy Activity to ingest Oracle HR Emplyee data to Azure Storage
We have established the connection services to the source Oracle DB and the sink Azure storage, you will create a copy activity to ingest the data from Oracle to Azure Storage in this task.

1. Select 'Pencil' icon on the left and select three dots next to pipelines to select 'new pipeline' action. 
- Name the pipeline as 'IngestOracleHREmployeeData' under the properties section on the right side.
- Drag the 'Copy data' from 'Move & transform' section under 'Activities' list to the convas in the middle of the screen.
- Name the copy activity as 'OracleHREmpToGen2' below the convas under the 'General' tab

<img src="./images/adf-copy-hr-emp-adls-gen2.png" alt="Copy activity to get Oracle HR Emp data to Gen2" width="900">

2. Select 'Source' tab next to 'General' to define the source system. 
- Select 'New' to create a new source dataset
- Select 'Oracle database' as the data store after filtering with 'Oracle' and click on 'Continue'
- Select 'Open' to define the source dataset
- Name the dataset as 'Oracle_HR_Employee_table' under the properties section on the right side.
- Select the 'OracleDB12cHR' linked service and click on 'Test connection' to test the connectivity.
- Filter table list by typing 'hr.emp' and select 'HR.EMPLOYEES' table and select 'Preview data'.

<img src="./images/adf-copy-select-hr-employee-table.png" alt="select Oracle HR table as the source data set" width="900">

Make sure you are able to see the employee data 

3. Select 'Sink' tab next to 'Source' to define the sink system
- Select 'New' to create a new sink data set
- Select 'ADLS Gen2' as the data store after filtering with 'Gen2' and click on 'Continue'
- Select 'Open' to define the sink dataset
- Select 'Delimited Text' as the format and click on 'Continue'
- Name the dataset as 'Gen2HREmpData'
- select the 'Gen2HRStorage' linked service.

You will have to enter the file system and directory path. 
- First we need to create the directory path in the storage accont. Leave this browser tab as is and switch to the Azure Services tab in the browser.  
- Access storage account and open up the 'storage explorer' to create 'hr' under 'data' file system and create 'Employee' as a subfolder under 'hr' folder.

<img src="./images/adf-storage-create-employee-folder.png" alt="Gen2 storage create employee folder" width="600">

Switch back to ADF author browser and enter 'data' and 'hr/Empolyee' as the File path and click on 'OK'

<img src="./images/adf-copy-sink-gen2-dataset.png" alt="create sink gen2 data set" width="600">

- Select it again under Datasets section on the left to set the first row as the header.
- Check the box 

<img src="./images/adf-copy-sink-gen2-row-header.png" alt="sink-gen2-check-header-row" width="600">

4. Turn on Dataflow Debug functionality to test the modifications.

- To preview data we need to turn on the dataflow debug option. 
- Click on 'Import projections' in the 'Projections' tab to view all the columns.

<img src="./images/adf-dataflow-turnon-debug-import-projections.png" alt="Turn on the debug option and import projections" width="500">

5. Test the copy activity by clicking on 'Debug' option just above the canvas.
- It will start the process and put it in the queue. Wait till it finishes.
- Check for the status and make sure it is successful

<img src="./images/adf-copy-trigger-activity.png" alt="execture copy activity to ingest data" width="600">

6. Verify the data ingestion in ADLS Gen2 storage.

Switch to Azure services tab and access the storage account.
- open up the 'Storage Explorer' and access the data file syste and drill down to 'hr' and 'Employee' folder. 
- Confirm the 'HR.EMPLOYEES.txt' file. Double click on the file to download and view the data.

You can see how the phone numbers are ingested as text. This is PII data and we should protect this data. 

<img src="./images/adf-storage-ingest-confirm.png" alt="Verify data ingestion to Gen2 from Oracle" width="600">

### Task-4: Create Data Flows to Transofrm Employee data and store it in Synapse SQL Analytics
We have noticed the PII data we just ingested into ADLS Gen2 storage. You will secure the PII data using the Data flow functionality in this task.
 1. Create Source data
- Drag and drop the data flow activity into the canvas from the 'Move&Transform' section.
- select 'create new data flow' and select 'Mapping Data Flow' optin and click on 'Ok'.

<img src="./images/adf-dataflow-create-new.png" alt="create a new data flow" width="400">

- Select "Source" data
- Name the data flow as 'SecurePIIdata'.

Provide the following information under 'Source settings' tab:
- Output stream name:Gen2HrEmpDataOut
- Source type - Select 'Dataset'
- Dataset - Select 'Gen2HREmpData'

<img src="./images/adf-dataflow-source-settings.png" alt="Provide source settings info" width="600">

2. Create a data flow step to hash the first 6 digits of the phone number.
- Add another data flow step to secure the phone number by selecting '+' sign and select 'Drived Column' under 'Schema modifier' section.

<img src="./images/adf-dataflow-derived-column.png" alt="Select derived column" width="600">

- Name the data flow as 'HREmpSecurePhoneNumber'
- Select 'PHONE_NUMBER' column from the list
- Click on 'Enter Expression' box

<img src="./images/adf-dataflow-select-phone-number.png" alt="Name the data flow and Select the Phone Number column" width="600">

- Write RegExpression to replace the digits before the '.' with '#' as shown in the picture. 
- Click on 'Refresh' to verify the output of the function.
- Click 'Save and finish' button after you satisfy the result

<img src="./images/adf-dataflow-regreplace-function.png" alt="Enter RegEx Replace function" width="800">

3. Create 'HR' schema in Azure Synapse Analytics.

We need to create 'HR' schema in the 'Azure Synapse Analytics' service to store the results
- Switch to Azure Services tab and select Azure Synapse Analytics 'ataadflabsqldb' we have created with the auto deployment step.

-  Select 'Query editor' on the left and enter the 'azureadmin' as the user and 'Ataadf123!' as the password.
- It will error out since it is protecting the access to 'Query editor'. Copy the IP address from the error and add it to the firewall.

<img src="./images/adf-synapse-query-editor-error.png" alt="Synapse-query-editor-access-error" width="600">

- Select 'Overview' and click on the 'Server name' link
- Select 'Firewalls and virtual networks' from 'Security' section
- add firewall rule to allow the IP address you copied from the error message.
- Click on 'Save' button after you added the rule.
- Select the Overview and scroll down to select the database
- Select the 'Query editor' and enter the login info again
- With successful login, you will see the dataware house tabels and views.
- Enter 'create master key;' in the query editor and click on 'Run' 
- Enter 'create schema hr;' in the query editor and click on 'Run'
- You should see 'Query succeeded' message in the bottom.

<img src="./images/adf-synapse-create-hr-schema.png" alt="Create HR Schema in the Query Editor" width="600">

4. Create a data flow step to store the transformed data into Synapse SQL Pool.

We are now ready to output the transformed data into Synapse SQL Pool
- Select '+' and select 'Sink' in the 'Destination' section. Last one in the list.
- Switch back to ADF Author tab and create the Synapse SQL Analytics data set
- Enter the following info
- Click on 'Create' button
- Specify Destination table info, HR schema and Employee Table name.

<img src="./images/adf-dataset-synapse-create.png" alt="Create Synaspe Data Set" width="600">

### Task-5: Build a pipeline to connect Copy and Dataflow actvities and Test the Execution 
We have a create copy activity to ingest the data from Oracle to Azure Storage and Dataflow activity to secure PII data. You will connect both activity to build a pipeline to execute the end to end functionality. 

1. Connect Copy activity with Data Flow activity
- Create a staging linked service to improve the performance of the data flow operations

2. Create a Staging Linked Service to store the temporary data between the dataflow steps.

- Select the data flow in the pipeline diagram and access the 'Settings' tab
- Click on 'New' 'Staging Linked Service' and enter the following info:
- Name:'dataflowstaging' 
- Type: select 'Azure Blob Storage'
- Authentication Method: select 'Account key'
- Select an available blob storage. create a new one if needed.
- Click on 'Test connection'
- Click on 'Create' button after successfully testing the connection.
- Specify a folder for the temporay storage during the transformations

<img src="./images/adf-dataflow-staging-linked-service.png" alt="create dataflow staging linked service" width="800">

- Add a folder to the storage

3. Trigger the pipeline execution.

- click on 'Debug' to test the data transformation to secure the PII data and storing it to Synapse SQL Analytics database.

<img src="./images/adf-dataflow-trigger-run.png" alt="Start the debug to test the data movement to Synapse" width="800">

- Execution will be queued and wait till it finishes.

<img src="./images/adf-End-to-End-Execution.png" alt="End to end Execution Status" width="800">

4. Test the transformed data in Azure Synapse Analytics.

- Switch to Azure Services and access the Synapse SQL Analytics to verify the employee data with secured phone numbers
- Access synapse SQL Analytics 'ataadflabsqldb' and select 'Query editor' from 'Common Tasks' section.
- Login with 'azureadmin' and password 'Ataadf123!' if needed.
- Enter 'select * from [HR].[Employee]' to view the ingested Oracle data.
- You should see all the employee data with the secured phone number.
<img src="./images/adf-lab-synapse-secure-PII-verify.png" alt="Verify ingested data in Synapse with secured phone number" width="800">

Congratulations!! You have successfully ingested the data from Oracle to Azure Datalake, transformed the data as per your needs such as securing the data and stored the data into Azure Synapse Analytics for your business analytics needs!!!