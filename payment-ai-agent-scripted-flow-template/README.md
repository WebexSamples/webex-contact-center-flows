# Hospital Payment Scripted AI Agent Voice Flow - Template

## Name

Hospital Payment Scripted AI Agent Voice Flow

## Labels

Intermediate, Voice, Inbound, Scripted AI Agent, Self-Service, Payments

## Description

An inbound Webex Contact Center voice flow that integrates a scripted AI Agent, routes balance and payment events to companion subflows, returns fulfillment data, and supports disconnect and human-escalation paths.

## Details

This is an atomic **FLOW** template. It contains the main telephony orchestration for a scripted hospital payment use case; the scripted AI Agent and each fulfillment subflow are imported and published separately.

**Key Features:**

- Hands the voice interaction to a Webex Contact Center Scripted AI Agent.
- Routes scripted custom events for balance lookup, payment initiation, payment-detail collection, completion, and escalation.
- Parses patient identity and card metadata supplied by the AI Agent.
- Invokes separately published `checkBalance` and `makePayment` subflows.
- Returns named balance and payment result events to the scripted AI Agent.
- Queues escalated contacts to a human billing team and disconnects completed calls.

### Pre-requisites

- Import and publish the companion [Scripted Check Balance Subflow](../payment-ai-agent-scripted-check-balance-subflow-template/) and [Scripted Make Payment Subflow](../payment-ai-agent-scripted-make-payment-subflow-template/) templates.
- Import or create the companion [Payment Scripted AI Agent](https://github.com/ciscoAISCG/webex-cx-ai/blob/main/Playbooks/Payment_AI_Agent_Scripted/exports/Payment_AI_Agent_Scripted.json) in AI Agent Studio.
- Create a billing handoff queue in Control Hub and make it available to the flow.
- Configure any required Text-to-Speech connector and language resources.
- Confirm that the backend APIs used by the two subflows are reachable from the tenant.

### Configuration After Previewing the Template

1. In `AI_Agent_payment`, select the scripted AI Agent created in your tenant.
2. In `checkBalance_pp0`, select the published scripted check-balance subflow.
3. In `makePayment_pgv`, select the published scripted make-payment subflow.
4. In `QueueContact_a09`, replace the sample `credit` queue with the billing queue for your organization.
5. Review the global voice and language variables.
6. Validate every success, error, escalation, and disconnect path before publishing.

Tenant-specific AI Agent, subflow, and queue identifiers in the exported sample must be rebound before the flow is published.

### Event Routing

| AI Agent event | Flow action |
|---|---|
| `Payment_Balance_Response_custom_Event` | Parses patient identity and invokes the check-balance subflow. |
| `make_Payment_Custom_Event` | Starts the payment journey by invoking the balance lookup first. |
| `state_update` | Returns state metadata so the AI Agent can collect payment details. |
| `collectPaymentDetails_customEvent` | Parses card metadata and invokes the make-payment subflow. |
| `Bye` | Disconnects the caller. |
| `Escalated` | Queues the caller to the configured billing team. |

The companion subflows return the `announceBalanceResponse` and `paymentResultResponse` event names expected by the scripted AI Agent.

### Activities Used in the Flow

- **NewPhoneContact** starts the inbound voice interaction.
- **AI_Agent_payment** exchanges control, state events, metadata, and results with the scripted AI Agent.
- **state_event_decider** selects the balance, payment, update, completion, or escalation path.
- **Parse_checkBanalnceData** extracts patient identity values from AI Agent metadata.
- **checkBalance_pp0** performs the account and balance lookup.
- **SetVariable_z7j** prepares the payment-detail state update.
- **Parse_makePaymentData** extracts card metadata.
- **makePayment_pgv** performs payment fulfillment.
- **dataBacktoAI** returns named response events and data to the AI Agent.
- **QueueContact_a09**, **PlayMusic_f5t**, and **DisconnectContact_gse** handle escalation and completion.

### Security and Production Readiness

- Treat patient ID, date of birth, card number, CVV, and expiry date as sensitive data.
- Mark sensitive variables as Secure and verify they are excluded from logs and desktop visibility.
- Use authenticated API endpoints and a tokenized payment pattern appropriate for PCI DSS requirements.
- Do not publish this template with the sample tenant bindings.

For general Flow Designer configuration, refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee).
