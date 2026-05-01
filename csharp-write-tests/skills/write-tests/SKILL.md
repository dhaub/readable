---
name: write-tests
description: This skill should be used when the user asks to 'write tests', 'generate tests', 'create unit tests', or 'add tests' for a C# class or file.
---

## Instructions

Read the target file(s), understand all its public methods and behaviors, then generate a comprehensive test class following the conventions below.

## Test Conventions

### Project and Namespace

- One test class per production class.
- Test class goes in the project with the same name and a `.UTest` suffix
- Test namespace mirrors the production namespace with `.UTest` inserted after the project name.
- There should be a static `TestHelpers` class in the root of the `.UTest` project for helper methods and test data generation.
- The `TestHelpers` class should always have a `Gen<T>()` method for generating random values of any type of the form `public static T Gen<T>() => Fixture.Create<T>();`.
- There should be a `GlobalUsings.cs` file in the root of the `.UTest` project with `using` statements for all the testing specific namespaces. It should have a `global using static TestHelpers`.
- Test class name: `<ClassName>Tests`.

### Test Method Naming

- Format: `<MethodName>_Should<ExpectedBehavior>_When<Conditions>`.
- Drop `_When<Conditions>` if there are no unique conditions.

### Test Paths

- Tests should cover all code but it is not necessary to cover every unique path.
- Tests should assert every output or state change that results from the method under test.

### Test Structure

- Divide each test into three sections separated by comments: `// given`, `// when`, `// then`.
- Creating the system under test must be the **last** statement in the `// given` section.
- Use `Assert.That` syntax for all assertions.
- Do NOT use `Assert.Multiple`.
- Use `[TestCase]` attribute when testing multiple scenarios for the same method.

### Variables and Readability

- No test instance variables — all state is local to each test method.
- Use `Gen<T>()` for values unless the value has an important meaning.
- Use descriptive variable names for values that do matter, including the system under test.
- Add comments to indicate the purpose of important values.

### **IMPORTANT:** Mocking

- Always use `Create<ClassName>()` methods to create and setup these mock objects at the bottom of the test class.
- Create methods that are or likely to be used by multiple tests should be moved to the `TestHelpers` class at the root of the UTest project.
- The Create methods should NEVER return mocks.
- Use `Mock.Get` to get the underlying mock when `Verify` is needed.
- Create methods can call other create methods only with parameterless calls
- Create calls should always use named parameters.
- Helper methods should have optional parameters with sensible defaults so tests only specify what they care about.

### Formatting

- No regions
- Use file-scoped namespaces
- Don't use `sut` as a variable name use a meaningful name

## Workflow

1. Read the target file and identify all public methods/properties to test
2. Read existing tests in the `.UTest` project for patterns and available test helpers
3. Use `TestHelpers` for Gen<T>() and Create methods common to multiple tests.
4. Write the test file in the correct location
5. Build with `dotnet build <TestProject>.csproj`
6. Run tests with `dotnet test <TestProject>.csproj --filter "FullyQualifiedName~<TestClassName>"`
7. Fix any failures and re-run until all tests pass
