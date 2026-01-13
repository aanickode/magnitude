<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [packages/magnitude-core/src/index.ts](https://github.com/aanickode/magnitude/blob/main/packages/magnitude-core/src/index.ts)
- [packages/magnitude-core/src/agent/browserAgent.ts](https://github.com/aanickode/magnitude/blob/main/packages/magnitude-core/src/agent/browserAgent.ts)
</details>

# Architecture Overview

## Introduction

The Magnitude project is a framework for building intelligent agents that can interact with web browsers and perform various tasks. The core of the project is the `BrowserAgent` class, which serves as the main entry point for creating and managing browser-based agents. This class extends the base `Agent` class and integrates with the `BrowserConnector` to facilitate communication with the browser environment.

The `BrowserAgent` class provides a high-level interface for navigating to web pages, extracting data from the rendered content, and potentially performing other actions within the browser context. It leverages the Playwright library for browser automation and the Zod library for data validation and extraction. The project also includes utilities for rendering accessibility trees, partitioning HTML content, and serializing data to Markdown format.

## BrowserAgent Class

The `BrowserAgent` class is the central component responsible for managing the browser agent's lifecycle and providing an interface for interacting with the browser environment.

### Constructor

The `BrowserAgent` constructor takes an object with two optional properties: `agentOptions` and `browserOptions`. These options are used to configure the behavior of the agent and the underlying browser connector, respectively.

```typescript
constructor({ agentOptions, browserOptions }: { agentOptions?: Partial<AgentOptions>, browserOptions?: BrowserConnectorOptions }) {
    super({
        ...agentOptions,
        connectors: [new BrowserConnector(browserOptions || {}), ...(agentOptions?.connectors ?? [])]
    });
}
```

The `agentOptions` object is passed to the base `Agent` class constructor, along with a new instance of the `BrowserConnector` and any additional connectors specified in the `agentOptions.connectors` array.

Sources: [packages/magnitude-core/src/agent/browserAgent.ts:36-44]()

### Navigation

The `nav` method allows the agent to navigate to a specified URL within the browser context.

```typescript
async nav(url: string): Promise<void> {
    this.browserAgentEvents.emit('nav', url);
    await this.require(BrowserConnector).getHarness().navigate(url);
}
```

It emits a `'nav'` event with the target URL and then calls the `navigate` method of the `BrowserConnector` harness to perform the actual navigation.

Sources: [packages/magnitude-core/src/agent/browserAgent.ts:53-57]()

### Data Extraction

The `extract` method is a key feature of the `BrowserAgent` class. It allows the agent to extract data from the rendered web page content based on a provided Zod schema.

```typescript
async extract<T extends Schema>(instructions: string, schema: T): Promise<z.infer<T>> {
    this.browserAgentEvents.emit('extractStarted', instructions, schema);
    const htmlContent = await getFullPageContent(this.page);
    // ... (omitted for brevity)
    const data = await this.models.extract(instructions, schema, screenshot, markdown);
    this.browserAgentEvents.emit('extractDone', instructions, data);
    return data;
}
```

The method performs the following steps:

1. Emits an `'extractStarted'` event with the provided instructions and schema.
2. Retrieves the full page content, including the content of any iframes, using the `getFullPageContent` helper function.
3. Partitions the HTML content using the `partitionHtml` function from the `magnitude-extract` library, with various options for extracting images, forms, links, and more.
4. Serializes the partitioned HTML content to Markdown format using the `serializeToMarkdown` function.
5. Captures a screenshot of the current page using the `BrowserConnector` harness.
6. Calls the `extract` method of the agent's models with the provided instructions, schema, screenshot, and Markdown content to perform the actual data extraction.
7. Emits an `'extractDone'` event with the instructions and extracted data.
8. Returns the extracted data.

Sources: [packages/magnitude-core/src/agent/browserAgent.ts:60-99]()

### Event Emitter

The `BrowserAgent` class includes an `EventEmitter` instance called `browserAgentEvents` that emits events related to navigation and data extraction processes.

```typescript
public readonly browserAgentEvents: EventEmitter<BrowserAgentEvents> = new EventEmitter();
```

The `BrowserAgentEvents` interface defines the following events:

- `'nav'`: Emitted when the agent navigates to a new URL, providing the target URL as the event payload.
- `'extractStarted'`: Emitted when the data extraction process starts, providing the instructions and the Zod schema as the event payload.
- `'extractDone'`: Emitted when the data extraction process completes, providing the instructions and the extracted data as the event payload.

Sources: [packages/magnitude-core/src/agent/browserAgent.ts:28-32]()

### Helper Functions

The `BrowserAgent` class relies on several helper functions for retrieving the full page content, including the content of iframes, and for partitioning and serializing the HTML content.

#### `getFullPageContent`

```typescript
async function getFullPageContent(page: Page): Promise<string> {
    // ... (omitted for brevity)
}
```

This function retrieves the full page content, including the content of any iframes, by iterating over the iframe elements, retrieving their content frames, and replacing the iframe elements with their respective content.

Sources: [packages/magnitude-core/src/agent/browserAgent.ts:100-137]()

## Startup and Configuration

The `startBrowserAgent` function is a helper function that simplifies the process of creating and starting a `BrowserAgent` instance.

```typescript
export async function startBrowserAgent(
    options?: AgentOptions & BrowserConnectorOptions & { narrate?: boolean }
): Promise<BrowserAgent> {
    const { agentOptions, browserOptions } = buildDefaultBrowserAgentOptions({ agentOptions: options ?? {}, browserOptions: options ?? {} });

    const agent = new BrowserAgent({
        agentOptions: agentOptions,
        browserOptions: browserOptions,
    });

    if (options?.narrate || process.env.MAGNITUDE_NARRATE) {
        narrateBrowserAgent(agent);
    }

    await agent.start();
    return agent;
}
```

This function takes an optional `options` object that can include `AgentOptions`, `BrowserConnectorOptions`, and a `narrate` flag. It then calls the `buildDefaultBrowserAgentOptions` function to construct the default agent and browser options based on the provided options or their defaults.

A new `BrowserAgent` instance is created with the constructed options, and if the `narrate` flag is set or the `MAGNITUDE_NARRATE` environment variable is present, the `narrateBrowserAgent` function is called to enable narration for the agent.

Finally, the `start` method is called on the agent instance to initialize it, and the agent is returned.

Sources: [packages/magnitude-core/src/agent/browserAgent.ts:18-41]()

## Exports and Imports

The `index.ts` file serves as the main entry point for the Magnitude project, exporting various components and types for use in other parts of the application or by external consumers.

### Exports

The following items are exported from the `index.ts` file:

- `Agent`: The base `Agent` class.
- `createAction`: A function for creating actions.
- `BrowserAgent`: The `BrowserAgent` class.
- `startBrowserAgent`: The helper function for creating and starting a `BrowserAgent` instance.
- Various other classes, types, and utilities related to agents, memory, web harness, actions, connectors, browser providers, errors, and telemetry.
- `buildDefaultBrowserAgentOptions`: A function for building default options for the `BrowserAgent`.
- `logger`: The logger instance.

Sources: [packages/magnitude-core/src/index.ts:4-25]()

### Imports

The `index.ts` file imports the following dependencies:

- `setLogLevel` from `@/ai/baml_client/config`: Used to set the log level.

Sources: [packages/magnitude-core/src/index.ts:2-3]()

## Conclusion

The Magnitude project provides a comprehensive framework for building intelligent agents that can interact with web browsers. The `BrowserAgent` class serves as the central component, offering functionality for navigating to web pages, extracting data based on Zod schemas, and potentially performing other actions within the browser context. The project leverages various libraries and utilities for browser automation, data validation, HTML partitioning, and content serialization. The `startBrowserAgent` helper function simplifies the process of creating and configuring a `BrowserAgent` instance, while the `index.ts` file acts as the main entry point, exporting the core components and types for use throughout the application or by external consumers.