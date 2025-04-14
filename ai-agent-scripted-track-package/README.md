# AI Agent Scripted (Package Tracking)

## Name
AI Agent Scripted (Package Tracking)

## Labels 
Advanced, Voice, AI Agent Studio, Virtual Assistant, Intent based agent handover, Queue routing

## Description

This flow is designed to handle voice interactions related to package tracking using a scripted virtual agent. This flow demonstrates the simplest way to perform fulfilment for a scripted agent. In addition to that, the flow demonstrates queueing customers to different agent queues based on the last active intent and custom reports for AI Agents in Analyser.

## Details

This flow leverages a scripted Webex AI Agent to interact with customers regarding package tracking. The VAV2 (Virtual Agent V2) activity exits through its 'Handled' edge when the scripted agent raises a custom event to track the package. The flow uses a package tracking API to achieve this. This API is available to developers for testing and demos. The output data is parsed in the flow and passed back to the Agent via a State Event.  

### Pre-requisites

To use this flow, ensure the following are set up:

- A Webex AI Agent configured to handle package tracking inquiries. This agent is available to import while creating a new agent.
- Entry Point, Queue, Teams, and Entry Point Mapping configured in the Control Hub settings page for Webex Contact Center.
- Ensure Cisco Text-to-Speech (TTS) is enabled for generating dynamic audio messages.
- Upload static audio files for custom system notifications if necessary.

### Integration Breakdown

1. **Caller initiates contact**: The call is received by Webex Contact Center and directed to the scripted AI agent.
2. **Interaction state is logged using Global Variable**: The flow sets CustomAIAgentInteractionOutcome global variable to log the state of customer's interaction with the AI agent. This is updated at various points and is used to build custom reports using visualiser.
3. **AI agent interaction**: The AI agent processes customer input and responds based on configured intents. If the user intends to track a package and provides a valid package number, the control is handed back to the flow via a custom event.
4. **AI agent metadata parsing and fulfilment**: The customer's package number is extracted from the VAV2 metadata and used in the HTTP activity.
5. **Fulfilment response conditions**: The flow checks if the package information is found or not and sets appropriate responses.
6. **AI agent interaction is resumed***: Depending on the fulfilment response, the message that should be sent to the customers is sent back to scripted agent via event data under 'State event'.
7. **Agent handover and case activity**: Determines the next steps based on the previous intent, guiding the flow to different queues based on previous intent.
8. **Queue to Agent**: If escalation is required or in case of errors, the caller is placed in a queue for a human agent.
9. **Disconnect**: The interaction ends once the caller’s request is handled or the caller is transferred to an agent.

### Activities Used in the Flow

**Start**

- Initiates the flow when a new call is received.

**Set Interaction Outcome Variable**

- Use the set variable activity to update CustomAIAgentInteractionOutcome global variable to store the latest state of the interaction with AI agent.

**AI Agent Interaction**

- Manages package tracking inquiries using scripted interactions.The same activity is used to initiate the conversation and to send state events to the AI agent.

**Parse Package Details**

- Extracts package number from the metadata provided by the virtual agent.

**HTTP Request for Package Info**

- Sends a request to the logistics API to retrieve package status and estimated delivery. Use ABC123456 as a sample package number.

**Conditional Logic**

- Determines the response based on the package status or the HTTP status code of the API call.

**Set Response Variables**

- Configures responses to communicate whether the package was found or the delivery details.

**Play Message**

- Provides system error messages using Cisco Text-to-Speech, especially in cases of system errors.

**Case Activity**

- Guides the flow based on the previous intent, deciding on routing to specific queues.

**Queue to Agent**

- Manages queueing logic for escalation to human agents.

**Play Music**

- Hold music played during queueing when the caller awaits agent connection.

**Disconnect**

- Ends the interaction after completion of tasks or if escalated to a human agent.

### Flow Specifics

The flow JSON used in this example contains variables and activities essential for interaction handling, error processing, and communication between Webex Contact Center and DialogFlow. The key variables used include:

- `event_name`: Name of the event sent to the AI Agent.
- `event_data`: Event payload sent to the AI Agent.
- `status`: Status of the package based on the HTTP response.
- `estimatedDelivery`: Estimated delivery date and time for the package based on the HTTP response.
- `packageResp`: Response to be sent back to the customer based on HTTP activity response.
- `Global_VoiceName`: Determines the voice used for text-to-speech.
- `CustomAIAgentInteractionOutcome`: Logs the state of interaction - abandoned, handled, escalated, or errored - based on the customer's interaction with the AI agent.

### Error Handling

The flow includes error management strategies to handle unexpected issues gracefully, ensuring the caller is informed and redirected appropriately.

### Additional Resources

For deeper insights into using Webex Contact Center with scripted AI agents, refer to related documentation:

- [Webex Contact Center Developer Documentation](https://developer.webex-cx.com)
- [Webex Contact Center Flow Designer Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide)
- [Manage-custom-reports-for-AI-agents](https://help.webex.com/en-us/article/ncs9r37/Webex-AI-Agent-Studio-Administration-guide#Manage-custom-reports-for-AI-agents)

## Developer Support

For support related to this flow, contact the Webex Contact Center Developer Support team via the [Webex Developer Portal](https://developer.webex-cx.com/support).

For further discussions, visit the [Webex Contact Center APIs Developer Community](https://community.cisco.com/t5/contact-center/bd-p/j-disc-dev-contact-center).
