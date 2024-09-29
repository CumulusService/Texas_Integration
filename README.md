# Texas_Integration
This Repository is to provide you with an overview of your running Function Apps and related workflows for your Logia Integration Service.
The extracted code is a collection of workflows / Services that are bound to Cloud Function Apps and in most cases the code snippet you will see has been generated  by appending multiple code snippets that point to the same function app.
This code is interacting with the On-Premise environment and its local resources (e.g. IF Server) via a VNet Integration with an Outbound IP(NAT gateway) -172.201.66.176
The code is NOT executable and will not work in the lack of dependencies- deployed services and the underlying compute power tied to the resource group.
The code uses 'managed identities' which basically means that resources that reside in other code spaces (e.g. The Power Platform ) were excluded from  this extract however the function apps do invoke them in certain cases.
The EXCLUDED resources are listed below:

1- Logia Stock Schedule: This is a Web App that allows users to set the run schedule interactively 
2- Integration Monitor : This is a Web app that was  developped to monitor Outbound/INbound traffic and resubmit failed jobs-partially developped , developement stopped when the project had been suspended.(70%-80% done)
3- Logia Sync Data Flow- this is an Azure DF(Data Factory) data pipeline that retrieves data from an Oracle DB and performs numerous  manipulations on the data and prepares it for the function app to execute the code. there is no code to 
    extract here ( based on DAX expressions & Power BI engines that are tenant-specific ).
4- Dataverse Tables- M365 Apps use those tables by definition and those could not be extracted . multiple function apps write / read from those tables 

Below is a short description to each of the Function Apps:

 
