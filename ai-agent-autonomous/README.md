# AI Agent Autonomous (Package Tracking)

## Name
AI Agent Autonomous (Package Tracking)

## Labels 
Advanced, Voice, Inbound, Virtual Assistant

## Description

This flow utilizes an autonomous AI agent to manage voice interactions related to package tracking. The flow provides an option to escalate to human agents when necessary or on AI Agent errors.

## Details

The flow is designed to handle customer interactions about package tracking via an autonomous AI agent. The AI agent consists of an action to track package and a knowledge base related to general shipping queries. Customers can ask to speack to a human agent at any time.

### Pre-requisites

To use this flow, ensure the following are set up:

- An autonomous AI Agent configured with the appropriate action (along with fulfilment) and knowledge documents. A sample fulfilment flow is available in Webex Connect flow templates.
- Entry Point, Queue, Teams, and Entry Point Mapping configured in the Control Hub settings page for Webex Contact Center.
- Cisco Text-to-Speech (TTS) is enabled for generating custom messages dynamically.
- Upload static audio files if you are not using Cisco’s default audio.

### Integration Breakdown

1. **Caller initiates contact**: The call is received by Webex Contact Center and directed to the autonomous AI agent.
2. **AI agent interaction**: The AI agent processes the caller’s request related to package tracking.
3. **Queue to Agent**: If escalation is required on customer request or due to AI Agent errors, the caller is placed in a queue for a human agent.
4. **Disconnect**: The interaction ends once the caller’s request is handled or the caller is transferred to an agent.

### Activities Used in the Flow

**Start (New Phone Contact)**

- This activity marks the beginning of the flow, triggered by a new call.

**Virtual Agent V2 (VAV2)**

- The activity responsible for the interaction between the flow and the AI Agent. Interaction. The same activity is used to initiate the conversation and to send state events to the AI agent.

**Play Message**

- Provides system messages using Cisco Text-to-Speech. Used to play an error message before escalation to human agent in case of VAV2 activity errors.

**Queue to Agent**

- Manages queueing logic for escalation to human agents.

**Play Music**

- Hold music played during queueing when the caller awaits agent connection.

**Disconnect**

- Ends the interaction after completion of tasks or if escalated to a human agent.

### Error Handling

The flow includes error management strategies to handle unexpected issues gracefully, ensuring the caller is informed and redirected appropriately.

### Additional Resources

For deeper insights into using Webex Contact Center with autonomous AI agents, refer to related documentation:

- [Set up Autonomous Agents on Webex AI Agent Studio](https://help.webex.com/en-us/article/ncs9r37/Webex-AI-Agent-Studio-Administration-guide#Set-up-autonomous-AI-agent)
- [Webex Contact Center Flow Designer Guide](https://help.webex.com/en-us/article/nhovcy4/Build-and-manage-flows-with-Flow-Designer)

## Developer Support

For support related to this flow, contact the Webex Contact Center Developer Support team via the [Webex Developer Portal](https://developer.webex-cx.com/support).

For further discussions, visit the [Webex Contact Center APIs Developer Community](https://community.cisco.com/t5/contact-center/bd-p/j-disc-dev-contact-center).
