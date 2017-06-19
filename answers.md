1)	Introduction:
Thank you for the opportunity to present to the technical team at DataDog. My name is Andrew Bennett and I have been an IT professional since just before the first tech bubble burst (started in 1998 at the ripe old age of 18). My professional experience is spread from technical support, to software development. Service implementation, to software sales. My prior experience has been predominately with Oracle technology (although mainly through acquisition), and I have specialized on the application / app dev side for most of my career.

2)	Level 0 (optional) - Setup an Ubuntu VM
As I already had a number of VM’s already setup with Oracle Linux, using Oracle VM, I decided it was quickest to leverage one of these. My test VM system is mainly WebLogic Application Server, Oracle Database and a bunch of Oracle and self-written Java apps.
https://github.com/abennett2014/hiring-engineers/blob/abennett2014-patch-1/Base.png
Signing up for the DD service was simple and the instructions were easy to follow. I first installed the agent in its’ base form, walking through the installation instructions and ensuring I had full communication between my VM and the DD cloud service. I hit a brief issue with network, which was a simple port conflict fix and within 10 minutes was able to see my host (name=content) and my basic system metrics in the DD dashboard.
https://raw.githubusercontent.com/abennett2014/hiring-engineers/abennett2014-patch-1/Infrastructure%20List.PNG

3)	Bonus question: In your own words, what is the Agent?
The agent is a background service that runs on 1..* hosts in the 1..* environments a customer wishes to monitor. The agent is able to capture events and metrics for both infrastructure statistics and application statistics. The agent comes with a number of pre-built integrations but a customer can also write as many custom integrations as they wish.

4)	Install a database on your machine (MongoDB, MySQL, or PostgreSQL) and then install the respective Datadog integration for that database.

After discovering that there was no native integration for Oracle Database (let’s get one going!!) I decided on installing MySQL. Once installed, I updated the MySQL.yaml file to include my connection information:

https://raw.githubusercontent.com/abennett2014/hiring-engineers/abennett2014-patch-1/mysql.yaml

To create load, I used one of my favorite DB load tools:

https://github.com/abennett2014/hiring-engineers/blob/abennett2014-patch-1/HammerDB%20-%20Excellent%20DB%20load%20generator.PNG

5)	Write a custom Agent check that samples a random value. Call this new metric: test.support.random
I borrowed code from the DataDog support site and built the custom agent using the example provided.

https://raw.githubusercontent.com/abennett2014/hiring-engineers/abennett2014-patch-1/random.py

https://raw.githubusercontent.com/abennett2014/hiring-engineers/abennett2014-patch-1/random.yaml 

6)	Since your database integration is reporting now, clone your database integration dashboard and add additional database metrics to it as well as your test.support.random metric from the custom Agent check.
I started by cloning the out of the box MySQL dashboard and adding the additional metrics I was looking at for system and random:

https://github.com/abennett2014/hiring-engineers/blob/abennett2014-patch-1/cloned-board.PNG

7)	Bonus question: What is the difference between a timeboard and a screenboard?

A timeboard allows you to view all your events and metrics for a specific scope of time (past hour, past 4 hours, past day, etc). It is a great view when looking to troubleshoot an issue that affected multiple applications or areas of infrastructure at the same time.

A screenboard allows the customer to get a high-level view into a complete system. It has a lot more flexibility then the timeboard as it allows you to look at different events and metrics across different time frames. This can be important when some metrics/events need minute to minute views while others need hour to hour or day to day to give you a complete picture of the system. Even more importantly, screenboards can be shared as a whole and timeboards can only be shared per graph. This is where the fun starts. 

The cloned dashboard for MySQL was a timeboard, but I wanted a screenboard so I could share a link as a part of the evaluation and instead of recreating by hand, I took a different approach:

a.	TL;DR - https://p.datadoghq.com/sb/02c466c25-b053e59c95
b.	Cloned the MySQL OOTB DashBoard.
c.	Added additional graphs
d.	Noticed the Dashboard was a Timeboard and not a screenboard, so i couldn't create a shared external link.
e.	Found a utility online "dashconverter.py" that allowed me to do a conversion: https://raw.githubusercontent.com/abennett2014/hiring-engineers/abennett2014-patch-1/dashconverter.py 
f.	Installed the dd base libs for python on the host (also my client)
g.	Hit an error and debugged to resolve: Traceback (most recent call last): File "dashconverter.py", line 197, in converter().main(sys.argv[1]) File "dashconverter.py", line 193, in main cls.convert_t2s(graphs) File "dashconverter.py", line 132, in convert_t2s print(graphs[i]['definition']['viz']) KeyError: 'viz'
h.	Issue was that there is a bug in the OOTB MySQL Integration Dashboard. It looks like the conversion script struggles when viz is not the first variable in the json config. Moving the viz variable above requests fixed the issue { "requests": [ { "q": "sum:mysql.net.connections{$scope}", "type": "line", "conditional_formats": [] }, { "q": "sum:mysql.net.max_connections{$scope}", "type": "line" } ], "status": "done", "viz": "timeseries", "autoscale": true }
i.	A couple of bugs that would be quick fixes in the dashconverter.py. Title is not converted and the scope got changed to $scope, but it was still way quicker then creating from scratch.

8)	Take a snapshot of your test.support.random graph and draw a box around a section that shows it going above 0.90. Make sure this snapshot is sent to your email by using the @notification

https://github.com/abennett2014/hiring-engineers/blob/abennett2014-patch-1/snapshot.PNG

https://github.com/abennett2014/hiring-engineers/blob/abennett2014-patch-1/event%20list.PNG

Level 3 - Alerting on your Data

9)	Set up a monitor on this metric that alerts you when it goes above 0.90 at least once during the last 5 minutes

https://github.com/abennett2014/hiring-engineers/blob/abennett2014-patch-1/monitor.PNG

10)	Bonus points: Make it a multi-alert by host so that you won't have to recreate it if your infrastructure scales up.

See Above!

11)	Give it a descriptive monitor name and message (it might be worth it to include the link to your previously created dashboard in the message). Make sure that the monitor will notify you via email. This monitor should alert you within 15 minutes. So when it does, take a screenshot of the email that it sends you.

https://github.com/abennett2014/hiring-engineers/blob/abennett2014-patch-1/emailed%20alert%20for%20random%20event.PNG

https://github.com/abennett2014/hiring-engineers/blob/abennett2014-patch-1/notification%20of%20monitor%20being%20modified.PNG


12)	Bonus: Since this monitor is going to alert pretty often, you don't want to be alerted when you are out of the office. Set up a scheduled downtime for this monitor that silences it from 7pm to 9am daily. Make sure that your email is notified when you schedule the downtime and take a screenshot of that notification.

https://github.com/abennett2014/hiring-engineers/blob/abennett2014-patch-1/monitor%20downtime.png 
