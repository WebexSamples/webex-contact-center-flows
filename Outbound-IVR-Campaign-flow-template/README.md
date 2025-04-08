# IVR Predictive Progressive Campaign - Template

## Name
IVR_Predictive_Progressive_Campaign_Template

## Labels 
IVR, Predictive, Progressive, Outbound

## Description

Use this flow template in Webex Contact Center to set up an interactive voice response (IVR) system for handling outbound dialer calls in predictive or progressive campaigns. This template ensures a structured approach to proactive customer engagement.

## Details

This flow sets up a comprehensive framework for managing outbound campaigns, addressing various contact outcomes and ensuring efficient error resolution.

Customize the flow to enhance contact handling and reinforce error management strategies for improved customer interaction.

> Note: The Flow uses Cisco Text-to-speech for all the audio activities that require prompts (if any). 
> 
> 
> For all organization specific configuration activities such as Queue, Entry Points, Connectors, Outdial ANI, etc. those need to be manually configured by the user before the template is published.
>

### Pre-requisites

- Create an Entry Point from the Control Hub settings page for Webex Contact Center. Refer to the Webex Contact Center Setup and Administration Guide.
- This flow uses Cisco TTS(Text-to-speech). Upload required static audio files if using your own audio for the prompts.
- Ensure the following configurations in the Webex Contact Center Management Portal before implementing this flow:
1. Establish Outdial Entry Points, Outdial queues, teams, entry point mappings, and other organization-specific settings like connectors and outdial ANI.
2. Configure a flow for each campaign, referenced by the outdial entry point. The flow is straightforward but dictates which variables are visible on the agent desktop and their sequence. This is managed via global variables. Adjust variables as needed.
3. Set up global variables for each variable imported into the contact list intended for use in the flow or potentially passed to the agent desktop. Ensure global variable names match those in the import list.
4. Configure customer data on the desktop layout within the flow's incoming popover section. Note: sensitive data is not supported in the incoming popover. [Download Desktop Layout](https://github.com/WebexCC/webexcc.github.io/blob/master/assets/Acqueon_Desktop_Layout.json\)

- For guidance on configuring campaigns with LCM, consult the Cisco Webex Contact Center Campaign Manager User Guide.



### Flow Breakdown

1. Contact Initiation: The flow initiates with a new phone contact.
2. Contact Handling: Evaluates call outcomes like answering machine detection or abandoned calls, directing flow accordingly.
3. Error Management: Incorporates error handling strategies and options for retrying calls.
4. Flow Conclusion: Ends the interaction with a disconnect activity after contact handling.

### Activities Used

**Start**

- Initiates a new phone contact at the beginning of the flow.

**Case Evaluation**

- Assesses call outcomes, guiding the flow based on conditions such as AMD (Answering Machine Detection) and Abandoned calls due to busy agents.

**Play Message**

- Utilizes Text-to-Speech for delivering voicemail greetings or capacity messages when agents are occupied.

**Disconnect**

- This activity disconnects the call, ending the interaction after the message has been played.


## Additional Details

For more information, refer to the detailed documentation on help.webex.com.

[Webex Contact Center Flow Designer - Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee)
[Campaign Manager User Guide](https://help.webex.com/en-us/article/ngtxjux/Webex-Contact-Center-Campaign-Manager-User-Guide)
[Download Desktop Layout](https://github.com/WebexCC/webexcc.github.io/blob/master/assets/Acqueon_Desktop_Layout.json)