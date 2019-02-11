# Structure

### Problem Definition and Stakeholders

The stakeholders of my application will be **fellow students of Loreto Sixth Form College**.
Being students, they need to organize revision sessions, in order to maintain the grades required
for their chosen university. Many Loreto college students have issues with organising a revision
timetable. For example, creating study sessions that do not clash with classes, or setting up
reminders for each of these study sessions, can be difficult. The needs of a College student also include
regularly checking up on their performance (grades, attendance) to ensure that they are making the
best of their study periods. <br>
I have chosen a fellow Computer Science student, Troy Sherlock, to be my stakeholder for this project. Troy is an ideal candidate for my software, as he is currently in his second year of A Levels with Maths, Physics and Computer Science. This means that efficient organisation of free study periods is vital, so he will benefit greatly from my solution.

The current system for acheiving these tasks is the following:
* Checking the current timetable
* Checking grades and subject performance
* Use this information to create a manual revision timetable
* Regularly update this timetable manually based on performance

The issue with this system is that it is entirely manual, which means that students may neglect to create
or update a revision timetable. Also, the current system does not allow srudents to add their own custom
lessons, or be notified for a lesson. My idea is to have a user-friendly system that integrates all these 
features, in one easy to use web application.

My application will solve this problem, by **integrating revision session management**, **homework
reminders** and **performance analysis** in one simple dashboard. Students will be able to access this
dashboard via a **Django web application**, and possibly a **Flutter mobile application**.

In order to solve this problem with the suggested solution, I will need to learn the following:
* Usage of the Django web framework
* Usage of the PostgreSQL Database Management System with Python
* Usage of JavaScript graphing libraries such as D3.js

### Justification for Computational Method

This problem lends itself to utilization of computational methods for several reasons: <br/>

* Student performance data will be abstracted, instead displayed as as a simple line graph.
    * In order to display data as a graph, a computational device is needed.
* The application will also automatically generate a study timetable that adapts to this data.
    * This solution utilizes algorithms that can only be executed on a computational device.
* The application stack is a system that includes my webserver, which will run on a computer.

Therefore, there is no alternative solution that would not require a computer to execute.

### Applications of Computational Methods

There are several ways that computational methods can be applied to this project.

| **Method of computational thinking**  | **Explanation of relevant features** |
|:-------------------------------------:|:------------------------------------:|
| Abstract | My web application will be as user-friendly as possible. this will require me to hide certain details from the user - for example, the raw data that is scraped by my webscraper. Instead, the user will only see data that is relevant to their current page: If they are on the summary page, they will only see simple summarized data and graphs for their performance. |
| Heuristic | The summary page of my dashboard will include rounded, approximate values to represent the correlation between various properties of the student's study time, free time, grades, and punctuality. |
| Procedural | My application stack will be comprised of several Python modules, each separated into classes, methods and functions. I will be able to remove modules, test modules and edit modules in a way that does not affect any irrelevant components of the application. |
| Concurrent | The web application must be able to handle multiple HTTP requests simultaneously. I will be applying concurrent thinking to plan and design the processing and response for each request. |

### Research

I found a service that is a similar solution to my own, **showmyhomework.co.uk**, by Satchel:

#### Show my Homework

Show My Homework is a service designed for schools, that aims to unify student timetables, homework tasks and revision session management.

