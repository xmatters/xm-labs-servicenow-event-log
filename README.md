# ServiceNow 4.0.2 - ServiceNow Event Log Update
The ServiceNow integration with xMatters was customized to submit all event data such as Event Status, Delivery Status, and Response information directly to a table in ServiceNow. This was implemented for all ServiceNow forms: Incident Alerts, Engage with xMatters, as well as the Conference Bridge. The purpose of this design was to have all xMatters event data stored in a ServiceNow table. This table would then provide users the ability to create reports off of the information stored in the table. This table also serves as a source of xMatters data for any auditing purposes as the data stored in ServiceNow will be stored for a longer period of time than it would be in xMatters.

# Pre-Requisites
* ServiceNow 4.0.2

# Files
* [ServiceNow 4.0.2 Update Set](xmatters-servicenow-v4-0-2.xml) - This is the standard ServiceNow 4.0.2 update set
* [ServiceNow Event Log](xmatters-event-log.xml) - This is the update set that will provide the customizations required.
* [ServiceNow Communication Plan](ServiceNow402.zip) - This is the communication plan for the ServiceNow integration

# How it works
When events execute in xMatters, outbound integrations are triggered to submit to a ServiceNow table on Event Status, Delivery Status, and Response. The available information related to each type of outbound integration trigger will be submitted to the table. The outbound integration builders submit directly to the table in ServiceNow with the data, there is no transform map required for access permissions into the table.

# Installation
The installation steps covered in this article only relate to getting this customization of the integration to work.

## xMatters

### Create an integration user
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

## ServiceNow

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
5. Once the user has been saved, assign the x_xma_xmatters.xmatters_rest role to the user.

### Complete the xMatters Configuration pages
From within ServiceNow, search for xMatters Configuration from within the navigation panel.

#### Common Configuration
From within common configuration enter the required information.

#### Data Sync
No specific requirements regarding this page. This is free to be customized as required.
* Sites are required for a successful sync so ensure that a Site is entered.

**Key Notes:**
* Select Enable Dynamic Sync if you intend to use for dynamic sync, otherwise leave unchecked for the Batch sync.
* Understand that whatever Role listed in the People section will always be applied to the user.

# Testing

## Testing the Dynamic Sync
**Note:** To successfully test this section ensure that Enable Dynamic Sync has been checked.
Complete the following to successfully test the Dynamic Sync:
1. From within ServiceNow in the navigation panel search for User Administration > Groups. Select Groups.
2. From within Groups, create three new Groups and assign the x_xma_xmatters.xmatters role to each of the groups.
      * xMatters Company Supervisor
      * xMatters Developer
      * xMatters Support User
3. Once the three groups have been created and the x_xma_xmatters.xmatters role has been assigned to each.
4. Review xMatters to confirm that the groups have not been created. The integration is designed to not sync xMatters Role Provisioning Groups.
5. Once confirmed, begin adding users to the Group. You will find that as the users get added to each respective group that they will gain the roles.

## Testing the Batch Load
**Note:** To successfully test this section ensure that Enable Dynamic Sync has **not** been checked.
Complete the following to successfully test the Batch Load:
1. From within ServiceNow in the navigation panel search for User Administration > Groups. Select Groups.
2. From within Groups, create three new Groups and assign the x_xma_xmatters.xmatters role to each of the groups.
      * xMatters Company Supervisor
      * xMatters Developer
      * xMatters Support User
3. Once the three groups have been created and the x_xma_xmatters.xmatters role has been assigned to each.
4. Begin adding users to the Group.
5. Assign the x_xma_xmatters.xmatters to other groups to test Group Membership
6. Navigate to Batch Load Users and select to begin the process.
7. Once completed, select Batch Load Groups to load the Groups.
8. Navigate to the xMatters environment to ensure that everything is as expected.

# Troubleshooting
Key items to remember:
* The Role Provisioning groups will not synchronize to xMatters. These Groups are for provisioning purposes only.
* The role provisioning groups must have the x_xma_xmatters.xmatters role as well as be named "xMatters " followed by the role such as "Company Supervisor". The format is: xMatters Company Supervisor, xMatters Developer, etc.
* Sites are required for a successful sync so ensure that a Site is entered.
* If two shifts are being created as listed below. Please ensure that the update set xMatters Default Shift is imported into the environment.
      * 24x7
      * Default Shift
