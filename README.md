# Automated Incident Response & Isolation Playbook 🛡️

<p align="center">
  <a href="SOAR-EDR.png">
    <img src="SOAR-EDR.png" alt="Project Diagram" width="500">
  </a>
</p>

## Objective
To design and implement a security automation playbook that streamlines incident triage, generates multi-channel alerts, and executes conditional endpoint isolation upon detecting credential dumping attempts.

## Tech Stack
* **EDR:** LimaCharlie (Endpoint Detection & Response)
* **SOAR:** Tines (Orchestration & Automation)
* **Infrastructure:** Vultr (Cloud), Windows Server 2022 (Endpoint)
* **Communication:** Slack, Email, Draw.io

## Implementation Steps

### 1. Workflow Design
* Created a logical flow diagram in Draw.io.
* **Logic:** Detection > Alerting > Human Analysis > Conditional Action (Isolate vs. Investigate).

### 2. Infrastructure & Sensor Deployment
* Deployed a Windows Server Virtual Machine via Vultr.
* Installed LimaCharlie EDR sensor using PowerShell.
* Verified real-time telemetry (processes, network connections, file system).

### 3. Threat Simulation & Detection
* **Tool Used:** LaZagne (Credential dumping utility).
* **Configuration:** Created a custom Detection & Response (D&R) rule in LimaCharlie to identify the specific process signature of LaZagne.
* **Validation:** Confirmed that execution triggered a high-severity alert in the EDR console.

### 4. Playbook Configuration (Tines)
* **Ingestion:** Configured a Webhook in Tines to receive JSON detection data from LimaCharlie.
* **Alerting:** Established API connections to send formatted alerts to a Slack channel and an Email address containing the sensor ID and threat details.
* **User Prompt (Human-in-the-Loop):** Implemented a Tines "Page" element to present the analyst with a decision interaction: *"Does the user want to isolate the machine? (Yes/No)"*.

### 5. Automated Response
* **Conditional Trigger:**
    * **Path A (True):** Tines utilizes stored LimaCharlie API credentials to trigger the `isolation` sensor command.
    * **Path B (False):** Workflow ends with a log entry for manual investigation.
* **Feedback Loop:** The playbook queries the endpoint status post-action and posts a confirmation message to Slack: *"The computer [Name] has been isolated."*
