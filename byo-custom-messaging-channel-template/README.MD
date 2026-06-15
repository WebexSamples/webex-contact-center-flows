# Bring Your Own Custom Messaging Channel - Template

## Name
BYO Custom Messaging Channel Template

## Labels
Advanced, Custom Messaging, BYOC, Digital Channel, Queue, Conversational

## Description

A comprehensive template for integrating custom messaging channels with Webex Contact Center. This flow enables organizations to bring their own messaging platforms (such as Instagram, Viber, WhatsApp Business, proprietary chat systems, or any third-party messaging service) and route conversations through Webex Contact Center's queue, flow execution, agent management, and reporting systems while the channel connectivity remains outside Cisco's boundary.

## Quick Reference

| Property | Value |
|----------|-------|
| **Channel Type** | Custom Messaging (`customMessaging`) |
| **Feature Flag** | `wxcc_digital_byoc` |
| **Licensing** | Standard Agent License |
| **Max Channels per Org** | 20 |
| **Max Time in Queue** | 14 days |
| **Key Activities** | SendCustomMessage, ReceiveMessage, QueueContact |
| **Message Types** | `text`, `text-with-attachments` |
| **API Requirements** | Task Creation API, Task Messages API, Webhook endpoint |
| **Feature JIRA** | [WXCC-11375](https://jira-eng-sjc12.cisco.com/jira/browse/WXCC-11375) |

## Details

This flow provides a complete framework for handling custom messaging channel interactions in a contact center:

1. A new contact arrives through the custom messaging channel entry point.
2. A welcome message with an attachment is sent to the customer via the SendCustomMessage activity.
3. The flow waits to receive the customer's response using the ReceiveMessage activity.
4. Once the customer responds, the contact is queued to an agent on the custom messaging channel.
5. While in queue, periodic updates are sent to keep the customer informed (every 10 seconds by default).
6. When an agent accepts the contact, a screen pop is displayed with relevant information.
7. Global error handling ensures graceful failure with appropriate customer notifications.

**Key Features:**
- Send text messages with attachments to customers
- Receive and parse customer messages with timeout handling
- Queue management for custom messaging channels
- Automated queue updates to keep customers engaged
- Parse message content and attachments using output variables
- Screen pop functionality when agent accepts the contact
- Comprehensive global error handling with customer notifications
- Support for message transcript tracking and conversation history
- Channel-aware activity filtering (only compatible activities are shown in Flow Designer)
- Support for both system-generated (flow) and agent-sent messages

**Supported Message Types:**
- `text`: Plain text messages
- `text-with-attachments`: Text messages with file attachments (images, documents, etc.)

**Activity Compatibility:**
Custom messaging flows support specific activities through the `supportedChannelTypes` contract. Activities visible in Flow Designer are automatically filtered based on channel compatibility. Unsupported activities for custom messaging will fail gracefully with explicit error paths if inadvertently used.

### Pre-requisites

- **Feature Flag**: Ensure the `wxcc_digital_byoc` feature flag is enabled for your organization.
- **Licensing**: Standard Agent License is sufficient for custom messaging channels.
- **Control Hub Configuration**: 
  - **Channel**: Create a custom messaging channel in Control Hub (Channels & Assets page) with:
    - Channel name and icon/logo
    - Message policies (max text size, emoji support)
    - File attachment settings (enabled/disabled, max count, size limits, allowed file types)
  - **Asset**: Create a custom messaging asset with:
    - Associated channel selection
    - Business address (destination identifier for routing)
    - Webhook URL and Secret for receiving outbound messages (flow → customer, agent → customer)
  - **Entry Point**: Create an entry point of custom messaging type with:
    - Channel and asset mapping
    - Flow association (filtered by custom messaging channel type)
    - SLA settings
  - **Queue**: Configure queues to support custom messaging channel type (max time in queue: 14 days)
  - **Multimedia Profile**: Configure concurrent task limits for custom messaging in agent multimedia profiles
  - **Teams**: Configure team settings to support custom messaging for queue ranking
- **API Integration**: Your messaging platform must:
  - Use the Task Creation API (POST `/v2/tasks`) to inject new conversations into Webex Contact Center
  - Use the Task Messages API (POST `/v2/tasks/{taskId}/messages`) to forward inbound customer messages
  - Implement a webhook endpoint to receive outbound messages from Webex Contact Center
  - Validate webhook signatures using the configured secret for security

### Activities Used in the Flow

Here are the main activities used in the flow:

**Start (New Contact):**
- The flow begins when a new contact is received via the custom messaging channel entry point.
- The contact enters the flow and proceeds to the welcome message.

**SendCustomMessage (Welcome Message):**
- Sends an initial message to the customer: "Hello, thanks for contacting us. What do you need help with?"
- Includes a sample image attachment (https://picsum.photos/400/300.jpg) demonstrating `text-with-attachments` message type.
- **Message Delivery**: This activity triggers an outbound webhook (`task-message:appended` event) to the partner's configured webhook URL. The partner is responsible for forwarding the message to the actual customer on the external channel.
- Uses output variables to capture the sent message text and attachment URL:
  - `sentMessageText`: Stores the message text sent to the customer (JSON path: `$.message.text`)
  - `sentMessageAttachment1Url`: Stores the URL of the first attachment (marked as secure, JSON path: `$.message.attachments[0].fileUrl`)
- The message is appended to the contact transcript for agent and supervisor visibility.

**ReceiveMessage:**
- Waits for the customer to respond with a message.
- Timeout is set to 600 seconds (10 minutes) - adjustable based on your use case.
- **Critical Dependency**: The partner's messaging platform must forward the customer's message to Webex Contact Center using the Task Messages API (`POST /v2/tasks/{taskId}/messages`) for the flow to proceed.
- Parses the received message using output variables with JSON path expressions:
  - `receivedMessageText`: Captures the text content of the customer's message (marked as secure)
  - Additional parsing examples: `$.message.attachments[0].fileUrl`, `$.message.timestamp`
- Supports three exit paths:
  - **default**: Message received successfully, flow continues
  - **timeout**: Customer didn't respond within timeout period
  - **error**: System error occurred (handled by global error handler)
- Messages are automatically appended to the conversation transcript for agent visibility.

**QueueContact:**
- Places the contact in the queue for routing to an available agent.
- Configured for custom messaging channel type.
- Routes to "templateQueue" with the longest available agent strategy.

**SendCustomMessage (Queue Updates):**
- Sends periodic updates to the customer while waiting in queue (message type: `text`).
- Default message: "Please wait while we connect you with an agent."
- This activity can be enhanced to include dynamic information:
  - Position in Queue (PIQ): Use Get Queue Info activity to fetch `{{GetQueueInfo.PositionInQueue}}`
  - Estimated Wait Time (EWT): Use Advanced Queue Info to fetch `{{AdvancedQueueInfo.EstimatedWaitTime}}`
  - Example enhanced message: "You are number {{GetQueueInfo.PositionInQueue}} in queue. Estimated wait time is {{AdvancedQueueInfo.EstimatedWaitTime}} minutes."
- Each message triggers an outbound webhook to the partner platform.

**Wait:**
- Introduces a 10-second delay between queue update messages.
- Duration can be adjusted based on customer experience requirements.
- The Wait and SendQueueUpdates activities are linked in a loop to provide continuous updates until an agent accepts the contact.

### Global Event Handlers

The template includes comprehensive event handling:

**AgentAccepted Event:**
- Triggers when an agent accepts the contact.
- Displays a screen pop with placeholder content (https://www.cisco.com).
- The screen pop URL and label can be customized to show customer context, CRM data, or other relevant information.

**OnGlobalError Event:**
- Handles any unexpected system errors during the flow execution.
- Sends an error message to the customer: "There was a server error. Please start a new conversation."
- Gracefully disconnects the contact after notifying the customer.

**AgentOffered Event:**
- Placeholder for handling when a contact is offered to an agent.
- Can be extended to implement custom logic when offering contacts to agents.

**ContactEnded Event:**
- Placeholder for handling cleanup or post-contact activities.
- Can be extended for post-contact surveys, disposition codes, or analytics.

### Flow Variables

The template uses three flow variables for message handling:

- **sentMessageText** (STRING): Stores the text of messages sent to the customer
- **sentMessageAttachment1Url** (STRING, Secure): Stores the URL of attachments sent to the customer
- **receivedMessageText** (STRING, Secure): Stores the text of messages received from the customer

These variables enable message parsing, content validation, and conditional flow logic based on message content.

### Error Handling

- The flow includes global error handling that catches any system failures.
- Customers receive a clear error message before the contact is disconnected.
- Timeout handling in the ReceiveMessage activity prevents indefinite waiting.
- All critical activities have error branches that can be customized for specific error recovery scenarios.

### Flow Metadata and Channel Association

This template includes the `associatedChannels` metadata field required for custom messaging flows:

```json
"associatedChannels": [
  {
    "channelType": "customMessaging",
    "id": "738848e7-8980-4ec0-b223-b7fe27194dbe",
    "name": "templateChannel"
  }
]
```

**Important:** The `associatedChannels` field is required for:
- Flow Designer channel-aware filtering (showing only relevant activities)
- Entry point flow association (only custom messaging flows appear in the dropdown)
- Template behavior (when creating flows from this template)
- Runtime validation (ensuring the flow can execute for custom messaging contacts)

### Customization Points

To adapt this template for your use case:

1. **Channel Selection**: 
   - When you import this template into your organization, you will be prompted to select a custom messaging channel from your Control Hub configuration
   - The selected channel will automatically be associated with the flow in the `associatedChannels` metadata
   - No manual channel ID updates are required after import
2. **Replace Queue Configuration**: Update "templateQueue" with your actual queue name.
3. **Customize Welcome Message**: Modify the initial greeting text and attachments to match your brand and use case.
4. **Adjust Queue Updates**: Change the frequency (Wait duration) and content of queue update messages.
5. **Enhance Screen Pop**: Configure the screen pop URL to display relevant customer context from your CRM or backend systems.
6. **Add PIQ/EWT**: Insert Get Queue Info or Advanced Queue Info activities before the SendQueueUpdates activity to provide real-time queue statistics.
7. **Extend Message Parsing**: Add more output variables to parse additional message properties (sender info, timestamps, rich media, etc.).
8. **Implement Conditional Logic**: Add condition nodes to route based on message content or customer intent.
9. **Add Global Variables**: Use the `globalVariables` field in the Task Creation API to pass custom data that can be used throughout the flow.

### Integration Requirements

Your custom messaging platform must implement the following integration patterns:

#### 1. Task Creation (Partner → Webex Contact Center)
When a customer initiates a conversation on your channel, create a new task using the Task Creation API (`POST /v2/tasks`). Store the returned `taskId` from the response for all subsequent message operations.

#### 2. Inbound Messages (Partner → Webex Contact Center)
When the customer sends additional messages during the conversation, forward them using the Task Messages API (`POST /v2/tasks/{taskId}/messages`).

#### 3. Outbound Messages (Webex Contact Center → Partner)
Implement a webhook endpoint to receive messages sent from flows and agents. The webhook will receive `task-message:appended` events with message content and metadata.

**Security**: Validate the `x-webexcc-signature` header using HMAC-SHA256 with your configured webhook secret to ensure message authenticity.

#### 4. Message Policies and Validation
Ensure all messages comply with the channel's configured policies:
- Max text message size limit (configurable per channel)
- Max number of attachments
- Single and total attachment size limits
- Allowed file types (MIME types)
- Emoji support settings

Messages that violate these policies will be dropped, and a task message append failure will be logged.

**API Reference**: For detailed API payloads, request/response formats, and authentication details, refer to the [Webex Contact Center Developer Portal](https://developer.webex.com/docs/contact-center).

### Troubleshooting

**Common Issues and Solutions:**

1. **Task Creation Fails**
   - Verify the `wxcc_digital_byoc` feature flag is enabled for your organization
   - Confirm the channel name matches exactly with the Control Hub configuration
   - Check that the destination business address matches the asset configuration
   - Ensure the authorization token has the required task scopes

2. **Messages Not Appearing in Flow**
   - Verify the ReceiveMessage activity timeout hasn't been exceeded (default: 600 seconds)
   - Check that the Task Messages API is being called with the correct `taskId`
   - Confirm the message payload complies with channel message policies (size, attachment limits)
   - Review logs for "task message append failure" errors

3. **Webhook Not Receiving Outbound Messages**
   - Verify the webhook URL is publicly accessible (not localhost or internal network)
   - Check webhook URL configuration in the asset settings
   - Validate webhook signature verification is implemented correctly using HMAC-SHA256
   - Review webhook delivery logs for HTTP errors or timeouts

4. **Flow Not Showing in Entry Point Dropdown**
   - Ensure the flow's `associatedChannels` metadata includes `channelType: "customMessaging"`
   - Verify the channel ID in `associatedChannels` matches your Control Hub channel
   - Confirm the flow is published and not in draft state

5. **Message Policy Violations**
   - Messages exceeding text size limits will be dropped silently
   - Attachments with unsupported MIME types will be rejected
   - Verify emoji usage matches channel configuration
   - Check total attachment size doesn't exceed configured limits

### Feature Background

This template implements the **Custom Messaging Channel (BYOC - Bring Your Own Channel)** feature which enables Webex Contact Center to support conversational digital interactions from external messaging platforms.

**Key Capabilities:**
- Supports text messages with attachments for rich conversational experiences
- Provides first-class channel support across routing, queuing, agent desktop, supervisor monitoring, and reporting
- Enables businesses to integrate custom channels without Cisco building channel-specific integrations
- Leverages proven architecture patterns for reliable task injection and management

**Architecture Note:**
- The `customMessaging` media type is treated as a first-class channel type across all WxCC services
- Maximum 20 custom messaging channels per organization
- Maximum time in queue: 14 days (aligned with data retention requirements)

### Additional Details

For more information on Webex Contact Center Custom Messaging Channels and Flow Designer:

- [Webex Contact Center Flow Designer - Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide)
- [Webex Contact Center Developer Portal - Task APIs and Webhooks](https://developer.webex.com/docs/contact-center)
- [Webex Contact Center Developer Portal](https://developer.webex.com/webex-contact-center/docs/webex-contact-center)

**Feature JIRA Reference:** [WXCC-11375](https://jira-eng-sjc12.cisco.com/jira/browse/WXCC-11375)
