# Unit VS Integration Testing

- **Unit tests:** These tests focus on individual functions or modules in isolation. They are fast and help ensure that each part of your code works correctly on its own. The functions or modules being tested should be free of side effects (DB calls, external API calls, filesystem access, network access, etc), therefore external dependencies should be mocked.
- **Integration tests:** These tests focus on the interaction between different parts of your application. They ensure that components work together as expected. They are slower but necessary to catch issues that unit tests might miss.

## Best practices

- **Unit tests:**
  - Focus on testing individual functions or modules.
  - Test internal implementation details of the function or module.
  - Use mocks and stubs to isolate the code being tested.
  - Ensure high coverage of edge cases and different input scenarios.
- **Integration tests:**
  - Focus on testing the interaction between different components. In other words, test the behaviour, no the implementation details.
  - Test real-world scenarios and workflows.
  - Ensure that the system works as a whole, including database interactions, external APIs, etc.

## Avoiding Redundancy

To avoid redundancy while ensuring comprehensive coverage, follow these steps:

- **Identify core logic:** Write unit tests for core business logic and utility functions.
- **Mock external dependencies:** In unit tests, mock external services, databases, and other dependencies.
- **End-to-end scenarios:** Write integration tests for end-to-end scenarios that involve multiple components interacting together.
- **Selective integration tests:** Focus on critical paths and high-risk areas in your integration tests.

## Scenario

Consider a scenario where we need to test user authentication, where a created user first needs to be activated before they can be authenticated. An integration test will look like this:

```Elixir
test "activated user can authenticate with correct credentials" do
    # Helper functions to create the user, send activation email & activate the user by "clicking" on the activation link
    # All these steps are done using the public APIs/REST, ensuring the behavour is tested.
    # They are only hidden from this test to make the test clear, since they are not directly related.
    activated_mail = register!(login: "foo@bar.baz", password: "password")
    activate_account!(activation_mail)

    response = authenticate(login: "foo@bar.baz", password: "password")

    # assertions
    assert response.status == 200
end
```

From the example above, you'll see that "unnecessary noise" has been abstracted away by use of helper functions. Admittedly, there is a compromise here between writing clearer/cleaner tests vs hiding implementation details.

Under the hood, the `register!/1` function creates a user and sends an activation email, all via public APIs; no calling repo/database functions directly. Same case, the `activate_account!/1` function issue a GET request to the given activation link, effectively activating the account through the public-facing API.

This scenario highlights the significance of integration tests in exercising the interaction of the various parts of the application. Unit tests on the other hand may not achieve the same objective because side effects are mocked/stubbed out.
