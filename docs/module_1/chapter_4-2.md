## Module 1: Advanced Python & Best Practices

### Chapter 4: Project - Refactoring & Enhancing an Existing Python Application

**Estimated Time:** 20 hours

**Goal:** To practically apply Python best practices, refactoring techniques, testing strategies, and feature enhancement to an existing codebase. This chapter uses the "Data Processor" project defined in `dev_course_module1_project_v1`.

**Project Overview (Recap from `dev_course_module1_project_v1`):**

You will start with the `data_processor_v0.py` script. This script reads employee data from a CSV, calculates total salary, and finds the highest earner. Your main objectives are to:

1. Refactor the existing code for clarity, efficiency, and Pythonic style.
    
2. Extend its functionality with new features (average salary per department) and robustness (error handling, logging).
    
3. Write unit tests for the core logic.
    

**4.1: Project Setup: Understanding the Provided Codebase, Setting up Environment**

- **Objective:** Familiarize yourself with the initial project code and set up your development environment.
    
- **Tasks:**
    
    1. Retrieve the Initial Code (`data_processor_v0.py`) and the sample `employees.csv` file.
        
    2. Create a dedicated Project Directory.
        
    3. Set up and activate a Virtual Environment for the project.
        
    4. Run the `data_processor_v0.py` script with the sample CSV to observe its current behavior and output.
        
    5. Thoroughly review `data_processor_v0.py` to understand its current functionality, data flow, and structure.
        

**4.2: Code Analysis and Refactoring Strategy**

- **Objective:** Analyze `data_processor_v0.py` to identify areas for improvement based on Chapter 1 concepts and devise a refactoring strategy.
    
- **Tasks:**
    
    1. Evaluate the script's current file handling mechanism. How can it be made more robust, especially concerning resource cleanup in case of errors?
        
    2. Assess the current data structure used for employee records. Is it optimal for clarity and ease of access? Consider alternatives.
        
    3. Analyze the script's iteration and data processing logic. Are there opportunities for improved efficiency or more Pythonic approaches?
        
    4. Identify any "magic numbers" or hardcoded indices used for data access. How can these be improved for better readability and maintainability?
        
    5. Review the script's error handling. Is it specific enough? How are different types of errors (e.g., file not found, data conversion errors, missing data) currently managed, and how could this be improved?
        
    6. Examine the code for adherence to PEP 8 style guidelines and overall Pythonic style.
        
    7. Consider if the main processing function has too many responsibilities. Could it benefit from decomposition into smaller, more focused functions or by introducing classes?
        

**4.3: Implementing Core Refactoring**

- **Objective:** Implement foundational refactoring changes to improve the codebase's structure, readability, and resource management.
    
- **Tasks:**
    
    1. Refactor the file I/O to use a context manager, ensuring files are properly closed.
        
    2. Implement a more structured data representation for individual employee records (e.g., using dictionaries, namedtuples, or a custom class).
        
    3. Modify the data reading and processing logic to operate more directly on the data stream (e.g., process rows as they are read from the CSV reader) and use the new structured data representation.
        
    4. Replace indexed access to data fields with named access based on your chosen data structure.
        

**4.4: Enhancing Functionality and Robustness**

- **Objective:** Extend the script with new features and make it more robust.
    
- **Tasks:**
    
    1. Implement functionality to calculate the average salary for each department. This will require grouping or aggregating data by department.
        
    2. Incorporate specific error handling for various potential issues, such as:
        
        - `FileNotFoundError` if the input CSV is not found.
            
        - Errors during data conversion (e.g., non-numeric salary).
            
        - Rows with an incorrect number of columns or missing essential data.
            
        - Empty files or files containing only a header row.
            
        - Potential division by zero when calculating averages if a department has no valid salary entries.
            
    3. Integrate the `logging` module to record key events, such as the start and end of processing, files being processed, errors encountered, and rows skipped due to issues. Define a clear logging format and appropriate log levels.
        
    4. Define a custom exception class (e.g., `DataProcessingError`) for application-specific errors that are not covered by built-in exceptions.
        

**4.5: Writing Unit Tests**

- **Objective:** Develop a suite of unit tests to ensure the correctness of the refactored and extended data processing logic.
    
- **Tasks:**
    
    1. Set up a testing environment using `unittest` or `pytest`. Create a `tests` directory and test files.
        
    2. Write unit tests for the original core functionalities:
        
        - Total salary calculation.
            
        - Identification of the highest earner.
            
    3. Write unit tests for the new functionality:
        
        - Average salary per department calculation.
            
    4. Develop strategies for testing logic that involves file I/O. This might involve mocking `open` and `csv.reader` or refactoring parts of your code to accept iterable data directly.
        
    5. Create tests for various edge cases and error conditions, including:
        
        - Processing an empty CSV file.
            
        - Processing a CSV file with only a header row.
            
        - Processing CSV files containing rows with invalid data (e.g., non-numeric salary, missing columns).
            
        - File not found scenarios.
            
    6. Ensure your tests cover different valid data scenarios to verify the accuracy of calculations.
        

**4.6: Code Polishing and Finalization**

- **Objective:** Ensure the final codebase is clean, well-documented, and adheres to Python best practices.
    
- **Tasks:**
    
    1. Perform a thorough PEP 8 compliance check using a linter (e.g., `flake8`).
        
    2. Use an auto-formatter (e.g., `black`) to ensure consistent code style.
        
    3. Review and ensure all functions, classes, and modules have clear and comprehensive docstrings.
        
    4. Add inline comments to explain any complex or non-obvious sections of code.
        
    5. Create a `requirements.txt` file listing any external dependencies (e.g., `pytest`, `pytest-mock` if used).
        
