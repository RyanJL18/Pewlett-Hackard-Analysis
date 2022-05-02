# Pewlett-Hackard-Analysis
## Understanding the Project
In this project, we were tasked with looking at large data sets involving the employees of Pewlett Hackard. Our initial look into this data was to find the number of employees that are a part of the baby boomer generation, and determine how many roles would need to be filled in relation to the number of employees that could potentially retire. Upon looking through all the data initially, we were tasked with these two additional assignments as a challenge: 1) determine the number of retiring employees per title, and 2) identify employees that are eligible for a mentorship program. Using SQL queries, we were able to identify and create new data with the requested information.
## Results
**Deliverable 1: The Number of Retiring Employees by Title**
The query below was executed to create a table that consisted of Retirement Titles for employees who are born between January 1, 1952 and December 31, 1955. This table was then exported to a CSV file.
```
SELECT e.emp_no,
	   e.first_name,
	   e.last_name,
	   t.title,
	   t.from_date,
	   t.to_date
INTO retirement_titles
FROM employees AS e
INNER JOIN titles AS t
ON (e.emp_no = t.emp_no)

WHERE (e.birth_date BETWEEN '1952-01-01' AND '1955-12-31')
ORDER BY emp_no ASC;
```
Following creating the table titled "retirement_titles" we created a new query that piled the names of Unique titles that contained the employee information as well. We were forced to use the (DISTINCT ON) statement to retrieve the first occurrence of the employee number for each set of roles.
```
-- Use Distinct with Orderby to remove duplicate rows
SELECT DISTINCT ON (emp_no) emp_no,
first_name,
last_name,
title
INTO unique_titles
FROM retirement_titles
ORDER BY emp_no, title DESC;
```
The following image is the table output based on this query.

![Unique_Titles_Table.png](https://github.com/RyanJL18/Pewlett-Hackard-Analysis/blob/main/Challenge%20Images/unique_titles_table.png)

Finally, we had to write a third query to retrieve the number of employees by their most recent job title who are about to retire. This helped us determine exactly how many employees were leaving based on any given role.
```
SELECT COUNT(ut.emp_no),
ut.title
INTO retiring_titles
FROM unique_titles as ut
GROUP BY title
ORDER BY COUNT(title) DESC;
```

![Retireing_Role_Count.png](https://github.com/RyanJL18/Pewlett-Hackard-Analysis/blob/main/Challenge%20Images/Retiring_titles_table.png)

After exporting all of these tables to CSV files, we completed the requirements for Deliverable 1, determining that "Staff" and "Senior Engineers" were the two roles with the largest number of employees reaching retirement.

---
Here is what we have determined so far:
- Pewlett Hackard will need to make changes to become very aggressive in their hiring tactics to fill the large number of empty roles as eligible employees begin taking advantage of their retirement opportunity.
- The roles with the largest number of employees leaving is Staff with 32,452 employees. 
- The second highest number of employees potentially retiring is Senior Engineers with 29,415 employees.
---

**Deliverable 2: The Employees Eligible for the Mentorship Program**
This portion of the project is used to determine candidates from the company that are eligible for a mentorship program. The birth date range that is applicable for these candidates are January 1, 1965 to December 31, 1965.

---
This code below was used to create the mentorship eligibility table for Deliverable 2.

```
SELECT DISTINCT ON(e.emp_no) e.emp_no, 
    e.first_name, 
    e.last_name, 
    e.birth_date,
    de.from_date,
    de.to_date,
    t.title
INTO mentorship_eligibility
FROM employees as e
Left outer Join dept_emp as de
ON (e.emp_no = de.emp_no)
Left outer Join titles as t
ON (e.emp_no=t.emp_no)
WHERE (e.birth_date BETWEEN '1965-01-01' AND '1965-12-31')
ORDER BY e.emp_no;
```
*In the process of running this code, I was met with an error multiple times. The error stated this "No operator matches the given name and argument type(s). You might need to add explicit type casts." and notated line "ON (e.emp_no = de.emp_no)" as the error. With this information I was able to determine that in the original creation of table dept_emp, I had created the emp_no column as a VARCHAR (string) value as opposed to an integer value, leaving SQL unable to join the columns as they both contained different data types. To resolve this I completed a DROP TABLE for the dept_emp table and recreated it containing the correct data type.*

The table below is the final result from the above code, giving us a list of eligible employees for the Pewlett Hackard Mentorship program.

![Mentorship_Eligibility.png](https://github.com/RyanJL18/Pewlett-Hackard-Analysis/blob/main/Challenge%20Images/mentorship_eligibility.png)

---

## Summary

Pewlett Hackard may find themselves in quite a difficult situation if their hiring tactics do not become more aggressive to meet the demand of the emptying roles at their company. In total, Pewlett Hackard will need to fill 90,398 roles as the "Silver Tsunami" of baby boomers begins to leave for retirement. Given the large number of retiring employees and the much smaller number of 1,940 employees who are eligible for mentorship, I do not believe Pewlett Hackard is equipped to handle the number of new hires they would require to meet their needs. I would recommend finding new employees as soon as possible and possibly creating a larger pool of mentors to train as new employees are hired.
