# Outbound Preview Campaign - Template

## Name

Outbound_Preview_Campaign_Template

## Labels 
Outbound, Voice, Campaign

## Description

Use this template to create an outbound voice campaign flow where agents can preview call details before engaging with contacts.

## Details

This flow provides a structured process for initiating outbound voice interactions in a campaign setting.

Modify the flow to ensure efficient campaign management by handling any errors or unexpected events.

> Note: The flow integrates with Webex Contact Center for voice interactions.

> For campaign-specific settings such as ANI, Phone Numbers, and Agent Details, these need to be configured by the user before the template is deployed.
>

### Pre-requisites

- Create an Entry Point from the Control Hub settings page for Webex Contact Center. Refer to the Webex Contact Center Setup and Administration Guide.

- Ensure that the following requirements are met in the Webex Contact Center Management Portal before implementing this flow:
- Create Outdial Entry Points, Out dial queues, teams, and entry point mappings, and any other organization-specific configurations such as connectors, outdial ANI, and more.

- A flow must be configured with each campaign, referenced by the outdial entrypoint. The flow is simple but dictates which variables are shown on the agent desktop and in which order. This is done via global variables. Configure variables in the flow as required.

- Global variables should be configured for each variable imported into the contact list which is desired to be utilized in the flow or potentially eventually passed to the agent desktop. The global variable name(s) should match the variable name in the import list. 

**You must configure this customer data on the desktop layout in the flow within the incoming popover section. Note that the incoming popover does not support sensitive data. Download Desktop Layout.
For more information about how to configure campaigns using LCM, see Cisco Webex Contact Center Campaign Manager User Guide.

Configure Voice Outbound Campaign Modes in Webex Contact Center
For detailed steps, see Webex Contact Center setup and administration guide.

### Flow Breakdown

1. A new phone contact is initiated in the campaign.
2. The flow processes the voice interaction according to specified campaign settings.
3. Flow execution is ended, completing the campaign interaction.

### Activities Used

**Start**

- The flow begins with the initiation of a new phone contact within the campaign.

**Play Message**

- None


**Disconnect**

- The flow ends the execution after processing the interaction and connected to the agent.


## Additional Details

For more information, refer to the detailed documentation on help.webex.com.

[Webex Contact Center Flow Designer - Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee)
[Campaign Manager User Guide](https://help.webex.com/en-us/article/ngtxjux/Webex-Contact-Center-Campaign-Manager-User-Guide)
[Download Desktop Layout](https://github.com/WebexCC/webexcc.github.io/blob/master/assets/Acqueon_Desktop_Layout.json)
