# Hello World - Template

## Name

Hello World

## Labels

Basic, Voice, Inbound

## Description

Use this template to create a simple inbound voice flow where callers are greeted with a message and then disconnected.

## Details

This flow provides a simple process for playing an announcement to the caller:

1. A call is received and enters the flow through the entry point.
2. A welcome message is played to the Caller.
3. The call is disconnected.

### Pre-requisites

From the Control Hub settings page for Webex Contact Center:

- Create Entry Point with Mapped Phone Number

This flow uses Cisco Text-to-Speech (TTS) for the welcome and queue messages. If you rather use WAV files, you can upload your own audio files, under: Control Hub > Contact Center > Audo Prompts.

Refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide) for more details on configuring these items.

## Activities Used

**Start Flow (NewPhoneContact)**

- The flow begins when a call is received via the entry point.
- The call is accepted into the flow and proceeds to the next step.

**Play Message (Welcome)**

- A message is played to welcome the caller. In this flow, the message says: "Hello World! Welcome to Webex Contact Center! I hope you have a nice day!"
- This message is configured using Cisco TTS, but can be replaced with a custom recording.

**Disconnect Contact (Hang Up)**

- After the welcome message, the call is directed to the disconnect activity.
- This activity disconnects the call, ending the interaction after the message has been played.

## Additional Details

For more information on Webex Contact Center Flows, refer to the detailed documentation on help.webex.com.

[Webex Contact Center Flow Designer - Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee)
