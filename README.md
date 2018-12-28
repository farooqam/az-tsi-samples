# az-tsi-samples
An Azure Time Series Insights (TSI) sample based on the tutorial [here.](https://blogs.technet.microsoft.com/uktechnet/2018/04/26/use-the-query-apis-to-unlock-the-power-of-azure-time-series-insights/) The end result of the sample is the ability to query TSI for temperature sensor data. You will use Postman to run the query.

## Prequisites
1. Install [Postman.](https://www.getpostman.com/)
2. Add an app registration for Postman in your Azure Active Directory tenant. Note down the app id and secret as you will need these values later.

## Provision Azure Resources
This repository contains an Azure ARM template which will provision an IoT Hub, TSI Environment, TSI Event Source, and a TSI Environment Access Policy.

The data is provided by a sample dotnet app which sends temperature data to the IoT Hub. The TSI Environment reads and stores this data via the TSI Event Source.

### Edit the ARM Template Parameters file
You will need to edit the values for the following parameters:
1. `tsiEnvironmentName` - this is the name of the TSI Environment.
2. `tsiAccessPolicyReaderObjectId` - this is the app id you obtained in step 2 of the Prequisites section.
3. `iotHubName` - this is the name of the IoT Event Hub.

**Note:** The template will create a Free Tier IoT Hub. You are allowed only one Free Tier IoT Hub per subscription so if you already have one then the deployment will fail. You can either delete the existing Free Tier IoT Hub or override the value of the `iotHubSkuName` parameter.

### Execute the ARM Template
1. Open a Powershell command prompt.
2. Navigate to the **/deploy** folder.
3. Run the following command supplying your own values for the resource group location and resource group name:

    `.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation {{location}} -ResourceGroupName {{name}} -TemplateFile azuredeploy.json -TemplateParametersFile azuredeploy.parameters.json`

4. When the deployment is done note the value of the `tsiDataAccessFQDN` output parameter as you will need this value later. This is the URI of the query API you will call in Postman.

