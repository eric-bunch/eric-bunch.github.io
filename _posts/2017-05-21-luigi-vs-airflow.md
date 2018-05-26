---
layout: post
title: "Luigi vs. Airflow"
date: 2017-05-21
blurb: Comparison of data pipelining libraries Spotify's Luigi and Airbnb's Airflow.
author: Eric Bunch
---





## Pipelines
<a href="http://luigi.readthedocs.io/en/stable/index.html">Luigi</a> and <a href="https://airflow.incubator.apache.org/">Airflow</a> are two pipelining libraries written in Python produced by teams at Spotify and Airbnb respectively. Recently at work, we had cause to examine these two pipelining libraries, and here I will sum up what we found. In the end, Airflow got the most favorable votes from the team, mainly for it's ability to scale better.


### Luigi

Luigi is a python pipelining framework designed by the data science team at Spotify. It's main object is a class called <code>Task</code>, which has three main methods to use when constructing a pipeline: <code>requires</code>, <code>output</code>, and <code>run</code>. The <code>requires</code> method is used to define which other tasks are prerequisites for the task in question. The <code>output</code> method is used to define what the output of the task will be-- whatever this is will be what a <code>requires</code> method will check for to see if a certain task has completed. This could be a local file, a file on S3, record in a database, etc. The <code>run</code> method is what the task actually does-- pull data from an API, train a machine learning model, upload records to a database, etc.




-  A Luigi Task is straightforward to define, but it can be difficult if a Task needs to be changed. Any tasks that depend on that task need to have that output specified in the `requires` method, which can be cumbersome if there are lots of them and they have to all be changed individually.  

- Luigi has a number of pre-baked Tasks $\text{(e.g. S3 Tasks, Redshift Tasks, R Tasks)}$ to help with some fairly common operations.

- Luigi has a pipeline visualizer in the form of a webapp that can be run locally. It has information about which tasks have are running, have failed, or completed successfully, as well as a graph of the task dependencies.

- In order to kick off a Luigi pipeline, it must be started from the command line-- either manually or through some cron job.

- If a pipeline fails and stops running, it is easy $\text{(once the reason for failure is addressed)}$ to restart the pipeline, and only the unfinished Tasks will run. However, it is difficult to re-run a pipeline once it has fully completed.

- As stated on the documentation page for Luigi, does not scale well beyond tens of thousands of jobs in a single pipeline. This is partly due to the fact that it does not handle execution distribution.



### Airflow


- An Airflow task is straightforward to define. There are a number of pre-baked operators that can be used to build tasks, including <code>BashOperator</code>, <code>PythonOperator</code>, <code>HTTPOperator</code>, and <code>SqlOperator</code>. For instance the <code>PythonOperator</code> is used to tell the Task to execute a Python function. Then, separate from the Task itself, the dependencies are specified.

- Airflow Tasks are typically broken down into smaller executable pieces $\text{(like a single function)}$, whereas Luigi Tasks are typically larger operations.

- Airflow has a scheduler that handles kicking off a workflow at specified intervals or times.

- Airflow has a quite extensive Task visualizer that shows the state of all workflows $\text{(running or not running)}$, as well as a graph dependency visualization showing which tasks have been completed, failed, or are currently running. The workflows and tasks can also be managed through the visualizer's GUI.

- A Task's operation is separated from it's state. A record of Task states is kept in a database table. This helps to avoid having to make dummy files to mark the completion of a Task, or some other such method.

- It is easy to rerun a workflow once it has been completed.

- Airflow supports a Celery executor on the backend. This means two things: 1. Airflow can leverage Celery's concurrent executing and resource allocation power, 2. Allows to easily restart a workflow if one fails partway through, due to the state persistence Celery lends.



<h2>Conclusion</h2>

We found Airflow much more suitable for our needs, in large part due to the option to use Celery on the backend. This allows us to dynamically allocate resources to a Task and execute them in parallel. Personally, I find Airflow easier to manage due to the separation of Task operation and state, as well as the ease of use of the visualizer.
