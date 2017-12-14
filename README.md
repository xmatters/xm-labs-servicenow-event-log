# ServiceNow 4.0.2 - ServiceNow Event Log Update
The ServiceNow 4.0.2 integration with xMatters was customized to submit all event data such as Event Status, Delivery Status, and Response information directly to a table in ServiceNow. This was implemented for all ServiceNow forms: Incident Alerts, Engage with xMatters, as well as the Conference Bridge. The purpose of this design was to have all xMatters event data stored in a ServiceNow table. This table would then provide users the ability to create reports off of the information stored in the table. This table also serves as a source of xMatters data for any auditing purposes as the data stored in ServiceNow will be stored for a longer period of time than it would be in xMatters.

# Pre-Requisites
* ServiceNow 4.0.2
* Working knowledge of the xMatters/ServiceNow integration

# Files
* [ServiceNow 4.0.2 Update Set](xmatters-servicenow-v4-0-2.xml) - This is the standard ServiceNow 4.0.2 update set
* [ServiceNow Event Log](xmatters-event-log.xml) - This is the update set that will provide the customizations required.
* [ServiceNow Communication Plan](ServiceNow402.zip) - This is the communication plan for the ServiceNow integration

# How it works
When events execute in xMatters, outbound integrations are triggered to submit to a ServiceNow table on Event Status, Delivery Status, and Response. The available information related to each type of outbound integration trigger will be submitted to the table. The outbound integration builders submit directly to the table in ServiceNow with the data, there is no transform map required for access permissions into the table.

# Installation
The installation steps covered in this article only relate to getting this customization of the integration to work.

## Configure xMatters REST API User

This integration requires a user who can authenticate REST web service calls when injecting events.

This user needs to be able to work with events, but does not need to update administrative settings. While you can use the default Company Supervisor role to authenticate REST web service calls, the best method is to create a user specifically for this integration with the "REST Web Service User" role that includes the permissions and capabilities. If this role does not exist in your deployment already, you may need to ask your xMatters Client Success Manager to create it for you.

**To create an integration user:**

1. Log in to the target xMatters system.
2. On the Users tab, click Add.
3. Enter the appropriate information for your new user. Because this user will affect how messages appear for recipients and how events will be displayed in the reports and Communication Center, you may want to identify the user as specific to ServiceNow; for example:
    * First Name: ServiceNow
    * Last Name: Integration
    * User ID: servicenow
4. Assign the user to the REST Web Service User role.
5. Set the user ID and password.
    * Make a note of these details; you will need them when configuring other parts of the integration.
6. Click **Save**.

## Install and Configure ServiceNow

**To complete the following from within ServiceNow:**

1. Log into ServiceNow as a user with the "admin" role, and open the Navigator.
2. Locate and expand the Integration - xMatters entry in the All Applications list, and then click xMatters Configuration.
3. Click the tabs at the top of the window to switch between pages.

### Install the xMatters Application
To install the xMatters application complete the following:
1. From within ServiceNow, in the navigation panel search for Retrieved Update Sets.
2. From within Retrieved Update Sets select Import Update Set from XML.
3. From within Import XML, import the ServiceNow 4.0.2 Update Set and select Upload.
4. Once imported, select the xMatters update set to view the entire update set package.
5. From within the xMatters update set package, select Preview Update Set.
6. Once successfully previewed, select Commit Update Set.
7. Once successfully committed, the application will have been successfully installed. To confirm search for xMatters in the navigation panel.

### Install the xMatters Event Log
To install the xMatters Event Log complete the following:
1. From within ServiceNow, in the navigation panel search for Retrieved Update Sets.
2. From within Retrieved Update Sets select Import Update Set from XML.
3. From within Import XML, import the xMatters Event Log Update Set and select Upload.
4. Once imported, select the xMatters Event update set to view the entire update set package.
5. From within the xMatters Event update set package, select Preview Update Set.
6. Once successfully previewed, select Commit Update Set.
7. Once successfully committed, the customization has been uploaded to ServiceNow.

### Create the ServiceNow API User
1. From within ServiceNow in the navigation panel search for User Administration > Users. Select Users.
2. From within Users, select New on the top ribbon.
3. Create an API user with a unique user id such as "xmatters". Insert a First Name, Last Name, enter a password (note it as it will be used later), and select Web service access only.
4. Select Save.
5. Once the user has been saved, assign the following roles:
      * x_xma_xmatters.xmatters_rest
      * x_xma_xmatters_xmatters_event_log_user

### Complete the xMatters Configuration pages
From within ServiceNow, search for xMatters Configuration from within the navigation panel.

#### Common Configuration
From within common configuration enter the required information.

#### Incident Alerts
From within Incident Alerts enter the required information.

#### Engage with xMatters
From within Engage with xMatters enter the required information.

## Import the xMatters Communication Plan
1. Import the ServiceNow Communication Plan attached in the article
2. Perform all standard form related configurations for access permissions. If references required see [here](https://support.xmatters.com/hc/en-us/articles/115004327803?_ga=2.256749643.11742807.1513024307-690329112.1507577016)
3. Once the standard configurations have been implemented properly, navigate to the Integration Builder.
4. From within the Integration Builder, select Edit Endpoints.
5. Ensure that the ServiceNow endpoint is properly set with the proper ServiceNow information.

# Testing
1. Submit each of the following events to xMatters
      * Incident Alerts
      * Engage with xMatters
      * Conference Bridge
2. From within ServiceNow, search from within the navigation panel for xMatters Event Logs
3. From within the xMatters Event Logs, confirm that the Event Status, Delivery Status, and Response information are populated in the table.

# Troubleshooting
For any troubleshooting complete the following:
1. Navigate to the Integration Builder tab of the ServiceNow Communication Plan
2. From within the Integration Builder tab, review the logs of each outbound integration.
