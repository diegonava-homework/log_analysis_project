LOGS ANALYSIS PROJECT
Building a informative summary from logs by SQL database queries. 
Interacting with a live DB both from the command line and from the py code. 

TECHNOLOGY USED
PostgreSQL
Python code with DB-API
Linux based virtual machine Vagrant

PROJECT REQUIREMENTS
The reporting tool should answer the next questions:

	1. Which are the most popular 3 articles of all the time?
	2. Who are the most popular article authors of all the time?
	3. On what days did more than 1% of requests lead to errors?
	
	- Project follows good SQL coding practices: Every question should be answered with a single database query.
	- The code doesn't have errors and conforms to the PEP8 style recommendations.
	- The code shows its output in clearly formatted plain text.
	
SYSTEMN SETUP AND HOW TO VIEW THE PROJECT
The project makes use of Udacity Linux based virtual machine configuration which includes all of the necessary software to run the application.
	1. Download Vagrant and install.
	2. Download Virtual Box and install.
	3. Clone this repository to a directory of your choice.
	4. Download the newsdata.sql (extract from newsdata.zip (not provided here)) and newsdata.py files from the respository and move them to the vagrant directory within your VM.

RUN THIS COMMANDS FROM TERMINAL IN THE FOLDER WHERE THE VAGRANT IS INSTALLED IN:
	1. vagrant up - start up the VM.
	2. vagrant ssh - log into the VM.
	3. cd /vagrant - change to your vagrant directory.
	4. psql -d news -f newsdata.sql - load the data and create the tables.
	5. python3 newsdata.py - run the reporting tool.


VIEWS USED
	statustotal
		CREATE VIEW statustotal AS
		SELECT time ::date,
		       status
		FROM log;


	statusfailed
		CREATE VIEW statusfailed AS
		SELECT time,
		       count(*) AS num
		FROM statustotal
		WHERE status = '404 NOT FOUND'
		GROUP BY time;


	statusall
		CREATE VIEW statusall AS
		SELECT time,
		       count(*) AS num
		FROM statustotal
		WHERE status = '404 NOT FOUND'
		  OR status = '200 OK'
		GROUP BY time;


	percentagecount
		CREATE VIEW percentagecount AS
		SELECT statusall.time,
		       statusall.num AS numall,
		       statusfailed.num AS numfailed,
		       statusfailed.num::double precision/statusall.num::double precision * 100 AS percentagefailed
		FROM statusall,
		     statusfailed
		WHERE statusall.time = statusfailed.time;