[comment]: # "Auto-generated SOAR connector documentation"
# Bitglass

Publisher: Bitglass Inc\.  
Connector Version: 1\.1\.1  
Product Vendor: Bitglass Inc\.  
Product Name: Bitglass Phantom App  
Product Version Supported (regex): "\.\*"  
Minimum Product Version: 5\.3\.0  

The app pulls Bitglass cloudaudit and access log data once configured and parses the specified DLP patterns from the asset configuration page

[comment]: # " File: README.md"
[comment]: # ""
[comment]: # "    Copyright (c) 2022 alexeiyur"
[comment]: # ""
[comment]: # "    Licensed under the MIT License (https://mit-license.org/)"
[comment]: # ""
The app pulls cloudaudit and access Bitglass log data filtered down to the specified DLP patterns.
It also provides actions for access to Bitglass REST APIs for group and user manipulation. A sample
playbook is included.

## Troubleshooting tips, known issues, and miscellaneous notes

-   After installing the app, please perform the following configuration steps:

      

    -   Create a new asset and save the required settings 'OAuth 2 Authentication Token' and
        'Bitglass API URL' in the 'Asset Settings' tab
    -   In the 'Ingest Settings' tab, select the source (i.e 'events') and enable polling interval
        or another scheduling option, press SAVE
    -   Do 'Asset Settings / TEST CONNECTIVITY' and make sure it passes

-   The Phantom logs are available in /var/log/phantom or ${phantom_home}/var/log/phantom (for a
    non-root installation)

-   The last ingested data with the time and error code (if failed) is available in the app state
    directory /opt/phantom/local_data/app_states/8119e222-818e-42f5-a210-1c7c9d337e81 in
    lastlog-\*.json files

-   Optionally, install the bitglass_dlp_response.tgz playbook sample before creating your playbook
    from scratch

## The actions available with the app roughly fall into 3 groups

<div style="margin-left: 2em">

Phantom standard and Bitglass log event retrieval

-   'test connectivity': Phantom requirement to test the asset settings (API url, authentication
    token, etc.)
-   'on poll': Phantom requirement for automatic data ingestion. Only the 'DLP Pattern for Access'
    and 'DLP Pattern for CloudAudit' matches from the log types 'Access' and 'CloudAudit'
    correspondingly get ingested into Phantom
-   'filter by dlp pattern': Additionally to filtering done according to the asset params 'DLP
    Pattern for Access' and 'DLP Pattern for CloudAudit' (as part of the ingestion), filter the
    ingested Bitglass log events further down as defined by the 'bg_match_expression' action param

User manipulation in response to log events from either Bitglass or other vendors

-   'add user to group': Add the user from the offending log event to a Bitglass group. For Bitglass
    log event source, the user is determined according to the asset params 'DLP Pattern for Access',
    'DLP Pattern for CloudAudit' and the 'bg_match_expression' param of the 'filter by dlp pattern'
    action by chaining this action after 'filter by dlp pattern'
-   'remove user from group': Same but in reverse
-   'deactivate user': A more drastic action in comparison to 'add user to group' above
-   'reactivate user': Same but in reverse

Other available actions covering the rest of the methods in Bitglass REST API, please refer to the
online documentation for reference

-   'create update user'
-   'create update group'
-   'delete group'

</div>

## The following describes creating a simple playbook from scratch for the 'User manipulation' use case described above. The resulting playbook should be similar to the sample bitglass_dlp_response.tgz playbook included with the app package

1.  Go to the 'Playbooks' page and click '+ PLAYBOOK'
2.  Drag the arrow on the 'START' block and click 'Action' in the menu on the left
3.  Choose 'Bitglass', the available actions will be listed
4.  Choose 'filter by dlp pattern'. This action will narrow down the available data (in the form of
    Phantom artifacts already available and the ones to be ingested into the future)
5.  Choose the asset configured earlier. Please note, that the artifacts available on the system
    have been already pre-filtered according to the 'DLP Pattern for Access' and 'DLP Pattern for
    CloudAudit' asset settings
6.  Override the 'bg_match_expression' and 'bg_log_event' params if necessary (keeping the defaults
    values of Malware.\* and artifact:\*.id correspondingly should be good for a start)
