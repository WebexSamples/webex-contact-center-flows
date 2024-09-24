# Comprehensive Inbound Call Flow - Template

## Name

Comprehensive Inbound Call Flow

## Labels

Intermediate, Voice, Inbound, Menu, Queue, PIQ, Music, Callback, Voicemail, Error Handling

## Description

A comprehensive voice flow where Callers are greeted, then we check business hours, holidays, and any overrides (i.e., emergency closing). We continue on with self-service menu options, queuing, announcing position in queue (PIQ), customer callback and voicemail opt-out options. We finish off with two event flows: global error handling, and Agent screen pop. This is suitable for environments where basic self-service and call queuing are essential.

## Details

This flow provides a comprehensive handling of incoming calls in a contact center:

1. A call is received and enters the flow through the entry point.
2. A welcome message is played to the Caller.
3. Business Hours, Holidays, Overrides are checked
   a. Overrides have highest priority, then holidays, then working hours (aka shifts), and if none of those three match, then Default (aka after hours) is the branch taken.
   b. For overrides, holidays and after hours we play an appropriate message to the caller then hang up.
4. A menu is presented to the caller, where the default option is set to the second menu option. This is important for when DTMF is either not working, or not possible (e.g., hands free calling while driving)
5. While waiting in the queue, hold messaging and music are played to the Caller.
6. Depending on queue depth, the Caller is offer alternatives to waiting in queue: Callback and voicemail opt-out.
7. We handle all errors in this flow, with the majority of the error handling being a continuation of the flow. We try to survive the error, and just keep moving forward.
8. We handle erros in queuing the Caller, by leveraging the event flow error handler, which tells the caller we're experiencing an error, and to try their call again later.

## Pre-requisites

From the Control Hub settings page for Webex Contact Center:

- Create Entry Point with Mapped Phone Number
- Create one or more Agents
- Create one or more Agent Based Teams, and associate Agent(s)
- Create two Inbound Telephony Queues, and associate Team(s)
- Create an Override schedule
- Create a Holiday schedule
- Create a Business Hours schedule and associate override and holiday schedules

This flow uses Cisco Text-to-Speech (TTS) for the welcome and queue messages. If you rather use WAV files, you can upload your own audio files, under: Control Hub > Contact Center > Audo Prompts.

Refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide) for more details on configuring these items.

## Activities Used in the Flow

Here are the activities used in the flow:

**Start Flow (NewPhoneContact)**

- The flow begins when a call is received via the entry point.
- The call is accepted into the flow and proceeds to the next step.

**Play Message (Welcome):**

- A message is played to welcome the caller. In this flow, the message says: "Welcome to Webex Contact Center!"
- This message is configured using Cisco TTS, but can be replaced with a custom recording.

**Business Hours:**

- The flow checks the current date & time against the defined business hours, holidays and overrides
- If the current date & time falls within any override defined, then the override path is chosen
- If the current date falls within any holiday defined, then the holiday path is chosen
- If the current day of week and time falls within a defined business hours shift, then the working hours path is chosen
- If none of the above happens, then the default path is chosen

**Play Message (Override, Holiday and After Hours):**

- These play message steps alert the caller to the reason for the closure

**Menu (Main):**

- Press 1 for Support: The call is queued for the support team
- Press 2 for Sales: The call is queued for the sales team
- Press nothing: The call is sent to Sales
- Press invalid option: The call is sent to Sales

**Queue Contact:**

- After the menu, the call is placed into a specific queue
- If there are available Agents, the Caller will be connected to an Agent, and the flow ends

**Advanced Queue Info:**

- This activity grabs multiple statistics about the caller and the queue selected, but we are only interested in using position in queue (PIQ) for this flow

**Set Variable (PIQ):**

- We use the set variable step to store the PIQ value
- We do this so that we can collapse the flow logic into a single stream, after starting as two separate streams with the outcome of the menu
- This is an optional optimization to collapse the logic

**Play Message (Agent Busy):**

- We inform the Caller that all Agents are busy
- We also inform the Caller of their PIQ

**Condition (PIQ):**

- This activity checks if the PIQ of the Caller is below or above a certain theshold
- In this way, we avoid offering Callback to a Caller who is first in line (PIQ = 1)

**Menu (Callback and Voicemail):**

- If the Caller's PIQ exceeds our defined threshold of 9 Callers in queue, we offer alterntive options than waiting in the queue listening to music
- If the Caller chooses Callback, we take their caller ID number and the entry point number they dialed, and submit a callback request on their behalf. We then thank them, and disconnect the call.
- If the Caller chooses to leave a voicemail, we use a blind transfer to send the Caller to an extension. This extension should be configured in your telephony system to be answered by voicemail.

**Play Message (Comfort):**

- A secondary message is played while the caller is waiting to keep them comfortable about the wait

**Play Music (Hold Music):**

- While waiting in the queue, the flow plays hold music. In this case, the default file "defaultmusic_on_hold.wav" is used, and it plays for 30 seconds before looping back to the Comfort Play Message activity.

**Play Message (Callback Failure):**

- If we have trouble with the Callback feature, we let the Caller know, and continue the queue treatment

### Additional Details

For more information on Webex Contact Center Flows, refer to the detailed documentation on help.webex.com.

[Webex Contact Center Flow Designer - Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee)
