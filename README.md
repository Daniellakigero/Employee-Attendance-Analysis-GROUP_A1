# Employee-Attendance-Analysis-GROUP_A1

### Employee Attendance Analysis Procedure

## Description
This PL/SQL procedure calculates and displays attendance statistics for employees based on the data from two tables, `employees` and `attendance`. It calculates the total number of days an employee was present or absent in a specified month and computes the attendance percentage.

## Database Schema
- **Employees Table**
  - `employee_id` (NUMBER, PRIMARY KEY)
  - `first_name` (VARCHAR2)
  - `last_name` (VARCHAR2)
  
- **Attendance Table**
  - `attendance_id` (NUMBER, PRIMARY KEY)
  - `employee_id` (NUMBER, FOREIGN KEY REFERENCES `employees(employee_id)`)
  - `attendance_date` (DATE)
  - `status` (VARCHAR2) - Values: 'Present' or 'Absent'

## Procedure Parameters
- **p_month**: The month for which attendance should be analyzed.
- **p_year**: The year for which attendance should be analyzed.

## Outputs
For each employee, the procedure outputs:
- Full name (first and last name).
- Total presents.
- Total absents.
- Attendance percentage for the specified month.

## Exception Handling
- **NO_DATA_FOUND**: If no attendance records exist for the specified month, an informative message is displayed.
- **OTHERS**: Catches all other errors and displays a generic error message.
## SQL Statements for Table Creation
                       -- Creating the Employees table
                                CREATE TABLE employees (
                                    employee_id NUMBER PRIMARY KEY,            -- Unique identifier for each employee
                                    first_name VARCHAR2(50),                   -- Employee's first name
                                    last_name VARCHAR2(50)                     -- Employee's last name
                                );
                                
                       -- Creating the Attendance table
                                CREATE TABLE attendance (
                                    attendance_id NUMBER PRIMARY KEY,            -- Unique identifier for each attendance record
                                    employee_id NUMBER,                          -- Foreign key referencing employee_id in employees table
                                    attendance_date DATE,                        -- Date of attendance
                                    status VARCHAR2(10),                         -- Attendance status: 'Present' or 'Absent'
                                    CONSTRAINT fk_employee FOREIGN KEY (employee_id) 
                                        REFERENCES employees(employee_id)        -- Enforces relationship to employees table
                                );
                                
                      -- Adding a check constraint to enforce values 'Present' or 'Absent' in the status column
                                ALTER TABLE attendance
                                ADD CONSTRAINT chk_status CHECK (status IN ('Present', 'Absent'));

