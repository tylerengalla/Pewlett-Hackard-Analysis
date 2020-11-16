# Pewlett-Hackard-Analysis

# OVERVIEW
We've been asked to determine the number of retiring employees per title, and identify employees who are eligible to participate in a mentorship program we have created as these employees look to retire. In order to achieve this we created a database using PostgreSQL, created and imported employee data into tables, queried those tables to create new views, and filter/ join tables where needed to get us the desired data in the format we're looking for. 

# RESULTS

- We filtered our employees by birth date to identify who is a part of the upcoming "silver tsunami" or retirement wave. The query we used to achieve this is: 

SELECT 
	e.emp_no,
	e.first_name,
	e.last_name,
	ti.title,
	ti.from_date,
	ti.to_date
INTO retirement_titles
FROM employees as e
LEFT JOIN titles as ti
	ON (e.emp_no = ti.emp_no)
WHERE (e.birth_date BETWEEN '1952-01-01' AND '1955-12-31')
ORDER BY emp_no ASC, to_date DESC; 

This gave us a list of employees that will be retiring.

![Retiring Employees](/images/retirement_titles.png)

The problem here is that our query gave us each title an employee held during their tenure, so if they were promoted, now we have employee duplicates. We just want their most recent title they're holding so we can understand what roles will be set to retire. 

In order to achieve this we need another query: 

SELECT DISTINCT ON (emp_no)
	emp_no,
	first_name,
	last_name,
	title
INTO unique_titles
FROM retirement_titles 
ORDER BY emp_no ASC;

Then to give us an idea of the amount of postions that will need to be filled  we group them by their title and use COUNT function. 

SELECT COUNT(ut.title), ut.title
INTO retiring_titles
FROM unique_titles as ut
GROUP BY ut.title
ORDER BY COUNT(ut.title) DESC;

![Overview of Titles Set to Retire](/images/retiring_titles.png)

- We can see that Senior Engineers and Senior Staff will be our biggest vacancy of roles that will be needed to filled. 

-  We have very little Managers that will be retiring in the next wave. 


Next we need a list of employees who are eligbile to particpate in our mentoship program. So we use query: 

SELECT DISTINCT ON (emp_no)
	e.emp_no,
	e.first_name,
	e.last_name,
	e.birth_date,
	de.from_date,
	de.to_date,
	ti.title
INTO mentorship_eligibility
FROM employees as e
LEFT JOIN dept_emp as de
	ON (e.emp_no = de.emp_no) 
JOIN titles as ti
	ON (e.emp_no = ti.emp_no)
WHERE (e.birth_date BETWEEN '1965-01-01' AND '1965-12-31') AND ti.to_date = '9999-01-01'
ORDER BY emp_no ASC;

This gives us a list of employees who will look to retire in the next 10 years resulting in: 

![Employees Eligible for Mentorship Program](/images/mentorship_eligibility.png)


# SUMMARY 

- 90,398 roles will need to be filled as a result of the "silver tsunami" that is approaching. 


