## Part 1 – LimaCharlie Setup

1. Create a new organization in [LimaCharlie](https://limacharlie.io).
2. Set up a new Installation Key.
3. Download and install the LimaCharlie sensor on the endpoint device using the appropriate command:

    ```bash
    # Windows EXE
    lc_sensor.exe -i YOUR_INSTALLATION_KEY

    # Windows MSI
    lc_sensor.msi InstallationKey=YOUR_INSTALLATION_KEY

    # macOS
    chmod +x lc_sensor
    ./lc_sensor -i YOUR_INSTALLATION_KEY

    # Linux
    chmod +x lc_sensor
    ./lc_sensor -d YOUR_INSTALLATION_KEY
    ```

4. Create a new Detection & Response (D&R) rule.
5. Add rules to detect malicious behavior.  
   For this example, we use a rule that detects usage of `LaZagne`.

    - Run `LaZagne` on your test endpoint.
    - In LimaCharlie’s Processes tab, locate the process and copy its SHA256 hash.
    - Use that hash in your custom detection rule.  
      Example rule provided in: `/Lazagne-D&R-Rule`.

---

## Part 2 – Setting up Slack and Tines

1. Create a new Slack workspace.
2. Set up an alerts channel in Slack.
3. Create a new Tines story (playbook).
4. In Tines:
    - Create a webhook and copy its URL.
    - In LimaCharlie, create a new Output, select the Detection stream, and paste the webhook URL into the destination host field.

---

## Part 3 – Linking It All Together

1. In Tines, set up a new Slack credential and link your Slack workspace.
2. In your Tines story:
    - Add a "Send a Message" Slack action.
    - Add a "Send Email" action and specify the recipient.
    - Add a Page block to create a user prompt asking whether to isolate the machine.
3. Extract and pass relevant data from the webhook trigger, such as:
    - `event_time`
    - `hostname`
4. Include this data in the **Slack message**, **email**, and **page prompt**.
5. Add Yes/No triggers to the page:
    - **No path:** Condition: `{{user_prompt.body.isolate}} == false`
    - **Yes path:** Condition: `{{user_prompt.body.isolate}} == true`
6. Drag in a LimaCharlie template, and select Isolate Sensor.
7. Connect this to the Yes trigger path.
8. Create a LimaCharlie credential using your Org API Key and assign it in the Credentials tab of the story.

---
