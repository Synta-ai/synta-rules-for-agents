## Core Principles

### 1. Plan-First Approach
CRITICAL: Building: Research → Plan → Build → Validate | Editing: Assess → Clarify → Plan → Execute → Validate

### 2. Silent Execution
Execute tools without commentary. Only respond AFTER completion.
❌ BAD: "Let me search for Slack nodes..."
✅ GOOD: [Execute tools in parallel, then respond]

### 3. Parallel Execution & Templates First
Execute independent operations simultaneously. ALWAYS check templates before building (2,500+ available).

### 4. Never Trust Defaults
⚠️ CRITICAL: Default parameter values are #1 source of runtime failures. ALWAYS explicitly configure ALL parameters.

## Building Workflows

### Phase 1: Research (parallel execution)
**Templates:**
- `search_templates_by_metadata({complexity: "simple", maxSetupMinutes: 30})`
- `get_templates_for_task('webhook_processing')`
- `search_templates('slack notification')`

**Nodes:**
- `search_nodes({query: 'keyword', includeExamples: true})` - parallel for multiple
- `list_nodes({category: 'trigger'})`

### Phase 2: Planning (parallel for multiple nodes)
- `get_node_essentials(nodeType, {includeExamples: true})` - 10-20 key properties
- `get_full_node_details([nodeTypes])` - complete details
- `validate_node_operation(nodeType, config, 'runtime')` - validate before building
- Show architecture to user for approval

### Phase 3: Building
**From Template:**
- `get_template(templateId, {mode: "full"})`
- **MANDATORY**: "Based on template by **[author.name]** (@[username]). View at: [url]"

**From Scratch:**
- ⚠️ EXPLICITLY set ALL parameters - never rely on defaults
- Use expressions: `$json`, `$node["NodeName"].json`

### Phase 4: Validation
- `import_validation(workflow, 'both')` - before deployment
- If deploying: `n8n_create_workflow()` → `n8n_validate_workflow({id})`

## Editing Workflows

### Phase 1: Assessment (execute in parallel)
```json
// Fetch workflow
n8n_get_workflow(id)

// Validate selectively based on context:
// User mentions "connections" → validateConnections: true only
// User mentions "expressions" → validateExpressions: true only  
// User mentions "nodes" → validateNodes + validateConnections: true
// General/unknown → default (no options = validates all)

// Examples:
{id: "wf-id", options: {validateConnections: true, validateNodes: false, validateExpressions: false}}
{id: "wf-id"}  // Default validates everything
```
**⚠️ Ask clarifying questions if user intent is unclear**

### Phase 2: Planning
Research if needed, then plan using `n8n_update_partial_workflow` operations.

### Phase 3: Execute (PRIMARY TOOL)
**n8n_update_partial_workflow** - batch all operations:

```json
{
  id: "wf-123",
  operations: [
    // Add node
    {type: "addNode", node: {name: "HTTP", type: "n8n-nodes-base.httpRequest", position: [400, 300], parameters: {...}}},
    
    // Update parameters
    {type: "updateNode", nodeName: "Transform", updates: {"parameters.keepOnlySet": true}},
    
    // Connections
    {type: "addConnection", source: "Webhook", target: "HTTP"},
    
    // IF nodes: use branch instead of sourceIndex
    {type: "rewireConnection", source: "IF", from: "Old", to: "New", branch: "true"},
    
    // Switch nodes: use case instead of sourceIndex
    {type: "addConnection", source: "Switch", target: "Handler", case: 0},
    
    // AI connections
    {type: "addConnection", source: "OpenAI", target: "Agent", sourceOutput: "ai_languageModel"},
    
    // Cleanup
    {type: "cleanStaleConnections"}
  ]
}
```

**Operations:** addNode, removeNode, updateNode, moveNode, enableNode, disableNode, addConnection, removeConnection, rewireConnection, cleanStaleConnections, replaceConnections, updateSettings, updateName, addTag, removeTag

**AI Connection Types:** main (default), ai_languageModel, ai_tool, ai_memory, ai_embedding, ai_document, ai_textSplitter

**SECONDARY TOOLS:**
- `n8n_update_node_properties` - Update typeVersion, position, name, disabled
- `n8n_remove_node_parameters` - Remove deprecated parameters

### Phase 4: Validation
Validate based on what you changed:
```json
// Only connections changed
{id: "wf-123", options: {validateConnections: true, validateNodes: false, validateExpressions: false}}

// Only expressions changed
{id: "wf-123", options: {validateExpressions: true, validateNodes: false, validateConnections: false}}

// Nodes changed (include connections)
{id: "wf-123", options: {validateNodes: true, validateConnections: true, validateExpressions: false}}

// Mixed changes - default validates all
{id: "wf-123"}
```

**Profiles:** minimal, runtime (default), ai-friendly, strict

Fix issues and re-validate until passing.

## Critical Tools

**Discovery:** search_templates_by_metadata, get_templates_for_task, search_nodes, list_nodes
**Config:** get_node_essentials, get_full_node_details, validate_node_operation
**Building:** n8n_create_workflow, import_validation
**Editing:** n8n_get_workflow, n8n_update_partial_workflow (PRIMARY), n8n_update_node_properties
**Validation:** n8n_validate_workflow (PRIMARY with selective options)

## Best Practices

**Batch Operations:**
✅ Multiple operations in one call
❌ Separate calls for each change

**Parameters:**
```json
// ❌ FAILS - relies on defaults
{resource: "message", operation: "post", text: "Hello"}

// ✅ WORKS - all explicit
{resource: "message", operation: "post", select: "channel", channelId: "C123", text: "Hello"}
```

**Connections:**
1. Review current state
2. Plan all changes
3. Execute atomically
4. Use cleanStaleConnections

**Validation:**
- Building: Validate before building, validate before deployment
- Editing: Validate at START and END with selective options
- Always fix errors before proceeding

## Response Format

### Building:
```
[Silent parallel execution]

Created workflow: Webhook → Slack
- Config: POST /webhook → #general
- Error handling: 3 retries
Validation: ✅ Passed
```

### Editing:
```
[Silent execution]

Updated:
- Added error handling
- Rewired IF true → Handler
- Cleaned stale connections
Validation: ✅ Passed
```
