# Hospital Payment Autonomous AI Agent Voice Flow - Template

## Name

Hospital Payment Autonomous AI Agent Voice Flow

## Labels

Intermediate, Voice, Inbound, AI Agent, Self-Service, Payments

## Description

An inbound Webex Contact Center voice flow that connects callers to an autonomous AI Agent, routes balance and payment requests to companion subflows, returns fulfillment data to the AI Agent, and supports human handoff.

## Details

This is an atomic **FLOW** template. It contains the main telephony orchestration for a hospital payment use case; the AI Agent and each fulfillment subflow are imported and published separately.

**Key Features:**

- Hands the voice interaction to a Webex Contact Center Autonomous AI Agent.
- Routes the `checkBalance` and `makePayment` state events emitted by the AI Agent.
- Parses patient identity or card metadata supplied by the AI Agent.
- Invokes separately published `checkBalance` and `makePayment` subflows.
- Returns the subflow JSON response to the AI Agent for caller-facing confirmation.
- Queues the caller to a human billing team when the AI Agent requests handoff.
- Disconnects the contact when the automated interaction completes.

### Pre-requisites

- Import and publish the companion [Autonomous Check Balance Subflow](../payment-ai-agent-autonomous-check-balance-subflow-template/) and [Autonomous Make Payment Subflow](../payment-ai-agent-autonomous-make-payment-subflow-template/) templates.
- Import or create the companion [Payment Autonomous AI Agent](https://github.com/ciscoAISCG/webex-cx-ai/blob/main/Playbooks/Payment_AI_agent/Payment_Agent.json) in AI Agent Studio.
- Create a billing handoff queue in Control Hub and make it available to the flow.
- Configure any required Text-to-Speech connector and language resources.
- Confirm that the backend APIs used by the two subflows are reachable from the tenant.

### Configuration After Previewing the Template

1. In `AI_Agent_payment`, select the autonomous AI Agent created in your tenant.
2. In `Invoke_checkBalance_Subflow`, select the published check-balance subflow.
3. In `Invoke_makePayment_Subflow`, select the published make-payment subflow.
4. In `QueueContact_a09`, replace the sample `credit` queue with the billing queue for your organization.
5. Review the global voice and language variables.
6. Validate every success, error, handoff, and disconnect path before publishing.

Tenant-specific AI Agent, subflow, and queue identifiers in the exported sample must be rebound before the flow is published.

### Event Routing

| AI Agent event | Flow action |
|---|---|
| `checkBalance` | Parses `patientID` and `dateOfBirth`, then invokes the check-balance subflow. |
| `makePayment` | Parses `CardNumber`, `CVV`, and `expiryDate`, then invokes the make-payment subflow with the account and balance from the previous lookup. |
| Handoff path | Sends the contact to the configured billing queue. |
| Completion path | Disconnects the contact. |

### Activities Used in the Flow

- **NewPhoneContact** starts the inbound voice interaction.
- **AI_Agent_payment** exchanges control, state events, metadata, and results with the AI Agent.
- **state_event_decider** selects the balance or payment path.
- **Parse_checkBanalnceData** extracts patient identity values from AI Agent metadata.
- **Invoke_checkBalance_Subflow** performs the account and balance lookup.
- **Parse_makePaymentData** extracts card metadata from the AI Agent event.
- **Invoke_makePayment_Subflow** performs payment fulfillment.
- **dataBacktoAI** maps the subflow response into the event payload returned to the AI Agent.
- **QueueContact_a09** and **PlayMusic_f5t** handle human escalation.
- **DisconnectContact_gse** ends the automated interaction.

### Security and Production Readiness

- Treat patient ID, date of birth, card number, CVV, and expiry date as sensitive data.
- Mark sensitive variables as Secure and verify they are excluded from logs and desktop visibility.
- Use authenticated API endpoints and a tokenized payment pattern appropriate for PCI DSS requirements.
- Do not publish this template with the sample tenant bindings.

For general Flow Designer configuration, refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee).
