# Akana API HOOK
![Image of Akana] 
(https://www.akana.com/img/formerlyLOGO8.png) 
[Akana.com](http://akana.com)

## Google Sheets API 
### About the API
- CRUD spreadsheets on Google Drive
- Home Page: [Google Sheets API docs] (https://developers.google.com/google-apps/spreadsheets/)
- API Documentation: [Google Sheets API docs] (https://developers.google.com/google-apps/spreadsheets/)

### Pre-Reqs
- you must install the pso extensions custom polices:
    + unzip the com.soa.pso.openapi.extensions_7.2.2.zip (available in this repository) into the <Policy Manager Home>/sm70 directory. This will result in files placed in the sm70/lib/pso.opeapi.extensions_7.2.2 subdirectory
    + restart both PM and ND(s)
    + Using the SOA Admin Console, install the following features in each PM container:
        * SOA Professional Services OpenAPI Extensions
        * SOA Professional Services OpenAPI Extensions UI
    + Using the SOA Admin Console, install the following features in each ND container:
        * SOA Professional Services OpenAPI Extensions
- you must install the com.soa.pso.policy.JWTToken_1.0.0.zip nto the <Policy Manager Home>/sm70 directory. This will result in files placed in the sm70/lib/pso.policy.JWTToken_1.0.0 subdirectory
    + restart ND(s) only
    +  Using the SOA Admin Console, on each ND:
        * select the Insert JWT as HTTP Header Policy Handler feature  
        * press the "install" button
        * follow the install wizard instructions and restart the ND
- This will only work for [Google Apps for work] (https://www.google.com/intx/en_au/work/apps/business/?utm_source=google&utm_medium=cpc&utm_campaign=japac-smb-apps-bkws-au-en&utm_content=gafb&utm_term=google%20apps&gclid=CPGWm82o5cMCFU06vAod9kcAOA&gclsrc=ds). You, or your organisation, must have a subscription to this service. The reason for this is that this is the only service that Google provides 2-legged OAuth to (via a service account). Google uses only 3-legged OAuth with its free and open Google Docs. 3-legged OAuth is unsuitable to server to server integration.
- Register the application in the [Google Developers Console] (https://console.developers.google.com/). Creating an account if you have not already registered.
- once the Project is defined, double click on it to be taken the the Project details page. 
- Click on the "Credentials" left hand menu item
- When the Credentials Portlet is displaid, click on the "Create new Client ID" button. then select the "Service Acccount" for the CLient ID type. (note: you must be the Google Apps sdministrator to do this).
- once this is done you should have a new section in the Credentials Portlet for the Service Account.
- Copy the clientId of the Service Account
- login to the  [Google Apps for work Admin Console] (https://admin.google.com) select the security icon, scroll to the botom of the admin page and select the "Show more" link, select the "Advanced Settings" link that appears, then the "Manage API client access" link.
- in the "Client Name" field paste the Service Account ClientId you coppied, then copy this list of API scopes into the Scopes field and click the "Authorize" button:
https://spreadsheets.google.com/feeds,https://www.googleapis.com/auth/drive,https://www.googleapis.com/auth/drive.appdata,https://www.googleapis.com/auth/drive.apps.readonly,https://www.googleapis.com/auth/drive.file,https://www.googleapis.com/auth/drive.metadata.readonly,https://www.googleapis.com/auth/drive.readonly 

### Getting Started Instructions
#### Download and Import
- Download GoogleSheetsHookAPI.zip
- Download the migrations.properties file, and edit it to replace the <replace this with your key> text with the "Container Key" of the ND or ND cluster in your target PM.
    - the container key is found by going to the "Deatils Tab" of the ND cluster, or ND defined in the Policy Manager Console, then looking at the " Container Overview" tab on that page, and copying the "Container Key:" value. ![container key screenshot](https://github.com/pogo61/Google-Sheets-API-Integration/blob/master/Screen%20Shot%202015-03-18%20at%2011.24.45%20am.png "ND Container Key")
- Login to PolicyManager  example: http://localhost:9900
- Select the root "Registry" organisation and click on the "Import Package" from the Actions navigation window on the right side of the screen
  - click on button to browse for the GoogleSheetsHookAPI.zip archive file 
  - make sure select the migrations.properties file 
  - click Okay to start the importation of the hook.
- this will create a Google Sheets API Hook Organisation with the requisite artefacts needed to run the API.
- you can use the API as is calling http://"URL of the Listener of your ND"/sheets_hook, or you can create an API in CM and expost it.
    - if you chose to create an API in CM, you must create a Service in your CM tenant which is a Virtual Service of the Google_Sheets_API VS that was created by the import. Then Go to CM and create a API from an existing Service.

#### Verify Import
- Expand the services folder in the Google Sheets API Hook you imported and find Google_Sheets_Integrator VS

#### Activate Anonymous Contract
- Expand the contracts folder in the Google Sheets API Hook you imported and find the "Anonymous" contract under the "Provided Contracts" folder
- click on the "Activate Contract" workflow activity in the righ-hand Activities portlet
- ensure that the status changes to "Workflow Is Completed"

#### Configure Security
- Go to Google Sheets API Hook -> Policies -> Operational Policies ->    Insert JWT into Downstream request policy
    - Click "modify" in the XML Policy Tab. An XML Policy Content editor dialog will be displayed.
    - change the value of the tns:fileLocation element to be the location and name of the file containing the private key of the Service account (this is obtain by exporting the key for the App in the [Google Developers Console] (https://console.developers.google.com/)). Note that the location must be absolute, not relative.
    - change the value of the tns:serviceAccountEmail element to be the email address of the Service account (this is obtain by exporting the key for the App in the [Google Developers Console] (https://console.developers.google.com/)).
    - change the value of the tns:userAccountEmail element to be the email address of the person who's spreadsheets the API is going to use (this must be a user in the Google Apps for work domain that the administrator has created).
    - save the changes
    - click on the "Activate Policy" workflow activity in the righ-hand Activities portlet
    - ensure that the status changes to "State: Active"
- Go to Google Sheets API Hook -> Policies -> Operational Policies ->    GetAuthToken policy
    - click on the "Activate Policy" workflow activity in the righ-hand Activities portlet
    - ensure that the status changes to "State: Active"

#### Verify Connectivity
- Using curl http://"URL of the Listener of your ND"/sheets_hook/helloworld
-  the response should be similar to the below, listing your spreadsheets:  
    ```<feed xmlns="http://www.w3.org/2005/Atom" xmlns:openSearch="http://a9.com/-/spec/opensearchrss/1.0/">
        <id>https://spreadsheets.google.com/feeds/spreadsheets/private/full</id>
        <updated>2015-03-17T02:16:48.634Z</updated>
        <category scheme="http://schemas.google.com/spreadsheets/2006" term="http://schemas.google.com/spreadsheets/2006#spreadsheet"/>
        <title type="text">Available Spreadsheets - paulpog@japarasolutions.com</title>
        <link href="http://docs.google.com" rel="alternate" type="text/html"/>
        <link href="https://spreadsheets.google.com/feeds/spreadsheets/private/full" rel="http://schemas.google.com/g/2005#feed" type="application/atom+xml"/>
        <link href="https://spreadsheets.google.com/feeds/spreadsheets/private/full" rel="self" type="application/atom+xml"/>
        <openSearch:totalResults>5</openSearch:totalResults>
        <openSearch:startIndex>1</openSearch:startIndex>
        <entry>
            <id>https://spreadsheets.google.com/feeds/spreadsheets/private/full/1Eqwv5WFMhCAomR5rT4ciklXpX4w7PvVpeCm7eRmgQDY</id>
            <updated>2015-03-12T10:03:26.506Z</updated>
            <category scheme="http://schemas.google.com/spreadsheets/2006" term="http://schemas.google.com/spreadsheets/2006#spreadsheet"/>
            <title type="text">Test</title>
            <content type="text">Test</content>
            <link href="https://spreadsheets.google.com/feeds/worksheets/1Eqwv5WFMhCAomR5rT4ciklXpX4w7PvVpeCm7eRmgQDY/private/full" rel="http://schemas.google.com/spreadsheets/2006#worksheetsfeed" type="application/atom+xml"/>
            <link href="https://docs.google.com/a/japarasolutions.com/spreadsheets/d/1Eqwv5WFMhCAomR5rT4ciklXpX4w7PvVpeCm7eRmgQDY/edit" rel="alternate" type="text/html"/>
            <link href="https://spreadsheets.google.com/feeds/spreadsheets/private/full/1Eqwv5WFMhCAomR5rT4ciklXpX4w7PvVpeCm7eRmgQDY" rel="self" type="application/atom+xml"/>
            <author>
                <name>paulpog</name>
                <email>paulpog@japarasolutions.com</email>
            </author>
        </entry>
    </feed>```

### How Hello World Works
#### An Akana Integration Primer
The Trello_API_Hook API is a "Virtual Service". That is, its interface is not that of a real service implementation. It can be a proxy to a "real" implementation, or it can be an aggregate (a combination) of a number of "real" implementations. In Policy Manager a "real" implementation is called a "Physical Service".
Apart from offering a different interface to the Physical Service, a Virtual Service offers the ability to attach Policies for security, logging, QoS, and a number of other non-functional capabilities.
Virtual Services also have the ability to have Custom Process and Scripts run before the Physical Service is called. Here is where a lot of the magic of Integration occurs.

#### Hello World
To create the helloworld operation in the Trello_API_Hook VS (Virtual Service) the Google Sheets API RAML was copied and the following was added to the copied RAML to create the Google Sheets API Hook RAML:  
    /helloworld:  
      &nbsp;get:  
        &nbsp;&nbsp;description: "returns all spreadsheets on your google drive"  
        &nbsp;&nbsp;&nbsp;responses:  
          &nbsp;&nbsp;&nbsp;&nbsp;200:  
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;body:  
              &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;application/atom+xml:  

Then a VS was created by using the RAML as the definition source.
Then all the Operations in the VS were mapped to the same operations in the Google Sheets API PS (Physical Service), except the "helloworld" operation, which is mapped to the GET /spreadsheets/{visibility}/{protection} operation.

However, you can see that the "/helloworld" operation has no parameters, but the "GET /spreadsheets/{visibility}/{protection}" operation needs a value for the {visibility} and {protection} URI parameters. Therefore we need to run a script to set up the request message to add these values before calling the operation.

Go to the Google_Sheets_API_Hook VS -> Operations Tab -> GET /hellowworld operation -> Process tab you'll see this image:
![Helloworld process] 
(https://github.com/pogo61/Google-Sheets-API-Hook/blob/master/Hello%20World%20Process.png)

Double click on the Script activity and the invoke activity to see how these work to make the Hello World operation call successful.


### Create Your Own Integration with the Google Sheets API
The Hello World operation is one simple way of integrating or extending your API's.
Take a look at the [Google Sheets API Integration](https://github.com/pogo61/Google-Sheets-API-Integration).
this will give you a deeper inderstanding of the richness of our gateway product in integrating to API's    

### Modify and Build
In the event you need to change the API Hook.   Here are the instructions to do so. 

### License
Put a link to an open source license

