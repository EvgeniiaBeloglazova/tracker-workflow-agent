# Tracker Workflow Agent

> **AI-agent for automatic workflow creation in Yandex Tracker**

This is an OpenCode skill that helps configure business processes in Yandex Tracker by natural language description. The agent transforms informal descriptions into proper Tracker configuration through API calls.

### Who is it for?

Ideal for **B2B/SaaS solutions**: uses the public host `api.tracker.yandex.net` and OAuth authorization.

## How the Agent Works

1. **get_queue** - loads existing statuses and task types of the queue
2. **Specification collection** - forms ProcessSpec from dialog, clarifies missing statuses
3. **validate_process_spec** - checks graph (connectivity, no duplicates, target statuses)
4. **Confirmation** - shows the plan to the user and waits for approval
5. **create_workflow** - creates workflow via API (`POST /v2/workflows`)
6. **get_workflow** - verifies the result
7. *(optional)* - configures automations (`create_trigger`, `create_autoaction`)

## Quick Start

This is an OpenCode skill. To use it:

1. Ensure you have OpenCode installed
2. Add this skill to your OpenCode configuration
3. Describe your Yandex Tracker workflow in natural language
4. The agent will configure it automatically via Tracker API

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
