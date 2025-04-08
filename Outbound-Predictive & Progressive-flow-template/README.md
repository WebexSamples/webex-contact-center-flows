# Predictive Progressive Campaign - Template

## Name
Predictive_Progressive_Campaign_Template

## Labels 
Predictive, Progressive,Outbound

## Description

Use this template to create a sophisticated outbound campaign flow where contacts are initiated using predictive or progressive dialing. This is often used for reaching out to customers proactively.

## Details

This flow provides a setup for outbound campaigns, including handling different call results and error scenarios.

Modify the flow to ensure effective contact management and robust error handling to improve customer engagement.

> Note: The Flow uses Cisco Text-to-speech for all the audio activities that require prompts (if any). 
> 
> 
> For all organization specific configuration activities such as Queue, Entry Points, Connectors, Outdial ANI, etc. those need to be manually configured by the user before the template is published.
>

### Pre-requisites

- This flow uses Cisco TTS(Text-to-speech). Upload required static audio files if using your own audio for the prompts.
- Ensure that the following requirements are met in the Webex Contact Center Management Portal before implementing this flow:
 1. Create Outdial Entry Points, Out dial queues, teams, and entry point mappings, and any other organization-specific configurations such as connectors, outdial ANI, and more.
 2.	A flow must be configured with each campaign, referenced by the outdial entrypoint. The flow is simple but dictates which variables are shown on the agent desktop and in which order. This is done via global variables. Configure variables in the flow as required. 
 3.	Global variables should be configured for each variable imported into the contact list which is desired to be utilized in the flow or potentially eventually passed to the agent desktop. The global variable name(s) should match the variable name in the import list. 
 4.	**You must configure this customer data on the desktop layout in the flow within the incoming popover section. Note that the incoming popover does not support sensitive data. Download Desktop Layout.
 5.	For more information about how to configure campaigns using LCM, see Cisco Webex Contact Center Campaign Manager User Guide.



### Flow Breakdown

1. Contact Initiation: The flow starts with initiating a new phone contact.
2. Contact Handling: The flow evaluates call results like answering machine detection or abandoned calls and handles them accordingly.
3. Error Management: Provides mechanisms for error handling and offers solutions like retrying calls.
4. Flow Conclusion: Ends with the disconnect activity after handling the contact.

### Activities Used

**Start**

- The flow begins with the initiation of a new phone contact.

**Case Evaluation**

- Evaluates the call results and directs the flow based on conditions like AMD (Answering Machine Detection) and Abandoned due to agents are busy.


**Play Message**

- Uses Text-to-Speech to convey messages for voicemail greetings or capacity messages when agents are busy.


**Disconnect**

- Ends the interaction by disconnecting the call after handling the contact.



## Additional Details

For more information, refer to the detailed documentation on help.webex.com.

[Webex Contact Center Flow Designer - Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee)
[Campaign Manager User Guide](https://help.webex.com/en-us/article/ngtxjux/Webex-Contact-Center-Campaign-Manager-User-Guide)
[Download Desktop Layout](https://github.com/WebexCC/webexcc.github.io/blob/master/assets/Acqueon_Desktop_Layout.json)
