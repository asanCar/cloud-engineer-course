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
    - Test Fixtures: Reusable setup code for tests (e.g., database connections, test data). Use the `@pytest.fixture` decorator to define fixtures and pass them as arguments to test functions.
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

        @patch('__main__.ExternalDependency.get_data')
        def test_method_calls_external_with_patch(self, mock_get_data):
            # Mocking with patch decorator
            mock_get_data.return_value = "Patched Data"
            instance = ProductionClass()
            result = instance.method_that_calls_external()
            self.assertEqual(result, "Patched Data")
            mock_get_data.assert_called_once()

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

- **Example Mocking an API Call:**

    ```python
    # --- The code we want to test (weather_service.py) ---
    import requests
    
    def get_weather(city: str) -> dict | None:
        """
        Retrieves weather data for a given city from an external API.
        """
        try:
            response = requests.get(f"https://api.weatherapi.com/v1/current.json?key=FAKE_KEY&q={city}")
            response.raise_for_status()  # Raises an exception for bad status codes (4xx or 5xx)
            return response.json()
        except requests.exceptions.RequestException:
            # If there's a network error or a bad status code, we return None.
            return None

    # --- The test code for our function (test_weather_service.py) ---
    from unittest.mock import patch, Mock
    
    def test_get_weather_success():
        """
        Tests the successful retrieval of weather data.
        """
        # Arrange: Set up our mock to behave like a successful API call.
        # We target 'weather_service.requests.get' to replace the `get` function inside our module.
        # The 'mock_get' argument is automatically injected by @patch.
        mock_api_response = {"location": {"name": "Barcelona"}, "current": {"temp_c": 25.0}}
    
        with patch('weather_service.requests.get') as mock_get:
            # Configure the mock object that `requests.get` will return.
            # This mock simulates the 'response' object from the requests library.
            mock_response = Mock()
            mock_response.status_code = 200
            mock_response.json.return_value = mock_api_response
            # Make raise_for_status do nothing for a successful test
            mock_response.raise_for_status.return_value = None 
    
            # This is what our patched `requests.get` will now return
            mock_get.return_value = mock_response
    
            # Act: Call the function we are testing.
            weather_data = get_weather("Barcelona")
    
            # Assert: Verify the results.
            # 1. Check that our function returned the correct data.
            assert weather_data is not None
            assert weather_data["current"]["temp_c"] == 25.0
    
            # 2. Check that the mock was called correctly by our function.
            mock_get.assert_called_once_with("https://api.weatherapi.com/v1/current.json?key=FAKE_KEY&q=Barcelona")


    def test_get_weather_api_error():
        """
        Tests how our function handles an API error (e.g., 404 Not Found).
        """
        # Arrange: Set up our mock to simulate an API error.
        with patch('weather_service.requests.get') as mock_get:
            mock_response = Mock()
            # Simulate an error by having raise_for_status() raise an exception,
            # just like the real requests library would do for a 4xx or 5xx response.
            mock_response.raise_for_status.side_effect = requests.exceptions.HTTPError("Not Found")
            mock_get.return_value = mock_response
    
            # Act: Call the function we are testing.
            weather_data = get_weather("FakeCity")
    
            # Assert: Verify that our function handled the error gracefully by returning None.
            assert weather_data is None
            mock_get.assert_called_once_with("https://api.weatherapi.com/v1/current.json?key=FAKE_KEY&q=FakeCity")

    ```

## 3.3 Fixtures

- **Objective:** Learn how to use `pytest` fixtures to manage test setup, teardown, and dependencies in a clean, reusable way.

- **Topics:**

  - **What are Fixtures?:** Fixtures are functions decorated with `@pytest.fixture` that provide a fixed baseline or resource (like test data or a database connection) for your tests, promoting reusability.

  - **Using Fixtures:** To use a fixture, a test function simply includes the fixture's function name as an argument, and `pytest` handles running it and passing its return value.

  - **Setup & Teardown:** Setup and teardown logic is managed within a fixture by using the `yield` keyword; code before the `yield` is for setup, and code after is for cleanup.

  - **Fixture Scope:** A fixture's lifecycle is controlled by its `scope` parameter (e.g., `'function'`, `'module'`), which defines how often it's created and destroyed to optimize test speed.

