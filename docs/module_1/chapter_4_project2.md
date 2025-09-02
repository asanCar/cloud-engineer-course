# Chapter 4: Project - Refactoring & Enhancing a Log File Analyzer

**Estimated Time:** 20 hours

**Goal:** To apply Python best practices, OOP principles, error handling, file processing techniques, and testing strategies to refactor, extend, and test a basic log file analysis script.

**Scenario:**

You are provided with a rudimentary Python script designed to perform a very basic analysis of web server log files. A web server log file typically contains one line per request, with details like the client's IP address, timestamp, the HTTP request made (method, path, protocol), the HTTP status code returned, and the size of the response.

The initial script only counts the total number of requests and the number of "404 Not Found" errors. It's inefficient, not robust, and lacks proper structure and testing.

**Initial Code (`log_analyzer_v0.py`):**

```python
# --- Initial Code for Log Analyzer Project ---
# (Save this as log_analyzer_v0.py)

import re # Though not effectively used yet

def analyze_server_log(log_file_path):
    """
    Performs a very basic analysis of a server log file.
    Counts total requests and 404 errors.
    """
    total_requests = 0
    not_found_errors = 0
    
    # Problematic: Manual file opening and closing
    try:
        log_file_handle = open(log_file_path, 'r')
    except FileNotFoundError:
        print(f"Error: Log file '{log_file_path}' not found.")
        return None, None # Not ideal error handling

    for line in log_file_handle:
        total_requests += 1
        # Very simplistic and potentially inaccurate check for 404 errors
        if " 404 " in line:
            not_found_errors += 1
            
    log_file_handle.close() # Must be closed manually

    print(f"--- Log Analysis Report for: {log_file_path} ---")
    print(f"Total Requests Processed: {total_requests}")
    print(f"Number of 404 (Not Found) Errors: {not_found_errors}")
    
    return total_requests, not_found_errors

# Sample log_file.txt content (Common Log Format - CLF like)
# 127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] "GET /apache_pb.gif HTTP/1.0" 200 2326
# 10.0.0.5 - - [10/Oct/2000:13:55:36 -0700] "GET /index.html HTTP/1.1" 200 5120
# 192.168.0.10 - user1 [10/Oct/2000:13:55:37 -0700] "POST /submit_form HTTP/1.1" 201 128
# 10.0.0.5 - - [10/Oct/2000:13:55:38 -0700] "GET /nonexistent_page.html HTTP/1.1" 404 200
# 127.0.0.1 - frank [10/Oct/2000:13:55:39 -0700] "GET /another_page.html HTTP/1.0" 200 1024
# 10.0.0.6 - - [10/Oct/2000:13:55:40 -0700] "GET /index.html HTTP/1.1" 200 5120
# 10.0.0.5 - - [10/Oct/2000:13:55:41 -0700] "GET /image.png HTTP/1.1" 404 150
# 10.0.0.5 - - [10/Oct/2000:14:35:41 -0700] "GET /another_request.html HTTP/1.1" 200 3000
```

**Sample `sample_log.txt` (for testing):**

```plain
127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] "GET /apache_pb.gif HTTP/1.0" 200 2326
10.0.0.5 - - [10/Oct/2000:13:55:36 -0700] "GET /index.html HTTP/1.1" 200 5120
192.168.0.10 - user1 [10/Oct/2000:13:55:37 -0700] "POST /submit_form HTTP/1.1" 201 128
10.0.0.5 - - [10/Oct/2000:13:55:38 -0700] "GET /nonexistent_page.html HTTP/1.1" 404 200
127.0.0.1 - frank [10/Oct/2000:13:55:39 -0700] "GET /another_page.html HTTP/1.0" 200 1024
10.0.0.6 - - [10/Oct/2000:13:55:40 -0700] "GET /index.html HTTP/1.1" 200 5120
10.0.0.5 - - [10/Oct/2000:13:55:41 -0700] "GET /image.png HTTP/1.1" 404 150
10.0.0.5 - - [10/Oct/2000:14:35:41 -0700] "GET /another_request.html HTTP/1.1" 200 3000
```

**Tasks:**

1. **Refactor the `analyze_server_log` function:**

    - Improve file handling for robustness and Pythonic style.

    - Implement more reliable parsing for log lines to extract key information (e.g., IP address, timestamp, status code, requested path).

    - Restructure the code for better readability and maintainability. Consider using helper functions or classes.

    - Apply Pythonic iteration techniques.

    - Ensure adherence to PEP 8 guidelines.

2. **Extend Core Functionality:**

    - **Count Requests per IP:** Calculate and display the number of requests originating from each unique IP address.

    - **Count Requests per HTTP Status Code:** Tally and display the occurrences of each HTTP status code (e.g., 200, 404, 500, etc.).

    - **Identify Top N Requested Paths:** Determine and display the top N (e.g., top 5) most frequently requested paths.

3. **Implement Robustness and Operational Features:**

    - **Robust Error Handling:** Implement specific error handling for malformed log lines (e.g., lines that don't match the expected format). Decide on a strategy: skip and log, or attempt partial parsing.

    - **Logging:** Integrate the `logging` module to record the analyzer's operations, such as when analysis starts/ends, files processed, and any errors or skipped lines.

    - **Custom Exceptions:** (Optional) Define custom exceptions for specific parsing or analysis errors.

4. **Implement User Session Identification (Interview-Style Challenge):**

    - **Define a Session:** A user session is defined as a sequence of requests from the _same IP address_ where the time between any two consecutive requests from that IP does _not exceed a specified inactivity timeout_ (e.g., 30 minutes).

    - **Task:** Calculate and report the total number of unique user sessions found in the log file.

    - This will require parsing timestamps accurately, grouping requests by IP, sorting them by time, and then iterating through each IP's requests to identify session boundaries based on the inactivity timeout.

5. **Implement Unit Tests:**

    - Develop a suite of unit tests using `unittest` or `pytest`.

    - Test the log line parsing logic thoroughly.

    - Test each statistical calculation (total requests, 404s, IP counts, status code counts, top paths, **and number of user sessions**).

    - Include tests for edge cases: empty log file, file with only malformed lines, files with various combinations of valid and invalid lines, logs where all requests from an IP form a single session, logs where requests from an IP form multiple sessions, logs with interleaved IPs.

    - Employ mocking techniques for file I/O to make tests independent of actual files.

This project will challenge you to apply a wide range of Python skills to a practical problem. Good luck!