7.  Click 'SAVE'. That concludes defining the first action
8.  Pull at the output pin of the first action and click 'Action' to define the next action
9.  Choose 'Bitglass', the available actions will be listed
10. Choose 'add user to risk group'. This action will extract the user name from the artifact data
    corresponding to the log event and call Bitglass REST API to add the offending user to the group
    of risky users
11. Choose the same asset configured earlier
12. Override the 'bg_group_name' param if necessary. This group must have been created previously so
    that it exists
13. For the 'bg_user_name', this value has been extracted by the previous action and is available to
    pick as 'data.\*.userName', use it
14. Click 'SAVE'. That concludes defining the second action
15. Connect the output pin of the second action to the 'END' block
16. Click 'PLAYBOOK SETTINGS' and set 'Operates on' to 'events' and check 'Active'
17. Enter the desired playbook name in the upper left corner
18. Click the 'SAVE' button
19. Enter the description if prompted and click the 'SAVE' button
20. From now on, the playbook will be run automatically whenever new data conforming to the filter
    parameters defined above arrives
21. IMPORTANT! If using the Playbook Debugger or invoking the playbook manually, be sure to change
    the default value of the 'Scope' setting from the default 'new' to 'all'. Skipping this step
    will result in a Phantom error as the input data set will be empty in such a case

## The following table summarizes all the params available

| Param name                   | Found in asset / action | Type             | Value example                                          |
|------------------------------|-------------------------|------------------|--------------------------------------------------------|
| 'DLP Pattern for Access'     | asset                   | regex            | Malware.\*\|PCI.\*                                     |
| 'DLP Pattern for CloudAudit' | asset                   | regex            | ^PCI.\*                                                |
| 'bg_match_expression'        | 'filter by dlp pattern' | regex            | Malware.\*                                             |
| 'bg_log_event'               | 'filter by dlp pattern' | Phantom wildcard | artifact:\*.id                                         |
| 'bg_group_name'              | 'add user to group'     | string           | RiskyUsers                                             |
| 'bg_user_name'               | 'add user to group'     | Phantom wildcard | filter_by_dlp_pattern_1:action_result.data.\*.userName |

All filtering params of the asset and actions described above are Python regular expressions. For
example, Malware.\*\|PCI.\* matches any string containing EITHER the substring of 'Malware' followed
by any number of chars including zero OR, likewise, with the 'PCI' char sequence. Please refer to
the Python regex documentation


### Configuration Variables
The below configuration variables are required for this Connector to operate.  These variables are specified when configuring a Bitglass Phantom App asset in SOAR.

VARIABLE | REQUIRED | TYPE | DESCRIPTION
-------- | -------- | ---- | -----------
**auth\_token** |  required  | password | OAuth 2 authentication token
**username** |  optional  | string | Username \(basic authentication\)
**api\_url** |  required  | string | Bitglass API URL
**password** |  optional  | password | Password \(basic authentication\)
**proxies** |  optional  | password | Proxy settings for Bitglass API, format\: https=https\://usr\:pswd\@1\.2\.3\.4\:999
**enable\_access** |  optional  | boolean | Pull Bitglass access logs \:
**filter\_access** |  optional  | string | DLP pattern for access
**enable\_cloudaudit** |  optional  | boolean | Pull Bitglass CloudAudit logs \:
**filter\_cloudaudit** |  optional  | string | DLP pattern for CloudAudit

