# Get items from a SharePoint list
In this example, we'll look at reading items from a SharePoint list. This list stores order information like the customer's FirstName, LastName, Email, Phone Number, and Order Number.

API documentation: [Enumerate items in a list](https://learn.microsoft.com/en-us/graph/api/listitem-list?view=graph-rest-1.0&tabs=http)

**Request**

The basic URL to get items in a SharePoint list is:
```
GET https://graph.microsoft.com/v1.0/sites/{SiteID}/Lists/{ListID}/items
```
Update this with the ```Site ID``` of your SharePoint site and the ```List ID``` of your SharePoint list.

You'll also need to pass your auth token in the ```Authorization``` header:
```
Authorization: Bearer {YourAuthToken}
```

**Response**

By default, this endpoint only returns general information about items in the list like created and modified dates.
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

### Use ```expand = fields``` to see the data in your list columns
To see the information stored in your list columns, use the ```expand``` parameter. This will return all fields in the list.
```
GET https://graph.microsoft.com/v1.0/sites/{SiteID}/Lists/{ListID}/items?expand=fields
```

**Response**
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

### Use ```$select``` to limit columns for better performance
You can limit the columns returned with ```$select``` to specify only the ones you want.

Make sure you use the internal name of the columns: [How to find the internal name of SharePoint columns](https://ganeshsanapblogs.wordpress.com/2023/04/17/how-to-find-the-internal-name-of-sharepoint-columns/)
```
GET https://graph.microsoft.com/v1.0/sites/{SiteID}/Lists/{ListID}/items?expand=fields($select=FirstName,LastName,Email)
```

**Response**
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

### Filter on columns in the list
Use the filter parameter to filter on a list column. Note that **you can only filter on indexed columns.**

Learn more about filtering syntax here: [Use the $filter query parameter
](https://learn.microsoft.com/en-us/graph/filter-query-parameter?tabs=http)
```
GET https://graph.microsoft.com/v1.0/sites/{SiteID}/Lists/{ListID}/items?expand=fields($select=FirstName,LastName,Email)&filter=fields/Email eq 'johnDoe@example.com'
```

**Response**
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