- **Example Snippet (Using a fixture for test data):**

    ```python
    # --- Example: Using a fixture for setup ---
    # In a file named test_user_profile.py
    import pytest
    
    # This fixture runs once for each test function that uses it (default scope is 'function').
    @pytest.fixture
    def user_profile():
        """A fixture to provide a sample user profile dictionary."""
        print("\n(Setting up user_profile fixture...)")
        profile_data = {
            "username": "testuser",
            "email": "test@example.com",
            "preferences": {
                "theme": "dark",
                "notifications": True
            }
        }
        # The 'yield' keyword passes the data to the test.
        yield profile_data
        # Code after 'yield' is the teardown/cleanup phase.
        print("\n(Tearing down user_profile fixture...)")
    
    # This test uses the 'user_profile' fixture by accepting it as an argument.
    def test_username(user_profile):
        """Tests that the username in the profile is correct."""
        assert user_profile["username"] == "testuser"
    
    # This test also uses the same fixture, getting a fresh, independent instance.
    def test_notification_preference(user_profile):
        """Tests the default notification setting."""
        assert user_profile["preferences"]["notifications"] is True
    ```

- **Example Snippet (A fixture using another fixture):**

    Fixtures can request other fixtures, allowing you to compose them into more complex setup logic.

    ```python
    # --- Example: Composing fixtures ---
    # In a file named test_user_model.py
    import pytest

    # A simple class we might want to test
    class User:
        def __init__(self, data):
            self.first_name = data["first_name"]
            self.last_name = data["last_name"]

        def full_name(self):
            return f"{self.first_name} {self.last_name}"

    # Fixture 1: Provides raw data
    @pytest.fixture
    def user_data():
        """Provides a dictionary of raw user data."""
        return {"first_name": "John", "last_name": "Doe"}

    # Fixture 2: Uses the 'user_data' fixture to create a User object
    @pytest.fixture
    def user_instance(user_data):
        """Creates a User instance from the user_data fixture."""
        # This fixture receives the dictionary returned by the user_data fixture.
        return User(user_data)

    # This test function only needs to request the final, composed fixture.
    def test_user_full_name(user_instance):
        """
        Tests the full_name method of the User class.
        pytest automatically resolves the dependency:
        1. test_user_full_name needs user_instance.
        2. user_instance needs user_data.
        3. pytest runs user_data, passes its result to user_instance, 
        then passes that result to the test.
        """
        assert user_instance.full_name() == "John Doe"
    ```

- **Example Snippet (Advanced Fixture: Database Connection):**

    ```python
    # --- Example: Fixture for a temporary database connection ---
    # In a file named test_database_operations.py
    import pytest
    import sqlite3
    
    # This fixture will be set up once per module and torn down after all tests in the module run.
    @pytest.fixture(scope="module")
    def db_connection():
        """
        A fixture that sets up an in-memory SQLite database,
        creates a 'users' table, and yields the connection object.
        """
        print("\n(Setting up database connection...)")
        # Use an in-memory database for speed and isolation
        conn = sqlite3.connect(":memory:")
        cursor = conn.cursor()
    
        # --- Setup Phase ---
        cursor.execute("""
            CREATE TABLE users (
                id INTEGER PRIMARY KEY,
                name TEXT NOT NULL,
                email TEXT NOT NULL
            )
        """)
        cursor.execute("INSERT INTO users (id, name, email) VALUES (?, ?, ?)", (1, "Alice", "alice@example.com"))
        conn.commit()
    
        # Yield the connection to the tests
        yield conn
    
        # --- Teardown Phase (runs after all tests in the module are done) ---
        print("\n(Tearing down database connection...)")
        conn.close()
    
    def test_query_user_by_id(db_connection):
        """
        Tests fetching a user from the database.
        """
        cursor = db_connection.cursor()
        cursor.execute("SELECT name FROM users WHERE id = 1")
        user = cursor.fetchone()
        assert user[0] == "Alice"
    
    def test_insert_new_user(db_connection):
        """
        Tests inserting a new user and verifying the insertion.
        """
        cursor = db_connection.cursor()
        cursor.execute("INSERT INTO users (id, name, email) VALUES (?, ?, ?)", (2, "Bob", "bob@example.com"))
        db_connection.commit()
    
        # Verify the new user was added
        cursor.execute("SELECT name FROM users WHERE id = 2")
        new_user = cursor.fetchone()
        assert new_user[0] == "Bob"
    ```

- **Example Snippet (Advanced Fixture: Temporary File):**

    ```python
    # --- Example: Fixture for creating a temporary config file ---
    # In a file named test_config_parser.py
    import pytest
    import tempfile
    import shutil
    import os
    import json
    
    @pytest.fixture
    def temp_config_file():
        """
        A fixture that creates a temporary directory and a sample config.json file inside it.
        It yields the path to the file and cleans up the directory afterward.
        """
        # --- Setup Phase ---
        temp_dir = tempfile.mkdtemp()
        config_path = os.path.join(temp_dir, "config.json")
        config_data = {"api_key": "12345-abcde", "timeout": 60}
    
        with open(config_path, "w") as f:
            json.dump(config_data, f)
    
        print(f"\n(Created temp config file at {config_path})")
    
        # Yield the path to the file for the test to use
        yield config_path
    
        # --- Teardown Phase (runs after the test function completes) ---
        print(f"\n(Removing temp directory {temp_dir})")
        shutil.rmtree(temp_dir)
    
    def load_config(path: str) -> dict:
        """A simple function that we want to test."""
        with open(path, "r") as f:
            return json.load(f)
    
    def test_load_config_parses_correctly(temp_config_file):
        """
        Tests that the load_config function correctly reads the JSON file
        created by our fixture.
        """
        config = load_config(temp_config_file)
        assert config["api_key"] == "12345-abcde"
    ```

## 3.4 Test Parametrization

- **Objective:** Learn to use parametrization to run the same test function with multiple different inputs, reducing code duplication and improving clarity.

- **Topics:**

  - **What is it?:** Parametrization is a feature that allows you to define multiple sets of arguments and expected outputs for a single test, and `pytest` will run the test separately for each set.

  - **Decorator:** You apply parametrization using the `@pytest.mark.parametrize` decorator, which takes a string of argument names and a list of argument sets.

  - **Benefits:** This approach makes it easy to test a wide range of inputs and edge cases with very little code, making your test suite more thorough and easier to maintain.

- **Example Snippet (Refactoring the calculator tests):**

    Notice how the three separate `test_add_*` functions from section 3.1 can be combined into a single, more powerful test.

    ```python
    # --- Example: Parametrizing a test function ---
    # In a file named test_calculator_parametrized.py
    import pytest
    
    def add(x, y):
        return x + y
    
    # The parametrize decorator will run this test three times,
    # substituting the arguments a, b, and expected for each run.
    @pytest.mark.parametrize("a, b, expected", [
        (2, 3, 5),          # Test case 1: Positive numbers
        (-1, -1, -2),       # Test case 2: Negative numbers
        (5, -2, 3),         # Test case 3: Mixed numbers
        (0, 0, 0),          # Test case 4: Zeros
        (100, -100, 0)      # Test case 5: Opposites
    ])
    def test_add(a, b, expected):
        """
        Tests the add function with multiple input scenarios.
        """
        assert add(a, b) == expected
    ```

- **Example Snippet (Testing an object's state):**

    Parametrization can be used to test the state of an object after performing an action, which is common in object-oriented programming.

    ```python
    # --- Example: Parametrizing tests for an object's state ---
    # In a file named test_bank_account.py
    import pytest

    class BankAccount:
        def __init__(self, initial_balance=0):
            self.balance = initial_balance

        def transact(self, amount):
            self.balance += amount

    @pytest.mark.parametrize("initial_balance, transaction_amount, final_balance", [
        (100, 50, 150),      # Test deposit
        (100, -50, 50),      # Test withdrawal
        (100, 0, 100),       # Test zero transaction
        (0, 200, 200),       # Test deposit from zero
        (50, -50, 0),        # Test withdrawal to zero
    ])
    def test_bank_transaction(initial_balance, transaction_amount, final_balance):
        """
        Tests that transactions result in the correct account balance.
        """
        # Arrange
        account = BankAccount(initial_balance)

        # Act
        account.transact(transaction_amount)

        # Assert
        assert account.balance == final_balance
    ```
