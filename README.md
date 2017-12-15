# ServiceNow - ServiceNow Event Log Update
The ServiceNow 4.0.2 integration with xMatters was customized to submit all event data such as Event Status, Delivery Status, and Response information directly to a table in ServiceNow. This was implemented for all ServiceNow forms: Incident Alerts, Engage with xMatters, as well as the Conference Bridge. The purpose of this design was to have all xMatters event data stored in a ServiceNow table. This table would then provide users the ability to create reports off of the information stored in the table. This table also serves as a source of xMatters data for any auditing purposes as the data stored in ServiceNow will be stored for a longer period of time than it would be in xMatters.

# Pre-Requisites
* ServiceNow 4.0.2
* Experience with ServiceNow/ xMatters integration

# Files
* [ServiceNow 4.0.2 Update Set](xmatters-servicenow-v4-0-2.xml) - This is the standard ServiceNow 4.0.2 update set or [here](https://xperts.xmatters.com/hc/en-us/articles/115004562626-ServiceNow-Update-Set-v4-0-Helsinki-Istanbul-Jakarta-?flash_digest=af59839cda4c247cf4e053bb1016a4e6186dd5a8) (Internal xMatters Resources Only).
* [ServiceNow Event Log](xmatters-event-log.xml) - This is the update set that will import the Event Log table into ServiceNow. This update set is only responsible for importing the Event Log table.
* [ServiceNow Communication Plan](ServiceNow402.zip) - This is the communication plan for the ServiceNow integration

# How it works
When events execute in xMatters, outbound integrations are triggered to submit event data to a ServiceNow table on Event Status, Delivery Status, and Response. The available information related to each type of outbound integration trigger will be submitted to the table. The outbound integration builders submit directly to the table in ServiceNow with the data, there is no transform map required for access permissions into the table.

## Event Log Table

| Column Label  | Field Type    | Reference|
| ------------- |:-------------:| -----:|
|Created|	Date/Time|	|
|Sys ID|	Sys ID (GUID)|	|
|Updates|	Integer|	|
|Updated by|	String|	|
|Updated|	Date/Time|	|
|Assigned to|	Reference|	User|
|Assignment group|	Reference|	Group|
|Comment|	String|	|
|Date|	Date/Time|	|
|Delivery event type|	String|	|
|Delivery message|	String|	|
|Device|	String|	|
|Device delivery status|	String|	|
|Device ID|	String|	|
|Event ID|	String|	|
|Event status|	String|	|
|Event update type|	String|	|
|Form type|	String|	|
|Incident|	Reference|	Incident|

## Outbound Integrations
The information below lists the data that is imported into the table.

Below are each type of data submission that will update the Event Log table in ServiceNow. Where, u_formtype will be either Incident Alert, Engage with xMatters, or Conference Bridge depending on the form. Each JSON package is the same for each form.

### Event Status
```
json = {
  'u_formtype': 'Incident Alerts',
  'u_event_update_type': 'Event Status',
  'u_event_id': callback.eventIdentifier,
  'u_incident_nbr': callback.eventProperties.number,
  'u_incident_sys_id': callback.eventProperties.sys_id,
  'u_assignment_group': callback.eventProperties.assignment_group,
  'u_assigned_to': callback.eventProperties.assigned_to,
  'u_event_status': callback.status,
  'u_date': gmtDate

};
```
### Delivery Status
```
json = {
  'u_formtype': 'Engage with xMatters',
  'u_event_update_type': 'Delivery Status',
  'u_event_id': callback.eventIdentifier,
  'u_incident_nbr': callback.eventProperties.number,
  'u_incident_sys_id': callback.eventProperties.sys_id,
  'u_assignment_group': callback.eventProperties.assignment_group,
  'u_assigned_to': callback.eventProperties.assigned_to,
  'u_date': gmtDate,
  'u_device': callback.device,
  "u_delivery_message": callback.message,
  "u_device_delivery_status": callback.deliveryStatus,
  "u_delivery_event_type": callback.eventType,
  "u_device_id": callback.deviceId,
  "u_recipient": callback.recipient
};
```
### Response
```
json = {
  'u_formtype': 'Conference Bridge',
  'u_event_update_type': 'Notification Response',
  'u_event_id': callback.eventIdentifier,
  'u_incident_nbr': callback.eventProperties.number,
  'u_incident_sys_id': callback.eventProperties.sys_id,
  'u_assignment_group': callback.eventProperties.assignment_group,
  'u_assigned_to': callback.eventProperties.assigned_to,
  'u_date': gmtDate,
  'u_device': callback.device,
  "u_comment": callback.annotation,
  "u_recipient": callback.recipient,
  "u_response": callback.response
};
```



## Business Use Case
This customization works well for organizations that need to store xMatters event data for longer periods of time than the xMatters instance is capable of retaining. This also works well for reporting purposes. Since the data will be inserted into a table that exists in ServiceNow, ServiceNow administrators will have the ability to create and generate reports.

## What has been updated
* New outbound integrations have been created for the Incident Alerts, Engage with xMatters, and Conference Bridge Form. They have been updated for each type of trigger: Event Status, Delivery Status, and Response.
* A new table has been created in ServiceNow which is responsible for storing the xMatters event data.

## Implementing for newer ServiceNow versions
This integration extension was built with ServiceNow 4.0.2. However, it can easily be ported to newer version of the ServiceNow/xMatters integration. From a high-level, all one would have to do is the following:
* Import the ServiceNow Event Log update set into the ServiceNow environment
* Import the ServiceNow 4.0.2 communication plan and copy the outbound integrations to the new communication plan version.

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
2. Perform all standard form related configurations for access permissions. If references are required see [here](https://support.xmatters.com/hc/en-us/articles/115004327803?_ga=2.256749643.11742807.1513024307-690329112.1507577016)
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
