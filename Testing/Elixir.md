## Anatomy of a test

Software tests are comprised of no more than four stages. They have different names but oftenly, they are:

- setup,
- exercise,
- verify, and,
- teardown

## Best Practices

- Describe your tests with `should` keyword for clarity.
- Group common/reusable test code in a `setup` or `setup_all` block.
- Prove that your code does what it's supposed to do.
- Prevent breaking changes (regressions).
- Make it easy to find where your code is broken.
- Write the least amount of test code possible.
- In unit tests, your test will call a function (or send a message) with parameters and it will get a return value.
- In unit tests, your tests might need to intercept the calls out and return responses, stepping in for a depedency, if your code depends on other code in your codebase (we call this "isolating your code under test").
- When writing unit tests, you should be able to "draw" a "black box" on the function under test.
- You can draw a balck box of the function alone if it is isolated/decoupled, or together with its dependencies, if the dependencies themselves are isolated enough.
