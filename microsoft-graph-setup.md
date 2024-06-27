# How to call the Microsoft Graph API

Summary:
- Configure an app in Azure so that you can pull data from an application, without user interaction (appliciation permissions instead of delegated user permissions)
- Use the Microsoft Graph API to pull data from a SharePoint list

Use Case:
- You have data stored in SharePoint that you need to access from another system, without user involvement.

Important Links:
- [Microsoft Graph REST API v1.0 endpoint reference](https://learn.microsoft.com/en-us/graph/api/overview?view=graph-rest-1.0)
- [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)

## Configure Azure
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
To call an endpoint, pass your auth token in the Authorization header:
```
Authorization: Bearer {YourAuthToken}
```
Make sure you gave the proper permissions to access the resource you're trying to view.

See [get-items-from-sharepoint-list.md](get-items-from-sharepoint-list.md) for an example using the [Enumerate items in a list](https://learn.microsoft.com/en-us/graph/api/listitem-list?view=graph-rest-1.0&tabs=http) endpoint
