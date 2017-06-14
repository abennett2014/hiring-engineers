Bonus question: In your own words, what is the Agent?

The agent is a background service that runs on 1..* hosts in the 1..* environments a customer wishes to monitor. The agent is able to capture events and metrics for both infrastructure statistics and application statistics. The agent comes with a number of pre-built integrations but a customer can also write as many custom integrations as they wish.

Bonus question: What is the difference between a timeboard and a screenboard?

Let me start by saying I had a lot more fun with this one then i first anticipated!!

To answer the question first, a timeboard allows you to view all your events and metrics for a specific scope of time (past hour, past 4 hours, past day, etc). It is a great view when looking to troubleshoot an issue that affected multiple applications or areas of infrastructure at the same time.

A screenboard allows the customer to get a high-level view into a complete system. It has a lot more flexibility then the timeboard as it allows you to look at different events and metrics across different time frames. This can be important when some metrics/events need minute to minute views while others need hour to hour or day to day to give you a complete picture of the system. Even more importantly, screenboards can be shared as a whole and timeboards can only be shared per graph.
This is where the fun starts. The cloned dashboard for MySQL was a timeboard, but I wanted a screenboard so I could share a link as a part of the evaluation and instead of recreating by hand, I took a different approach: 

TL;DR - https://p.datadoghq.com/sb/02c466c25-b053e59c95

1. Cloned the MySQL OOTB DashBoard. 
2. Added additional graphs
3. Noticed the Dashboard was a Timeboard and not a screenboard, so i couldn't create a shared external link.
4. Found a utility online "dashconverter.py" that allowed me to do a conversion
5. Installed the dd base libs for python on the host (also my client)
6. Hit an error and debugged to resolve:
	Traceback (most recent call last):
	  File "dashconverter.py", line 197, in <module>
		converter().main(sys.argv[1])
	  File "dashconverter.py", line 193, in main
		cls.convert_t2s(graphs)
	  File "dashconverter.py", line 132, in convert_t2s
		print(graphs[i]['definition']['viz'])
	KeyError: 'viz'
7. Issue was that there is a bug in the OOTB MySQL Integration Dashboard. It looks like the conversion script struggles when viz is not the first variable in the json config. Moving the viz variable above requests fixed the issue
{
  "requests": [
    {
      "q": "sum:mysql.net.connections{$scope}",
      "type": "line",
      "conditional_formats": []
    },
	{
      "q": "sum:mysql.net.max_connections{$scope}",
      "type": "line"
    }
  ],
  "status": "done",
  "viz": "timeseries",
  "autoscale": true
}
8. A couple of bugs that would be quick fixes in the dashconverter.py. Title is not converted and the scope got changed to $scope, but it was still way quicker then creating from scratch.