# Dynamically Add Data Source Items on Reveal Client

This topic walks through the process of dynamically adding data sources at runtime to a Reveal client. You are not required to use a specific Reveal server, as this sample has the tables hard-coded in the GetAllowedDatabaseObjects function.  You will need to implement an API that returns the allowable objects based on your own security context.  Using the Reveal server API, you can achieve a similar result using the IRVObjectFilter interface.  Read more about that approach here: 


[https://help.revealbi.io/web/user-context#using-the-user-context-in-the-objectfilterprovider](https://help.revealbi.io/web/user-context#using-the-user-context-in-the-objectfilterprovider)

## Prerequisites

- Ensure you have access to a Reveal BI server.
- Your application must serve over HTTP/HTTPS and support JavaScript.

## Step 1: Set Up the Reveal Server

First, you need to configure the base URL to your Reveal server. This URL points to where the Reveal BI services are hosted.

```javascript
$.ig.RevealSdkSettings.setBaseUrl("[https://samples.revealbi.io/upmedia-backend/reveal-api/](https://samples.revealbi.io/upmedia-backend/reveal-api/)");
```

## Step 2: Initialize the Reveal View

Instantiate the `RevealView` to manage and render your dashboards. The `#revealView` is a placeholder element in your HTML where the dashboard will be rendered.

```javascript
var revealView = new $.ig.RevealView("#revealView");
```

## Step 3: Define Data Source Request Handler

Configure how the data sources are requested by implementing `onDataSourcesRequested`. This function defines how to fetch and configure the data sources based on the user's allowed access.

```javascript
revealView.onDataSourcesRequested = async (callback) => {
    var sqlDataSource = new $.ig.RVSqlServerDataSource();

    var listOfTableObjects = await GetAllowedDatabaseObjects();

    var datasourceItems = listOfTableObjects.map(obj => {
        var dsi = new $.ig.RVSqlServerDataSourceItem(sqlDataSource);
        dsi.title = obj.name + "_" + obj.type; // Unique name using name and type

        if (obj.type === "Table") {
            dsi.table = obj.name; // Table name
        } else if (obj.type === "StoredProcedure") {
            dsi.procedure = obj.name; // Stored Procedure name
        }

        return dsi;
    });

    // Only the allowed data source items are returned
    callback(new $.ig.RevealDataSources([], datasourceItems, true));
};
```

## Step 4: Implement Allowed Database Objects Fetching

You will need to implement the `GetAllowedDatabaseObjects` function to fetch the database objects that a user is permitted to access. Here, replace the mock data with a real data fetching logic as per your environment.

```javascript
async function GetAllowedDatabaseObjects() {
    // Replace the below mock data with your fetch logic
    const objects = [
        { name: "Customers", type: "Table" },
        { name: "Categories", type: "Table" },
        { name: "Sales by Year", type: "StoredProcedure" }
    ];

    return objects.map(obj => {
        return { name: obj.name, type: obj.type };
    });
}
```

## Conclusion

After these steps, your application will be equipped to dynamically load and manage dashboards with Reveal BI, tailored to the data access permissions of different users.
```