## PL/SQL Code with Comments

                      -- PL/SQL Procedure to calculate and display employee attendance statistics
                                
                                CREATE OR REPLACE PROCEDURE calculate_employee_attendance (
                                    p_month IN NUMBER,     -- Input parameter: the month for attendance calculation (e.g., 10 for October)
                                    p_year IN NUMBER       -- Input parameter: the year for attendance calculation (e.g., 2024)
                                ) AS
                                    -- Variable declarations to store employee data, attendance counts, and percentage
                                    v_employee_id EMPLOYEES.employee_id%TYPE;     -- Variable to hold the employee ID
                                    v_first_name EMPLOYEES.first_name%TYPE;       -- Variable to hold the employee's first name
                                    v_last_name EMPLOYEES.last_name%TYPE;         -- Variable to hold the employee's last name
                                    v_total_presents NUMBER := 0;                 -- Counter for the number of days the employee was present
                                    v_total_absents NUMBER := 0;                  -- Counter for the number of days the employee was absent
                                    v_attendance_percentage NUMBER;               -- Variable to store calculated attendance percentage
                                    v_days_in_month NUMBER;                       -- Variable to hold the total number of days in the specified month
                                    
                                    -- Cursor to fetch all employees from the employees table
                                    CURSOR emp_cursor IS
                                        SELECT employee_id, first_name, last_name
                                        FROM employees;                           -- Retrieves each employee's ID, first name, and last name
                                
                                BEGIN
                                    -- Calculate the number of days in the specified month by using the LAST_DAY function
                                    -- which returns the last day of the month for the specified date
                                    SELECT TO_NUMBER(TO_CHAR(LAST_DAY(TO_DATE(p_month || '-' || p_year, 'MM-YYYY')), 'DD'))
                                    INTO v_days_in_month
                                    FROM dual;
                                
                                    -- Loop through each employee in the employees table using the cursor
                                    FOR emp_rec IN emp_cursor LOOP
                                        -- Reset counters for each employee to start from zero
                                        v_total_presents := 0;
                                        v_total_absents := 0;
                                
                                        -- Nested loop to fetch attendance records for the specific employee in the specified month and year
                                        FOR attendance_rec IN (
                                            SELECT status
                                            FROM attendance
                                            WHERE employee_id = emp_rec.employee_id
                                              AND TO_CHAR(attendance_date, 'MM') = TO_CHAR(p_month, 'FM00')    -- Filters attendance records by month
                                              AND TO_CHAR(attendance_date, 'YYYY') = TO_CHAR(p_year)           -- Filters attendance records by year
                                        ) LOOP
                                            -- Check the status of each attendance record
                                            IF attendance_rec.status = 'Present' THEN
                                                v_total_presents := v_total_presents + 1;   -- Increment presents counter if status is 'Present'
                                            ELSIF attendance_rec.status = 'Absent' THEN
                                                v_total_absents := v_total_absents + 1;     -- Increment absents counter if status is 'Absent'
                                            END IF;
                                        END LOOP;
                                
                                        -- Calculate attendance percentage based on the total number of presents and days in the month
                                        v_attendance_percentage := (v_total_presents / v_days_in_month) * 100;
                                
                                        -- Display results for each employee using DBMS_OUTPUT
                                        DBMS_OUTPUT.PUT_LINE('Employee: ' || emp_rec.first_name || ' ' || emp_rec.last_name);  -- Display employee's name
                                        DBMS_OUTPUT.PUT_LINE('Total Presents: ' || v_total_presents);                           -- Display total presents
                                        DBMS_OUTPUT.PUT_LINE('Total Absents: ' || v_total_absents);                             -- Display total absents
                                        DBMS_OUTPUT.PUT_LINE('Attendance Percentage: ' || v_attendance_percentage || '%');      -- Display attendance percentage
                                        DBMS_OUTPUT.PUT_LINE('-------------------------');                                      -- Separator for readability
                                    END LOOP;
                                
                                EXCEPTION
                                    -- Exception handling for cases where no data is found or other errors occur
                                    WHEN NO_DATA_FOUND THEN
                                        DBMS_OUTPUT.PUT_LINE('No attendance records found for the specified month.');
                                    WHEN OTHERS THEN
                                        DBMS_OUTPUT.PUT_LINE('An error occurred: ' || SQLERRM);  -- Displays error message in case of unexpected errors
                                END calculate_employee_attendance;
                                /

## Usage
To execute the procedure:
```sql
BEGIN
    calculate_employee_attendance(10, 2024); -- Example for October 2024
END;
/
## Testing the procedure 
          -- Anonymous PL/SQL block to test the calculate_employee_attendance procedure
          BEGIN
              -- Call the procedure with a specific month and year (e.g., October 2024)
              calculate_employee_attendance(10, 2024);
          END;
          /


## DONE BY GROUP A1
MEMBERS : 
           1.  KIGERO KANYANA DANIELLA : 24652
           2.  ISIMBI  SYLVIA : 25342
           3. IRIZA GATERA MERVEILLE : 26266
           4. ANGE KIMBERLY MUKESHIMANA : 25580
           5.  ANGE MIREILLE IGIHOZO NZARAMBA : 25487
           6. GISUBIZO GISELE : 26188
           7.HARERIMANA GASPARD : 24960
           8. UMUTONI GAELLA : 25524
           9. IRAKARAMA BERGERAC : 24873
           10. NSENGIYUMVA EGIDE : 24622
## done
##This is looking great
