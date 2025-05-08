## Chapter 4 Project Solution: Refactored Data Processor

This document provides a potential solution to the Chapter 4 project, demonstrating refactoring, feature extension, and unit testing based on the concepts covered in Chapter 1.

**1. Refactored and Extended Code (`data_processor_refactored.py`)**

This version incorporates:

- Context managers for file handling.
    
- OOP using an `Employee` class (or `namedtuple` could also be used).
    
- Direct iteration over the CSV reader.
    
- Specific error handling (`try...except`).
    
- Logging using the `logging` module.
    
- Calculation of average salary per department.
    
- PEP 8 styling and clearer naming.
    

```
# --- Refactored Code ---
# (Save this as data_processor_refactored.py)

import csv
import logging
from collections import defaultdict, namedtuple

# Configure basic logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Using namedtuple for simple data structure
# Alternatively, a full class could be defined with methods if more complexity was needed
Employee = namedtuple("Employee", ["id", "name", "department", "salary"])

class DataProcessingError(Exception):
    """Custom exception for data processing issues."""
    pass

def parse_employee(row: list, row_num: int) -> Employee | None:
    """
    Parses a single row from the CSV into an Employee object.
    Handles potential conversion errors for a single row.
    Returns None if parsing fails.
    """
    try:
        # Check for expected number of columns
        if len(row) != 4:
            raise ValueError(f"Incorrect number of columns ({len(row)} instead of 4)")

        emp_id = int(row[0])
        name = row[1].strip()
        department = row[2].strip()
        salary = float(row[3])

        if not name or not department:
             raise ValueError("Missing name or department")
        if salary < 0:
             raise ValueError("Negative salary")

        return Employee(id=emp_id, name=name, department=department, salary=salary)

    except ValueError as e:
        logging.warning(f"Skipping row {row_num}: Invalid data format in row {row}. Error: {e}")
        return None
    except IndexError:
        logging.warning(f"Skipping row {row_num}: Missing columns in row {row}.")
        return None

def process_employee_data(filepath: str) -> dict:
    """
    Reads employee data from a CSV file, calculates aggregate statistics,
    and handles potential errors during processing.

    Args:
        filepath: The path to the employee CSV file.

    Returns:
        A dictionary containing processing results:
        {
            'total_salary': float,
            'highest_earner': Employee | None,
            'average_salary_per_department': dict[str, float],
            'processed_rows': int,
            'skipped_rows': int
        }

    Raises:
        FileNotFoundError: If the specified filepath does not exist.
        DataProcessingError: If the file is empty or contains only headers.
    """
    logging.info(f"Starting processing for file: {filepath}")

    total_salary = 0.0
    highest_salary = -1.0
    highest_earner = None
    department_stats = defaultdict(lambda: {"total_salary": 0.0, "count": 0})
    processed_rows = 0
    skipped_rows = 0

    try:
        # Use context manager for safe file handling
        with open(filepath, mode='r', newline='', encoding='utf-8') as file:
            reader = csv.reader(file)

            try:
                # Read header safely
                header = next(reader)
                logging.info(f"CSV Header: {header}")
                processed_rows += 1 # Count header row conceptually
            except StopIteration:
                raise DataProcessingError("File is empty.")

            # Iterate directly over reader using enumerate for row numbers (starting after header)
            for i, row in enumerate(reader, start=2): # Start row count from 2 for logging
                employee = parse_employee(row, i)

                if employee:
                    total_salary += employee.salary
                    department_stats[employee.department]["total_salary"] += employee.salary
                    department_stats[employee.department]["count"] += 1

                    if employee.salary > highest_salary:
                        highest_salary = employee.salary
                        highest_earner = employee
                    processed_rows += 1
                else:
                    skipped_rows += 1

    except FileNotFoundError:
        logging.error(f"Error: File not found at {filepath}")
        raise # Re-raise the exception after logging
    except Exception as e: # Catch other potential I/O errors
        logging.error(f"An unexpected error occurred during file processing: {e}")
        raise DataProcessingError(f"Failed to process file due to: {e}") from e

    if processed_rows <= 1 and skipped_rows == 0: # Only header was processed
         raise DataProcessingError("File contains only headers or is effectively empty.")

    # Calculate averages
    average_salary_per_department = {}
    for dept, stats in department_stats.items():
        if stats["count"] > 0:
            average_salary_per_department[dept] = round(stats["total_salary"] / stats["count"], 2)
        else:
             average_salary_per_department[dept] = 0.0 # Or handle as appropriate

    logging.info(f"Processing Complete. Processed data rows: {processed_rows-1}, Skipped rows: {skipped_rows}") # -1 for header

    results = {
        'total_salary': round(total_salary, 2),
        'highest_earner': highest_earner,
        'average_salary_per_department': average_salary_per_department,
        'processed_rows': processed_rows -1, # Exclude header
        'skipped_rows': skipped_rows
    }

    return results

# Example Usage (requires 'employees.csv')
if __name__ == "__main__":
    try:
        results = process_employee_data('employees.csv')
        print("\n--- Processing Results ---")
        print(f"Total Salary Expenditure: ${results['total_salary']:.2f}")
        if results['highest_earner']:
            print(f"Highest Earner: {results['highest_earner'].name} with ${results['highest_earner'].salary:.2f}")
        else:
            print("Highest Earner: Not found (no valid data).")
        print("Average Salary Per Department:")
        for dept, avg_salary in results['average_salary_per_department'].items():
            print(f"  - {dept}: ${avg_salary:.2f}")
        print(f"Processed Rows (data): {results['processed_rows']}")
        print(f"Skipped Rows: {results['skipped_rows']}")
    except (FileNotFoundError, DataProcessingError) as e:
        print(f"\nProcessing failed: {e}")
    except Exception as e:
        print(f"\nAn unexpected error occurred: {e}")

```

