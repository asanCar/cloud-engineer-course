# Chapter 1: (Python) Deep Dive & Idiomatic Code

**Estimated Time:** 10 hours

**Goal:** To solidify understanding of fundamental Python concepts and introduce advanced features, focusing on writing clean, efficient, and "Pythonic" code.

## **1.1 Data Structures & Control Flow**

- **Objective:** Quickly refresh core data structures and control flow mechanisms.
    
- **Topics:**
    
    - **Lists:** Methods (`append`, `extend`, `insert`, `remove`, `pop`, `sort`), slicing, list comprehensions (brief intro - covered more in 1.8).
        
    - **Dictionaries:** Key-value pairs, methods (`keys`, `values`, `items`, `get`, `pop`), dictionary comprehensions (brief intro).
        
    - **Sets:** Unordered unique elements, methods (`add`, `remove`, `union`, `intersection`, `difference`), use cases (membership testing, removing duplicates).
        
    - **Tuples:** Immutable sequences, packing/unpacking, use cases (dictionary keys, returning multiple values).
        
    - **Control Flow:** `if`/`elif`/`else`, `for` loops (iterating over sequences, `range`), `while` loops, `break`, `continue`, `pass`.
        
- **Example Snippet (Tuple Unpacking):**
    
    ```
    # --- Example 1: Basic Tuple Unpacking ---
    point = (10, 20)
    x, y = point # Unpacking
    print(f"x: {x}, y: {y}")
    
    # --- Example 2: Unpacking in Loops ---
    coordinates = [(0, 0), (1, 1), (2, 4)]
    for x_coord, y_coord in coordinates: # Unpacking in loops
        print(f"Processing point: ({x_coord}, {y_coord})")
    ```
    

## **1.2 Functions Deep Dive**

- **Objective:** Understand function scope rules, closures, and flexible argument handling.
    
- **Topics:**
    
    - **Scope (LEGB Rule):** Local, Enclosing function locals, Global, Built-in. How Python searches for names.
        
    - **Closures:** Functions that "remember" their enclosing lexical scope, even when the enclosing function has finished executing. Practical examples (e.g., factory functions).
        
    - **Argument Packing/Unpacking:**
        
        - `*args`: Collects positional arguments into a tuple.
            
        - `**kwargs`: Collects keyword arguments into a dictionary.
            
        - Using `*` and `**` when _calling_ functions to unpack iterables/dictionaries.
            
