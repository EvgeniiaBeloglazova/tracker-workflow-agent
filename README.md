# Tracker Workflow Agent

> **AI-agenta for automatic workflow creation in Yandex Tracker**

This project is a prototype of an intelligent agent that transforms a natural language description of a business process into a fully configured workflow in Yandex Tracker via API.

### Who is it for?

Ideal for **B2B/SaaS solutions**: uses the public host `api.tracker.yandex.net` and OAuth authorization.

## File Structure

| File | Description |
|------|---------|
| `tracker_client.py` | Client for Tracker REST API (`/v2/workflows`, `/v3/queues/...`) |
| `workflow_spec.py` | ProcessSpec conversion and graph validation |
| `tools.py` | JSON schemas for function-tools + call dispatcher |
| `system_prompt.py` | Agent system prompt |
| `function.py` | Entry point: CLI and Cloud Functions handler |
| `requirements.txt` | Project dependencies |

## How the Agent Works

1. **get_queue** - loads existing statuses and task types of the queue
2. **Specification collection** - forms ProcessSpec from dialog, clarifies missing statuses
3. **validate_process_spec** - checks graph (connectivity, no duplicates, target statuses)
4. **Confirmation** - shows the plan to the user and waits for approval
5. **create_workflow** - creates workflow via API (`POST /v2/workflows`)
6. **get_workflow** - verifies the result
7. *(optional)* - configures automations (`create_trigger`, `create_autoaction`)

## Quick Start

### Requirements
- Python 3.8+
- Access to Yandex Tracker
- API keys (see below)

### Installation

```bash
# Clone the repository
git clone https://github.com/EvgeniiaBeloglazova/tracker-workflow-agent.git
cd tracker-workflow-agent

# Install dependencies
pip install -r requirements.txt
```

### Configuration

Set environment variables:

```bash
export YC_API_KEY=...              # OAuth token for service account (Yandex Cloud)
export YC_FOLDER_ID=...            # Folder ID in Yandex Cloud
export TRACKER_TOKEN=...           # OAuth token for Tracker admin
export TRACKER_ORG_ID=...          # Organization ID in Tracker
# export TRACKER_CLOUD_ORG=true    # Uncomment for Yandex Cloud organizations
```

### Run

```bash
python function.py
```

## Important Limitations and Risks

### Access Rights
Creating/modifying workflows is protected by the `Secured.Role.SUPPORT` role, which means:
- **Organization administrator**, or
- **Support group member**

**Warning:** The agent token must belong to an organization administrator.

### Undocumented API
- `/v2/workflows` endpoints are **not publicly documented**
- Contract may change without notice
- Contract is verified against Tracker source code (rev. r19858620)
- **Before production:** coordinate usage with the Tracker team

### Architectural Limitations
- **Stateless handler** - each request creates a new thread
  - For full-fledged dialogs, save `thread_id` between calls
- **Model version** - uses `yandexgpt` (rc)
  - Test function calling quality on real multi-step graphs

### Components
Implementation relies on:
- `WorkflowInputParameters`, `StepInputParameters`, `ActionInputParameters`
- `WorkflowProto`
- Reference implementations `DEFAULT_WORKFLOW_DATA` and integration tests

## License

MIT License - see the [LICENSE](LICENSE) file for details.
