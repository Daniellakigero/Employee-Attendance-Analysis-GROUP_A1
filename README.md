# Employee-Attendance-Analysis-GROUP_A1

# Employee Attendance Analysis Procedure

## Description
This PL/SQL procedure calculates and displays attendance statistics for employees based on data from two tables, `employees` and `attendance`. It calculates the total number of days an employee was present or absent in a specified month and computes the attendance percentage.

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
## PL/SQL Code with Comments

                    -- PL/SQL Procedure to calculate and display employee attendance statistics

                    CREATE OR REPLACE PROCEDURE calculate_employee_attendance (
                        p_month IN NUMBER,
                        p_year IN NUMBER
                    ) AS
                        -- Variable declarations
                        v_employee_id EMPLOYEES.employee_id%TYPE;
                        v_first_name EMPLOYEES.first_name%TYPE;
                        v_last_name EMPLOYEES.last_name%TYPE;
                        v_total_presents NUMBER := 0;
                        v_total_absents NUMBER := 0;
                        v_attendance_percentage NUMBER;
                        v_days_in_month NUMBER;
                        
                        -- Cursor to fetch all employees
                        CURSOR emp_cursor IS
                            SELECT employee_id, first_name, last_name
                            FROM employees;

                    BEGIN
                        -- Calculate the number of days in the specified month
                        SELECT TO_NUMBER(TO_CHAR(LAST_DAY(TO_DATE(p_month || '-' || p_year, 'MM-YYYY')), 'DD'))
                        INTO v_days_in_month
                        FROM dual;

                        -- Loop through each employee
                        FOR emp_rec IN emp_cursor LOOP
                            v_total_presents := 0;
                            v_total_absents := 0;

                            -- Fetch attendance records for the employee in the specified month
                            FOR attendance_rec IN (
                                SELECT status
                                FROM attendance
                                WHERE employee_id = emp_rec.employee_id
                                AND TO_CHAR(attendance_date, 'MM') = TO_CHAR(p_month, 'FM00')
                                AND TO_CHAR(attendance_date, 'YYYY') = TO_CHAR(p_year)
                            ) LOOP
                                -- Count presents and absents
                                IF attendance_rec.status = 'Present' THEN
                                    v_total_presents := v_total_presents + 1;
                                ELSIF attendance_rec.status = 'Absent' THEN
                                    v_total_absents := v_total_absents + 1;
                                END IF;
                            END LOOP;

                            -- Calculate attendance percentage
                            v_attendance_percentage := (v_total_presents / v_days_in_month) * 100;

                            -- Display result
                            DBMS_OUTPUT.PUT_LINE('Employee: ' || emp_rec.first_name || ' ' || emp_rec.last_name);
                            DBMS_OUTPUT.PUT_LINE('Total Presents: ' || v_total_presents);
                            DBMS_OUTPUT.PUT_LINE('Total Absents: ' || v_total_absents);
                            DBMS_OUTPUT.PUT_LINE('Attendance Percentage: ' || v_attendance_percentage || '%');
                            DBMS_OUTPUT.PUT_LINE('-------------------------');
                        END LOOP;

                    EXCEPTION
                        WHEN NO_DATA_FOUND THEN
                            DBMS_OUTPUT.PUT_LINE('No attendance records found for the specified month.');
                        WHEN OTHERS THEN
                            DBMS_OUTPUT.PUT_LINE('An error occurred: ' || SQLERRM);
                    END calculate_employee_attendance;
                    /

## Usage
To execute the procedure:
```sql
BEGIN
    calculate_employee_attendance(10, 2024); -- Example for October 2024
END;
/
