
# Integration Mechanics - Building an Integration from PrintIQ to HubWoo

## Task Outline

The PrintIQ team are developing a new integration for a customer which would integrate with a third party system called **Hubwoo**. This integration will provide the ability for the customer to export jobs from PrintIQ into Hubwoo.This integration is to be provided in the form of a simple API that PrintIQ support personnel can configure using the PrintIQ's webhooks setup.

## Background

PrintIQ maintains a set of applications for providing various integrations. One of those applications, is **DirectoryMonitor**. This application is a windows service which is installed in the customer's infrastructure. **DirectoryMonitor** monitors a hot folder for files(XML,JSON,CSV etc.) and once a file is dropped, it would then ingest the file and gets the required details from PrintIQ. It then builds an output based off the details which were fetched from PrintIQ and sends the output over HTTP to third party system.

As DirectoryMonitor is installed in the customer network, there is very little to no control on how the application performs. Often times, there might be issues with the customer's network or firewall or infrastructure which would impact the application. This is causing an increased no.of desk tickets being logged.

This application would eliminate the need for installing **DirectoryMonitor** application as this would be hosted in the **Microsoft Azure** cloud and thus giving PrintIQ the capability to monitor and troubleshoot the application quickly should there be an issue.

It is important that this application should expose an simple api for the whole integration workflow.

## Task

The current project needs to be built using the [Azure Functions Service](https://docs.microsoft.com/en-us/azure/azure-functions/). The language to be is **C#**. Feel free to develop the using various [Azure Functions Triggers](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?tabs=csharp) and patterns. Your task is to complete the implementation such that it meets the following requirements.

### Requirement 1 - Configure your function to accept webhook payload and extract the QuoteNo from it

When the status of a quote is changed a webhook is fired from PrintIQ to a predefined endpoint url. This url needs to configured as part of the webhook setup.

1. Make sure your function accepts the payload
2. Extract the QuoteNo from the payload 

If the QuoteNo is null or empty then an exception needs to be thrown as part of the validation logic. 

### Requirement 2 - Get the login token and Quote details 

Based on the QuoteNo extracted from the previous step, now you need to retrieve the QuoteDetails for that quoteNo. To get the quote details you need to follow the below steps

1. Get the LoginToken from QuoteProcess API. Use the below username and password to retrieve login token.
    a. Username:
    b. Password:
2. Get the QuoteDetails from the QuoteProcess API

If there an exception throw and exception and follow the retry and error notification process. 

### Requirement 3 - Check for the 'UOA' in the customer name in the payload

If the customer name in the payload doesn't have the "UOA" in it, then you need to exit out of the workflow.

### Requirement 4 - Construct an XML as per the format

Once the quote details have been retrieved, you now need to build a XML based on the below format. 

### Requirement 5 - Send the XML to Hubwoo

Send the XML to HubWoo over Http using their UAT credentials. 

### Requirement 6 - Implement Error Handling

You need to implement an error retry and handling mechanism. 

1. You need to implement the error retry functionality where the system would retry for 3 times at 20 minutes apart before failing and quitting the workflow.
2. Once the workflow quits, we should be able to send an notification to the customer with the error and the xml file attached.

### Requirement 7 - Implement Logging

You need to implement logging mechanism. The functionality should be easily extensible should we require to change logging datastore. 

1. Logs should be written to a CosmosDB database
2. If in future, the team should be able to swap the logging to Azure Storage if needed for lowering the costs.


### Requirement 8 - Implement CI/CD pipelines

You need to implement CI/CD mechanism. This functionality would allow for a faster deployment of the changes to the integrations.
You can either use Github Actions or Azure Devops


## Assumptions

The following assumptions have been made - you are free to countermand these as you feel is appropriate to your design.

1. It is sufficient to mock Email Notification service for the purpose of testing.
2. Unit Tests can just be a set of dumb tests which would verify the final XML.
3. We would like to use Pull-Request workflow for this project. 

## Constraints

Each requirement should be having the a set of unit tests covering both the happy and sad execution paths. 