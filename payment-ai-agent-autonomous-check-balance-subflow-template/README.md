# Hospital Payment Autonomous Check Balance Subflow - Template

## Name

Hospital Payment Autonomous Check Balance Subflow

## Labels

Intermediate, Subflow, HTTP, Data Dip, AI Agent, Payments

## Description

A reusable Webex Contact Center subflow that accepts a patient ID and date of birth, calls a billing API, and returns the caller's account number and outstanding balance to an invoking flow.

## Details

This is an atomic **SUBFLOW** template for the balance-lookup fulfillment step used by the autonomous hospital payment voice flow. It can also be invoked by another compatible flow after its input and output mappings are configured.

**Key Features:**

- Accepts patient identity values from the invoking flow.
- Sends an HTTP `POST` request to a configurable billing endpoint.
- Extracts `accountId`, `balanceAmount`, and error data from the API response.
- Returns both individual values and a JSON payload to the invoking flow.
- Provides separate success and error result paths before ending the subflow.

### Pre-requisites

- Provide a billing API that accepts patient ID and date of birth and returns the documented response fields.
- Replace the placeholder `https://example.invalid/replace-with-your-check-balance-api-endpoint` URL in `HTTPRequest_p83`.
- Add the authentication headers or connector required by your API gateway.
- Import and publish this subflow before binding it in the companion [Autonomous AI Agent Voice Flow](../payment-ai-agent-autonomous-flow-template/).

### Subflow Inputs

| Variable | Type | Purpose |
|---|---|---|
| `subpatientID` | String | Patient identifier supplied by the invoking flow. |
| `subDOB` | String | Patient date of birth supplied by the invoking flow. |

### Subflow Outputs

| Variable | Type | Purpose |
|---|---|---|
| `paymentBalance` | String | Outstanding balance parsed from `$.balanceAmount`. |
| `accountNumber` | String | Billing account identifier parsed from `$.accountId`. |
| `outPutJSON` | JSON | Success payload containing `paymentBalance`, or an error payload containing `error` and `errorMessage`. |

### API Contract

The HTTP activity sends an `Application/JSON` request shaped like:

```json
{"patientId":"<patient ID>","dateOfBirth":"<date of birth>"}
```

The response is expected to expose `accountId`, `balanceAmount`, and, on failure, `error`.

### Activities Used in the Subflow

- **StartSubflow** receives the mapped input variables.
- **HTTPRequest_p83** calls the billing API.
- **Condition_2yp_okd** selects the success or error path.
- **SetVariable_xbw** builds the successful balance response.
- **SetVariable_5f3_as2** builds the error response.
- **EndSubflow_57h** returns control and outputs to the invoking flow.

### Security and Production Readiness

- Treat patient ID and date of birth as sensitive data and mark the corresponding variables as Secure.
- Use TLS, authentication, and least-privilege credentials for the billing API.
- Review the two-second HTTP timeout and error behavior for your production service levels.
- Do not expose raw backend error details to callers.

For general subflow configuration, refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide#Cisco_Generic_Topic.dita_e338e055-64b0-4973-bd52-8a5581dcb0ee).
