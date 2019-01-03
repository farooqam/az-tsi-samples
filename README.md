# az-tsi-samples
An Azure Time Series Insights (TSI) sample based on the tutorial [here.](https://blogs.technet.microsoft.com/uktechnet/2018/04/26/use-the-query-apis-to-unlock-the-power-of-azure-time-series-insights/) The end result of the sample is the ability to query TSI for temperature sensor data. You will use Postman to run the query.

## Prequisites
1. Install [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
2. Install [Postman.](https://www.getpostman.com/)
3. Add an app registration for Postman in your Azure Active Directory tenant. Note down the object id, app id, and secret as you will need these values later.

## Provision Azure Resources
This repository contains an Azure ARM template which will provision an IoT Hub, TSI Environment, TSI Event Source, and a TSI Environment Access Policy.

The data is provided by a sample dotnet app which sends temperature data to the IoT Hub. The TSI Environment reads and stores this data via the TSI Event Source.

### Edit the ARM Template Parameters file
You will need to edit the values for the following parameters:
1. `tsiEnvironmentName` - this is the name of the TSI Environment.
2. `tsiAccessPolicyReaderObjectId` - this is the object id you obtained in step 2 of the Prequisites section.
3. `iotHubName` - this is the name of the IoT Event Hub.

**Note:** The template will create a Free Tier IoT Hub. You are allowed only one Free Tier IoT Hub per subscription so if you already have one then the deployment will fail. You can either delete the existing Free Tier IoT Hub or override the value of the `iotHubSkuName` parameter.

### Execute the ARM Template
1. Open a Powershell command prompt.
2. Navigate to the **/deploy** folder.
3. Run the following command supplying your own values for the resource group location and resource group name:

    `.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation {{location}} -ResourceGroupName {{name}} -TemplateFile azuredeploy.json -TemplateParametersFile azuredeploy.parameters.json`

4. When the deployment is done note the value of the `tsiDataAccessFQDN` output parameter as you will need this value later. This is the URI of the query API you will call in Postman.

### Register the device
1. Follow the steps [here] (https://docs.microsoft.com/en-us/azure/iot-hub/quickstart-send-telemetry-dotnet#register-a-device) to register the dotnet app which will act as the IoT device. For the purposes of this walkthrough ignore step 3.

2. Paste the connection string obtained in step 2 above into line 22 of **/device/SimulatedDevice.cs**

### Send sensor data to IoT Hub

Follow the steps [here](https://docs.microsoft.com/en-us/azure/iot-hub/quickstart-send-telemetry-dotnet#send-simulated-telemetry) to have the dotnet app send temperature data to the IoT Hub.

### Query TSI API using Postman

#### Import the Postman collection
The Postman requests are stored in a Postman collection JSON file which must be imported.

1. Open Postman.
2. Click the **Import** button.
3. Click **Click Folder.**
4. Click **Choose Folders.**
5. Select the **/postman** folder.
6. Select the **Collections** tab in the left pane and verify that the TSI-Samples collection appears and contains three requests.
7. Click the Environments drop down in the upper right corner (to the left of the eye icon) and select the **TSISample** environment.

### Provide Request Parameters
The Postman environment contains variables whose values must be specified. 

1. Click the eye icon in the top right corner and provide the following values:

    a. tenantId - this is your Azure Active Directory tenant id.
    
    b. client_id - this is the app id of the Postman app that was registered in Active Directory in step 3 of the Prerequisites section.
    
    c. client_secret - this is the client secret of the Postman app that was registered in Active Directory in step 3 of the Prerequisites section.

    d. token - this is the OAuth token which will be obtained in the next step.

    d. fqdn - this is the value of the `tsiDataAccessFQDN` ARM template output parameter.

2. Perform the following steps to obtain a token:

    a. Expand the TSI-Samples collection in the left pane.

    b. Select the request that starts with **https://login.microsoftonline.com**.

    c. Click **Send**.

    d. Copy the value of the **access_token** property in the response body and paste it in for the **token** variable in the TSISample environment.

### Execute a Sensor Data Query

1. Click the request that starts with **https://{{fqdn}}/events**.
2. Click **Send**.
3. Verify a response containing temperature values (among other values) is returned. 

### View Sensor Data Event Schema

1. Click the request that starts with **https://{{fqdn}}/metadata**.
2. Click **Send**.
3. Verify a response containing the schema is returned. 