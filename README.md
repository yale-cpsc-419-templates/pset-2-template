# Pset 2: Desktop GUI Application

### Due Friday Oct 10 10:59 PM NHT (New Haven Time)

## Table of Contents

- [Pset 2: Desktop GUI Application](#pset-2-desktop-gui-application)
  - [Due Friday Oct 16 11:59 PM NHT (New Haven Time)](#due-friday-oct-16-1159-pm-nht-new-haven-time)
  - [Table of Contents](#table-of-contents)
  - [Purpose](#purpose)
  - [Rules](#rules)
  - [Getting Started](#getting-started)
  - [Your Task](#your-task)
  - [The Database](#the-database)
  - [Your Assignment](#your-assignment)
  - [The `reg.py` Program](#the-regpy-program)
    - [Output Requirements](#output-requirements)
    - [Filtering Requirements](#filtering-requirements)
    - [The Details Dialog](#the-details-dialog)
    - [Sample Behavior](#sample-behavior)
  - [The `regserver.py` Program](#the-regserverpy-program)
  - [Communication among tiers](#communication-among-tiers)
    - [The Justification](#the-justification)
  - [Communication with the Database](#communication-with-the-database)
    - [The Justification](#the-justification-1)
  - [Source Code Guide](#source-code-guide)
  - [Input Specification](#input-specification)
  - [Error Handling](#error-handling)
    - [Unavailable Port](#unavailable-port)
    - [Unavailable Server](#unavailable-server)
  - [Testing](#testing)
    - [Boundary Testing](#boundary-testing)
    - [Statement Testing](#statement-testing)
    - [Unit Testing](#unit-testing)
  - [Program Style](#program-style)
  - [Advice](#advice)
  - [Submission](#submission)
    - [Late Submissions](#late-submissions)
    - [Grading](#grading)

## Purpose

The purpose of this assignment is to help you learn or review graphical user interface programming and network programming in Python.

---

## Rules

You may work with one teammate on this assignment, and we prefer that you do so.

It must be the case that either you submit all of your team’s files or your teammate submits all of your team’s files.
(It must not be the case that you submit some of your team’s files and your teammate submits some of your team’s files.)
Your `README` file and your source code files must contain your name and your teammate’s name.

---

## Getting Started

> **Note**: this section contains exactly the text on the Canvas assignment, reproduced here only for completeness of this document.
> Since you made it here, you can safely ignore this section.

1. Accept the GitHub Classroom assignment.
    * Select your team.
    * This step creates a GitHub repository for your team and links your team members' GitHub ids.
        * If you do not have a GitHub account, you are required to create one for this course
    * Use this git repository to track your assignment development.

2. The `reg.sqlite` database file is included in your GitHub repository, so no separate download or setup is required.

---

## Your Task

As with Pset 1, assume you are working for Yale's Registrar's Office.
You are given a database containing data about courses and classes offered during this semester at Yale.
Your task is to compose an application that allows Yale students and other interested parties to query the database.

Your application must consist of two programs: a **client program** called `reg.py` and a **server program** called `regserver.py`.
Your `reg.py` program must have a **graphical user interface** and must communicate over a network with your `regserver.py` program.
The `regserver.py` program might be running on the same computer as `reg.py`, or it might be running on a different computer: the application must behave identically in either case.

---

## The Database

The database is identical to the one from Pset 1.
Refer to the Pset 1 specification for the list of tables and fields in the database, and for the relationships among those tables.
The database is a SQLite database in a file named `reg.sqlite`, included in this template repository.

---

## Your Assignment

For this assignment, you will create two programs: `reg.py` and `regserver.py`.
`reg.py` is a graphical client for your `regserver.py` program.
`regserver.py` must communicate with the database and serve the information requested by `reg.py`.

## The `reg.py` Program

When executed with `-h` as a command-line argument, your `reg.py` must display a help message that describes the program's behavior:

```text
usage: reg.py [-h] host port

Client for the registrar application

positional arguments:
  host        the host on which the server is running
  port        the port at which the server is listening

options:
  -h, --help  show this help message and exit
```

> **Hint**: Use the standard Python `argparse` module.
> The required help message is the default behavior of that module.
> The help message may differ slightly based on your platform or Python version; we are looking for the default behavior of `argparse`.

Your `reg.py` must accept exactly two command-line arguments. The first must be the **host**: the IP address or domain name of the computer on which your `regserver.py` is running.
The second must be the number of the port at which your `regserver.py` is listening.

As noted previously, your `reg.py` must have a graphical user interface.
Your graphical user interface must have these properties:

* It must be built using the `PyQt6` or `PySide6` GUI framework.

  > **Note**: The `PyQt6` framework is only intermittently compatible with Apple Silicon CPUs (M1, M2).
  > Use `PySide6` if you have such a machine, just to be safe.
  > Fortunately, all relevant class, type, and function names are identical between the two packages.

* When the application is launched, the main window should be no larger than 1/4 of the total screen area (*i.e.*, no wider than 1/2 of the screen's width and no taller than 1/2 of the screen's height).
* Four `QLineEdit`s (text fields) labeled "dept", "coursenum", "subjectcode", and "title" (in that order) that allow the user to specify a department code, a course number, a subject code, and a title.
* A `QPushButton` (button) labeled "Submit" that commands your `reg.py` to communicate with your `regserver.py`.
  Your `reg.py` must communicate with your `regserver.py` to query the database each time the user clicks the Submit button or types the `Enter` key in any of the text fields.
* A `QListWidget` (list box) whose entries are the results of the query.
  The list box must have vertical and horizontal scroll bars (as needed), and so must be able to scroll vertically and/or horizontally.

### Output Requirements

The fields displayed in the list must be, in order, the `deptname`, `subjectcode`, `coursenum`, `title`, and `crn` of each **section** that matches the specified criteria, or of all sections if the user specifies no criteria.

> **Note**: The required output for this assignment differs somewhat from the required output for Pset 1.
> In particular, the list in your `reg.py` window must display *one row per section*, with the `deptname`, `subjectcode`, `coursenum`, and `title` repeated on each row (rather than one row per course, with several CRNs listed together).

* The list box entries must be sorted. The primary sort must be by `deptname` in ascending order, the secondary sort must be by `coursenum` in ascending order, and the tertiary sort must be by `crn` in ascending order.
* The list box must have the appearance of a table, that is, the respective fields of each row must be aligned vertically.
  * **There should be no header row of the table displayed.**
* There is no need to wrap text for the list box; since the box is scrollable it is permissible to have each row of the table on one line.
* There must be no more than 1000 rows displayed in response to any query.

> **Note**: To ensure the fields of your table are aligned, your list box ought to be displayed using a fixed-width font.
> However, we did not and will not discuss `PyQt6` font or typeface specifications in class, so the provided `dialog.py` file provides an instantiated fixed-width `QFont` that you can use, named `FW_FONT`.
> Examine [the Qt Framework Reference](https://doc.qt.io/qt-6/qtmodules.html) to determine where and how to use that font.

### Filtering Requirements

The four text fields correspond to the filters from Pset 1.
An empty field does not constrain the query.
If every field is empty, the result is every section in the database (subject to the 1000-row limit), in the sort order above.

| Field | Meaning |
| --- | --- |
| dept | Include sections whose course `deptcode` contains the supplied value. |
| coursenum | Include sections whose `coursenum` contains the supplied value. |
| subjectcode | Include sections whose `subjectcode` contains the supplied value. |
| title | Include sections whose `title` contains the supplied value. |

If several fields are non-empty, `reg.py` must combine the filters using `AND`.
Filters must be case-insensitive: a title field containing `web` must match a title such as "Full Stack Web Programming".
Filters must preserve leading and trailing whitespace.

### The Details Dialog

Your `reg.py` must allow the user to select a particular list box entry.
If the list box contains any entries, then exactly one of them must be selected at any given time.
If the user clicks on an entry, then your `reg.py` must select that entry.
If the user double-clicks on an entry, then your `reg.py` must select that entry and display a dialog containing detailed data about the section that the selected entry describes.
If the user clicks or double-clicks in the list widget when it contains no entries (or in an unfilled part of the list widget), then your `reg.py` must do nothing.

The detailed data must be the same information, in the same sections, as the output of your `regdetails.py` program from Pset 1 for that section's `crn`.
Those sections are:

1. A table with the columns `deptcode`, `deptname`, `subjectcode`, and `coursenum`, for the course associated with the selected section.
2. A section labeled `title`, containing the course title.
3. A section labeled `descrip`, containing the course description, or the string `None` if there is no description.
4. A section labeled `prereqs`, containing the course prerequisites, or the string `None` if there are no prerequisites.
5. A table with the columns `sectionnumber`, `crn`, and `meetinginfo`.
   Each `meetinginfo` entry has the format `<timestring>@<locstring>`, one meeting per line.
6. A table of crosslistings, with the columns `subjectcode` and `coursenum`.
7. A section labeled `professors`, containing the `profname` of each professor for the course, one per line.

> **Note**: The description and prerequisites strings for some courses in the database contain HTML-like tags.
> They must be included verbatim.

The data must be well formatted, and no line may exceed 100 characters long.
It also must be displayed in a fixed-width font, so as to preserve the formatting generated by your table printing software.
A correctly implemented `table.py` from Pset 1 can produce this text.

> **Note**: There is no easy way to use built-in `Qt` classes to generate a dialog box that displays a message in a fixed-width font; feel free to use the provided `FixedWidthMessageDialog` class in `dialog.py` to accomplish it, or write your own `QDialog` subclass.

Your `reg.py` must be usable *without* a pointing device (mouse, touchpad, etc.). That is, your `reg.py` must be usable with only the keyboard. The user must be able to:

* Cycle the keyboard focus through the text fields and list box by pressing the `Tab` key.
* When the list widget has keyboard focus, indicate a current entry by pressing the `up-arrow` and `down-arrow` keys.
* When the list widget has keyboard focus, **activate** the selected list box entry by typing the `Enter` key or `Command-o`, depending on the user's platform (see note below), thereby displaying a detail dialog box (as described above).
* **Dismiss** the detail dialog box by typing the `Enter` key.

> **Note**: On Linux and Microsoft Windows systems, programs should be designed such that the user activates the selected list box entry by typing the `Enter` key.
> Remarkably, that's not true on Macs.
> According to the Mac user interface standards, Mac programs should be designed such that the user activates the selected list box entry by typing `Command-o`.
> (Many popular Mac programs don't conform to that Mac standard.)
>
> Programs that use `PyQt6` and `PySide6` conform to the standards of each platform.
> That is, when running on Linux or Microsoft Windows, they require the user to type the `Enter` key; when running on Macs, they require the user to type `Command-o`.

It is possible to design a better interface than the one just described.
(In fact, you will do so later in the course!)
For now, the layout of your application must be as described.

### Sample Behavior

A query with dept `cpsc` and coursenum `4190` includes a row for the section of CPSC 4190, for example:

```text
Computer Science (CPSC)  CPSC  4190  Full Stack Web Programming  10859
```

A query with dept `chem` and coursenum `6000` includes one row for every section of CHEM 6000, with the department, subject, course number, and title repeated, for example:

```text
Chemistry (CHEM)  CHEM  6000  Research Seminar  13753
Chemistry (CHEM)  CHEM  6000  Research Seminar  13754
```

Double-clicking the row whose `crn` is `10859` must show the same class details that `python regdetails.py 10859` prints in Pset 1.
Column spacing in the list will depend on the width of the values in the result; the examples above illustrate the fields and the one-row-per-section rule, not a required character width.

---

## The `regserver.py` Program

Your second program must be named `regserver.py`.

When executed with `-h` as a command-line argument, your `regserver.py` program must display the following help message that describes the program's behavior:

```text
usage: regserver.py [-h] port

Server for the registrar application

positional arguments:
  port        the port at which the server should listen

options:
  -h, --help  show this help message and exit
```

> **Hint**: Use the standard Python `argparse` module.
> The help message may differ slightly based on your platform or Python version; we are looking for the default behavior of `argparse`.

Your `regserver.py` must accept a single command-line argument.
That argument must be the number of the **port** on which the program will listen for connections.

Your `regserver.py` must perform queries to the SQLite database.
Your `reg.py` program must **not** access the SQLite database.

Your `regserver.py` must protect the database against SQL injection attacks.
That is, it must use SQL prepared statements.

> **Note**: Your `regserver.py` must loop infinitely, as many servers do.
> The issue then becomes...how can you kill your server?
> That is, after creating a process by issuing a `python regserver.py someport` command, how can you kill that process?
>
> On any Unix-like computer the answer is easy: type `Ctrl-c`. Doing that sends a `SIGINT` signal to the process, which (by default) kills it.
> (Don't type `Ctrl-z`.
> Doing that sends a `SIGTSTP` signal to the process, which does not kill it, but places it in the background.
> The process would continue to occupy the specified port.
> Subsequently issuing a `fg` command would bring the process back to the foreground.)
>
> On a Microsoft Windows computer the answer is more complicated.
> `Ctrl-c` might work. `Ctrl-Break` might work.
> If your keyboard doesn't have a `Break` key, then consulting the Wikipedia [Break Key](https://en.wikipedia.org/wiki/Break_key) article might help.
> In the worst case you can kill the process via the Windows Task Manager; but that's an awkward last resort.

---

## Communication among tiers

Your program must make use of the Python standard `socket` module for network communication.
That is, it may *not* make use of web server frameworks such as Flask.

Beyond the use of the `socket` module, it is additionally required that the network connection between your `reg.py` and your `regserver.py` must not be persistent.
That is, it must *not* be the case that your `reg.py` and your `regserver.py` create a socket upon startup of `reg.py`, transfer data using that one socket during the entire execution of your `reg.py`, and then close that socket upon exit of your `reg.py`.
Instead the network connection must be *transient*.
Each time the user initiates a communication between your `reg.py` and your `regserver.py`, your `reg.py` and your `regserver.py` must create a socket, transfer data using that socket, and then close that socket.

### The Justification

The transient approach, although slightly slower than the persistent approach, is more robust in the presence of server failures.
For example, using the transient approach your `regserver.py` could crash and restart while your `reg.py` is running, with no negative effects upon your `reg.py`.
(The transient approach also mimics HTTP, which we will study soon in class.
In that sense using the transient approach provides a better basis for comparing/contrasting the code that you compose for this assignment with the code that you will compose for subsequent ones.)

## Communication with the Database

Similarly, the connection between your `regserver.py` and the database must not be persistent.
That is, it must not be the case that your `regserver.py` creates a database connection upon startup of your `regserver.py`, uses that database connection during the entire execution of your `regserver.py`, and then closes that connection upon exit of your `regserver.py`.
Instead the database connection must be *transient*.
Each time the user initiates a communication between your `reg.py` and your `regserver.py`, your `regserver.py` must create a database connection, transfer data using that connection, and then close that connection.

### The Justification

The transient approach, although slightly slower than the persistent approach, is more robust in the presence of database failures.
For example, using the transient approach the `reg.sqlite` file could disappear, be changed, and reappear while your `regserver.py` is running, with no negative effects upon your `regserver.py` or your `reg.py`.

---

## Source Code Guide

Here are the **requirements** for the source code of your solution.

* The `regserver.py` program must communicate with a SQLite database in a file named `reg.sqlite`, organized as described above.
* The `regserver.py` program must use SQL prepared statements for every database query.
  (This protects the database against SQL injection attacks.)
* Every module used by your program(s) must either be from the Python standard library or written by your team (and included in your submission).
  The only exceptions to this are the `PySide6`/`PyQt6` packages for GUI programming and the scaffolding files we have provided you this semester (`dialog.py`, `table.py`).

Here are some **recommendations** for the source code of your solution.

* Reuse code from your solution to Pset 1 in this assignment.
    * The database queries executed by `regserver.py` will be *almost identical* to the queries executed by your solution to Pset 1.
    * The requirements for the class-details dialog are exactly the same as Pset 1's `regdetails.py`.
      The class list is only slightly modified: one row per section, rather than one row per course.
* Modularize `regserver.py` so that database communication code is cleanly separated from network code.
* Modularize `reg.py` so that GUI code is cleanly separated from network code.
    * Be able to, if needed, change the layout (or the language, or the color scheme...) of the user interface without needing to change anything except code that uses `PySide6` or `PyQt6`.
    * Better yet, be able to swap out the GUI framework for *anything else* without needing to change anything except the GUI code.
* Study the [**M**odel-**V**iew-**C**ontroller (MVC)](https://en.wikipedia.org/wiki/Model–view–controller) design pattern and implement it in your solution.

---

## Input Specification

You may assume the users of your `reg.py` and `regserver.py` programs are generally acting "in good faith", that is, they will not intentionally provide input to your program that is corrupted, unusable, or otherwise incorrect.
In particular, you may assume...

* That the user will only ever provide arguments at the command line that conform to the allowed arguments:
    * The only provided arguments to `reg.py` will be `-h` or a `host`/`port` pair
    * The only provided argument to `regserver.py` will be a single positional argument or the `-h` flag
* That the database exists in a file named `reg.sqlite` and is well-formed according to the database specification above

However, you may *not* assume...

* That there are any courses at all in the database
* That the `port` argument to either `reg.py` or `regserver.py` is a numeric argument
* That `regserver.py` is running on the specified `host`/`port` when you run `reg.py`
* That the values entered into the text fields in `reg.py` conform to the expected types
* That the user will interact with `reg.py` "slowly".
  That is, your program must handle arbitrarily quick interactions from the user.
  Precisely how you handle these repeated interactions is up to you; we recommend directing `reg.py` to ignore input while a request to the server is in process.
  Whatever you do, though, must **prevent your app from crashing** under the use of a very fast mouse-clicker (such as a Starcraft champion or a robot).

---

## Error Handling

Your `reg.py` and `regserver.py` should be reasonably robust.
However, since we have only touched on error handling in this course, the kinds of things you are required to handle is limited in this assignment.
Keep in mind that as we progress through the course, error handling will become more important (and the specification of program input more relaxed!).

There are two classes of errors, however, that must be handled by any client-server application, including yours.

### Unavailable Port

Some port numbers are reserved by the operating system for certain processes and cannot be claimed by anyone other than the OS itself.
Other port numbers are in use sometimes, and can't be used by another process.
Still other numbers that allegedly refer to ports are simply too large to be a port number (on most modern systems there are 16 bits for port numbers; that is, valid ports are numbered 0&ndash;65,536).

If `regserver.py` is given a port number that is not available (for whatever reason), then it must write a descriptive error message to `stderr` and exit with status 1.

### Unavailable Server

If `regserver.py` is unavailable at the time `reg.py` sends a request, then `reg.py` must display a descriptive error message&mdash;specifically, the message contained in the thrown `Exception` object&mdash;in a `QErrorMessage` dialog and continue executing.

---

## Testing

Since software testing is not a focus of this course for now it will be sufficient for testing to rely upon (1) your knowledge of testing from your previous experience, and (2) this [A Software Testing Taxonomy](https://yale.instructure.com/assignments/TestingTaxonomy.pdf) document, courtesy of Princeton University.

Test your `reg.py` and `regserver.py` programs by (1) reviewing this assignment specification thoroughly, making sure that your programs conform to every aspect of it, and (2) comparing the behavior of your program with the example outputs given above.

### Boundary Testing

Focus on boundary (alias corner case) testing.
Of course, make sure that your programs handle normal data.
But also make sure that your programs handle unusual data: courses that have multiple crosslistings, long titles, long descriptions, multiple professors, no professors, many sections, and so forth.

### Statement Testing

Also focus on statement (alias coverage) testing.
Your tests should cause every statement of your `reg.py` and `regserver.py` to be executed.

You're encouraged, but not required, to use the Python `coverage` tool to generate a coverage report showing which lines of your programs have and have not been executed by your tests. These are the steps:

1. Issue commands of the form `python -m coverage run -p regserver.py argument` and `python -m coverage run -p reg.py arguments`&mdash;multiple times if necessary.
   Interact with the programs to generate coverage reports in files named `.coverageX` (for some program name `X`).
2. Issue the command `python -m coverage combine` to combine the coverage reports generated by step 1 into one large coverage report in a file named `.coverage`.
3. Issue the command `python -m coverage html` to use the `.coverage` file to generate a human-readable report as a set of HTML documents in a directory named `htmlcov`.
4. Browse to `htmlcov/index.html` to check the report.

### Unit Testing

The kind of testing described above, in which the entire program is repeatedly run and the output compared to correct output, is called **system testing**.
You probably will notice that testing your `reg.py` program as a whole is quite tedious.
If you have sufficiently modularized your code, unit testing can help dramatically with all of the non-GUI parts of testing `reg.py` because it can test *individual functions*, including those that don't necessarily do anything to the GUI.
Testing the GUI parts of your program will remain tedious, but you can at least sleep a little more soundly knowing that your GUI is resting on a solid foundation.

> **Note**: Incidentally, if you're interested, the [pytestqt](https://pypi.org/project/pytest-qt/) package can help automate GUI testing.
> You are not required to automate any testing, much less GUI testing, but the tools exist and we encourage you to check them out.

Unit testing will be covered briefly later in the semester; for now, it is sufficient to read a blog post or two and study briefly the documentation for the Python `unittest` or `pytest` modules.

---

## Program Style

As with Pset 1, your solution to this assignment must be well-styled.
This includes not only good style as defined by the `pylint` static analysis tool, but also that your code conforms to "good" software project design principles such as modularity, abstraction, and an eye toward reusability.

As you self-evaluate program style, consider the following things:

* How much of your solution for Pset 1 was reusable in this assignment?
  What changes could you make so that it is more reusable for the rest of the assignments, in which you will change the presentation tier and add sophistication to the application tier?
* If a specific protocol was required to be "spoken" by `regserver.py`, how much effort would it take to modify your program to conform to that protocol?
  * For example, a common protocol for communication over the Internet is the [**H**yper**t**ext **T**ransfer **P**rotocol (HTTP)](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol).
    We'll talk about it in detail over the coming weeks, but you should aim to minimize the changes to `regserver.py` that are needed to conform to it, for example, by encapsulating the creation of a server response inside of a function whose body can be swapped with an HTTP-conforming response creator.
* If the client of `regserver.py` were not capable of unpickling your objects, how much effort would it take to change your server program to support that client?
* If a new Python GUI framework came into the picture that you liked better than `PySide6` or `PyQt6`, how much effort would it take to change your `reg.py` program to use the new framework?

---

## Advice

As noted above, your `reg.py` must handle communication with the human user, and your `regserver.py` must handle communication with the database.
Beyond those obvious requirements, it's up to you to decide how to divide tasks between your `reg.py` and your `regserver.py`.

It's also up to you to design the communication protocol between the two programs (other than that it must be over a network).
The protocol could be a "low-level" one involving transmitting unstructured text.
Or it could be a "high-level" one involving transmitting `pickle`d Python objects.
Or it could be a "medium-level" one involving transmitting structured text such as JSON or XML.
Any of those approaches is acceptable, but you probably will find the "high-level" approach easiest to implement.
That said, you should keep in mind that you will be writing non-Python clients in future assignments.

> **Note**: You *might* need to make some small modification to `table.py` if you intend to use object pickling (the "high-level" protocol).
> This is because objects of type `sqlite3.Row` are not `pickle`-able!
> You can handle this in one of two ways: convert your data to `pickle`-able objects *before* handing it to the `Table` class, or modify the `Table` class to convert the data to `pickle`-able objects such as `list`s upon initialization.
> Either is acceptable for this assignment, and you should decide which makes the most sense to you.

While it is not required that your program(s) handle every possible error, if you would like to explore that aspect of software engineering, you could design your `regserver.py` such that, after receiving a request from your `reg.py` client, it sends two things back to your `reg.py` client:

* The first is an indication of whether the processing of the request succeeded or failed (or, better, an indication of *why* it failed, if indeed it did).
* If the processing succeeded, then the second thing contains the requested data.
* If the processing failed, then the second thing is a string which is an error message, or a pickled `Exception` object containing a string which is an error message.

Design your `reg.py` such that if the user double-clicks in the list box, or if the user types the `Enter` key (or, on a Mac, `Command-o`) while the list box has keyboard focus, then the program:

1. **Detects the event**. Then in the list box event handler...
2. **Sends messages** to (*i.e.*, calls functions of) the list box to determine which list box entry was selected and the text that it contains.
3. **Analyzes the text** of that row to determine the `crn`.
4. **Sends a request** to the server to get the data for that `crn`.
5. **Displays a dialog** based upon that server response.

If you are having trouble wrapping your head around GUI programming, recognize that the most important part of this assignment is the communication between tiers.
Start the assignment with a command-line client that sends a request to the server and displays a table (perhaps this is done by refactoring `reg.py` from Pset 1 to communicate with a server rather than the database directly, or if you chose the "low-level" protocol it may be simply running `telnet`).
Only once you have a reliable communication mechanism should you start the GUI side of things.

---

## Submission

Rename this file `TEMPLATE_README.md` and replace it with a new `README.md` file.
Your new `README` file must contain:

* Your name and netid and your teammate's name and netid, at the beginning of the file
* A paragraph describing your contribution, and another paragraph describing your teammate’s contribution.
    * Please be thorough; we are looking for two substantial paragraphs, not a sentence or two.
* A description of whatever help (if any) you received from other people while doing the assignment.
* A description of the sources of information that you used while doing the assignment, that are not direct help from other people.
* An indication of how much time you spent doing the assignment, rounded to the nearest hour.
* Your assessment of the assignment:
    * Did it help you to learn?
    * What did it help you to learn?
    * Do you have any suggestions for improvement? *Etc.*
* (Optionally) Any information that will help us to grade your work in the most favorable light.
    * In particular, describe all known bugs and explain why any `pylint` style warnings you received are unavoidable or why you know better than `pylint` (a convincing argument might negate some `pylint` style penalties you may accrue).

Your README file must be a plain text file: don't create it using Microsoft Word or any other word processor, although you are encouraged to format it using [markdown](https://www.markdownguide.org/) tags.

Package your assignment files by [creating a release](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository#creating-a-release) on GitHub in your assignment repository.
There must be at least the following files with the following (exact) names in that repository when you submit it:

* `README.md`
* `reg.py`
* `regserver.py`

Ensure that any additional files needed by your program (such as other Python modules, including starter code) are in the repository snapshot captured by the release.

> **Note**: If you have installed external packages, you must also include a file named `requirements.txt` containing the dependencies of your project.
> It can be created from your virtual environment by running the following command:
>
> ```text
> $ pip freeze > requirements.txt
> ```
>
> Failure to include a `requirements.txt` file if you use third-party packages will result in an automatic 5% penalty and a request that you submit an appropriate `requirements.txt` file to the graders.

Submit your solution to Canvas (in the assignment named "Pset 2: Desktop GUI Application") as [a link to that release](https://docs.github.com/en/repositories/releasing-projects-on-github/linking-to-releases).

As noted above in the [Rules](#rules) section, it must be the case that either you submit all of your team’s files or your teammate submits all of your team’s files.
(It must not be the case that you submit some of your team’s files and your teammate submits some of your team’s files.)
You and your team may submit multiple times; we will grade the latest files that you submit before the deadline unless a particular version is requested as the canonical version.

**Please follow the directions on what to submit and how.**
It will be a big help to us if you get the filenames right and submit exactly what’s asked for.
Thanks.

### Late Submissions

The deadline for this assignment is **10:59 PM NHT (New Haven Time) on Friday Oct 10**.
There is a strict 15 minute grace period beyond the deadline, to be used in case of technical or administrative difficulties, and not for putting final touches on your solution.
(If you can do it in as little as 15 minutes, it probably is insignificant enough not to change your grade.)

Late submissions will receive a 5% deduction for every 12-hour period (or part thereof) after the deadline.
After 48 hours, the Canvas assignment will close and submissions after that time will not receive any credit.

Except for submissions after the 48-hour deadline (*which are not accepted*), the timestamp on the commit associated with the linked release will determine what late penalties, if any, are applied.

---

### Grading

Your grade will be based upon:

* Correctness, that is, how closely your programs conform to the specifications in this document.
* Style, that is, the quality of your program style. This includes not only style as qualitatively assessed by the graders (including modularity, cleanliness, and algorithmic efficiency) but also style as reported by the pylint tool, using the default settings, and when executed via the command `python -m pylint **/*.py`.
    * Part of your grade will be based upon the quality of your program style as reported by pylint. Your grader will start with the 10-point score reported by pylint. Your pylint style grade is your pylint score rounded **up** to the next integer (minimum 0). For example, if your pylint score is 9.1, then your pylint style grade will be 10; if your pylint score is 7.7, then your pylint style grade will be 8.

If your code fails the tests on some particular functionality, your grader will inspect your code manually to try to assign partial credit for that functionality.
Partial credit will be given only if there is an *obvious* "quick fix" (*e.g.*, you have accidentally changed the name of the database file and your solution points to a file with a name that does not match the grader's copy of the database); if no such quick fix exists then no partial credit for that feature will be given.

---

Adapted from Assignment 2 for COS 333 &copy; 2021 by Robert M. Dondero, Jr., Princeton University

This version &copy; 2026 by Alan Weide, Yale University
