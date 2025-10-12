# VibeSurf to MCP Conversion

This repository contains the conversion of VibeSurf (a browser automation extension + FastAPI backend) to a modern MCP (Model Context Protocol) server architecture.

## 🎯 Project Overview

**Original VibeSurf**: Chat extension + FastAPI backend for browser automation
**New Architecture**: MCP server + simplified Chrome extension for direct AI-browser communication

## 📁 Repository Structure

```
├── browsermcp/           # MCP server implementation (TypeScript/Node.js)
│   ├── src/
│   │   ├── config/       # Configuration files
│   │   ├── messaging/    # WebSocket communication
│   │   ├── tools/        # MCP tool definitions
│   │   ├── types/        # TypeScript type definitions
│   │   └── utils/        # Utility functions
│   ├── dist/             # Built server (generated)
│   └── package.json
├── vibesurf/             # Original VibeSurf codebase (Python)
│   └── vibe_surf/
│       ├── backend/      # FastAPI backend
│       ├── browser/      # Browser automation
│       ├── chrome_extension/  # Simplified extension
│       └── agents/       # AI agents
├── .config/              # OpenCode configuration
└── docs/                 # Documentation
```

## 🚀 Quick Start

### 1. MCP Server Setup
```bash
cd browsermcp
npm install
npm run build
```

### 2. Chrome Extension
- Load `vibesurf/vibe_surf/chrome_extension/` as unpacked extension in Chrome developer mode
- The extension connects to the MCP server via WebSocket

### 3. OpenCode Configuration
The `.config/opencode/opencode.json` is already configured to use the MCP server.

## 🛠️ Available Tools

The MCP server provides these browser automation tools:

- **Navigation**: `navigate`, `go_back`, `go_forward`
- **Interaction**: `click`, `type`, `hover`, `select_option`
- **Observation**: `snapshot`, `screenshot`
- **Utilities**: `press_key`, `wait`, `get_console_logs`

## 🔧 Technical Details

### MCP Server
- **Framework**: TypeScript + Node.js
- **Protocol**: Model Context Protocol (MCP)
- **Communication**: WebSocket to Chrome extension
- **Tools**: Zod schemas for type safety

### Chrome Extension
- **Architecture**: Simplified from original VibeSurf
- **Permissions**: Minimal (activeTab, scripting, tabs)
- **Communication**: WebSocket client to MCP server

### Integration
- **OpenCode**: Configured in `.config/opencode/opencode.json`
- **Claude Code**: Can be configured with `mcpServers` in `claude.json`

## 📚 Documentation

- `idea.md` - Original conversion concept
- `mcp_design.md` - Technical design decisions
- `browsermcp/README.md` - MCP server documentation

## 🔄 Conversion Status

✅ **Completed:**
- MCP server with browser automation tools
- WebSocket communication between server and extension
- OpenCode integration
- Build system and TypeScript compilation

🔄 **In Progress:**
- Tab-specific session management
- Enhanced error handling

## 🤝 Contributing

This is a conversion project showing how to modernize browser automation from traditional APIs to the MCP standard.

## 📄 License

See individual directories for licensing information.