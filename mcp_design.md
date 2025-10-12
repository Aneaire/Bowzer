# MCP Architecture Design for VibeSurf Conversion

## Overview
Convert VibeSurf's FastAPI backend + chat extension to an MCP server + simplified extension, enabling direct AI-to-browser communication without UI overhead.

## Current VibeSurf Architecture
- **Backend**: FastAPI server with routers (task, browser, agent, etc.)
- **Browser Automation**: BrowserManager → AgentBrowserSession → CDP client
- **Extension**: Chat UI (sidepanel.html) + background.js for HTTP API communication
- **Communication**: Extension ↔ HTTP API ↔ Agents ↔ Browser

## Target MCP Architecture
- **MCP Server**: TypeScript server implementing MCP protocol
- **Tools**: Browser automation tools (navigate, click, type, snapshot, etc.)
- **WebSocket Communication**: Server ↔ Extension ↔ CDP
- **Tab Assignment**: Tools accept `tabId` parameter for target specification

## MCP Tools Mapping

### Core Browser Tools
| VibeSurf Function | MCP Tool | Description |
|------------------|----------|-------------|
| `navigate_to_url` | `browser_navigate` | Navigate to URL in specified tab |
| `click_element` | `browser_click` | Click element by selector |
| `type_text` | `browser_type` | Type text into element |
| `get_page_content` | `browser_snapshot` | Capture page state/screenshot |
| `scroll_page` | `browser_scroll` | Scroll page or element |
| `wait_for_element` | `browser_wait` | Wait for element to appear |

### Advanced Tools
| VibeSurf Function | MCP Tool | Description |
|------------------|----------|-------------|
| `execute_javascript` | `browser_evaluate` | Execute JavaScript in page context |
| `get_tabs` | `browser_get_tabs` | List all open tabs |
| `switch_tab` | `browser_switch_tab` | Switch active tab |
| `close_tab` | `browser_close_tab` | Close specified tab |

## WebSocket Message Protocol

### Server → Extension Messages
```typescript
interface BrowserNavigateMessage {
  type: "browser_navigate";
  payload: { url: string; tabId?: string; };
}

interface BrowserClickMessage {
  type: "browser_click";
  payload: { selector: string; tabId?: string; };
}

// Similar for other actions...
```

### Extension → Server Messages
```typescript
interface ActionResultMessage {
  type: "action_result";
  payload: {
    actionId: string;
    success: boolean;
    data?: any;
    error?: string;
  };
}

interface TabUpdateMessage {
  type: "tab_update";
  payload: {
    tabId: string;
    url: string;
    title: string;
  };
}
```

## Extension Simplification

### Remove Components
- `sidepanel.html` - Chat interface
- `ui-manager.js` - UI event handlers
- `modal-manager.js` - Settings/history modals
- `session-manager.js` - Chat session management
- `history-manager.js` - Chat history

### Keep Components
- `background.js` - WebSocket connection, tab management
- `content.js` - Page interaction scripts
- `manifest.json` - Basic extension structure

### Add Components
- WebSocket client for MCP server communication
- Message handlers for browser actions
- Tab assignment logic

## Implementation Phases

### Phase 1: MCP Server Foundation
1. Set up TypeScript/Node.js project
2. Implement basic MCP server with WebSocket support
3. Create core browser tools (navigate, click, type, snapshot)
4. Add tab management tools

### Phase 2: Extension Adaptation
1. Remove UI components from extension
2. Implement WebSocket client in background.js
3. Add message handlers for browser actions
4. Update manifest.json permissions

### Phase 3: Integration & Testing
1. Replace FastAPI backend with MCP server
2. Test with MCP-enabled AI clients (Claude, VS Code)
3. Verify browser automation functionality
4. Performance optimization

## Key Technical Decisions

### Language & Framework
- **TypeScript/Node.js** for MCP server (matches BrowserMCP)
- **WebSocket** for real-time communication (vs HTTP polling)
- **Zod** for schema validation (BrowserMCP uses zod-to-json-schema)

### Tab Management
- **Explicit tab assignment**: Tools accept `tabId` parameter
- **Default tab**: Use currently active tab if no `tabId` specified
- **Tab validation**: Verify tab exists before actions

### Error Handling
- **Connection errors**: Graceful handling of WebSocket disconnections
- **Tab errors**: Clear messages when target tab unavailable
- **Action timeouts**: Configurable timeouts for browser actions

### Security Considerations
- **Origin validation**: Restrict WebSocket connections to localhost
- **Input sanitization**: Validate all tool parameters
- **Permission scoping**: Minimal extension permissions

## Migration Path

### Backend Migration
1. Extract browser automation logic from `BrowserManager`
2. Convert Python CDP calls to JavaScript/TypeScript equivalents
3. Map FastAPI endpoints to MCP tools
4. Implement WebSocket server

### Extension Migration
1. Strip UI-related code
2. Convert HTTP API calls to WebSocket messages
3. Simplify background script to core functionality
4. Update content scripts for direct browser interaction

### Testing Strategy
1. Unit tests for MCP tools
2. Integration tests with extension
3. End-to-end tests with AI clients
4. Performance benchmarks vs original VibeSurf

## Benefits of Conversion
- **Direct AI Integration**: No UI intermediary
- **MCP Ecosystem**: Compatible with Claude, VS Code, Windsurf
- **Simplified Architecture**: Reduced complexity
- **Better Performance**: WebSocket vs HTTP polling
- **Stealth Capabilities**: Maintain CDP-based automation