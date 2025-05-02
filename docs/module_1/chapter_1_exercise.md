# Chapter 1 Project: Refactoring and Enhancing a Data Processor

**Goal:** Apply the concepts learned in Chapter 1 (Pythonic code, OOP, error handling, context managers, decorators, testing) to refactor, extend, and test a simple data processing script.

**Scenario:**

You are given a Python script that reads employee data from a CSV file. Each row contains an employee ID, name, department, and salary. The script currently calculates the total salary expenditure and identifies the employee with the highest salary. However, the code is written in a somewhat procedural and less "Pythonic" style, lacks robust error handling, and has no tests.

**Initial Code (`data_processor_v0.py`):**

```
# --- Initial Code for Refactoring ---
# (Save this as data_processor_v0.py)

import csv

def process_employee_data(filepath):
    # Reads data, calculates total salary and finds highest earner

    file = open(filepath, 'r')
    reader = csv.reader(file)

    header = next(reader) # Skip header row

    total_salary = 0
    highest_salary = -1
    highest_earner_name = None

    employee_data = []
    for row in reader:
        employee_data.append(row)

    file.close()

    i = 0
    while i < len(employee_data):
        row = employee_data[i]
        try:
            emp_id = int(row[0])
            name = row[1]
            department = row[2]
            salary = float(row[3])

            total_salary = total_salary + salary

            if salary > highest_salary:
                highest_salary = salary
                highest_earner_name = name

        except Exception as e:
            print("Error processing row:", row, "Error:", e)
            # Potentially skips rows silently or with just a print

        i = i + 1

    print("Processing Complete.")
    print("Total Salary Expenditure:", total_salary)
    print("Highest Earner:", highest_earner_name, "with salary:", highest_salary)

    return total_salary, highest_earner_name

# Example Usage (requires a sample 'employees.csv' file)
# Create a file named 'employees.csv' with content like:
# ID,Name,Department,Salary
# 1,Alice,Engineering,90000
# 2,Bob,Sales,80000
# 3,Charlie,Engineering,95000
# 4,David,HR,70000

# total, highest_name = process_employee_data('employees.csv')
```

**Sample `employees.csv`:**

```
ID,Name,Department,Salary
1,Alice,Engineering,90000
2,Bob,Sales,80000
3,Charlie,Engineering,95000
4,David,HR,70000
5,Eve,Sales,82000
```

**Tasks:**

1. **Refactor the `process_employee_data` function** to improve its style, readability, and efficiency. Consider aspects like resource management, iteration, data handling, code structure, data access, and error handling.
    
2. **Extend the script's functionality** by adding robust error handling, logging capabilities, and a new calculation for average salary per department.
    
3. **Implement unit tests** for the core processing logic using `unittest` or `pytest`.
    

**Hints & Potential Solution Approaches:**

- **Task 1 (Refactoring):**
    
    - Use a `with` statement for file handling.
        
    - Iterate directly over the `csv.reader` object instead of creating an intermediate `employee_data` list.
        
    - Consider representing each row using a dictionary, `collections.namedtuple`, or a custom `Employee` class.
        
    - Access data fields by name (e.g., `row['Salary']` or `employee.salary`) instead of index (`row[3]`).
        
    - Look for opportunities to use list/generator comprehensions.
        
    - Apply PEP 8 naming (`snake_case`) and formatting (`black`).
        
- **Task 2 (Extending):**
    
    - Use `try...except` blocks to catch specific exceptions like `FileNotFoundError`, `ValueError`, `TypeError`, `IndexError`.
        
    - Implement a clear strategy for handling rows with errors (e.g., logging the error and skipping the row).
        
    - Use the `logging` module for informative messages (e.g., file processing start/end, errors encountered). A simple logging decorator could also be used.
        
    - To calculate average salary per department, use a dictionary to accumulate total salaries and counts for each department encountered during iteration.
        
- **Task 3 (Testing):**
    
    - Create test functions for `test_total_salary`, `test_highest_earner`, `test_average_salary_per_department`.
        
    - Use `unittest.mock` or `pytest` fixtures/mocks to simulate file reading or pass in sample data structures (lists of lists, lists of dicts, etc.) directly to your refactored processing logic.
        
    - Include tests for edge cases: empty input, input with only headers, rows with missing data, rows with non-numeric salaries.
        

**Concepts to Apply from Chapter 1:**

- Context Managers (`with` statement)
    
- Pythonic Loops (`for`, list/generator comprehensions, `enumerate`, `zip` if applicable)
    
- Functions (`*args`, `**kwargs` might be useful if refactoring)
    
- OOP (Consider using a class for `Employee` or the processor itself)
    
- Dunder Methods (If creating classes, implement `__init__`, `__repr__`, etc.)
    
- Properties (If using classes with controlled attribute access)
    
- Decorators (Optional, but useful for logging or timing)
    
- Generators/Iterators (Reading CSV row by row is inherently iterator-based)
    
- Advanced Error Handling (`try...except SpecificError`, `else`, `finally`, custom exceptions if desired)
    
- PEP 8 Styling & Linters/Formatters (`flake8`, `black`)
    
- Clear Naming Conventions
    
