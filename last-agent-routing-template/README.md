# Last Agent Routing Template

## Name
Last Agent Routing Template

## Labels
Voice, HTTP Activity, GraphQL, Search API, Last Agent Routing

## Description

This flow template demonstrates how to implement Last Agent Routing within Webex Contact Center by leveraging the enhanced HTTP Activity with support for `Content-Type: GraphQL`. It uses the WebexCC APIs HTTP connector to interact with the Search API, enabling routing calls to the last agent who handled the call. This template enhances customer experience by connecting them with a familiar agent.

## Details

This flow template checks if a customer has called within the last 24 hours and, if so, routes the call to the same agent. It utilizes the Search API via GraphQL to find the last agent who handled the call based on the caller's ANI (Automatic Number Identification).

It uses the feature that enhances the HTTP Activity within Webex Contact Center by adding support for `Content-Type: GraphQL` - Ability to use the WebexCC APIs HTTP connector to use the Search API via the new GraphQL content type: including variable substitution.

### Pre-requisites

- Configure a Connector to Webex Contact Center APIs.
- Ensure the Webex Contact Center environment is properly set up: Entry Point, Entry Point Mapping, Queues, etc.

### Flow Breakdown

1. **Call is Received:**
   - Call enters the flow at the **NewPhoneContact** activity.

2. **Initial Greeting:**
   - The **PlayMessage** activity plays an initial greeting message to the caller.

3. **Extract Current Time:**
   - The **CurrentTime** activity extracts the current time.

4. **Calculate Time 24 Hours Ago:**
   - The **Goback_By_a_day** activity calculates the time 24 hours prior to the current time.

5. **Trim the ANI:**
    - The **SetVariable** activity trims the ANI (caller's phone number) to remove the "+1" prefix for lookup purposes.

6. **Search API Call (GraphQL):**
   - The **SearchAPILastAgent** activity makes a call to the Webex Contact Center Search API using GraphQL to find the agent who handled the previous call based on the ANI.
   - It uses the `goback_by_a_day` and `currentTime` variables to search within the last 24 hours.
   - The GraphQL query searches for tasks matching the caller's ANI or trimmed ANI that are not active and extracts the owner ID (agent ID) of the task.

7. **Debug Logging:**
   - The **DebugLog** activity logs the HTTP status code and response body from the Search API call.
   - The **Debug_Log** activity logs the extracted agent ID.

8. **Check API Response:**
    - The **Condition_kxu** activity checks if the HTTP status code from the Search API call is 200 (success).

9. **Check if Agent ID is Extracted:**
   - The **Condition_jtn** activity checks if an agent ID was successfully extracted from the Search API response.

10. **Route to Last Agent (If Found):**
    - If an agent ID is found, the **PlayMessage_ee8** activity plays a confirmation message to the caller, informing them that they are being transferred to the same agent they spoke with previously.
    - The **QueueToAgent_xh1** activity queues the call to the agent with the extracted agent ID.

11. **Route to Default Queue (If Not Found):**
    - If no agent ID is found (either the API call failed or no previous call was found within 24 hours), the **QueueToDefault** activity queues the call to a default queue.

12. **Play Music on Hold:**
    - The **PlayMusic_i73** activity plays music on hold while the caller is waiting in the queue.

### Variables

-   **agentId:** (STRING) - The ID of the last agent who handled the call.
-   **currentTime:** (STRING) - The current time in milliseconds since epoch.
-   **goback_by_a_day:** (STRING) - The time 24 hours ago in milliseconds since epoch.
-   **Response:** (STRING) - The HTTP response from the Search API.
-   **ANITrim:** (STRING) - The trimmed ANI (phone number) of the caller.

### Activities Used

**Start**

-   **NewPhoneContact:** Starts the flow when a new phone contact is received.

**Action**

-   **PlayMessage:** Plays a message to the caller.
-   **QueueToAgent:** Queues the call to a specific agent.
-   **PlayMusic:** Plays music on hold.
-   **Queue:** Queues the call to a default queue.
-   **HTTP:** Makes an HTTP request to the Search API using GraphQL.
-   **PlayMessage:** Plays a message indicating the caller is being routed to the last agent.

**Set Variable**

-   **CurrentTime:** Sets a variable to the current time.
-   **Goback_By_a_day:** Sets a variable to the time 24 hours ago.
-   **DebugLog:** Logs the API response for debugging.
-   **Debug_Log:** Logs the extracted agent ID for debugging.
-   **SetVariable_7b4:** Trims the ANI for lookup.

**Conditions**

-   **Condition_jtn:** Checks if an agent ID is extracted.
-   **Condition_kxu:** Checks if the HTTP status code is 200.

### Additional Details

For more information on using HTTP requests with GraphQL and other activities within Webex Contact Center, refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide).  

Also refer to the Webex Contact Center APIs documentation for details on the Search API and GraphQL queries.
[Search API Documentation - Developer Portal](https://developer.webex-cx.com/documentation/search/v1/search-tasks)