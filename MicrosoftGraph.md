# How to call the Microsoft Graph API

Important Links:
- [Microsoft Graph REST API v1.0 endpoint reference](https://learn.microsoft.com/en-us/graph/api/overview?view=graph-rest-1.0)
- [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)

## Register an app in Azure
### 1. Register an app in Azure
```
Azure Home -> App Registrations
```
Create a new app registration. Make note of the Application (client) id on the ```Overview``` page; you'll need this when you get an auth token.
### 2. Configure API permissions<br>
```
Azure Home -> App Registrations -> Your app -> Manage -> API Permissions
```
You'll need to grant access to specific resources that you want the app to access. Make sure to choose "Microsoft Graph" when choosing the permissions.
- Click ```Add Permission```
- Choose ```Microsoft Graph``` and then ```Application Permissions```
- Find the permissions you need and select those to add them
  - You can tell what permissions you need in the documentation for each endpoint
  - Note that you may need an Azure admin to approve the permission grant at your organization
### 3. Create a client secret
```
Azure Home -> App Registrations -> Your app -> Manage -> Certificates and Secrets
```
You'll need to use this client secret to get an auth token.
- Create a secret and give it an expiration date
- Store the secret in a secure place, like a password manager
## Test with MS Graph Explorer
https://developer.microsoft.com/en-us/graph/graph-explorer

You can use [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) to test endpoints under your user.
Note that this works with delegated permissions, so it is not exactly the same as the application permissions you set up for your app. But it's a good way to test endpoints and learn about the data structures.

## Get your Tenant ID
You'll need your Tenant ID to get an auth token. If you don't know your Tenant ID, you can find it easily using the [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer).

Run the below query in Graph Explorer:
```
GET https://graph.microsoft.com/v1.0/organization
```
The Tenant ID will be the ```id``` key in the response. In this example, the Tenant ID is **asdf-123**
```
{
   "value":[
      {
         "id":"asdf-123",
         "city":"Denver",
         "country":"USA"
      }
   ]
}
```
## Get an Auth token
The MS Graph API uses OAuth 2.0 (client credentials) authentication. You will need the ```Tenant ID```, ```Client ID```, and ```Client Secret``` to get a token.
### Token url
```
POST https://login.microsoftonline.com/{TenantID}/oauth2/v2.0/token
```
- Pass your ```Tenant ID``` in the token url
### Request body
```
{
   "client_id":"123456",
   "client_secret":"asdfgh",
   "grant_type":"client_credentials",
   "scope":"https://graph.microsoft.com/.default"
}
```
- ```client_id```
  - Pass the Application (client) id from the ```Overview``` page of your App Registration in Azure
- ```client_secret```
  - Pass the Client Secret Value from the secret you configured in Azure
- ```grant_type```
  - Pass ```client_credentials```
- ```scope```
  - Pass ```https://graph.microsoft.com/.default```
  - This tells the token endpoint to look at the permissions you granted earlier in the app registration
## Make your first call to an endpoint
In this example, we'll look at reading items from a SharePoint list. This list stores order information like the customer's FirstName, LastName, Email, etc.
### Get items from a SharePoint list
```
GET https://graph.microsoft.com/v1.0/sites/{SiteID}/Lists/{ListID}/items
```
- Update with the ```Site ID``` of your SharePoint site and the ```List ID``` of your SharePoint list

Response:
- By default, this endpoint only returns general information about items in the list like created and modified dates.
```
{
   "value":[
      {
         "createdDateTime":"2024-06-01T15:33:22Z",
         "lastModifiedTime":"2024-06-09T09:21:12Z",
         "id":"1",
         "createdBy":{...},
         "lastModifiedBy":{...}
      },
      {
         "createdDateTime":"2024-06-15T15:33:22Z",
         "lastModifiedTime":"2024-06-20T09:21:12Z",
         "id":"2",
         "createdBy":{...},
         "lastModifiedBy":{...}
      },
{
         "createdDateTime":"2024-06-05T15:33:22Z",
         "lastModifiedTime":"2024-06-19T09:21:12Z",
         "id":"3",
         "createdBy":{...},
         "lastModifiedBy":{...}
      }
   ]
}
```
#### Use ```expand = fields``` to see the data in your list columns
To see the information stored in your list columns, use the ```expand``` parameter

Response:
```
{
   "value":[
      {
         "createdDateTime":"2024-06-01T15:33:22Z",
         "lastModifiedTime":"2024-06-09T09:21:12Z",
         "id":"1",
         "createdBy":{...},
         "lastModifiedBy":{...},
         "fields":{
            "FirstName":"John",
            "LastName":"Doe",
            "Email":"johnDoe@example.com",
            "Phone Number":"123-456-7890",
            "Order Number":"1001"
         }
      },
      {
         "createdDateTime":"2024-06-15T15:33:22Z",
         "lastModifiedTime":"2024-06-20T09:21:12Z",
         "id":"2",
         "createdBy":{...},
         "lastModifiedBy":{...},
         "fields":{
            "FirstName":"Johnny",
            "LastName":"Doe",
            "Email":"johnDoe@example.com",
            "Phone Number":"123-456-7890",
            "Order Number":"1002"
         }
      },
{
         "createdDateTime":"2024-06-05T15:33:22Z",
         "lastModifiedTime":"2024-06-19T09:21:12Z",
         "id":"3",
         "createdBy":{...},
         "lastModifiedBy":{...},
         "fields":{
            "FirstName":"Liz",
            "LastName":"Doe",
            "Email":"ElizabethDoe@example.com",
            "Phone Number":"234-567-8912",
            "Order Number":"1003"
         }
      }
   ]
}
```

#### Use ```$select``` to limit columns for better performance
You can limit the columns returned with ```$select``` to specify only the ones you want.

Make sure you use the internal name of the columns: [How to find the internal name of SharePoint columns](https://ganeshsanapblogs.wordpress.com/2023/04/17/how-to-find-the-internal-name-of-sharepoint-columns/)
```
GET https://graph.microsoft.com/v1.0/sites/{SiteID}/Lists/{ListID}/items?expand=fields($select=FirstName,LastName,Email)
```
Response:
```commandline
{
   "value":[
      {
         "createdDateTime":"2024-06-01T15:33:22Z",
         "lastModifiedTime":"2024-06-09T09:21:12Z",
         "id":"1",
         "createdBy":{...},
         "lastModifiedBy":{...},
         "fields":{
            "FirstName":"John",
            "LastName":"Doe",
            "Email":"johnDoe@example.com"
         }
      },
      {
         "createdDateTime":"2024-06-15T15:33:22Z",
         "lastModifiedTime":"2024-06-20T09:21:12Z",
         "id":"2",
         "createdBy":{...},
         "lastModifiedBy":{...},
         "fields":{
            "FirstName":"Johnny",
            "LastName":"Doe",
            "Email":"johnDoe@example.com"
         }
      },
      {
         "createdDateTime":"2024-06-05T15:33:22Z",
         "lastModifiedTime":"2024-06-19T09:21:12Z",
         "id":"3",
         "createdBy":{...},
         "lastModifiedBy":{...},
         "fields":{
            "FirstName":"Liz",
            "LastName":"Doe",
            "Email":"ElizabethDoe@example.com"
         }
      }
   ]
}
```
#### Filter on columns in the list
Use the filter parameter to filter on a list column. Note that **you can only filter on indexed columns.**

Learn more about filtering syntax here: [Use the $filter query parameter
](https://learn.microsoft.com/en-us/graph/filter-query-parameter?tabs=http)
```
GET https://graph.microsoft.com/v1.0/sites/{SiteID}/Lists/{ListID}/items?expand=fields($select=FirstName,LastName,Email)&filter=fields/Email eq 'johnDoe@example.com'
```
Response:
```
{
   "value":[
      {
         "createdDateTime":"2024-06-01T15:33:22Z",
         "lastModifiedTime":"2024-06-09T09:21:12Z",
         "id":"1",
         "createdBy":{...},
         "lastModifiedBy":{...},
         "fields":{
            "FirstName":"John",
            "LastName":"Doe",
            "Email":"johnDoe@example.com"
         }
      },
      {
         "createdDateTime":"2024-06-15T15:33:22Z",
         "lastModifiedTime":"2024-06-20T09:21:12Z",
         "id":"2",
         "createdBy":{...},
         "lastModifiedBy":{...},
         "fields":{
            "FirstName":"Johnny",
            "LastName":"Doe",
            "Email":"johnDoe@example.com"
         }
      }
   ]
}
```