# Comprehensive Inbound Contact Flow - Template

## Name
Comprehensive Inbound Contact Flow

## Labels 
Intermediate, Voice, Inbound, PIQ, Queue

## Description

This flow demonstrates a comprehensive inbound voice call scenario for Webex Contact Center. It includes handling business hours, holidays, emergency overrides, self-service options, position in queue (PIQ) announcements, and customer callback options. This is suitable for environments where basic self-service and call queuing are essential.

## Details

This inbound flow provides a comprehensive handling of incoming calls, covering business hours checks, position in queue announcements, and callback options. Modify the flow to fit specific organization needs and to handle unknown conditions gracefully.

> Note: This flow uses Cisco Text-to-Speech (TTS) for audio activities requiring prompts (if any).
>
> For music, it defaults to the `defaultmusic_on_hold.wav` file provided out-of-box.
>
> All organization-specific configurations such as Queue, Entry Points, Connectors, Outdial ANI must be manually configured in the Control Hub settings page for Webex Contact Center before publishing.

### Pre-requisites

- Create Entry Point, Queue, Teams, and Entry Point Mapping from the Control Hub settings page for Webex Contact Center. Refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee).
- Set up working hours, holiday lists, and emergency overrides from Control Hub → Services → Contact Center Setup → Business Hours.
- If Cisco Text-to-Speech (TTS) is not enabled for prompts, upload the required static audio files.

### Flow Breakdown

1. **Call is Received:**
   - Call enters the flow at the **NewPhoneContact** activity.

2. **Check Business Hours:**
   - The flow checks the current time against the defined business hours using the **BusinessHours** activity.
     - **Working Hours:** The call is routed to the **Work_Non_WorkHours_Match** activity, and further handled based on conditions such as open hours or after-hours.
     - **Holidays:** The **Holiday_Closed** message plays, informing the caller that the office is closed due to a holiday, followed by disconnection.
     - **Emergency Override:** The **Override_Emergency** activity plays an emergency override message, followed by disconnection.
     - **After Hours:** The **AfterHours_Prompt** activity plays a closed-hours message, and the call is disconnected.

3. **Self-Service Options:**
   - During open hours, the **WelcomeMenu** (IVR Menu) activity plays a menu offering basic self-service options to callers:
     - **Press 1 for Customer Support:** The call is queued for the support team.
     - **Press 2 for Sales:** The call is queued for the sales team.

4. **Queue Placement:**
   - The caller is placed in a queue using the **Queue** activity.
   - The **GetPositioninQueue** activity retrieves the caller’s position in the queue, and this information is announced to the caller using the **PlayPIQ** activity.

5. **Callback and Voicemail Options:**
   - If the caller chooses to leave a voicemail or request a callback, the **FinalMenu** activity is triggered:
     - **Press 1 for Callback:** The **Callback_guf** activity is used to schedule a callback.
     - **Press 2 for Voicemail:** The call is transferred to voicemail using the **VoiceMail** activity.

6. **Hold Music:**
   - While waiting in the queue, the caller hears hold music using the **MusicOnHold** activity.

7. **Loop Handling:**
   - The flow ensures that if a caller loops too many times (via the **CallLoopCycle** and **LoopCycle** activities), they are directed to the final menu options (callback or voicemail).

8. **Call Disconnection:**
   - After all steps are completed or if the caller chooses to exit, the call is disconnected using the **DisconnectContact** activities.

### Activities Used

**Start**
- **NewPhoneContact:** Starts the flow when the call is received.

**Business Hours Check**
- **BusinessHours:** Checks if the call is during business hours, holidays, or emergency override situations.

**IVR Menu**
- **WelcomeMenu:** Plays a menu with options for self-service (Press 1 for Support, Press 2 for Sales).

**Queue Handling**
- **Queue:** Places the caller in a queue for the appropriate team (e.g., support or sales).
- **GetPositioninQueue:** Retrieves and announces the caller's position in the queue.
- **PlayPIQ:** Announces the caller’s position in the queue.

**Callback and Voicemail Options**
- **FinalMenu:** Offers callback or voicemail options if the call loops multiple times.
- **Callback_guf:** Schedules a callback for the caller.
- **VoiceMail:** Transfers the caller to voicemail.

**Hold Music**
- **MusicOnHold:** Plays hold music while the caller waits in the queue.

**Loop Handling**
- **CallLoopCycle and LoopCycle:** Ensures that calls looping too many times are directed to the final menu.

**Disconnection**
- **DisconnectContact:** Disconnects the call after messages or when the caller chooses to end the interaction.

### Additional Details

For more information, refer to the detailed documentation on the Webex Contact Center help portal.

[Webex Contact Center Flow Designer - Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee)