# HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products
Synchronizes Exchange Online Shared Mailboxes to HelloID Self service products

<a href="https://github.com/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products/network/members"><img src="https://img.shields.io/github/forks/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products" alt="Forks Badge"/></a>
<a href="https://github.com/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products/pulls"><img src="https://img.shields.io/github/issues-pr/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products" alt="Pull Requests Badge"/></a>
<a href="https://github.com/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products/issues"><img src="https://img.shields.io/github/issues/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products" alt="Issues Badge"/></a>
<a href="https://github.com/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products/graphs/contributors"><img alt="GitHub contributors" src="https://img.shields.io/github/contributors/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products?color=2b9348"></a>

| :information_source: Information                                                                                                                                                                                                                                                                                                                                                       |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| This repository contains the connector and configuration code only. The implementer is responsible to acquire the connection details such as username, password, certificate, etc. You might even need to sign a contract or agreement with the supplier before implementing this connector. Please contact the client's application manager to coordinate the connector requirements. |

## Table of Contents
- [HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products](#helloid-conn-sa-sync-exo-sharedmailbox-to-selfservice-products)
  - [Table of Contents](#table-of-contents)
  - [Requirements](#requirements)
  - [Introduction](#introduction)
  - [Getting started](#getting-started)
      - [Create an API key and secret](#create-an-api-key-and-secret)
    - [Installing the Microsoft Exchange Online PowerShell V3.1 module](#installing-the-microsoft-exchange-online-powershell-v31-module)
    - [Getting the Microsoft Entra ID graph API access](#getting-the-microsoft-entra-id-graph-api-access)
      - [Creating the Microsoft Entra ID App Registration and certificate](#creating-the-microsoft-entra-id-app-registration-and-certificate)
      - [Application Registration](#application-registration)
      - [Configuring App Permissions](#configuring-app-permissions)
      - [Assign Microsoft Entra ID roles to the application](#assign-microsoft-entra-id-roles-to-the-application)
      - [Authentication and Authorization](#authentication-and-authorization)
    - [Synchronization settings](#synchronization-settings)
  - [Remarks](#remarks)
  - [Getting help](#getting-help)
  - [HelloID Docs](#helloid-docs)

## Requirements
- Make sure you have Windows PowerShell 5.1 installed on the server where the HelloID agent and Service Automation agent are running.
- Installed and available **Microsoft Exchange Online PowerShell V3.1 module**. Please see the [Microsoft documentation](https://learn.microsoft.com/en-us/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps) for more information. The download [can be found here](https://www.powershellgallery.com/packages/ExchangeOnlineManagement/3.0.0).
- Required to run **On-Premises** since it is not allowed to import a module with the Cloud Agent.
- An **App Registration in Microsoft Entra ID** is required.
- Make sure the sychronization is configured to meet your requirements.

## Introduction
By using this connector, you will have the ability to create and remove HelloID SelfService Products based on shared mailboxes in your Exchange Online environment.

The products will be created for each mailbox in scope. This way you won't have to manually create a product for each group.

And vice versa for the removing of the products. The products will be removed (or disabled, based on your preference) when a mailbox is no longer in scope. This way no products will remain that "should no longer exist".

This is intended for scenarios where there are (lots of) shared mailboxes that we want to be requestable as a product. This group sync is desinged to work in combination with the [EXO SharedMailbox FullAccess Permissions to Productassignments Sync](https://github.com/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-FullAccess-Permissions-To-HelloID-Productassignments).

## Getting started

#### Create an API key and secret

1. Go to the `Manage portal > Security > API` section.
2. Click on the `Add Api key` button to create a new API key.
3. Optionally, you can add a note that will describe the purpose of this API key
4. Optionally, you can restrict the IP addresses from which this API key can be used.
5. Click on the `Save` button to save the API key.
6. Go to the `Manage portal > Automation > Variable library` section and confim that the auto variables specified in the [connection settings](#connection-settings) are available.

### Installing the Microsoft Exchange Online PowerShell V3.1 module
Since we use the cmdlets from the Microsoft Exchange Online PowerShell module, it is required this module is installed and available for the service account.
Please follow the [Microsoft documentation on how to install the module](https://learn.microsoft.com/en-us/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps#install-and-maintain-the-exchange-online-powershell-module). 

### Getting the Microsoft Entra ID graph API access
#### Creating the Microsoft Entra ID App Registration and certificate
> _The steps below are based on the [Microsoft documentation](https://docs.microsoft.com/en-us/powershell/exchange/app-only-auth-powershell-v2?view=exchange-ps) as of the moment of release. The Microsoft documentation should always be leading and is susceptible to change. The steps below might not reflect those changes._
> >**Please note that our steps differ from the current documentation as we use Access Token Based Authentication instead of Certificate Based Authentication**

#### Application Registration
The first step is to register a new **Microsoft Entra ID Application**. The application is used to connect to Exchange and to manage permissions.

* Navigate to **App Registrations** in Microsoft Entra ID, and select “New Registration” (**Microsoft Entra Portal > Microsoft Entra ID > App Registration > New Application Registration**).
* Next, give the application a name. In this example we are using “**ExO PowerShell CBA**” as application name.
* Specify who can use this application (**Accounts in this organizational directory only**).
* Specify the Redirect URI. You can enter any url as a redirect URI value. In this example we used http://localhost because it doesn't have to resolve.
* Click the “**Register**” button to finally create your new application.

Some key items regarding the application are the Application ID (which is the Client ID), the Directory ID (which is the Tenant ID) and Client Secret.

#### Configuring App Permissions
The [Microsoft Graph documentation](https://docs.microsoft.com/en-us/graph) provides details on which permission are required for each permission type.

* To assign your application the right permissions, navigate to **Microsoft Entra Portal > Microsoft Entra ID > App Registrations**.
* Select the application we created before, and select “**API Permissions**” or “**View API Permissions**”.
* To assign a new permission to your application, click the “**Add a permission**” button.
* From the “**Request API Permissions**” screen click “**Office 365 Exchange Online**”.
  > _The Office 365 Exchange Online might not be a selectable API. In thise case, select "APIs my organization uses" and search here for "Office 365 Exchange Online"__
* For this connector the following permissions are used as **Application permissions**:
  *	Manage Exchange As Application ***Exchange.ManageAsApp***
* To grant admin consent to our application press the “**Grant admin consent for TENANT**” button.

#### Assign Microsoft Entra ID roles to the application
Microsoft Entra ID has more than 50 admin roles available. The **Exchange Administrator** role should provide the required permissions for any task in Exchange Online PowerShell. However, some actions may not be allowed, such as managing other admin accounts, for this the Global Administrator would be required. and Exchange Administrator roles. Please note that the required role may vary based on your configuration.
* To assign the role(s) to your application, navigate to **Microsoft Entra Portal > Microsoft Entra ID > Roles and administrators**.
* On the Roles and administrators page that opens, find and select one of the supported roles e.g. “**Exchange Administrator**” by clicking on the name of the role (not the check box) in the results.
* On the Assignments page that opens, click the “**Add assignments**” button.
* In the Add assignments flyout that opens, **find and select the app that we created before**.
* When you're finished, click **Add**.
* Back on the Assignments page, **verify that the app has been assigned to the role**.

For more information about the permissions, please see the Microsoft docs:
* [Permissions in Exchange Online](https://learn.microsoft.com/en-us/exchange/permissions-exo/permissions-exo).
* [Find the permissions required to run any Exchange cmdlet](https://learn.microsoft.com/en-us/powershell/exchange/find-exchange-cmdlet-permissions?view=exchange-ps).
* [View and assign administrator roles in Microsoft Entra ID](https://learn.microsoft.com/en-us/powershell/exchange/find-exchange-cmdlet-permissions?view=exchange-ps).

#### Authentication and Authorization
There are multiple ways to authenticate to the Graph API with each has its own pros and cons, in this example we are using the Authorization Code grant type.

*	First we need to get the **Client ID**, go to the **Microsoft Entra Portal > Microsoft Entra ID > App Registrations**.
*	Select your application and copy the Application (client) ID value.
*	After we have the Client ID we also have to create a **Client Secret**.
*	From the Microsoft Entra Portal, go to **Microsoft Entra ID > App Registrations**.
*	Select the application we have created before, and select "**Certificates and Secrets**". 
*	Under “Client Secrets” click on the “**New Client Secret**” button to create a new secret.
*	Provide a logical name for your secret in the Description field, and select the expiration date for your secret.
*	It's IMPORTANT to copy the newly generated client secret, because you cannot see the value anymore after you close the page.
*	At last we need to get the **Tenant ID**. This can be found in the Microsoft Entra Portal by going to **Microsoft Entra ID > Overview**.

### Synchronization settings

| Variable name                              | Description                                                                                                                    | Notes                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $portalBaseUrl                             | String value of HelloID Base Url                                                                                               | (Default Global Variable)                                                                                                                                                                                                                                                                                      |
| $portalApiKey                              | String value of HelloID Api Key                                                                                                | (Default Global Variable)                                                                                                                                                                                                                                                                                      |
| $portalApiSecret                           | String value of HelloID Api Secret                                                                                             | (Default Global Variable)                                                                                                                                                                                                                                                                                      |
| $EntraOrganization                         | String value of Microsoft Entra ID Organization                                                                                | Recommended to set as Global Variable                                                                                                                                                                                                                                                                          |
| $EntraTenantID                             | String value of Microsoft Entra ID Tenant ID                                                                                   | Recommended to set as Global Variable                                                                                                                                                                                                                                                                          |
| $EntraAppID                                | String value of Microsoft Entra ID App ID                                                                                      | Recommended to set as Global Variable                                                                                                                                                                                                                                                                          |
| $EntraAppSecret                            | String value of Microsoft Entra ID App Secret                                                                                  | Recommended to set as Global Variable                                                                                                                                                                                                                                                                          |
| $exchangeMailboxesFilter                   | String value of filter of which EXO shared mailboxes to include                                                                | Optional, when no filter is provided ($exchangeMailboxesFilter = $null), all mailboxes will be queried                                                                                                                                                                                                         |
| $productAccessGroup                        | String value of which HelloID group will have access to the products                                                           | Optional, if not found, the product is created without Access Group                                                                                                                                                                                                                                            |
| $calculateProductResourceOwnerPrefixSuffix | Boolean value of whether to check for a specific "owner" group in HelloID to use as resource owner for the products            | Optional, can only be used when the "owner group" exists and is available in HelloID                                                                                                                                                                                                                           |
| $calculatedResourceOwnerGroupSource        | String value of source of the shared mailboxes in HelloID                                                                      | Optional, if left empty, this will result in creation of a new group                                                                                                                                                                                                                                           |
| $calculatedResourceOwnerGroupPrefix        | String value of prefix to recognize the owner group                                                                            | Optional, the owner group will be queried based on the group name and the specified prefix and suffix - if both left empty, this will result in creation of a new group - if group is not found, it will be created                                                                                            |
| $calculatedResourceOwnerGroupSuffix        | String value of suffix to recognize the owner group                                                                            | Optional, the owner group will be queried based on the group name and the specified prefix and suffix - if both left empty, this will result in creation of a new group - if group is not found, it will be created                                                                                            |
| $productResourseOwner                      | String value of which HelloID group to use as resource owner for the products                                                  | Optional, if empty the groupname will be: "local/[group displayname] Resource Owners"                                                                                                                                                                                                                          |
| $productApprovalWorkflowId                 | String value of HelloID Approval Workflow GUID to use for the products                                                         | Optional, if empty. The Default HelloID Workflow is used. If specified Workflow does not exist the task will fail                                                                                                                                                                                              |
| $productVisibility                         | String value of which Visbility to use for the products                                                                        | Supported values: All, Resource Owner And Manager, Resource Owner, Disabled. For more information, see the HelloID Docs [here](https://docs.helloid.com/en/service-automation/products/product-settings-reference.html)                                                                                        |
| $productRequestCommentOption               | String value of which Comment Option to use for the products                                                                   | Supported values: Optional, Hidden, Required. For more information, see the HelloID Docs [here](https://docs.helloid.com/en/service-automation/products/product-settings-reference.html)                                                                                                                       |
| $productAllowMultipleRequests              | Boolean value of whether to allow Multiple Requests for the products                                                           | If True, the product can be requested unlimited times                                                                                                                                                                                                                                                          |
| $productFaIcon                             | String value of which Font Awesome icon to use for the products                                                                | For more valid icon names, see the Font Awesome cheat sheet [here](https://fontawesome.com/v5/cheatsheet)                                                                                                                                                                                                      |
| $productCategory                           | String value of which HelloID category will be used for the products                                                           | Required, must be an existing category if not found, the task will fail                                                                                                                                                                                                                                        |
| $productReturnOnUserDisable                | Boolean value of whether to set the option Return Product On User Disable for the products                                     | For more information, see the HelloID Docs [here](https://docs.helloid.com/en/service-automation/products/product-settings-reference.html)                                                                                                                                                                     |
| $removeProduct                             | Boolean value of whether to remove the products when they are no longer in scope                                               | If set to $false, obsolete products will be disabled                                                                                                                                                                                                                                                           |
| $overwriteExistingProduct                  | Boolean value of whether to overwrite existing products in scope with the specified properties of this task                    | If True, existing product will be overwritten with the input from this script (e.g. the approval worklow or icon). Only use this when you actually changed the product input. **Note:** Actions are always overwritten, no compare takes place between the current actions and the actions this sync would set |
| $overwriteAccessGroup                      | Boolean value of whether to overwrite existing access groups in scope with the specified access group this task                | Should be on false by default, only set this to true to overwrite product access group - Only meant for "manual" bulk update, not daily scheduled. **Note:** Access group is always overwritten, no compare takes place between the current access group and the access group this sync would set              |
| $ProductSkuPrefix                          | String value of prefix that will be used in the Code for the products                                                          | Optional, but recommended, when no SkuPrefix is provided the products won't be recognizable as created by this task                                                                                                                                                                                            |
| $exchangeMailboxUniqueProperty             | String value of name of the property that is unique for the EXO shared mailboxes and will be used in the Code for the products | The default value ("GUID") is set be as unique as possible                                                                                                                                                                                                                                                     |

## Remarks
- The Products are created and removed by default. Make sure your configuration is correct to avoid unwanted removals (and change this to disable)
- This group sync is desinged to work in combination with the [EXO SharedMailbox FullAccess Permissions to Productassignments Sync](https://github.com/Tools4everBV/HelloID-Conn-SA-Sync-EXO-SharedMailbox-FullAccess-Permissions-To-HelloID-Productassignments).

## Getting help
> _For more information on how to configure a HelloID PowerShell scheduled task, please refer to our [documentation](https://docs.helloid.com/hc/en-us/articles/115003253294-Create-Custom-Scheduled-Tasks) pages_

> _If you need help, feel free to ask questions on our [forum](https://forum.helloid.com)_

## HelloID Docs
The official HelloID documentation can be found at: https://docs.helloid.com/