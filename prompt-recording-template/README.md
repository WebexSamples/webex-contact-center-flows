# Audio Prompt Recording and Management

## Name
Audio Prompt Recording and Management

## Labels
Voice, Audio File, Prompt Management, TUI, HTTP Activity, Form Data

## Description

This flow template provides a streamlined method for administrators to record and manage audio prompts within Webex Contact Center via a Telephony User Interface (TUI). It leverages enhanced HTTP Activity capabilities, including support for `Content-Type: Form Data` to interact with Webex Contact Center's Audio File (Prompt) APIs. This template replicates functionalities familiar from on-premises systems, improving customer experience and operational efficiency.

## Details

This flow template empowers administrators to easily record, replace, and manage system/flow prompts directly through a phone interface. It addresses the limitations of manual prompt management and enhances the usability of the Flow Designer, especially in emergency scenarios where desktop access is limited. Add additional checks to authorize admins, providing additional action menus, by modifying the template.

### Pre-requisites

- Create and Entry Point, and configure the Entry Point Mapping from the Control Hub settings page for Webex Contact Center. Refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee).
- Configure a Connector to Webex Contact Center APIs.
- If Cisco Text-to-Speech (TTS) is not enabled for prompts, upload the required static audio files.

### Flow Breakdown

1. **Call is Received:**
   - Call enters the flow at the **NewPhoneContact** activity.

2. **(OPTIONAL) Admin Authentication via OTP:**
   - The flow developer can implement an optional authentication barrier for the administrator, using a secure method like OTP delivered via SMS to the ANI, or a randomly generated number/PIN. This can be added before the main menu.

1. **Main Menu:**
   - The **MainMenu** activity (IVR Menu) presents the administrator with the following options:
     - **Press 1 to create a new prompt.**
     - **Press 2 to update an existing prompt.**
     - **Press 3 to delete an existing prompt.**
     - **Press 4 to exit the flow**

2. **Create Prompt (Option 1):**
   - The **PlayMessage_kcx** activity prompts the administrator to record a new audio prompt.
   - The **Record_e0j** activity records the audio input from the administrator.
   - The recorded audio is then sent to the Webex Contact Center API using the **CreatePrompt** HTTP request activity with `Content-Type: Form Data`.
   - The **Parse_gke** activity parses the response to extract the `id` and `blobId` of the newly created prompt. This is needed in case one needs to update the same prompt.
   - The **PlayMessage_q16** activity confirms that the message has been created.
   - The system plays back the recorded prompt for confirmation using **PlayRecordedMessage**.

3. **Update Prompt (Option 2):**
   - The **RecordPromptAfterTone** activity prompts the administrator to record an updated prompt.
   - The **Record_e38** activity records the new audio.
   - The flow then renames the file to be deleted using **RenameFileToDelete**. This ensures references to this audio file are removed wherever audio files are referred by name.
   - The updated audio is sent to the Webex Contact Center API using the **HTTPRequest_13n** HTTP request activity with `Content-Type: Form Data`.
   - The system confirms the update and plays back the new prompt using **PlayMessage_q16_jpg_03l** and **PlayMessage_0l6**.

4.  **Delete Prompt (Option 3):**
    - The **DeleteConfirm** activity confirms the deletion.
    - The flow renames the file to be deleted using **RenameFileToDelete**.
    - The **HTTPRequest_raf** activity sends a DELETE request to the Webex Contact Center API to delete the prompt.
    - The system confirms the deletion using **DeleteConfirm**.

5. **Exit (Option 4):**
   - The **Goodbye** activity plays a thank you message.
   - The call is disconnected using the **DisconnectContact_cz4** activity.

### Variables

- **blobId:** (STRING) - The Blob ID of the audio file.
- **audioFileName:** (STRING) - The name of the audio file (default: "EmergencyDemo.wav").
- **id:** (STRING) - The ID of the audio file.
- **status:** (STRING) - The status of the API request.
- **newFileName:** (STRING) - The name of the updated audio file (default: "updatedFile.wav").
- **response:** (STRING) - The HTTP response from the API requests. This is optional, for debugging.

### Activities Used

**Start**
- **NewPhoneContact:** Starts the flow when the call is received.

**IVR Menu**
- **MainMenu:** Plays a menu with options for prompt management.

**Create Prompt**
- **PlayMessage:** Prompts the administrator to record a new prompt.
- **Record:** Records the audio input.
- **HTTP Request:** HTTP request to create the audio prompt using `Content-Type: FORM-DATA`.
- **Parse:** Parses the HTTP response to extract the `id` and `blobId`.
- **PlayMessage:** Confirms the message creation.
- **PlayMessage:** Plays back the recorded prompt.

**Update Prompt**
- **Menu:** Prompts the administrator to record an updated prompt.
- **Record_e38:** Records the audio input.
- **HTTP Request:** HTTP request to update the audio prompt using `Content-Type: FORM-DATA`.
- **PlayMessage:** Confirms the message update.
- **PlayMessage:** Plays back the updated prompt.

**Delete Prompt**
- **Menu:** Confirms the deletion of the audio prompt.
- **HTTP Request:** Renames the file to be deleted. Needs Id of the prompt to be defined.
- **HTTP Request:** HTTP request to delete the audio prompt using `Content-Type: Application/JSON` and DELETE request.

**Other**
- **Wait:** Wait activity.
- **SetVariable:** Sets variables.
- **DisconnectContact:** Disconnects the call.

### Additional Details

For more information on using the Record Activity, HTTP requests and Recording controls within Webex Contact Center, refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide).