<details>
<summary>Relevant source files</summary>

The following file was used as context for generating this wiki page:

- [README.md](https://github.com/aanickode/magnitude/blob/main/README.md)
</details>

# Introduction

Magnitude is a vision AI-powered browser automation tool that enables users to control their browser with natural language commands. It provides a suite of capabilities, including navigation, interaction, data extraction, and verification, making it a versatile solution for automating tasks on the web, integrating between applications without APIs, extracting data, and testing web applications.

## Overview

Magnitude's core functionality revolves around an intelligent agent that can understand and execute high-level tasks or granular actions within a web browser. The agent leverages vision AI to perceive and comprehend user interfaces, enabling it to navigate, interact with elements, and extract structured data. Additionally, Magnitude includes a built-in test runner with powerful visual assertions, allowing for efficient testing of web applications.

## Key Features

### Navigation

Magnitude's agent can understand and navigate through any interface by visually interpreting the user interface elements. This capability enables seamless navigation across various web applications without relying on specific DOM structures or APIs.

### Interaction

The agent can execute precise actions within the browser, such as mouse clicks, keyboard inputs, and drag-and-drop operations. This allows for automated interaction with web applications, mimicking human behavior.

### Data Extraction

Magnitude can intelligently extract useful structured data from web pages based on provided schemas or patterns. This feature is particularly valuable for scraping data from websites or integrating data across different applications.

### Verification and Testing

Magnitude includes a built-in test runner with powerful visual assertions, enabling developers to create and run automated tests for their web applications. The visual assertions allow for accurate verification of UI elements, ensuring the correct behavior and appearance of the application.

## Architecture and Components

Magnitude's architecture is centered around a vision-first approach, where a visually grounded large language model (LLM) specifies pixel coordinates for interactions within the browser. This architecture provides true generalization independent of DOM structure, making it future-proof for various applications, including desktop apps and virtual machines.

The key components of Magnitude's architecture include:

1. **Vision AI**: Magnitude utilizes state-of-the-art vision AI models to perceive and understand user interfaces. These models are responsible for interpreting visual elements and providing the necessary context for the agent to plan and execute actions.

2. **Intelligent Agent**: The intelligent agent is the core component that receives natural language commands and translates them into a series of actions to be executed within the browser. It leverages the vision AI models to understand the current state of the user interface and plan the appropriate actions.

3. **Browser Automation**: Magnitude integrates with popular browser automation tools, such as Puppeteer or Playwright, to execute the planned actions within the browser. This includes actions like mouse clicks, keyboard inputs, and navigation.

4. **Test Runner**: The built-in test runner allows developers to create and run automated tests for their web applications. It provides a framework for defining test cases, executing them, and verifying the expected behavior using visual assertions.

Sources: [README.md](https://github.com/aanickode/magnitude/blob/main/README.md)

## Getting Started

Magnitude provides two primary ways to get started: running browser automations and using the test runner.

### Running Browser Automations

To run your first browser automation with Magnitude, you can use the `create-magnitude-app` command-line tool:

```bash
npx create-magnitude-app
```

This command will create a new project and guide you through the setup process for Magnitude. It will also generate an example script that you can run immediately to experience Magnitude's capabilities.

### Using the Test Runner

If you have an existing web application and want to integrate Magnitude's test runner, you can install it using the following command:

```bash
npm i --save-dev magnitude-test && npx magnitude init
```

This command will install the necessary dependencies and create a basic tests directory (`tests/magnitude`) with the following files:

- `magnitude.config.ts`: Magnitude test configuration file
- `example.mag.ts`: An example test file

For more information on running tests and integrating Magnitude into your CI/CD pipeline, refer to the [official documentation](https://docs.magnitude.run/core-concepts/running-tests).

Sources: [README.md](https://github.com/aanickode/magnitude/blob/main/README.md)

## Advantages of Magnitude

Magnitude addresses two key problems faced by traditional browser agents:

1. **Problem #1**: Most browser agents draw numbered boxes around page elements, which does not generalize well due to the complexity of modern websites.

   **Solution: Vision-first architecture**
   - Magnitude's visually grounded LLM specifies pixel coordinates for interactions, providing true generalization independent of DOM structure.
   - This vision-first approach makes Magnitude's architecture future-proof for various applications, including desktop apps and virtual machines.

2. **Problem #2**: Many browser agents follow the "high-level prompt + tools = work until done" approach, which works well for demonstrations but may not be suitable for production environments.

   **Solution: Controllable and repeatable automation**
   - Magnitude offers flexible abstraction levels, allowing users to work with granular actions or higher-level flows.
   - Users can define custom actions and prompts at the agent and action levels, providing greater control and customization.
   - Magnitude includes a native caching system (in progress) that enables deterministic runs, ensuring consistent and repeatable automation.

Sources: [README.md](https://github.com/aanickode/magnitude/blob/main/README.md)

## Large Language Model (LLM) Configuration

Magnitude requires a large visually grounded model for optimal performance. The recommended model is Claude Sonnet 4, but Magnitude is also compatible with Qwen-2.5VL 72B. For more information on configuring the LLM, refer to the [official documentation](https://docs.magnitude.run/customizing/llm-configuration).

Sources: [README.md](https://github.com/aanickode/magnitude/blob/main/README.md)

## Additional Resources

The official Magnitude documentation (https://docs.magnitude.run) provides more detailed information on building Magnitude automations, creating test cases, and leveraging the tool's capabilities effectively.

Sources: [README.md](https://github.com/aanickode/magnitude/blob/main/README.md)

## Community and Support

Magnitude offers several channels for community engagement and support:

- **Discord Community**: Join the [Magnitude Discord community](https://discord.gg/VcdpMh9tTy) for help, suggestions, and discussions with other users and the development team.
- **Follow the Founders**: Follow [Tom Greenwald](https://x.com/tgrnwld) and [Anders Sørkål](https://x.com/ndrsrkl) on X (formerly Twitter) for updates and announcements.

For enterprise users seeking additional features or support, you can reach out to the Magnitude team at founders@magnitude.run or schedule a call [here](https://cal.com/tom-greenwald/30min) to discuss your needs.

Sources: [README.md](https://github.com/aanickode/magnitude/blob/main/README.md)

In summary, Magnitude is a powerful and innovative browser automation tool that leverages vision AI to enable natural language control of web browsers. With its vision-first architecture, flexible abstraction levels, and built-in test runner, Magnitude provides a versatile solution for automating tasks, extracting data, and testing web applications.