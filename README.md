# Synta Agent Configuration Rules
Open-source configuration files for Synta to turn any AI assistant into an N8N expert.

## What This Does
This repository provides pre-configured rules and prompts that teach AI coding agents how to effectively use Synta MCP for N8N workflow automation.

## Quick Setup

### 1. Copy Configuration

```bash
# For Claude Desktop
cp .claude/agents/synta-oracle.md ~/.claude/agents/

# For Claude Code
cp .claude-code/prompts/synta-oracle.md ~/.claude-code/prompts/

# For Cursor  
cp .cursor/rules/synta-oracle.mdc ~/.cursor/rules/

# For other agents, follow similar pattern
```

### 2. Enable Synta MCP
Ensure your agent has access to Synta's MCP tools. See [synta.io](https://synta.io) for setup instructions.

### 3. Start Building N8N Workflows
Your agent now becomes an N8N workflow specialist that can:

- Search and discover 1000+ N8N nodes with intelligent search
- Build workflows with expert-level node configurations
- Validate and debug workflows with real-time error checking
- Access 10,000+ curated workflow templates
- Manage workflows directly in your N8N instance
- Execute and monitor workflow runs

## 🤝 Contributing
- Add configurations for new AI agents
- Improve existing rule sets
- Share workflow patterns and examples

## 🔗 Links
- **Synta Platform**: [synta.io](https://synta.io)
- **Documentation**: [mcp-docs.synta.io](https://mcp-docs.synta.io)
- **Issues**: Report bugs or request features

---

*Making N8N workflow automation accessible to AI agents*