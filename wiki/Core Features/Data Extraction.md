<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [packages/magnitude-extract/src/index.ts](https://github.com/aanickode/magnitude/blob/main/packages/magnitude-extract/src/index.ts)
- [docs/core-concepts/data-extraction.mdx](https://github.com/aanickode/magnitude/blob/main/docs/core-concepts/data-extraction.mdx)

</details>

# Data Extraction

## Introduction

Data Extraction is a core feature of the Magnitude project that enables intelligent extraction of structured data from web pages. It allows users to provide instructions and a Zod schema, and the system will analyze the browser content (including the DOM and rendered page) to extract data conforming to the specified schema.

This feature is particularly useful for automating data collection tasks, integrating data into other web applications, or triggering additional workflows based on the extracted data. It provides a flexible and powerful way to interact with and process web content programmatically.

## Data Extraction Process

The data extraction process in Magnitude involves the following steps:

1. **Providing Instructions and Schema**: The user passes instructions (a natural language description of the data to extract) and a Zod schema (defining the structure of the expected data) to the `extract()` function.

2. **Browser Content Analysis**: Magnitude analyzes the current browser content, including:
   - A screenshot of the browser window
   - A simplified version of the DOM content

3. **Data Extraction**: Based on the provided instructions and schema, Magnitude intelligently extracts data from the browser content, conforming to the specified Zod schema.

4. **Data Handling**: The extracted data can be saved to a file system, uploaded to a database, or passed to other processes or workflows within the application.

## Data Chaining and Control Flow

The extracted data can be used in various ways, including:

1. **Data Chaining**: The extracted data can be integrated into other web applications or used to trigger additional agent workflows using the `act()` function.

2. **Control Flow**: Standard control flow statements (e.g., `if` statements, loops) can be used based on the extracted data to make decisions or perform specific actions.

### Example: Extracting and Processing Tasks

```ts
import z from 'zod';

// Extract tasks from the current page
const tasks = await agent.extract(
    'list all tasks',
    z.array(z.object({
        title: z.string(),
        status: z.enum(['todo', 'inprogress', 'done']),
        description: z.string(),
        priority: z.enum(['low', 'medium', 'high', 'urgent']),
        labels: z.array(z.string()),
        assignee: z.string()
    }))
);

// Filter urgent tasks that are still todo
const urgentTasks = tasks.filter(
    task => task.priority === 'urgent' && task.status === 'todo'
);

// If there are more than 10 urgent tasks, create a new task
if (urgentTasks.length > 10) {
    await agent.act('create a new task', {
        title: 'get some of these urgent tasks done!',
        description: urgentTasks.map(task => task.title).join(', ')
    });
}
```

Sources: [docs/core-concepts/data-extraction.mdx](https://github.com/aanickode/magnitude/blob/main/docs/core-concepts/data-extraction.mdx)

## Supported Data Structures

Magnitude supports extracting data into any structure that can be defined using the Zod schema library. This includes:

- Primitive types (e.g., strings, numbers, booleans)
- Arrays
- Objects with nested structures
- Enumerations (enums)
- And more complex data structures built from these basic types

```ts
// Example of extracting a simple number
const numInProgress = await agent.extract(
    'how many items are "In Progress"?',
    z.number()
);
```

Sources: [docs/core-concepts/data-extraction.mdx](https://github.com/aanickode/magnitude/blob/main/docs/core-concepts/data-extraction.mdx)

For more information about the Zod schema library and the types of data structures it supports, refer to the official Zod documentation: https://zod.dev/

## Conclusion

The Data Extraction feature in Magnitude provides a powerful and flexible way to extract structured data from web pages. By combining natural language instructions with Zod schemas, users can intelligently collect and process data from browser content. The extracted data can then be integrated into other applications, used to trigger additional workflows, or processed using standard control flow statements. This feature streamlines data collection tasks and enables seamless integration of web data into various workflows and applications.