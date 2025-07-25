---
description: Understand the technical structure of prompts in Roo Code. Learn how messages are constructed, system prompts work, and optimize your interactions.
keywords:
  - prompt structure
  - system prompt
  - message flow
  - technical documentation
  - LLM communication
image: /img/social-share.jpg
---

# Prompt Structure

This page explains the technical structure of prompts in Roo Code - how messages are constructed and sent to the Large Language Model (LLM).

---

## Core Message Types

Roo Code uses three primary message types when communicating with LLMs:

- **System Prompt**: The initial instructions that define Roo's capabilities, persona, and operational rules
- **User Messages**: Content sent by you (the user) to Roo
- **Assistant Messages**: Responses generated by the LLM based on your requests

At the API level, there's also a fourth message role:

- **Tool Messages**: Results returned from tool executions, sent back to the LLM as input

Understanding these message types helps you work more effectively with Roo and can be valuable for troubleshooting or advanced customization.

---

## System Prompt

The system prompt is the foundation of Roo's behavior. It contains:

- **Role Definition**: The core persona instructions based on the selected mode (Code, Ask, Debug, etc.)
- **Tool Descriptions**: Detailed information about available tools, including parameters and examples
- **Tool Use Guidelines**: Rules for how tools should be used (sequential execution, waiting for results)
- **Capabilities**: Description of what Roo can do in the current environment
- **Available Modes**: List of all available modes and their descriptions
- **Operational Rules**: Critical guidelines for handling files, project structure, and user interaction
- **System Information**: Details about your environment (OS, shell, working directory)
- **Custom Instructions**: Your global and mode-specific customizations

The system prompt is generated dynamically each time you interact with Roo, adapting to your current mode, available tools, and custom settings.

### Custom System Prompts

Advanced users can create custom system prompts for specific modes by placing a `.roo/system-prompt-<mode_slug>` file in their workspace. When present, Roo uses this file instead of generating the standard system prompt sections, allowing for complete customization of Roo's behavior in that mode.

---

## User Messages

User messages contain your direct inputs to Roo, plus additional contextual information:

- **Your Query**: The text you type in the chat interface
- **Images**: Any images you include in your message (for supported models)
- **Environment Details**: Automatically appended information about your workspace state:
  - Open files/tabs
  - Cursor position
  - Active terminals with output
  - Recently modified files
  - Current time
  - Token/cost information
  - Current mode
  - File listing (on initial connection)

This automatic context enrichment helps Roo understand your workspace without requiring you to explicitly describe it.

---

## Assistant Messages

Assistant messages are the LLM's responses, which may include:

- **Text Responses**: Direct answers to your queries
- **Thinking**: Internal reasoning process (visible when enabled)
- **Tool Calls**: Requests to use specific tools like reading files or executing commands

Note that while assistant messages contain tool calls, the results of those tools are sent back to the LLM in separate tool messages, not as part of the assistant message itself.

---

## Message Flow

Here's how these components work together:

1. **Initial Setup**: Roo generates the system prompt based on your selected mode and configuration
2. **User Input**: You send a message, which is enriched with environment details
3. **LLM Processing**: The LLM receives all previous messages plus your new input
4. **Assistant Response**: The LLM generates a response, potentially using tools
5. **Tool Execution**: If the LLM requests a tool, Roo executes it and provides the result
6. **Conversation History**: All messages are maintained in a structured history for context

---

## Technical Implementation

Internally, Roo's prompt construction is handled by several components:

- **System Prompt Generation**: The `SYSTEM_PROMPT` function in `src/core/prompts/system.ts` assembles the complete system prompt
- **Section Generators**: Specialized functions create each section of the system prompt
- **Message Transformation**: Provider-specific transformers convert Roo's internal message format to the format required by each LLM API
- **Custom Prompt Loading**: The `loadSystemPromptFile` function checks for and processes custom system prompt files

---

## Support Prompts

Alongside the main chat flow, Roo uses specialized templates for specific code actions:

- **Code Action Prompts**: For commands like "Explain", "Fix", "Improve", or "Add to Context"
- **Template-Based**: Generated from templates in `src/shared/support-prompt.ts`
- **Independent Context**: Often operates without the main chat history
- **Task-Specific Format**: Optimized for the specific code task being performed

These support prompts work outside the normal conversation flow to provide focused assistance for specific coding tasks.

---

## Optimizing Your Interactions

Understanding this structure can help you:

- **Write Better Prompts**: Knowing what context Roo already has helps you avoid redundant information
- **Troubleshoot Issues**: Understanding message flow helps identify where problems might occur
- **Create Custom Modes**: With knowledge of the system prompt structure, you can create more effective custom modes
- **Use Custom System Prompts**: Advanced users can create entirely custom system prompts for specialized use cases

This technical foundation powers all of Roo's capabilities, enabling it to understand your requests and effectively utilize available tools to complete tasks.