- **Example Snippet (Closure & *args/**kwargs):**
    
    ```
    # --- Example 1: Closure ---
    def outer_function(msg):
        # msg is in the enclosing scope
        def inner_function():
            # inner_function "closes over" msg
            print(msg)
        return inner_function # Return the inner function
    
    hello_func = outer_function("Hello")
    hello_func() # Output: Hello
    
    # --- Example 2: *args and **kwargs ---
    def process_data(id, *values, **options):
        print(f"Processing ID: {id}")
        print(f"Values: {values}") # Tuple
        print(f"Options: {options}") # Dictionary
        if options.get("verbose", False):
             print("Verbose mode enabled.")
    
    process_data(101, 'a', 'b', 'c', verbose=True, retries=3)
    # Output:
    # Processing ID: 101
    # Values: ('a', 'b', 'c')
    # Options: {'verbose': True, 'retries': 3}
    # Verbose mode enabled.
    ```
    

## **1.3 Object-Oriented Programming (OOP) Revisited**

- **Objective:** Solidify OOP concepts and understand the role of special ("dunder") methods.
    
- **Topics:**
    
    - **Classes and Objects:** Review of basic definition, instantiation.
        
    - **Inheritance:** Single and multiple inheritance (Method Resolution Order - MRO), `super()`.
        
    - **Polymorphism:** Duck typing ("If it walks like a duck and quacks like a duck...").
        
    - **Encapsulation:** Using naming conventions (`_protected`, `__private` name mangling) - Python relies on convention more than strict enforcement.
        
    - **Dunder Methods:**
        
        - `__init__(self, ...)`: Constructor.
            
        - `__str__(self)`: User-friendly string representation (`str()`).
            
        - `__repr__(self)`: Developer-friendly string representation (`repr()`). Aim for unambiguous representation.
            
        - `__len__(self)`: Length (`len()`).
            
        - `__eq__(self, other)`: Equality comparison (`==`).
            
        - Others (`__add__`, `__getitem__`, etc.) as needed.
            
    - **Properties:** Using `@property` decorator for getter methods, `@<property_name>.setter` for setters - provides controlled access to attributes.
        
- **Example Snippet (Inheritance, Dunder Methods & Properties Combined):**
    
    ```
    # --- Example Combined OOP Concepts ---
    
    # Base Class: Publication
    class Publication:
        """Represents a generic publication with a title and price."""
        def __init__(self, title, price):
            self.title = title
            self._price = price # Use underscore for intended protected attribute
    
        @property
        def price(self):
            """Getter for the price using property decorator."""
            # Could add logic here, e.g., currency conversion
            return self._price
    
        @price.setter
        def price(self, value):
            """Setter for the price with validation."""
            if value < 0:
                raise ValueError("Price cannot be negative.")
            self._price = value
    
        # Dunder method for user-friendly string representation
        def __str__(self):
            return f"{self.title} (Price: ${self.price:.2f})"
    
        # Dunder method for developer-friendly representation
        def __repr__(self):
            # Should ideally be unambiguous and allow recreating the object
            return f"{self.__class__.__name__}(title='{self.title}', price={self.price})"
    
        # Dunder method for equality comparison
        def __eq__(self, other):
            if not isinstance(other, Publication):
                return NotImplemented # Indicate comparison not supported for this type
            # Compare based on title and price for this example
            return self.title == other.title and self.price == other.price
    
    # Derived Class: Book (inherits from Publication)
    class Book(Publication):
        """Represents a book, inheriting from Publication and adding an author."""
        def __init__(self, title, author, price):
            # Call the parent class's __init__ method using super()
            super().__init__(title, price)
            self.author = author
    
        # Override __str__ to include the author
        def __str__(self):
            # Reuse parent's __str__ or format differently
            # return f'"{self.title}" by {self.author} (Price: ${self.price:.2f})'
            # Alternative: Reuse parent's logic via super() if desired, then add more
            base_str = super().__str__()
            return f'"{self.title}" by {self.author} - [{base_str}]'
    
    
        # Override __repr__ to include the author
        def __repr__(self):
            # Recreate the specific Book object
            return f"Book(title='{self.title}', author='{self.author}', price={self.price})"
    
        # Override __eq__ to include author comparison
        def __eq__(self, other):
            if not isinstance(other, Book):
                return NotImplemented
            # Use parent's __eq__ via super() for title/price and add author check
            return super().__eq__(other) and self.author == other.author
    
        # Method specific to Book
        def read_excerpt(self):
            print(f"Reading an excerpt from '{self.title}' by {self.author}...")
    
    
    # --- Example Usage: Combined Concepts ---
    print("--- Creating Instances ---")
    generic_pub = Publication("Generic Magazine", 5.99)
    book1 = Book("The Pragmatic Programmer", "Andy Hunt", 29.95)
    book2 = Book("Clean Code", "Robert C. Martin", 35.50)
    book3 = Book("The Pragmatic Programmer", "Andy Hunt", 29.95) # Same as book1
    
    print("\n--- Testing Dunder Methods (__str__, __repr__) ---")
    print(f"Generic Pub (__str__): {generic_pub}")
    print(f"Book 1 (__str__):      {book1}")
    print(f"Generic Pub (__repr__): {repr(generic_pub)}")
    print(f"Book 1 (__repr__):      {repr(book1)}")
    
    print("\n--- Testing Property ---")
    print(f"Book 1 original price: ${book1.price:.2f}") # Getter
    book1.price = 32.00 # Setter
    print(f"Book 1 new price: ${book1.price:.2f}")
    try:
        book1.price = -10 # Setter validation
    except ValueError as e:
        print(f"Error setting price: {e}")
    
    print("\n--- Testing Inheritance & Overriding ---")
    book1.read_excerpt() # Method specific to Book
    # generic_pub.read_excerpt() # Would cause AttributeError
    
    print("\n--- Testing Equality (__eq__) ---")
    print(f"book1 == book2: {book1 == book2}") # False
    print(f"book1 == book3: {book1 == book3}") # True
    print(f"book1 == generic_pub: {book1 == generic_pub}") # False (different types handled by isinstance check)
    
    print("\n--- Testing isinstance ---")
    print(f"Is book1 a Book? {isinstance(book1, Book)}")           # True
    print(f"Is book1 a Publication? {isinstance(book1, Publication)}") # True
    print(f"Is generic_pub a Book? {isinstance(generic_pub, Book)}") # False
    ```
    

## **1.4 Decorators**

- **Objective:** Understand how decorators work conceptually, why they are useful, and how to implement custom ones for common tasks like logging or timing.
    
- **Topics:**
    
    - **What are Decorators?** Syntactic sugar (`@`) for a pattern where a function takes another function as input, adds some functionality (wraps it), and returns the modified function. Think of it like wrapping a gift – you add wrapping paper (extra functionality) without changing the gift (original function) itself.
        
    - **Why Use Decorators?**
        
        - _Code Reusability:_ Apply the same extra logic (like logging, timing, access control) to multiple functions without repeating code.
            
        - _Separation of Concerns:_ Keep the core logic of your function separate from cross-cutting concerns (like logging or performance monitoring).
            
        - _Readability:_ The `@decorator_name` syntax clearly indicates that a function's behavior is being modified.
            
    - **Functions as First-Class Objects:** Decorators rely on the fact that functions in Python can be passed as arguments, returned from other functions, and assigned to variables.
        
    - **How Decorators Work (Conceptual Steps):**
        
        1. You define a function (e.g., `my_decorator`) that accepts another function (`func`) as an argument.
            
        2. Inside `my_decorator`, you define a _nested_ function (often called `wrapper` or `inner`). This `wrapper` function will contain the extra logic _plus_ a call to the original function `func`.
            
        3. `my_decorator` returns the `wrapper` function.
            
        4. When you use `@my_decorator` above another function definition (e.g., `say_hello`), Python essentially does this: `say_hello = my_decorator(say_hello)`. Now, `say_hello` actually refers to the `wrapper` function returned by the decorator.
            
    - **Using `functools.wraps`:** Essential for preserving the original function's metadata (like its name `__name__` and docstring `__doc__`). Without it, the decorated function would appear to be the `wrapper` function, which can confuse debugging and documentation tools.
        
    - **Decorators with Arguments:** Requires an extra layer of nesting. The outer function takes the decorator arguments and returns the actual decorator function, which then takes the target function and returns the wrapper.
        
    - **Class-Based Decorators:** You can also use classes to create decorators, typically by implementing the `__init__` and `__call__` methods. The instance is initialized, and then `__call__` is invoked when the decorated function is called.
        
- **Example Snippet (Simple Timer Decorator - Explained):**
    
    ```
    # --- Example 1: Timer Decorator ---
    import functools
    import time
    
    # 1. Define the decorator function: takes the target function 'func'
    def timer(func):
        # 3. Use functools.wraps to copy metadata from 'func' to 'wrapper'
        @functools.wraps(func)
        # 2. Define the inner 'wrapper' function: takes the same args as 'func'
        def wrapper(*args, **kwargs):
            print(f"Entering {func.__name__!r}...") # Added entry log
            start_time = time.perf_counter()
            # 4. Call the original function 'func' with its arguments
            result = func(*args, **kwargs)
            end_time = time.perf_counter()
            run_time = end_time - start_time
            print(f"Finished {func.__name__!r} in {run_time:.4f} secs") # Original timing log
            # 5. Return the result of the original function
            return result
        # 6. The decorator returns the 'wrapper' function
        return wrapper
    
    # 7. Apply the decorator using @ syntax
    @timer
    def complex_calculation(n):
        """Simulates a time-consuming task."""
        total = 0
        for i in range(n):
            total += i*i
        return total
    
    # 8. Calling complex_calculation now actually calls the 'wrapper'
    result = complex_calculation(1000000)
    # Output:
    # Entering 'complex_calculation'...
    # Finished 'complex_calculation' in X.XXXX secs (time will vary)
    
    print(f"Result: {result}")
    print(complex_calculation.__name__) # Output: complex_calculation (thanks to wraps)
    print(complex_calculation.__doc__) # Output: Simulates a time-consuming task. (thanks to wraps)
    ```
    

## **1.5 Generators and Iterators**

- **Objective:** Learn how generators provide memory-efficient ways to create iterables, especially for large datasets.
    
- **Topics:**
    
    - **Iterator Protocol:** Defines how iteration works via `__iter__()` (returns the iterator object itself) and `__next__()` (returns the next item). `StopIteration` exception is raised when no more items are available.
        
    - **Generators:** Functions using `yield` to produce a sequence of values lazily. State is saved between calls.
        
    - **Generator Expressions:** Concise syntax similar to list comprehensions but creating generators `(x*x for x in range(10))`.
        
    - Use cases: Processing large files, infinite sequences, pipelines.
        
- **Example Snippet (Generator for Fibonacci):**
    
    ```
    # --- Example 1: Fibonacci Generator ---
    def fibonacci_generator(limit):
        a, b = 0, 1
        while a < limit:
            yield a # Pauses execution and returns value
            a, b = b, a + b # Resumes here on next call
    
    # Using the generator
    print("Fibonacci sequence:")
    for number in fibonacci_generator(100):
        print(number, end=" ") # Output: 0 1 1 2 3 5 8 13 21 34 55 89
    print("\n")
    
    # --- Example 2: Generator Expression ---
    squares = (x*x for x in range(5))
    print(type(squares)) # <class 'generator'>
    for sq in squares:
        print(sq, end=" ") # Output: 0 1 4 9 16
    print()
    ```
    
- **Example Snippet (Simple Generator Pipeline):**
    
    ```
    # --- Example 3: Simple Generator Pipeline ---
    # 0. Our initial data
    numbers = [1, 2, 3, 4, 5, 6, 7, 8]
    
    # 1. First pipeline step: Square the numbers
    def square_numbers(nums):
      for n in nums:
        yield n * n
    
    # 2. Second pipeline step: Filter for even numbers
    def filter_even(squared_nums):
      for n in squared_nums:
        if n % 2 == 0:
          yield n
    
    # 3. Build the pipeline by chaining the generators
    pipeline = filter_even(square_numbers(numbers))
    
    # 4. Pull data through the pipeline
    print("Starting pipeline execution...")
    for even_square in pipeline:
      print(f"PIPELINE RESULT: {even_square}")
    print("Pipeline finished.")
    ```
    

## **1.6 Context Managers**

- **Objective:** Understand how the `with` statement simplifies resource management (files, network connections, locks).
    
- **Topics:**
    
    - **The need for cleanup (`try...finally`):** Manually managing resources (like closing files or releasing locks) with `try...finally` is necessary but verbose and error-prone; it's easy to forget the `finally` block or handle exceptions incorrectly.
        
    - **The `with` statement:** Provides a cleaner, more reliable way to ensure that setup and teardown actions (like opening/closing a resource) happen automatically, even if errors occur within the block.
        
    - **Context Manager Protocol:** This is what makes the `with` statement work. Requires an object to have two special methods:
        
        - `__enter__(self)`: Executed at the start of the `with` block. Often returns the resource itself (like the file object) or `self`.
            
        - `__exit__(self, exc_type, exc_val, exc_tb)`: Executed when exiting the `with` block (normally or due to an exception). It receives exception details (type, value, traceback) if any occurred and performs the cleanup. Returning `True` from `__exit__` suppresses the exception.
            
    - **Using `contextlib.contextmanager`:** A decorator that lets you create a context manager more easily using a generator function with a single `yield`. Code before `yield` acts as `__enter__`, code after `yield` (in a `finally` block) acts as `__exit__`.
        
- **Example Snippet (File Handling & Custom Context Manager):**
    
    ```
    # --- Example 1: Standard file handling with 'with' ---
    try:
        with open("example.txt", "w") as f:
            f.write("Hello, context managers!\n")
            # Simulate an error
            # raise ValueError("Something went wrong")
        # File is automatically closed here, even if an error occurs inside the block
        print("File 'example.txt' written and closed.")
    except Exception as e:
        print(f"An error occurred: {e}")
        # File is still closed if it was opened
    
    # --- Example 2: Custom context manager using contextlib ---
    import contextlib
    import time
    
    @contextlib.contextmanager
    def simple_timer(label="Execution"):
        """A simple context manager to time a block of code."""
        start = time.perf_counter()
        try:
            yield # Execution pauses here, control goes to the 'with' block
        finally:
            # Cleanup code runs regardless of exceptions in the 'with' block
            end = time.perf_counter()
            print(f"{label} took {end - start:.4f} seconds")
    
    # Using the custom timer context manager
    with simple_timer("Block Timer"):
        print("Inside the timed block...")
        time.sleep(0.5) # Simulate work
        print("...block finished.")
    
    # Expected Output:
    # File 'example.txt' written and closed.
    # Inside the timed block...
    # ...block finished.
    # Block Timer took 0.5XXX seconds
    ```
    

## **1.7 Advanced Error Handling**

- **Objective:** Learn to create and use custom exceptions for more specific error reporting.
    
- **Topics:**
    
    - **`try...except`:** The fundamental block for handling errors. Code that might raise an exception goes in the `try` part, and code to run if a specific error occurs goes in the `except` part.
        
    - **Handling specific exception types vs. broad `Exception`:** It's best practice to catch _specific_ errors you anticipate (like `ValueError` or `FileNotFoundError`). Catching a broad `Exception` can hide bugs or catch errors you didn't intend to handle (like `SystemExit` or `KeyboardInterrupt`), making debugging harder.
        
    - `else` block: Code that runs only if no exceptions occurred in the `try` block.
        
    - `finally` block: Code that runs _always_ (cleanup).
        
    - `raise` is used to raise Exceptions.
        
    - Custom exception classes can be created inheriting from `Exception` or more specific built-ins.
        
    - **Chaining exceptions (`raise NewException from original_exception`):** This preserves the original error context, making it easier to debug the root cause when wrapping exceptions.
        
- **Example Snippet (Custom Exception & Chaining):**
    
    ```
    # --- Example 1: Custom Exception & Chaining ---
    class InsufficientFundsError(Exception):
        """Custom exception for bank account operations."""
        def __init__(self, requested, available):
            self.requested = requested
            self.available = available
            super().__init__(f"Attempted to withdraw {requested}, but only {available} available.")
    
    def withdraw(balance, amount):
        """Withdraws amount if possible, raises errors otherwise."""
        if amount <= 0:
            raise ValueError("Withdrawal amount must be positive.")
        if amount > balance:
            # Example of raising a custom exception
            raise InsufficientFundsError(requested=amount, available=balance)
        return balance - amount
    
    def process_withdrawal(balance, amount):
        """Processes withdrawal, demonstrating exception chaining."""
        try:
            return withdraw(balance, amount)
        except InsufficientFundsError as e:
            print("Logging insufficient funds event...")
            # Raise a more general processing error, but link it to the original cause
            raise RuntimeError(f"Withdrawal processing failed for amount {amount}") from e
        except ValueError as e:
             print("Logging invalid amount event...")
             # Raise a more general processing error, linking to the original cause
             raise RuntimeError(f"Invalid withdrawal amount: {amount}") from e
    
    
    account_balance = 100
    try:
        print("Attempting withdrawal processing...")
        # account_balance = process_withdrawal(account_balance, 150) # Example for InsufficientFundsError
        account_balance = process_withdrawal(account_balance, -50) # Example for ValueError -> RuntimeError
        print("Withdrawal successful.") # This won't run if error occurs
    except RuntimeError as e:
         print(f"Caught Processing Error: {e}")
         if e.__cause__:
             print(f"  --> Original Cause: {type(e.__cause__).__name__}: {e.__cause__}") # Show the chained exception
    except Exception as e:
        print(f"An unexpected error occurred: {type(e).__name__}: {e}") # Less specific, use sparingly
    else:
        print("Transaction completed without errors.") # Runs only if try succeeds
    finally:
        print(f"Current balance: {account_balance}") # Runs always
    
    # Example Output (for amount = -50):
    # Attempting withdrawal processing...
    # Logging invalid amount event...
    # Caught Processing Error: Invalid withdrawal amount: -50
    #   --> Original Cause: ValueError: Withdrawal amount must be positive.
    # Current balance: 100
    
    # Example Output (for amount = 150):
    # Attempting withdrawal processing...
    # Logging insufficient funds event...
    # Caught Processing Error: Withdrawal processing failed for amount 150
    #   --> Original Cause: InsufficientFundsError: Attempted to withdraw 150, but only 100 available.
    # Current balance: 100
    ```
    

## **1.8 Pythonic Code**

- **Objective:** Embrace Python's idioms for more readable, concise, and efficient code.
    
- **Topics:**
    
    - **List Comprehensions:** `[expr for item in iterable if condition]`.
        
    - **Dictionary Comprehensions:** `{key_expr: val_expr for item in iterable if condition}`.
        
    - **Set Comprehensions:** `{expr for item in iterable if condition}`.
        
    - **Generator Expressions:** `(expr for item in iterable if condition)` - memory efficient, especially useful for large sequences or when you don't need the result list in memory.
        
    - Using `enumerate` for index and value in loops.
        
    - Using `zip` to iterate over multiple sequences simultaneously.
        
    - Avoiding manual index manipulation in loops where possible.
        
    - Truthy and Falsy values (checking for empty sequences/collections directly instead of using `len()`).
        
    - **f-Strings:** Prefer f-strings (`f"..."`) for embedding expressions inside string literals, as they are generally the most concise and readable method.
        
- **Example Snippet (Pythonic Examples):**
    
    ```
    # --- Example 1a: Non-Pythonic loop (Squaring) ---
    numbers = [1, 2, 3, 4, 5]
    squares = []
    for i in range(len(numbers)):
        squares.append(numbers[i] * numbers[i])
    print(squares)
    
    # --- Example 1b: Pythonic using list comprehension (Squaring) ---
    squares_comp = [n * n for n in numbers]
    print(squares_comp)
    
    # --- Example 2a: Non-Pythonic filtering ---
    evens = []
    for n in numbers:
        if n % 2 == 0:
            evens.append(n)
    print(evens)
    
    # --- Example 2b: Pythonic filtering with list comprehension ---
    evens_comp = [n for n in numbers if n % 2 == 0]
    print(evens_comp)
    
    # --- Example 3a: Iterating with index (less Pythonic) ---
    items = ['a', 'b', 'c']
    for i in range(len(items)):
        print(f"Index {i}: {items[i]}")
    
    # --- Example 3b: Pythonic iteration with enumerate ---
    for index, item in enumerate(items):
        print(f"Index {index}: {item}")
    
    # --- Example 4: Checking for empty list (Pythonic) ---
    my_list = []
    if not my_list: # Instead of if len(my_list) == 0:
        print("List is empty")
    
    # --- Example 5: Iterating with zip ---
    names = ["Alice", "Bob", "Charlie"]
    scores = [85, 92, 78]
    # zip pairs corresponding elements from multiple iterables
    for name, score in zip(names, scores):
        print(f"{name}: {score}")
    # Stops when the shortest iterable is exhausted
    
    # --- Example 6a: Summing squares (less Pythonic - creates intermediate list) ---
    big_numbers = range(1, 1000001) # Represents a potentially large sequence
    # Creates a potentially huge list in memory first
    total_sum_list = sum([x*x for x in big_numbers])
    print(f"Sum of squares (via list): {total_sum_list}") # Works, but memory intensive
    
    # --- Example 6b: Summing squares (Pythonic - uses generator expression) ---
    # Creates items one by one as sum() consumes them - much more memory efficient
    total_sum_gen = sum(x*x for x in big_numbers)
    print(f"Sum of squares (via generator): {total_sum_gen}")
    ```
    

## **1.9 Style Guides & Linters**

- **Objective:** Understand the importance of consistent code style and how tools can help enforce it.
    
- **Topics:**
    
    - **Linters:** Tools that analyze code for style errors and potential bugs (can be configured as pre-commit hooks).
        
        - `Flake8`: Combines `PyFlakes` (error checking), `pycodestyle` (PEP 8 checking), and McCabe (complexity checking).
            
        - `Pylint`: More extensive checks, highly configurable, can be "noisy".
            
    - **Formatters:** Tools that automatically reformat code to comply with a style guide.
        
        - `Black`: The "uncompromising code formatter", enforces a strict subset of PEP 8.
            
    - Integrating linters/formatters into development workflow (editor integration, pre-commit hooks).
        
- **Exercise:** Install `flake8` and `black` (e.g., `pip install flake8 black`). Save the following code into a Python file (e.g., `style_practice.py`) and run `flake8 style_practice.py` to see the style/error reports. Then, run `black style_practice.py` to automatically format the code. Run `flake8` again to see the difference.
    
    ```
    # --- Code for Linter/Formatter Exercise ---
    import sys
    
    def my_func( a,b ):
        '''A simple function with style issues.'''
        if (a>b): # Poor spacing
            result=a*2
        else:
              result = b+1 # Bad indentation
        unused_variable = "I am not used" # Flake8 should find this
        really_long_line = "This line is intentionally made very very very very very very very very very very very very long to exceed the typical line length limit."
        return result
    
    x =10
    y= 5 # Missing space around operator
    
    print (my_func( x,y )) # Extra space before parenthesis
    
    # Missing newline at end of file (Flake8 might warn)
    ```
    

## **1.10 Naming Conventions and Code Readability**

- **Objective:** Emphasize the critical role of clear, descriptive naming in writing understandable code.
    
- **Topics:**
    
    - PEP 8 Naming Conventions:
        
        - `lower_case_with_underscores` for functions, methods, variables (snake_case). Also for filenames.
            
        - `UPPER_CASE_WITH_UNDERSCORES` for constants.
            
        - `CapitalizedWords` (CamelCase or PascalCase) for classes.
            
        - `_leading_underscore`: Internal use/protected convention.
            
        - `__leading_double_underscore`: Name mangling for class attributes.
            
        - `__leading_and_trailing_double_underscore__`: "Magic" objects or attributes (dunders).
            
    - Choosing descriptive names: Avoid single letters (except simple loop counters), abbreviations, ambiguity. Name reflects purpose.
        
    - Function/Method Naming: Often verbs or verb phrases (e.g., `calculate_total`, `is_valid`).
        
    - Variable Naming: Often nouns or noun phrases (e.g., `user_name`, `total_count`).
        
    - Boolean variables/functions: Often start with `is_`, `has_`, `should_` (e.g., `is_empty`, `has_permission`).
        
    - Writing clear comments: Explain _why_, not _what_ (if the code is already clear). Document complex logic or assumptions. Docstrings for functions/classes/modules.