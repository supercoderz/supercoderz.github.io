---
layout: post
title: "Building a simple workflow engine in Python"
date: 2011-11-03 18:45
comments: false
categories:
---

In my last post I described how a <a  title="Workflow engine" href="http://en.wikipedia.org/wiki/Workflow_engine" rel="wikipedia">workflow engine</a> works and the various key parts of a workflow engine. In this post I will show you how to build  simple workflow engine in Python. This will be built in Python using the <a  title="Django (web framework)" href="http://www.djangoproject.com" rel="homepage">django framework</a>.

Read on to find out more - It would be recommended to get an overview of django framework if you plan to download the code and try this out. I will not be going into the details of how to setup django and the internals.
<h3><!--more-->The basic idea</h3>
The idea of this post is to show a simple workflow engine. We will build a workflow engine that can take a process and a set of tasks associated with the process in a given sequence and execute the tasks. The tasks can be code tasks which have a piece of <a  title="Python (programming language)" href="http://www.python.org/" rel="homepage">Python code</a> that would be executed, or manual tasks which would require someone to approve the task.

The work of creating the tasks and storing them somewhere in a DB will be handled by django. django is a web development framework for Python that makes building web applications very easy. The django admin application which comes bundled with the framework make it very trivial to create simple <a  title="Create, read, update and delete" href="http://en.wikipedia.org/wiki/Create%2C_read%2C_update_and_delete" rel="wikipedia">CRUD</a> web pages for databases.

The workflow engine that takes tasks and either executes them or queues them for approval is written as a django custom function so that it can be easily invoked with access to all the database models.

Starting a new process and approving a task - since this a demo application to show how the workflow engine works, I did not build a UI for these tasks. These are also built as custom django commands. But you can easily build some sort of a simple django page for these with very less effort.

The code for this can be accessed from - <a title="Krishna" href="http://krishna.lifeandit.com/">http://krishna.lifeandit.com</a>
<h3>The Models</h3>
Any workflow engine has a DB model to maintain the relationships between the processes, tasks and the various states. We will have three main entities - Process, Task and TaskTransitions. TaskTransitions will be responsible for maintaining the relationship between a task and the process that it is part of. This will also contain details about where the task occurs in that process  in the form of a sequence number.

Apart from this, there will be two tables to maintain a list of running processes and a list of tasks waiting for approval.

These models are defined as shown below
<pre style="padding-left:60px;">from django.db import models

class Process(models.Model):
	name=models.CharField(max_length=200)

	def __str__(self):
		return self.name

class Task(models.Model):
	name=models.CharField(max_length=200)
	manual=models.BooleanField()
	parent=models.ForeignKey(Process)
	code=models.CharField(max_length=2000)
	approver=models.CharField(max_length=200)

	def __str__(self):
		return self.name

class TaskTransitions(models.Model):
	process=models.ForeignKey(Process)
	task=models.ForeignKey(Task)
	sequence=models.IntegerField()

	def __str__(self):
		return self.process.name+str(self.sequence)

class RunningProcesses(models.Model):
	process=models.ForeignKey(Process)
	state=models.CharField(max_length=200)
	step=models.IntegerField()class WaitingForApproval(models.Model):
	process=models.ForeignKey(Process)
	task=models.ForeignKey(Task)
	rpid=models.IntegerField()</pre>
Once these models are defines, you can easily create tasks in the django admin page. If you are downloading the code for this, then please edit the djnago settings.py file and point to a database of your choice. Then run 'python manage.py syncdb' to create all the tables and 'python manage.py runserver' to start the server. You can browse to the django admin page at http://localhost:8000/admin and login with the user ID that you created while doing syncdb. You should be able to create some process, tasks and define the relationships between them.
<h3>The Engine</h3>
The workflow engine needs to have three parts, one to create a running instance of a process, one to approve a task and one to actually run through the processes and execute them. We will build these using django models to easily access the database.

For creating a running instance of a process definition, we just need to make an entry in the running processes table. This can be done very easily as shown here
<p style="padding-left:60px;"></p>

<pre style="padding-left:60px;">from django.core.management.base import BaseCommand, CommandError
from krishna.workflow.models import *

class Command(BaseCommand):
	args='&lt;process name&gt; &lt;process name&gt; ...'
	help='Start the specified processes'

	def handle(self, *args, **options):
		for process_name in args:
			p=Process.objects.get(name=process_name)
			r=RunningProcesses(process=p,state="READY",step=0)
			r.save()</pre>
<p class="zemanta-related-title" style="font-size:1em;">On similar lines, approving the tasks can be done as follows - note that here the code approves all instances of that task in the process - there is no ability to approve on a per process instance basis - this can be built on top easily.</p>

<pre style="padding-left:60px;">from django.core.management.base import BaseCommand, CommandError
from krishna.workflow.models import *

class Command(BaseCommand):
	args='&lt;process name&gt; &lt;task name&gt;'
	help='Approve the given task in the specified process'

	def handle(self, *args, **options):
		process_name=args[0]
		task_name=args[1]
		p=Process.objects.get(name=process_name)
		t=Task.objects.get(name=task_name)
		approvals=WaitingForApproval.objects.filter(process=p,task=t)
		for approval in approvals:
			approval.task.approved=True
			approval.task.save()
			rp=RunningProcesses.objects.get(id=approval.rpid)
			rp.state='READY'
			rp.step=rp.step+1
			rp.save()	
			approval.delete()</pre>
<p class="zemanta-related-title" style="font-size:1em;">The workflow engine itself has a very simple mandate - look for all entries in the running process table with state as READY - these are either newly inserted instances or executing processes. Take the process name and the current step from here and get the corresponding task. If this task is a code task then execute it and increment the step number in the running process table. Manual tasks are added to another table and the state of the process instance is set to WAITING. Once approved, the state will be set to READY and the step number incremented.  This entire flow runs in a loop with a 10 second sleep between each run. In each run, it executes one step from any given process.</p>

<pre style="padding-left:60px;">from django.core.management.base import BaseCommand, CommandError
from krishna.workflow.models import *
import time

class Command(BaseCommand):
	args='No arguments'
	help='Start the workflow engine so that it can run the processes'

	def handle(self, *args, **options):
		while True:
			#Get all running processes which are READY
			rprocesses=RunningProcesses.objects.filter(state='READY')
			for rprocess in rprocesses:
				try:
					#get the underlying process and task 
					p=rprocess.process
					seq=rprocess.step
					#get the task for this sequence in the flow
					tr=TaskTransitions.objects.get(process=p,sequence=seq)
					t=tr.task
					if t.manual:
						#If it is manual then put it in wait state and make 
						#an entry in approval table
						w=WaitingForApproval(process=p,task=t,rpid=rprocess.id)
						w.save()
						rprocess.state='WAITING'
						rprocess.save()
					else:
						#else execute the code and save the result
						res=eval(t.code)
						print 'Result of ',p.name,' task ',t.name,' is ',res
						rprocess.step=rprocess.step+1
						rprocess.save()
				except:
					pass
			time.sleep(10)</pre>
<p class="zemanta-related-title" style="font-size:1em;"></p>

<h2>Conclusion</h2>
<p class="zemanta-related-title" style="font-size:1em;">This code illustrates a very simple workflow - this cannot be used in a real life scenario without adding more states, more easy ways to define processes, start process or approve processes from a UI. Also this would need to support some sort of a process definition standard like BPEL and derieve the processes and tasks from that. But this is a good exercise to prove the basics.</p>

<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://supercoderz.in/2011/10/08/workflows-demystified/">Workflows demystified</a> (supercoderz.in)</li>
</ul>