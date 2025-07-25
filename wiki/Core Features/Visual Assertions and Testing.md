<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [packages/magnitude-test/src/index.ts](https://github.com/aanickode/magnitude/blob/main/packages/magnitude-test/src/index.ts)
- [docs/testing/building-test-cases.mdx](https://github.com/aanickode/magnitude/blob/main/docs/testing/building-test-cases.mdx)

</details>

# Visual Assertions and Testing

## Introduction

Visual Assertions and Testing is a core feature of the Magnitude project, enabling developers to write and execute test cases for web applications using natural language descriptions and visual assertions. This approach aims to simplify the testing process by allowing developers to define test steps and checks in a more human-readable and intuitive manner, without relying heavily on low-level implementation details or complex selectors.

The testing framework is designed to represent user flows within a web application, where each test case navigates to a specific URL, executes a series of test steps, and verifies visual assertions (checks) along the way. This approach aligns with the overall goal of Magnitude, which is to facilitate the development and testing of web applications using natural language processing and machine learning techniques.

Sources: [packages/magnitude-test/src/index.ts](), [docs/testing/building-test-cases.mdx]()

## Test Cases

A test case in Magnitude represents a single user flow within a web application. It consists of a series of test steps and checks, executed in a specific order. Each test case can be configured with a starting URL, which defaults to the project's configured URL if not specified.

```typescript
test('can add and remove todos', { url: "https://mytodoapp.com" }, async (agent) => {
    await agent.act('Add a todo');
    await agent.act('Remove the todo');
});
```

Sources: [docs/testing/building-test-cases.mdx:19-25]()

### Test Steps

Test steps are the building blocks of a test case, representing individual actions or interactions within the user flow. Each step is defined using a natural language description, which should be specific enough to convey the intended action but not overly detailed with implementation specifics.

```typescript
test('example', async (agent) => {
    await agent.act('Log in'); // step description
});
```

Sources: [docs/testing/building-test-cases.mdx:30-35]()

### Checks

Checks, also known as visual assertions, are natural language descriptions used to verify the state of the web application after a specific test step. These checks are designed to be human-readable and understandable, allowing developers to express assertions in a more intuitive manner.

```typescript
test('example', async (agent) => {
    await agent.act('Log in');
    await agent.check('Dashboard is visible');
});
```

Sources: [docs/testing/building-test-cases.mdx:39-48]()

### Test Data

Magnitude supports providing additional test data relevant to specific test steps. This data can be supplied as key-value pairs or as freeform strings, depending on the requirements of the test case.

```typescript
test('example', async (agent) => {
    await agent.act('Log in', { data: { email: "foo@bar.com", password: "foo" } });
    await agent.check('Dashboard is visible');
});
```

```typescript
test('example', async (agent) => {
    await agent.act('Add 3 todos', { data: 'Use "Take out trash" for the first todo and make up the other 2' });
});
```

Sources: [docs/testing/building-test-cases.mdx:52-65]()

### Custom LLM Prompting

Magnitude allows developers to provide custom instructions or prompts to the underlying language model used for interpreting test steps and checks. These prompts can be specified at the test case level, test group level, or individual step level, providing flexibility in guiding the language model's behavior.

```typescript
test('example', async (agent) => {
    await agent.act('create 3 todos', { prompt: 'all todos must be animal-related' });
});
```

```typescript
test.group('todo list', { prompt: 'Each todo should be exactly 5 words'}, () => {
    test('can add todos', { url: 'https://magnitodo.com', prompt: 'All todos should be animal related' }, async (agent) => {
        await agent.act('create 3 todos', { prompt: 'the first and last word on the todo must start with the same letter'});
    });
});
```

Sources: [docs/testing/building-test-cases.mdx:69-83]()

## Example: Migrating a Playwright Test Case to Magnitude

The documentation provides an example of migrating a test case from the Playwright testing framework to Magnitude. This example highlights the difference in approach between traditional testing frameworks and Magnitude's visual assertions and natural language testing.

**Playwright Test Case:**

```typescript
test('should allow me to add todo items', async ({ page }) => {
    const newTodo = page.getByPlaceholder('What needs to be done?');

    await newTodo.fill(TODO_ITEMS[0]);
    await newTodo.press('Enter');

    await expect(page.getByTestId('todo-title')).toHaveText([
        TODO_ITEMS[0]
    ]);

    await newTodo.fill(TODO_ITEMS[1]);
    await newTodo.press('Enter');

    await expect(page.getByTestId('todo-title')).toHaveText([
        TODO_ITEMS[0],
        TODO_ITEMS[1]
    ]);
});
```

**Magnitude Test Case:**

```typescript
test('should allow me to add todo items', async (agent) => {
    await agent.act('Create todo', { data: TODO_ITEMS[0] });
    await agent.check('First todo appears in list');
    await agent.act('Create another todo', { data: TODO_ITEMS[1] });
    await agent.check('List has two todos');
});
```

In the Magnitude test case, the steps and checks are defined using natural language descriptions, without relying on low-level implementation details or selectors. This approach aims to make the test cases more readable and maintainable, while still providing the necessary functionality for testing web applications.

Sources: [docs/testing/building-test-cases.mdx:87-108]()

## Conclusion

Visual Assertions and Testing is a core feature of the Magnitude project, enabling developers to write and execute test cases for web applications using natural language descriptions and visual assertions. This approach simplifies the testing process by allowing developers to define test steps and checks in a more human-readable and intuitive manner, without relying heavily on low-level implementation details or complex selectors. The testing framework is designed to represent user flows within a web application, with each test case navigating to a specific URL, executing a series of test steps, and verifying visual assertions along the way.

Sources: [packages/magnitude-test/src/index.ts](), [docs/testing/building-test-cases.mdx]()