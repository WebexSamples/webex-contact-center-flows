# Hospital Payment Scripted Make Payment Subflow - Template

## Name

Hospital Payment Scripted Make Payment Subflow

## Labels

Advanced, Subflow, HTTP, Scripted AI Agent, Payments, PCI

## Description

A reusable Webex Contact Center subflow that accepts billing-account and payment-card data, calls a payment API, and returns a structured result plus the scripted AI Agent response event name.

## Details

This is an atomic **SUBFLOW** template for the payment-fulfillment step used by the scripted hospital payment voice flow. It can also be invoked by another compatible flow after its inputs and outputs are mapped.

**Key Features:**

- Accepts card, account, and payment amount values from the invoking flow.
- Sends an HTTP `POST` request to a configurable payment endpoint.
- Extracts payment status, currency, balance amount, and error data from the response.
- Returns a normalized JSON result and the `paymentResultResponse` event name.
- Provides separate success and error result paths before ending the subflow.

### Pre-requisites

- Provide a payment API with the request and response contract documented below.
- Replace the placeholder `https://example.invalid/replace-with-your-make-payment-api-endpoint` URL in the `makePayment` HTTP activity.
- Add the authentication headers or connector required by your payment gateway.
- Import and publish this subflow before binding it in the companion [Scripted AI Agent Voice Flow](../payment-ai-agent-scripted-flow-template/).
- Complete your organization's PCI DSS, privacy, and security review before processing payment-card data.

### Subflow Inputs

| Variable | Type | Purpose |
|---|---|---|
| `subFlowCardNumber_makePayment` | String | Card number supplied by the invoking flow. |
| `subFlowCVV_makePayment` | String | Card verification value supplied by the invoking flow. |
| `subFlowExpiryDate_MakePayment` | String | Card expiry date supplied by the invoking flow. |
| `subflowAccountNumber_makePayment` | String | Billing account returned by the earlier balance lookup. |
| `subFlowBalanceAmount_makePayment` | String | Payment amount supplied to the payment API. |

### Subflow Outputs

| Variable | Type | Purpose |
|---|---|---|
| `outPutJSONFromMakePayment` | JSON | Success payload containing `status`, `currency`, and `amount`, or an error payload containing `error`. |
| `subFlowEventName` | String | Scripted AI Agent response event, set to `paymentResultResponse`. |

### API Contract

The HTTP activity sends an `Application/JSON` request shaped like:

```json
{
  "accountId": "<account number>",
  "cardNumber": "<card number>",
  "cvv": "<CVV>",
  "expiryDate": "<expiry date>",
  "amount": 0
}
```

The response is expected to expose `status`, `currency`, `balanceAmount`, and, on failure, `error`.

### Activities Used in the Subflow

- **StartSubflow** receives the mapped input variables.
- **makePayment** calls the payment API.
- **Condition_2yp** selects the success or error path.
- **SetVariable_5f3** builds the successful payment response.
- **SetVariable_5f3_kkt** builds the error response.
- **EndSubflow_zik** returns control and outputs to the invoking flow.

### Security and Production Readiness

- Mark all card, CVV, expiry, and account variables as Secure and confirm that they are not logged.
- Prefer tokenized or hosted payment capture so raw PAN and CVV do not traverse the flow.
- Never store CVV after authorization; follow your organization's PCI DSS controls.
- Use TLS, authenticated endpoints, least-privilege credentials, and sanitized error responses.
- Review the two-second HTTP timeout and retry behavior with the payment provider.

For general subflow configuration, refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee).
