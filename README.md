# Bitglass

Publisher: Bitglass Inc. \
Connector Version: 1.1.2 \
Product Vendor: Bitglass Inc. \
Product Name: Bitglass Phantom App \
Minimum Product Version: 5.3.0

The app pulls Bitglass cloudaudit and access log data once configured and parses the specified DLP patterns from the asset configuration page

### Configuration variables

This table lists the configuration variables required to operate Bitglass. These variables are specified when configuring a Bitglass Phantom App asset in Splunk SOAR.

VARIABLE | REQUIRED | TYPE | DESCRIPTION
-------- | -------- | ---- | -----------
**auth_token** | required | password | OAuth 2 authentication token |
**username** | optional | string | Username (basic authentication) |
**api_url** | required | string | Bitglass API URL |
**password** | optional | password | Password (basic authentication) |
**proxies** | optional | password | Proxy settings for Bitglass API, format: https=https://usr:pswd@1.2.3.4:999 |
**enable_access** | optional | boolean | Pull Bitglass access logs : |
**filter_access** | optional | string | DLP pattern for access |
**enable_cloudaudit** | optional | boolean | Pull Bitglass CloudAudit logs : |
**filter_cloudaudit** | optional | string | DLP pattern for CloudAudit |

### Supported Actions

[test connectivity](#action-test-connectivity) - Validate the asset configuration for connectivity using supplied configuration \
[on poll](#action-on-poll) - Polls for new pattern-matched Bitglass events used for other actions to act upon \
[filter by dlp pattern](#action-filter-by-dlp-pattern) - Filter log artifacts by DLP pattern \
[create update group](#action-create-update-group) - Create or update a group \
[delete group](#action-delete-group) - Delete a group \
[add user to group](#action-add-user-to-group) - Add risky user to a group \
[remove user from group](#action-remove-user-from-group) - Remove risky user from a group \
[create update user](#action-create-update-user) - Create or update user \
[deactivate user](#action-deactivate-user) - Deactivate user \
[reactivate user](#action-reactivate-user) - Reactivate user

## action: 'test connectivity'

Validate the asset configuration for connectivity using supplied configuration

Type: **test** \
Read only: **True**

#### Action Parameters

No parameters are required for this action

#### Action Output

No Output

## action: 'on poll'

Polls for new pattern-matched Bitglass events used for other actions to act upon

Type: **ingest** \
Read only: **True**

Use this action to poll for and ingest new pattern-matched Bitglass log events. These events will be used as input for other playbook actions.

#### Action Parameters

No parameters are required for this action

#### Action Output

No Output

## action: 'filter by dlp pattern'

Filter log artifacts by DLP pattern

Type: **investigate** \
Read only: **True**

Use this action to filter log artifacts by DLP patterns with a Python regex.

#### Action Parameters

PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg_match_expression** | required | Python regex expression to match the patterns | string | |
**bg_log_event** | required | Phantom artifact ID | string | `phantom artifact id` |

#### Action Output

DATA PATH | TYPE | CONTAINS | EXAMPLE VALUES
--------- | ---- | -------- | --------------
action_result.parameter.bg_match_expression | string | | |
action_result.parameter.bg_log_event | string | `phantom artifact id` | |
action_result.data.\*.userName | string | `user name` | joedoe@whitehat.com |
action_result.status | string | | success failed |
action_result.message | string | | |
action_result.summary | string | | |
summary.total_objects | numeric | | 1 |
summary.total_objects_successful | numeric | | 1 |

## action: 'create update group'

Create or update a group

Type: **correct** \
Read only: **False**

Use this action to create or update group for risky users.

#### Action Parameters

PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg_group_name** | required | Name of the group to create or update | string | `bg group name` |
**bg_new_group_name** | optional | New name of the group to rename to | string | |

#### Action Output

DATA PATH | TYPE | CONTAINS | EXAMPLE VALUES
--------- | ---- | -------- | --------------
action_result.parameter.bg_group_name | string | `bg group name` | |
action_result.parameter.bg_new_group_name | string | | |
action_result.data | string | | |
action_result.status | string | | success failed |
action_result.message | string | | |
action_result.summary | string | | |
summary.total_objects | numeric | | 1 |
summary.total_objects_successful | numeric | | 1 |

## action: 'delete group'

Delete a group

Type: **contain** \
Read only: **False**

Use this action to delete group for risky users.

#### Action Parameters

PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg_group_name** | required | Name of the group to delete | string | `bg group name` |

#### Action Output

DATA PATH | TYPE | CONTAINS | EXAMPLE VALUES
--------- | ---- | -------- | --------------
action_result.parameter.bg_group_name | string | `bg group name` | |
action_result.data | string | | |
action_result.status | string | | success failed |
action_result.message | string | | |
action_result.summary | string | | |
summary.total_objects | numeric | | 1 |
summary.total_objects_successful | numeric | | 1 |

## action: 'add user to group'

Add risky user to a group

Type: **correct** \
Read only: **False**

Use this action to add user determined risky to a special group.

#### Action Parameters

PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg_group_name** | required | Name of the group to add the risky user to | string | `bg group name` |
**bg_user_name** | required | User name to add | string | `user name` |

#### Action Output

DATA PATH | TYPE | CONTAINS | EXAMPLE VALUES
--------- | ---- | -------- | --------------
action_result.parameter.bg_group_name | string | `bg group name` | |
action_result.parameter.bg_user_name | string | `user name` | |
action_result.data | string | | |
action_result.status | string | | success failed |
action_result.message | string | | |
action_result.summary | string | | |
summary.total_objects | numeric | | 1 |
summary.total_objects_successful | numeric | | 1 |

## action: 'remove user from group'

Remove risky user from a group

Type: **contain** \
Read only: **False**

Use this action to remove user previously determined risky from the group.

#### Action Parameters

PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg_group_name** | required | Name of the group to add the risky user to | string | `bg group name` |
**bg_user_name** | required | User name to remove | string | `user name` |

#### Action Output

DATA PATH | TYPE | CONTAINS | EXAMPLE VALUES
--------- | ---- | -------- | --------------
action_result.parameter.bg_group_name | string | `bg group name` | |
action_result.parameter.bg_user_name | string | `user name` | |
action_result.data | string | | |
action_result.status | string | | success failed |
action_result.message | string | | |
action_result.summary | string | | |
summary.total_objects | numeric | | 1 |
summary.total_objects_successful | numeric | | 1 |

## action: 'create update user'

Create or update user

Type: **correct** \
Read only: **False**

Use this action to create or update user.

#### Action Parameters

PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg_user_name** | required | Email of the user to create or update | string | `user name` |
**bg_first_name** | optional | First name | string | |
**bg_last_name** | optional | Last name | string | |
**bg_secondary_email** | optional | Secondary email | string | |
**bg_netbios_domain** | optional | NetBIOS domain | string | |
**bg_sam_account_name** | optional | SAM account domain | string | |
**bg_user_principal_name** | optional | User principal name | string | |
**bg_object_guid** | optional | Object GUID | string | |
**bg_country_code** | optional | Country code | string | |
**bg_mobile_number** | optional | Mobile number | string | |
**bg_admin_role** | optional | Admin role | string | |
**bg_group_membership** | optional | Place the user under the group | string | |

#### Action Output

DATA PATH | TYPE | CONTAINS | EXAMPLE VALUES
--------- | ---- | -------- | --------------
action_result.parameter.bg_user_name | string | `user name` | |
action_result.parameter.bg_first_name | string | | |
action_result.parameter.bg_last_name | string | | |
action_result.parameter.bg_secondary_email | string | | |
action_result.parameter.bg_netbios_domain | string | | |
action_result.parameter.bg_sam_account_name | string | | |
action_result.parameter.bg_user_principal_name | string | | |
action_result.parameter.bg_object_guid | string | | |
action_result.parameter.bg_country_code | string | | |
action_result.parameter.bg_mobile_number | string | | |
action_result.parameter.bg_admin_role | string | | |
action_result.parameter.bg_group_membership | string | | |
action_result.data | string | | |
action_result.status | string | | success failed |
action_result.message | string | | |
action_result.summary | string | | |
summary.total_objects | numeric | | 1 |
summary.total_objects_successful | numeric | | 1 |

## action: 'deactivate user'

Deactivate user

Type: **contain** \
Read only: **False**

Use this action to deactivate user.

#### Action Parameters

PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg_user_name** | required | Email of the user to deactivate | string | `user name` |

#### Action Output

DATA PATH | TYPE | CONTAINS | EXAMPLE VALUES
--------- | ---- | -------- | --------------
action_result.parameter.bg_user_name | string | `user name` | |
action_result.data | string | | |
action_result.status | string | | success failed |
action_result.message | string | | |
action_result.summary | string | | |
summary.total_objects | numeric | | 1 |
summary.total_objects_successful | numeric | | 1 |

## action: 'reactivate user'

Reactivate user

Type: **correct** \
Read only: **False**

Use this action to reactivate user.

#### Action Parameters

PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**bg_user_name** | required | Email of the user to reactivate | string | `user name` |

#### Action Output

DATA PATH | TYPE | CONTAINS | EXAMPLE VALUES
--------- | ---- | -------- | --------------
action_result.parameter.bg_user_name | string | `user name` | |
action_result.data | string | | |
action_result.status | string | | success failed |
action_result.message | string | | |
action_result.summary | string | | |
summary.total_objects | numeric | | 1 |
summary.total_objects_successful | numeric | | 1 |

______________________________________________________________________

Auto-generated Splunk SOAR Connector documentation.

Copyright 2025 Splunk Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and limitations under the License.
