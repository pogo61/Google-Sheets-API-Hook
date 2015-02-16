# SOA Software API HOOK
Put a nice company logo image here. 
Link to product documentation and product overview page
## Google Sheets API 
### About the API
- CRUD spreadsheets on Google Drive
- Home Page: [SmartyStreet] (https://smartystreets.com)
- API Documentation: [Google Sheets API docs] (https://developers.google.com/google-apps/spreadsheets/)

### Pre-Reqs
- This will only work for [Google Apps for work] (https://www.google.com/intx/en_au/work/apps/business/?utm_source=google&utm_medium=cpc&utm_campaign=japac-smb-apps-bkws-au-en&utm_content=gafb&utm_term=google%20apps&gclid=CPGWm82o5cMCFU06vAod9kcAOA&gclsrc=ds). You, or your organisation, must have a subscription to this service. The reason for this is that this is the only service that Google provides 2-legged OAuth to (via a service account). Google uses only 3-legged OAuth with its free and open Google Docs. 3-legged OAuth is unsuitable to server to server integration.
- Register the application in the [Google Developers Console] (https://console.developers.google.com/). Creating an account if you have not already registered.
- once the App is defined, double click on it to be taken the the App details page. 
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
- Download the migrations.properties file, and edit it to replace the <replace this with your key> text with the "Container Key" of the ND or ND cluster in your target PM
- Login to PolicyManager  example: http://localhost:9900
- Select the root "Registry" organisation and click on the "Import Package" from the Actions navigation window on the right side of the screen
  - click on button to browse for the GoogleSheetsHookAPI.zip archive file 
  - make sure select the migrations.properties file 
  - click Okay to start the importation of the hook.
- this will create a Google Sheets API Hook Organisation with the requisite artefacts needed to run the API.
- you can us the API as is calling http://"URL of the Listener of your ND"/cell/cell, or you can create an API in CM and expost it.
    - if you chose to create an API in CM, you must create a Service in your CM tenant which is a Virtual Service of the Google_Sheets_Integrator VS that was created by the import. Then Go to CM and create a API from an existing Service.

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

#### Verify Connectivity
- Using curl http://"URL of the Listener of your ND"/cell/cell
-  the response should be {"status":"success", "cellContent": "value"} where "value" is the contents of the first cell, in the first workbook, of the first spreadsheet, that the user defined in the tns:userAccountEmail element of the security policy assertion.

### Modify and Build
In the event you need to change the API Hook.   Here are the instructions to do so. 

### License
Put a link to an open source license