**2. Unit Tests (`test_data_processor.py`)**

This example uses `pytest` and `unittest.mock`. You would need to install them (`pip install pytest pytest-mock`).

```
# --- Unit Tests ---
# (Save this as test_data_processor.py in a 'tests' subdirectory)

import pytest
from unittest.mock import mock_open, patch
import csv
import io  # Used for mocking file content

# Import the refactored function and Employee namedtuple
# Assuming the refactored file is in the parent directory or accessible via PYTHONPATH
# Adjust the import path as necessary for your project structure
from data_processor_refactored import process_employee_data, Employee, DataProcessingError

# --- Test Data ---
VALID_CSV_DATA = """ID,Name,Department,Salary
1,Alice,Engineering,90000
2,Bob,Sales,80000
3,Charlie,Engineering,95000
4,David,HR,70000
5,Eve,Sales,82000
"""

EDGE_CASE_CSV_DATA_INVALID_SALARY = """ID,Name,Department,Salary
1,Alice,Engineering,90000
2,Bob,Sales,Eighty Thousand
3,Charlie,Engineering,95000
"""

EDGE_CASE_CSV_DATA_MISSING_COLUMN = """ID,Name,Department,Salary
1,Alice,Engineering,90000
2,Bob,Sales
3,Charlie,Engineering,95000
"""

EMPTY_CSV_DATA = ""
HEADER_ONLY_CSV_DATA = "ID,Name,Department,Salary\n"

# --- Test Fixtures (Optional but good practice with pytest) ---
@pytest.fixture
def mock_csv_file():
    """Fixture to mock the open() call and csv.reader."""
    def _mock_file(content):
        # Use io.StringIO to simulate a file object from a string
        mock_file_obj = io.StringIO(content)
        # Patch 'open' in the context of the module being tested
        m_open = mock_open(read_data=content)
        # We need to make the mock file object behave like an iterator for csv.reader
        m_open.return_value.__iter__ = lambda self: iter(self.readline, '')
        m_open.return_value.__next__ = lambda self: next(iter(self.readline, ''))
        return m_open
    return _mock_file

# --- Test Cases ---

def test_process_valid_data(mock_csv_file):
    """Tests processing with a standard valid CSV."""
    mock_opener = mock_csv_file(VALID_CSV_DATA)
    with patch('builtins.open', mock_opener):
        results = process_employee_data('dummy_path.csv')

        assert results['total_salary'] == 417000.00
        assert results['highest_earner'] == Employee(id=3, name='Charlie', department='Engineering', salary=95000.0)
        assert results['average_salary_per_department'] == {
            'Engineering': 92500.00,
            'Sales': 81000.00,
            'HR': 70000.00
        }
        assert results['processed_rows'] == 5
        assert results['skipped_rows'] == 0

def test_process_invalid_salary(mock_csv_file):
    """Tests handling of rows with non-numeric salary."""
    mock_opener = mock_csv_file(EDGE_CASE_CSV_DATA_INVALID_SALARY)
    with patch('builtins.open', mock_opener):
        results = process_employee_data('dummy_path.csv')

        assert results['total_salary'] == 185000.00 # Only Alice and Charlie
        assert results['highest_earner'] == Employee(id=3, name='Charlie', department='Engineering', salary=95000.0)
        assert results['average_salary_per_department'] == {
            'Engineering': 92500.00 # Average of Alice and Charlie
            # Sales department has no valid entries
        }
        assert results['processed_rows'] == 2
        assert results['skipped_rows'] == 1 # Bob's row skipped

def test_process_missing_column(mock_csv_file):
    """Tests handling of rows with missing columns."""
    mock_opener = mock_csv_file(EDGE_CASE_CSV_DATA_MISSING_COLUMN)
    with patch('builtins.open', mock_opener):
        results = process_employee_data('dummy_path.csv')

        assert results['total_salary'] == 185000.00 # Alice and Charlie
        assert results['highest_earner'] == Employee(id=3, name='Charlie', department='Engineering', salary=95000.0)
        assert results['average_salary_per_department'] == {
            'Engineering': 92500.00
        }
        assert results['processed_rows'] == 2
        assert results['skipped_rows'] == 1 # Bob's row skipped

def test_file_not_found():
    """Tests FileNotFoundError handling."""
    # No need to mock open here, we want the actual error
    with pytest.raises(FileNotFoundError):
        process_employee_data('non_existent_file.csv')

def test_empty_file(mock_csv_file):
    """Tests processing an empty file."""
    mock_opener = mock_csv_file(EMPTY_CSV_DATA)
    with patch('builtins.open', mock_opener):
        with pytest.raises(DataProcessingError, match="File is empty"):
            process_employee_data('dummy_path.csv')

def test_header_only_file(mock_csv_file):
    """Tests processing a file with only a header."""
    mock_opener = mock_csv_file(HEADER_ONLY_CSV_DATA)
    with patch('builtins.open', mock_opener):
         with pytest.raises(DataProcessingError, match="File contains only headers"):
            process_employee_data('dummy_path.csv')

# Add more tests:
# - Test division by zero if a department has count 0 for average calculation
# - Test different data types in columns if applicable
# - Test negative salaries if the validation logic changes
```

**Setup & Running Tests:**

1. Save the refactored code as `data_processor_refactored.py`.
    
2. Create a directory named `tests`.
    
3. Save the test code as `test_data_processor.py` inside the `tests` directory.
    
4. Make sure your virtual environment is active.
    
5. Install necessary packages: `pip install pytest pytest-mock`
    
6. Navigate to the _parent directory_ of your `tests` folder in the terminal.
    
7. Run pytest: `pytest`
    

This solution provides a solid refactoring incorporating many Chapter 1 concepts and includes basic unit tests to verify functionality and handle edge cases.