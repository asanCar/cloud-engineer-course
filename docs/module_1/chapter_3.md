# Chapter 3: Testing Strategies

**Estimated Time:** 8 hours

**Goal:** To equip developers with a comprehensive understanding of software testing principles and practices, including unit testing, mocking, TDD, and basic integration testing.

## 3.1 Unit Testing with `pytest`

- **Objective:** Learn how to write effective unit tests using the `pytest` framework.

- **Topics:**

  - `pytest` framework:
    - Test Discovery: pytest automatically collects test cases by identifying files (e.g., `test_*.py` or `*_test.py`) and functions (e.g., `test_*` or `*_test`).
    - Use `assert` to verify conditions in your code.
    - Test Organization: Structure your test code using functions (for simple tests) or classes (for grouping related tests and setup/teardown) to improve clarity.
    - Test Fixtures: Manage test setup and teardown.
    - Test Parametrization: Run tests with multiple inputs.
    - Test Marking: Tag tests with markers (using decorators like `@pytest.mark.marker_name`) to skip them, mark them as expected to fail, or group them for selective execution.
    - pytest Configuration: Configure pytest settings (e.g., test discovery, markers) using the `pytest.ini` file.
  - Writing good unit tests:
    - AAA Pattern: Structure tests as Arrange-Act-Assert.
    - Testing Edge Cases: Test with unusual inputs.
    - Test Best Practices: Adhere to principles of independence (avoid shared state, use proper setup/teardown), speed (fast execution for rapid feedback), and focus (test one unit/behavior) to create reliable and maintainable tests.

- **Example Snippet (Basic `pytest`):**

    ```python
    #   --- Example: Basic pytest ---
    #   test_calculator.py
    def   add(x, y):
        return   x + y

    def   test_add_positive_numbers():
        assert   add(2, 3) == 5

    def   test_add_negative_numbers():
        assert   add(-1, -1) == -2

    def   test_add_mixed_numbers():
        assert   add(5, -2) == 3
    ```

    ```bash
    #   Running the tests
    pytest test_calculator.py
    ```

## 3.2 Mocking

- **Objective:** Understand the concept of mocking and how to use mocking libraries to isolate units under test.

- **Topics:**

  - Stubs vs. Mocks vs. Spies:
    - Stubs: Provide fixed return values or responses to calls, primarily used to control the state of dependencies.
    - Mocks: More advanced than stubs; they also allow you to verify that specific interactions with the substitute occurred (e.g., that a method was called with certain arguments).
    - Spies: A type of mock that also records how the substitute was used, allowing you to both control its behavior and assert on its interactions.
  - `unittest.mock` (Python's built-in mocking library):
    - `Mock` class: (Used to create basic mock objects to stand in for real classes or instances).
    - `MagicMock` class: (Extends `Mock` to easily handle method calls and magic methods).
    - `patch()` decorator/context manager: (Simplifies the process of replacing objects with mocks during tests).
    - Asserting calls and interactions: (Use methods like `assert_called_once_with()` and `assert_called()` to verify how mock objects were called during the test).

- **Example Snippet (`unittest.mock`):**

    ```python
    #   --- Example: unittest.mock ---
    import unittest
    from unittest.mock import Mock, MagicMock, patch

    class ProductionClass:
        def method_that_calls_external(self):
            return ExternalDependency.get_data()  # Relies on an external component

    class ExternalDependency:
        @staticmethod
        def get_data():
            # In a real system, this might hit a database or API
            raise NotImplementedError("This is a real external call")


    class TestProductionClass(unittest.TestCase):

        def test_method_calls_external_with_mock(self):
            # 1. Mocking a function directly (using Mock)
            ExternalDependency.get_data = Mock(return_value="Mocked Data")
            instance = ProductionClass()
            result = instance.method_that_calls_external()
            self.assertEqual(result, "Mocked Data")
            ExternalDependency.get_data.assert_called_once()  # Verify it was called

        def test_method_calls_external_with_patch(self):
            # 2. Mocking with patch (as a decorator)
            @patch('__main__.ExternalDependency.get_data')  # Target the full path
            def test_using_patch(mock_get_data):
                mock_get_data.return_value = "Patched Data"
                instance = ProductionClass()
                result = instance.method_that_calls_external()
                self.assertEqual(result, "Patched Data")
                mock_get_data.assert_called_once()

            test_using_patch()  # Execute the patched test

        def test_method_calls_external_with_magicmock(self):
            # 3. Mocking with MagicMock (for methods/magic methods)
            mock_external = MagicMock()
            mock_external.get_data.return_value = "Magic Mocked"
            with patch('__main__.ExternalDependency', new=mock_external):
                instance = ProductionClass()
                result = instance.method_that_calls_external()
                self.assertEqual(result, "Magic Mocked")
                mock_external.get_data.assert_called_once()


        def test_method_calls_external_with_spy_like_behavior(self):
            # 4. Demonstrating "spy" like behavior with call_args_list

            # Let's say we want to track all calls to a method
            with patch('__main__.ExternalDependency.get_data') as mock_get_data:
                instance = ProductionClass()
                instance.method_that_calls_external()
                instance.method_that_calls_external()
                instance.method_that_calls_external(10)  # Call with an argument
                instance.method_that_calls_external("abc")

                # Inspecting calls
                self.assertEqual(mock_get_data.call_count, 4)  # Total calls
                self.assertEqual(mock_get_data.call_args_list[0], unittest.mock.call()) # First call
                self.assertEqual(mock_get_data.call_args_list[2], unittest.mock.call(10)) # Call with arg
                self.assertEqual(mock_get_data.call_args_list[3], unittest.mock.call("abc"))
    ```