|    Online Dashboard      |    Mobile Application    |
|:------------------------:|:------------------------:|
![](https://satchel-sw-prod.imgix.net/images/products/timetables/timetables_@2x.png) | ![](https://satchel-sw-prod.imgix.net/images/products/timetables/mobile_timetables_@2x.png) |
| Fully-featured management system | Minimal features, simple timetable with reminders  |

### Stakeholder Interview Questions

* Have you ever used a homework management system before?
* If so, what are your favourite features of this system?
* Are there any features you want to see from a system like this?
* Are there any features you want removed?


### Interview results

#### Have you ever used a homework management system before?
*Yes, I have used Show My Homework in high school to manage my timetabled lessons and homework.*
#### If so, what are your favourite features of this system?
*My favourite feature is the ability to colour-code homework slots depending on whether they are completed or not.*
#### Are there any features you want to see from a system like this?
*I would like to see a reminder system, such as push notifications for due homework.*
#### Are there any features you want removed?
*I do not like Show My Homework's "my calendar" system, and would like it to display more accurate time stamps.*

# Design

### Decomposing the Problem

![image](https://github.com/AnonGuy/Structure/blob/master/docs/images/StructureDecomposition.png?raw=true)
Using procedural thinking, I have broken down my solution into six main problems: 
* **Web Scraper**
   * The Web Scraper will be the system that communicates with MyLoreto. It will utilize the `requests` library to
   authenticate a MyLoreto session, and scrape the relevant HTML elements such as timetabled lessons, markbook grades
   and attendance data, with the `re` library to match [HTML Regular Expressions](https://stackoverflow.com/a/1732454).
* **Django Webserver**
   * The Webserver will be the system that communicates with the customer. It will utilize the [Django](https://www.djangoproject.com/) backend web framework to handle HTTP requests from multiple users in parallel, and it will dynamically generate unique HTML files for each user, based on defined templates. This system will also render data such as timetabled lessons, in the form of styled HTML elements.
* **Object-Relational Mapper**
   * An Object-Relational Mapper (ORM) is a library that utilises Object-Oriented Programming (OOP) to abstract complex SQL queries from the developer. For example, with a defined class `User`, `User(name="Jeremiah", age=18).save()` will execute the equivalent SQL statement, `INSERT INTO User (name, age) VALUES ("Jeremiah", 18)`. Although Django provides a builtin ORM for this purpose, I will be writing my own implementation from scratch.
* **Web Application Programming Interface**
   * The server-side API will be an interface providing raw data - for example, a POST request with authorization headers to `/api/student-data` might return JSON-formatted data about the student in question. This will be used for any further client applications, such as a mobile app or desktop feature.
* **Web Dashboard**
   * The Web Dashboard will be the HTML frontend that will be displayed to the customers. The Django backend will generate this HTML from a defined template, and I will be using a popular frontend web framework, [Bootstrap](https://getbootstrap.com/), to create and style this template. 
* **Mobile Application**
   * The Mobile application will be a portable client for the Web API. It will be able to retrieve student timetables, add reminders for lessons, add custom timetable entries and add homework notes.

| Database Relationships | Data Flow Diagram |
|:----------------------:|:-----------------:|
|![](https://github.com/AnonGuy/Structure/blob/master/docs/images/StructureERD.png?raw=true)|![](https://github.com/AnonGuy/Structure/blob/master/docs/images/StructureDataFlow.png?raw=true) Users communicate with the system via the Mobile Client and online Dashboard.|

# Version 1

I started by creating a simple script that will allow me to request and parse data from MyLoreto. I have decided to use the `requests` library to handle HTTP requests and responses, and the `re` builtin module to parse the content with regular expressions.

I will be using Regular Expression patterns to catch certain substrings of a HTTP response. For example, from this snippet of HTML:
```html
	<dl class="dl-horizontal" style="font-size: 0.9em">
		<dt>Reference: </dt>
		<dd>S20170000420</dd>
	</dl>
```
I am able to catch the Reference number with the following regex pattern: `Reference: </dt>\s+<dd>([A-Z0-9]+)`
The `\s+` represents one or more whitespace characters, and the `[A-Z0-9]+` represents one or more capital alphanumeric characters.
```python
>>> html = """
... 	<dl class="dl-horizontal" style="font-size: 0.9em">
... 		<dt>Reference: </dt>
... 		<dd>S20170000420</dd>
... 	</dl>"""

>>> match = re.search(r'Reference: </dt>\s+<dd>([A-Z0-9]+)', html)
>>> match.group(1)
'S20170000420'
```
An example prototype of the script is below:
```python
import re

import requests


endpoint = "https://my.loreto.ac.uk/"

patterns = {
    'name': b'fullName: "([A-Za-z ]+)"',
    'username': b'username: "([A-Za-z0-9]+)"',
    'avatar': b'base64,(.*?)">',
    'reference_number': br'Reference: </dt>\s+<dd>([A-Z0-9]+)',
    'tutor': b'Tutor: </dt> <dd> (.*?) </dd>'
}


def get_student_data(*credentials: str) -> dict:
    """Get data about a student, given their username and password."""
    student_data = {}
    landing_page = requests.get(endpoint, auth=credentials).content
    for key, pattern in patterns.items():
        student_data[key] = re.search(pattern, landing_page).group(1)
    return student_data
```
An example of this function is use can be seen below:
```python
>>> get_student_data('jerbob42', 'password')
{
    'name': b'Jeremiah Boby',
    'username': b'JerBob42',
    'avatar': b'/9j/4AAQSkZJRgABAQEASABIAAD...',
    'reference_number': 'S20170000420',
    'tutor': 'Mr Bloggs'
}
```
### Potential Improvement
**I could improve this function's performance by utilising Python's builtin `threading` module, starting a new `Thread` for each regex search that I start. This means that each regex search will not block the execution of the next.**

I also decided to create a method for use in validating MyLoreto credentials:
```python
def valid_user(*credentials: str) -> bool:
    """Take a student's credentials and validate them."""
    print('Validating user...')
    return requests.get(
        endpoint, auth=credentials
    ).status_code == 200
```
This function checks that the HTTP response code for a request with the provided credentials is successful.
With some simple validation and webscraping done, I then decided to start work on the main webserver.