### Supported Actions  
[test connectivity](#action-test-connectivity) - Validate the asset configuration for connectivity using supplied configuration  
[on poll](#action-on-poll) - Polls for new pattern\-matched Bitglass events used for other actions to act upon  
[filter by dlp pattern](#action-filter-by-dlp-pattern) - Filter log artifacts by DLP pattern  
[create update group](#action-create-update-group) - Create or update a group  
[delete group](#action-delete-group) - Delete a group  
[add user to group](#action-add-user-to-group) - Add risky user to a group  
[remove user from group](#action-remove-user-from-group) - Remove risky user from a group  
[create update user](#action-create-update-user) - Create or update user  
[deactivate user](#action-deactivate-user) - Deactivate user  
[reactivate user](#action-reactivate-user) - Reactivate user  

## action: 'test connectivity'
Validate the asset configuration for connectivity using supplied configuration

Type: **test**  
Read only: **True**

#### Action Parameters
No parameters are required for this action

#### Action Output
No Output  

## action: 'on poll'
Polls for new pattern\-matched Bitglass events used for other actions to act upon

Type: **ingest**  
Read only: **True**

Use this action to poll for and ingest new pattern\-matched Bitglass log events\. These events will be used as input for other playbook actions\.

#### Action Parameters
No parameters are required for this action

#### Action Output
No Output  

## action: 'filter by dlp pattern'
Filter log artifacts by DLP pattern

Type: **investigate**  
Read only: **True**

Use this action to filter log artifacts by DLP patterns with a Python regex\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg\_match\_expression** |  required  | Python regex expression to match the patterns | string | 
**bg\_log\_event** |  required  | Phantom artifact ID | string |  `phantom artifact id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.bg\_match\_expression | string | 
action\_result\.parameter\.bg\_log\_event | string |  `phantom artifact id` 
action\_result\.data\.\*\.userName | string |  `user name` 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'create update group'
Create or update a group

Type: **correct**  
Read only: **False**

Use this action to create or update group for risky users\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg\_group\_name** |  required  | Name of the group to create or update | string |  `bg group name` 
**bg\_new\_group\_name** |  optional  | New name of the group to rename to | string | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.bg\_group\_name | string |  `bg group name` 
action\_result\.parameter\.bg\_new\_group\_name | string | 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'delete group'
Delete a group

Type: **contain**  
Read only: **False**

Use this action to delete group for risky users\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg\_group\_name** |  required  | Name of the group to delete | string |  `bg group name` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.bg\_group\_name | string |  `bg group name` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'add user to group'
Add risky user to a group

Type: **correct**  
Read only: **False**

Use this action to add user determined risky to a special group\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg\_group\_name** |  required  | Name of the group to add the risky user to | string |  `bg group name` 
**bg\_user\_name** |  required  | User name to add | string |  `user name` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.bg\_group\_name | string |  `bg group name` 
action\_result\.parameter\.bg\_user\_name | string |  `user name` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'remove user from group'
Remove risky user from a group

Type: **contain**  
Read only: **False**

Use this action to remove user previously determined risky from the group\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg\_group\_name** |  required  | Name of the group to add the risky user to | string |  `bg group name` 
**bg\_user\_name** |  required  | User name to remove | string |  `user name` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.bg\_group\_name | string |  `bg group name` 
action\_result\.parameter\.bg\_user\_name | string |  `user name` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'create update user'
Create or update user

Type: **correct**  
Read only: **False**

Use this action to create or update user\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg\_user\_name** |  required  | Email of the user to create or update | string |  `user name` 
**bg\_first\_name** |  optional  | First name | string | 
**bg\_last\_name** |  optional  | Last name | string | 
**bg\_secondary\_email** |  optional  | Secondary email | string | 
**bg\_netbios\_domain** |  optional  | NetBIOS domain | string | 
**bg\_sam\_account\_name** |  optional  | SAM account domain | string | 
**bg\_user\_principal\_name** |  optional  | User principal name | string | 
**bg\_object\_guid** |  optional  | Object GUID | string | 
**bg\_country\_code** |  optional  | Country code | string | 
**bg\_mobile\_number** |  optional  | Mobile number | string | 
**bg\_admin\_role** |  optional  | Admin role | string | 
**bg\_group\_membership** |  optional  | Place the user under the group | string | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.bg\_user\_name | string |  `user name` 
action\_result\.parameter\.bg\_first\_name | string | 
action\_result\.parameter\.bg\_last\_name | string | 
action\_result\.parameter\.bg\_secondary\_email | string | 
action\_result\.parameter\.bg\_netbios\_domain | string | 
action\_result\.parameter\.bg\_sam\_account\_name | string | 
action\_result\.parameter\.bg\_user\_principal\_name | string | 
action\_result\.parameter\.bg\_object\_guid | string | 
action\_result\.parameter\.bg\_country\_code | string | 
action\_result\.parameter\.bg\_mobile\_number | string | 
action\_result\.parameter\.bg\_admin\_role | string | 
action\_result\.parameter\.bg\_group\_membership | string | 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'deactivate user'
Deactivate user

Type: **contain**  
Read only: **False**

Use this action to deactivate user\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg\_user\_name** |  required  | Email of the user to deactivate | string |  `user name` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.bg\_user\_name | string |  `user name` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'reactivate user'
Reactivate user

Type: **correct**  
Read only: **False**

Use this action to reactivate user\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg\_user\_name** |  required  | Email of the user to reactivate | string |  `user name` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.bg\_user\_name | string |  `user name` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric | 