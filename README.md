# Employee-Attendance-Analysis-GROUP_A1

## Employee Management System

## Overview

This project is an Employee Management System that allows you to manage employee records in a company database. The system is built using PL/SQL and includes:

# A function to retrieve an employee’s salary based on their ID.
# A procedure to update an employee's salary by a given percentage increase.

# Requirements

## Function
- Name: get_employee_salary
- Parameters: employee_id (number)
- Returns: The employee’s salary.

## Procedure
- Name: update_employee_salary
- Parameters:
- employee_id (number)
- percentage_increase (number)
- Functionality: Updates the employee’s salary by the given percentage. If the - employee does not exist, it raises an exception.

# Implementation

## Step 1: Create the Employees Table
### The employees table stores the details of each employee, including their ID, first name, last name, and salary.

              -- Creating a table named employees with columns for ID, first name, last name, and salary
                CREATE TABLE employees (
                    employee_id NUMBER PRIMARY KEY, -- Unique identifier for each employee
                    first_name VARCHAR2(50),        -- Stores the employee's first name
                    last_name VARCHAR2(50),         -- Stores the employee's last name
                    salary NUMBER(10, 2)            -- Stores the employee's salary with 2 decimal precision
                );

## Step 2: Create the get_employee_salary Function
### This function retrieves the salary of an employee based on their employee ID. If the employee ID does not exist, it returns NULL.

             -- Function to retrieve an employee's salary based on their employee ID
                CREATE OR REPLACE FUNCTION get_employee_salary (p_employee_id NUMBER) -- Input parameter for employee ID
                RETURN NUMBER -- Specifies that the function returns a NUMBER
                IS
                    v_salary NUMBER; -- Variable to store the retrieved salary
                BEGIN
                    -- Query to select the employee's salary
                    SELECT salary INTO v_salary
                    FROM employees
                    WHERE employee_id = p_employee_id;
                    
                    RETURN v_salary; -- Return the retrieved salary
                EXCEPTION
                    WHEN NO_DATA_FOUND THEN -- Exception handling for non-existent employee ID
                        RETURN NULL; -- Returns NULL if employee ID is not found
                END;
                /

## Step 3: Create the update_employee_salary Procedure
### This procedure updates an employee's salary by a specified percentage increase. If the employee ID does not exist, it raises a custom error.  

                -- Procedure to update an employee's salary by a given percentage
                CREATE OR REPLACE PROCEDURE update_employee_salary (
                    p_employee_id NUMBER, -- Input parameter for employee ID
                    p_percentage_increase NUMBER -- Input parameter for percentage increase
                )
                IS
                    v_salary NUMBER; -- Variable to store the current salary of the employee
                BEGIN
                    -- Query to retrieve the current salary of the employee
                    SELECT salary INTO v_salary
                    FROM employees
                    WHERE employee_id = p_employee_id;
                    
                    -- Calculate the new salary by applying the percentage increase
                    v_salary := v_salary * (1 + p_percentage_increase / 100);
                    
                    -- Update the salary in the employees table with the calculated new salary
                    UPDATE employees
                    SET salary = v_salary
                    WHERE employee_id = p_employee_id;
                    
                    COMMIT; -- Save the changes
                EXCEPTION
                    WHEN NO_DATA_FOUND THEN -- Exception handling for non-existent employee ID
                        -- Raise a custom error if the employee ID is not found
                        RAISE_APPLICATION_ERROR(-20001, 'Employee does not exist');
                END;
                /


## Step 4: Testing the Function and Procedure
### To verify the functionality of the function and procedure, we insert sample data into the employees table and perform tests.

# Insert Sample Data
## Adding two sample employees to test the function and procedure.

            -- Insert sample employee data
            INSERT INTO employees (employee_id, first_name, last_name, salary) 
            VALUES (1, 'John', 'Doe', 50000); -- Employee with ID 1

            INSERT INTO employees (employee_id, first_name, last_name, salary) 
            VALUES (2, 'Jane', 'Smith', 60000); -- Employee with ID 2

            COMMIT; -- Save the inserted data

## Test the get_employee_salary Function
### This test retrieves and displays the salary of an employee with ID 1.

                -- Testing the get_employee_salary function
                DECLARE
                    v_salary NUMBER; -- Variable to store the function output
                BEGIN
                    -- Retrieve salary for employee ID 1
                    v_salary := get_employee_salary(1);
                    
                    -- Output the result
                    DBMS_OUTPUT.PUT_LINE('Salary of employee 1: ' || v_salary);
                END;
                /

## Test the update_employee_salary Procedure
### This test updates the salary of an employee with ID 1 by a 10% increase and then retrieves the updated salary.

                -- Testing the update_employee_salary procedure
                BEGIN
                    -- Update salary by 10% for employee ID 1
                    update_employee_salary(1, 10);
                    
                    -- Display the updated salary
                    DBMS_OUTPUT.PUT_LINE('Updated salary of employee 1: ' || get_employee_salary(1));
                END;
                /

# Explanation of Code Structure
## Table Creation: Sets up the employees table to store employee data.
## Function (get_employee_salary): Fetches an employee's salary based on their ID. If the ID does not exist, it returns NULL.
## Procedure (update_employee_salary): Updates the salary by a given percentage increase. If the employee ID is invalid, it raises an error.
## Testing: Adds sample data to the table and verifies that the function and procedure work as expected.


# Requirements for Running
### Oracle SQL Developer or another Oracle database client.
### Access to Oracle Database for executing PL/SQL.
# Notes
## Remember to enable DBMS_OUTPUT in your SQL client to view the output of DBMS_OUTPUT.PUT_LINE.








# GROUP NAME : A1
## MEMBERS : 
           1.  KIGERO KANYANA DANIELLA : 24652
           2.  ISIMBI  SYLVIA : 25342
           3. IRIZA GATERA MERVEILLE : 26266
           4. ANGE KIMBERLY MUKESHIMANA : 25580
           5.  ANGE MIREILLE IGIHOZO NZARAMBA : 25487
           6. GISUBIZO GISELE : 26188
           7.HARERIMANA GASPARD : 24960
           8. UMUTONI GAELLA : 25524
