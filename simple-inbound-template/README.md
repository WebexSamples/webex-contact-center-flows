# Simple Inbound call Flow - Template

## Name

Simple Inbound Call to Queue

## Labels

Basic, Voice, Inbound, Queue, Music

## Description

A simple inbound voice flow where Callers are greeted, and then sent to a queue, along with hold music during their wait to be answered.

## Details

This flow provides a straightforward process for handling inbound calls in a contact center:

1. A call is received and enters the flow through the entry point.
2. A welcome message is played to the Caller.
3. The Caller is placed in a queue for the next available Agent.
4. While waiting in the queue, hold music is played to the Caller.

## Pre-requisites

From the Control Hub settings page for Webex Contact Center:

- Create Entry Point with Mapped Phone Number
- Create one or more Agents
- Create an Agent Based Team, and associate Agents
- Create an Inbound Telephony Queue, and associate Team

This flow uses Cisco Text-to-Speech (TTS) for the welcome and queue messages. If you rather use WAV files, you can upload your own audio files, under: Control Hub > Contact Center > Audo Prompts.

Refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide) for more details on configuring these items.

## Activities Used in the Flow

Here are the activities used in the flow:

**Start Flow (NewPhoneContact):**

- The flow begins when a call is received via the entry point.
- The call is accepted into the flow and proceeds to the next step.

**Play Message (Welcome):**

- A message is played to welcome the caller. In this flow, the message says: "Welcome to Webex Contact Center!"
- This message is configured using Cisco TTS, but can be replaced with a custom recording.

**Queue Contact:**

- After the welcome message, the call is placed into a queue.
- The queue is set to direct the call to the configured queue.
- If there are available Agents, the Caller will be connected to an Agent, and the flow ends.

**Play Message (Agents Busy):**

- A secondary message is played while the caller is waiting: "All Agents are busy assisting other callers. Please stay on the line, and an Agent will be with you shortly."
- This message is also configured using Cisco TTS, and can be replaced with a custom recording.

**Play Music (Hold Music):**

- While waiting in the queue, the flow plays hold music. In this case, the default file "defaultmusic_on_hold.wav" is used, and it plays for 30 seconds before looping back to the Agents Busy Play Message activity.

### Additional Details

For more information on Webex Contact Center Flows, refer to the detailed documentation on help.webex.com.

[Webex Contact Center Flow Designer - Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee)
