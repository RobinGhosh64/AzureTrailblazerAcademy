# Azure WebApps Lab

## Prerequisites

- Microsoft Azure subscription
- Resource Group to deploy Azure services
- Permissions to create the following resource  
    - App Service Plan
    - Blob Storage


## Step 1: Create a Resource Group
1. In the Azure Portal, search for **Resource Groups**
2. Click on the **Add** button
3. Fill out the **Basics** tab as follows:
- **Subscription:** Choose your subscription
- **Resource group:** Provide a unique name like **ata-webapp-username-rg**
- **Region:** EastUS

![RG Basic Tab](images/app-service-create-resource-group.JPG)  

4. Click the **Next: Review + Create** button
5. Click the **Create** button

## Step 2: Create a Storage Account

1. In the Azure Portal, search for **Storage accounts**
2. Click **Add+**
3. Fill out the form:
- **Resource Group:** Select resource group created earlier
- **Storage account name:** Enter a globally unique name for your storage account
- **Location:** Select region closest to you (or your users)
- **Performance:** Standard
- **Account Kind:** StorageV2 (general purpose v2)
- **Replication:** Locally-redundandt storage (LRS)
- **Blob access tier:** Hot

![Storage Basic Tab](images/storage_form.jpg) 

4. Click **Next: Networking**
5. Leave everything as default and click **Next: Data Protection**
6. Leave everything as it is and click **Next:Advanced**
7. Leave defaults and click **Next:Tags**
8. Enter any tags if you'd like and click **Next: Review + Create**
9. Click **Create**
10. Once the Storage Account has been created go to the resource and select **Access Keys**
11. Copy and paste the value from **key 1: Connection String**. You will use that value later to connect your Web Application to the Storage Account.


4. Click **Review + Create**


## Step 3: Create a App Service Plan
1. In the Azure Portal, search for **App Service Plan**
2. Click on the **Create** button
3. Fill out the **Basics** tab as follows:
- **Subscription:** Choose your subscription
- **Resource group:** Select the Resource Group you created for this lab
- **Region:** East US
- **App Service Name:** Choose a unique name for the App Service
- **Pricing tier:** Standard

![App Service Basic Tab](images/app-service-create.JPG)

4. Click the **Review + create** button

![App Service Basic Tab](images/app-service-create-final.JPG)

5. Click the **Create** button

6. You now ended up creating a App Service Plan. One App Service Plan can hold many App Services/Web Services and Apps.


## Step 4: Add a Web App using your App Service Plan
1. In the Azure Portal, search for **App Services**
2. From the left menu, click on **App Services** under **Explorers**, then click **+ Add**

3. Fill out the **Basics** tab as follows:
- **Subscription:** Choose your subscription
- **Resource group:** Select the Resource Group you created for this lab
- **Region:** East US
- **Name:** Choose a unique name for the Web App
- **Publish:** Code
- **Runtime Stack:** Choose NodeJS 12 LTS
- **Operating System:** Choose Windows
- **App Service Plan:** Select the App Service Plan created earlier

![Web Service Basic Tab](images/webapp-create.JPG)

4. Click the **Review + create** button

![Web Service Basic Tab](images/webapp-create-final.JPG)

5. Click the **Create** button

You should see a deployment screen with progress bar and a message **Your deployment is underway..**

![Web Service Basic Tab](images/webapp-create-underway.JPG)

This screen will transition to .. **Your deployment was successful..**.  There is no need to wait on this page, you could be doing something else on our portal and come back at a later time.

6. If everything was okay, you just successfully created a new 'App Service'. 

## Step 5: Go to your newly created Web App
1. In the Azure Portal, search for **App Services**
2. From the left menu, click on **App Services** under **Explorers**, find your app service and then click **on your new app service**
   OR
   You also have another option to go through the top **Notifications** button and select **Deployment Succeeded** message and click on **Go to resource**
   
3. You should be on the new app service

![Web Service Basic Tab](images/webapp-goto.JPG)

4. Now locate the **URL** section on the top right section and then Click on the **url link of the web site/web app** 
   This is your website, a sample starter one from Microsoft

![Web Service Basic Tab](images/webapp-happy.JPG)
  

5. CONGRATULATIONS !! YOU JUST CREATED A WEB APPLICATION ON AZURE CLOUD
  
  
## Step 6: Configure CI/CD to deploy our Web App
          
           
1. In the Azure Portal, search for **App Services**
2. From the list of App Services select your find your webapp and then click select **your new app service**
 
3. You should be on the new app service

4. Find the **Deployment** section on the left blade and then Click on  **Deployment Center**

![Web Service Basic Tab](images/app-service-deployment-center.JPG)

5. Select the **External** from the list of options and click on **Continue**

![Web Service Basic Tab](images/app-service-deployment-continue-kudu.JPG)

6. Select Kudu **App Service Build Service** option and click on **Continue**

7. Configure the following fields:
- **Repository Name:** to https://github.com/RobinGhosh64/Test3in1
- **Branch:** master
- **Repository Type:** Git
- **Private Repository:** No
8. Click the **Continue** button

![Web Service Basic Tab](images/app-service-deployment-finish.JPG)

9. Click the **Finish** button
![Web Service Basic Tab](images/app-service-deployment-onway.JPG)

10. You should see a screen that will show the deployment steps. There is a single table record, that will show the progress of the deployment. It should finally settle and show 'Success Active' which is the correct status for the deployment

![Web Service Basic Tab](images/app-service-check-deployment-logs.JPG)

11. Click on the **Logs** icon to see the details about the deployment.

![Web Service Basic Tab](images/app-service-deployment-successful.JPG)

12. Confirm the **Status** of the deployment is **Success (Active)**


## Step 7: Configure application to connect to Storage Account

1. Go back and again Select our web application  
3. You should be back on your new web app

4. Go down on your left to the  **Settings** section, then  click on  **Configuration**

![Config Tab](images/app-service-goto-settings.JPG)

5. You should see options to different web settings

6. Select and Click on **Application Settings**

![App_Settings Tab](images/app-service-add-app-settings.JPG)

7. Click on **New application Setting**

8. Crete a new Application Setting that contins the Connection String for the Storage Account that was created earlier.
- **Name:** AZURE_STORAGE_CONNECTION_STRING
- **Value:** Enter the connection string that was copied earlier in the lab
    
![RG Basic Tab](images/app-service-add-app-settings-1.JPG)

9. When finished with all, click on the **SAVE** button on the top panel

![RG Basic Tab](images/app-service-add-app-settings-end.JPG)

10. You should see a message stating 'Updating the Web app'

## Step 8: Test Web App

1. Go back to you App Service by selecting the **Overview** on the left blade

![RG Basic Tab](images/app-service-overview.JPG)

2. Click on **URL**

![RG Basic Tab](images/app-service-deployment-happy.JPG)

3. Make sure you web applications renders and shows the connected message in your Web App.

**Blob Storage: Success**

  CONGRATULATIONS. Great job you have finished the LAB successfully!



