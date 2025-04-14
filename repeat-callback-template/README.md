# Avoid Duplicate Callback

## Name
Avoid Duplicate Callback

## Labels
Voice, HTTP Activity, GraphQL, Search API, Avoid Duplicate Callback

## Description

This flow template demonstrates how to prevent duplicate callback entries within Webex Contact Center by leveraging the enhanced HTTP Activity with support for `Content-Type: GraphQL`. It uses the WebexCC APIs HTTP connector to interact with the Search API, enabling the flow to check for existing callback requests from the same caller. This template improves efficiency and customer experience by avoiding redundant callbacks.

## Details

This flow template checks if a customer has already placed a callback request in the system. It utilizes the Search API via GraphQL to determine if an active callback task exists for the caller's ANI (Automatic Number Identification).

It uses the feature that enhances the HTTP Activity within Webex Contact Center by adding support for `Content-Type: GraphQL` - Ability to use the WebexCC APIs HTTP connector to use the Search API via the new GraphQL content type, including variable substitution.

### Pre-requisites

- Configure a Connector to Webex Contact Center APIs.
- Ensure the Webex Contact Center environment is properly set up: Entry Point, Entry Point Mapping, Queues, etc.

### Flow Breakdown

1. **Call is Received:**
   - Call enters the flow at the **NewPhoneContact** activity.

2. **Initial Greeting:**
   - The **PlayMessage_wgk** activity plays an initial greeting message to the caller.

3. **Extract Current Time:**
   - The **SetVariable_7a1** activity extracts the current time in epoch milliseconds and stores it in the `currentTime` variable.

4. **Calculate Time 24 Hours Ago:**
   - The **SetVariable_8t9** activity calculates the time 24 hours prior to the current time in epoch milliseconds and stores it in the `goback_by_a_day` variable.

5. **Trim the ANI:**
    - The **SetVariable_ak4** activity trims the ANI (caller's phone number) to remove the "+1" prefix for lookup purposes.

6. **Search API Call (GraphQL):**
   - The **SearchAPIRequest** activity makes a call to the Webex Contact Center Search API using GraphQL to find any existing active callback tasks based on the ANI.
   - It uses the `goback_by_a_day` and `currentTime` variables to search within the last 24 hours.
   - The GraphQL query searches for tasks matching the caller's ANI or trimmed ANI that are active and have a callback status.

7. **Check API Response:**
   - The **SetVariable_xye** activity combines the HTTP status code, callback status, and HTTP response body from the Search API call into the `apiOutput` variable.
   - The **Condition_ts8** activity checks if the HTTP response body from the Search API call contains "Not Processed", indicating no duplicate callback.

8. **Handle Duplicate Callback (If Found):**
   - If a duplicate callback is found (the API response contains a callback), the **PlayMessage_99x** activity informs the caller that a callback is already scheduled and then the **DisconnectContact_mx8** activity disconnects the call.

9. **Schedule New Callback (If Not Found):**
   - If no duplicate callback is found, the flow proceeds to the **Menu_lsi** activity, which presents the caller with options to schedule a callback or wait in the queue.

10. **Schedule Callback:**
    - If the caller chooses to schedule a callback (presses 1), the **Callback_20e** activity schedules a callback using the caller's ANI. A confirmation message is played via **PlayMessage_ysw** and then **DisconnectContact_mx8_2bg** disconnects the call.

11. **Wait in Queue:**
    - If the caller chooses to wait in the queue (presses 2), the **SetVariable_c0y** increments a counter. The call is then queued to an agent via **QueueContact_95e** and music is played on hold via **PlayMusic_qne**. The call loops back to the **Menu_lsi** activity.

### Variables

-   **callBackStatus:** (STRING) - The status of the callback.
-   **counter:** (INTEGER) - A counter variable.
-   **currentTime:** (STRING) - The current time in milliseconds since epoch.
-   **goback_by_a_day:** (STRING) - The time 24 hours ago in milliseconds since epoch.
-   **apiOutput:** (STRING) - The combined HTTP status code, callback status, and HTTP response from the Search API.
-   **ANITrim:** (STRING) - The trimmed ANI (phone number) of the caller.
-   **response:** (STRING) - The HTTP response from the Search API.

### Activities Used

**Start**

-   **NewPhoneContact:** Starts the flow when a new phone contact is received.

**Action**

-   **PlayMessage:** Plays a message to the caller.
-   **Callback:** Schedules a callback for the caller.
-   **PlayMusic:** Plays music on hold.
-   **QueueContact:** Queues the call to an agent.
-   **HTTP:** Makes an HTTP request to the Search API using GraphQL.
-   **DisconnectContact:** Disconnects the call.

**Set Variable**

-   **SetVariable:** Sets various variables, including current time, time 24 hours ago, trimmed ANI, and API output.

**Conditions**

-   **Condition:** Checks if the HTTP response body contains "Not Processed".
-   **Menu:** Provides the caller with options to schedule a callback or wait in the queue.

### Additional Details

For more information on using HTTP requests with GraphQL and other activities within Webex Contact Center, refer to the [Webex Contact Center Setup and Administration Guide](https://help.webex.com/en-us/article/n5595zd/Webex-Contact-Center-Setup-and-Administration-Guide).

Also refer to the Webex Contact Center APIs documentation for details on the Search API and GraphQL queries.
[Search API Documentation - Developer Portal](https://developer.webex-cx.com/documentation/search/v1/search-tasks)