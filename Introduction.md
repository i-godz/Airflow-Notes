- [Benefits of Airflow](#2--benefits-of-airflow)
    - [Dynamic](#21--dynamic)
    - [Flexibility and Scalability](#22--flexibility-and-scalability)
    - [Fully Functional User Interface](#23--fully-functional-user-interface)
- [Airflow Components](#3--airflow-components)
    - [The Web Server](#31--the-web-server)
    - [The Scheduler](#32--the-scheduler)
    - [Meta Database](#33--meta-database)
    - [The Triggerer](#34--the-triggerer)
    - [The Executor](#35--the-executor)
    - [The Queue](#36--the-queue)
    - [The Worker](#37--the-worker)
- [Airflow Core Concepts](#4--airflow-core-concepts)
    - [The DAG (Directed Acyclic Graph)](#41--the-dag-directed-acyclic-graph)
    - [Operator](#42--operator)
    - [Tasks / Task Instances](#43--tasks--task-instances)
    - [Workflow](#44--workflow)
- [Different Architectures](#5--different-architectures)
    - [Single Node Architecture](#51--single-node-architecture)
    - [Multi Node Architecture](#52--multi-node-architecture)
- [DAG Execution Flow](#6--dag-execution-flow)

## 1- What is Airflow.
- Am open source tool to programmatically author, schedule and monitor workflows. 
- it like a very smart to-do list for your workflow that runs itself.
Apache Airflow helps you to 
- Set the order of your tasks 
- Make sure each task starts only when the previous ones are done.
- Control the timing of your entire data process.
## 2- Benefit's of Airflow
### Dynamic 
Airflow can adapt and change based on whats happening.
- **Python based:** Easy-to-use and powerful, write your workflows in python.
- **Dynamic Tasks:** Generate task based on dynamic input.
- **Dynamic Workflows:** Generate workflows based on static inputs.
- **Branching:** Execute a different set of tasks based on a condition or result.
### Flexibility and Scalability 
- Connect to many data sources and tools.
- Start small and grow as your projects get bigger.
- Customize your work to fit your extracts needs.
### Fully functional and User interface
Airflow has a visual dashboard where you can see and control your tasks and workflow.
- Monitor and troubleshot your workflows.
- Highlight relations between workflows and tasks.
- Identify bottlenecks with performance metrics.
- Manage users and roles of your airflow instance.
## 3- Airflow Components
### 3.1- The Web Server
Provides a user interface when you use airflow.
- Allows you to view, manage and monitor your workflow through a web browser.
### 3.2- The Scheduler 
- Responsible for determining when tasks should run.
### 3.3- Meta Database 
- This is a database that stores informations about your tasks and their status.
- It keeps track of all the important details about your workflows.
### 3.4- The Triggerer 
- Responsible for managing deferrable tasks, that are waiting for external events.
- Efficiently handles tasks that depends on external factors without blocking other processes.
### 3.5- The Executor
- Determines how your tasks your run.
- Managing the execution of the tasks, deciding whether to run them in sequence or in parallel and on which system.
### 3.6- The Queue
- List of tasks waiting to be executed.
- Helps to manage the order of tasks execution, especially when there are many tasks to run.
### 3.7- The Worker
- The processes that actually perform the tasks.
- They do the actual work defined in your tasks
## 4- Airflow Core Concepts
![](https://i.imgur.com/ixZUYg3.png)
### 4.1- The DAG (Directed Acyclic Graph)
- Is a collection of all the tasks you want to run, organized in a way that reflects their dependencies.
- It helps you define the structure of your entire workflow, showing which tasks needed to happen before others. 
### 4.2- Operator 
- Defines a single, ideally idempotent tasks in your DAG.
- Allow you to break down your workflow into discrete, manageable pieces of work. 
- Airflow has thousands of operators:
	- PythonOperator to execute a python script or function.
	- BashOperator to execute a bash script or command.
	- SQLExecuteQueryOperator to execute a SQL query to a database.
	- FileSensor to wait for a file.
### 4.3- Tasks / Tasks Instance
- Specific instance of an operator. When an operator is assigned to a DAG, it becomes a tasks.
- Tasks are the actual units of work that gets executed when your DAG runs.
### 4.4- Workflow 
- The entire process defined by your DAG, including all the tasks and their dependencies.
- Represents your entire data pipeline, showing how all the pieces fir together to achieve your goal.

## 5- Different Architectures
-  A node is a single computer or server.
### 5.1- Single Node Architecture
- A single node architectures means all components of airflow are running on one machine.
  ![](https://i.imgur.com/785QliE.png)
#### Why use single node 
- Greta to get started or small workflows
- SImple to setup and manage
### 5.2- Multi Node Architecture
- Running airflow across multiple computers or servers.
![](https://i.imgur.com/ejaj2k9.png)
#### Why use single node 
- Can add more workers to handle more tasks.
- If one machine fails, other can take over its work.
- By distributing work across machines, you can process more tasks simultaneously.
### 6- DAG Execution Flow 
![](https://i.imgur.com/22XNZGX.png)

- Write your DAG file and place it in the DAG folder.
- Then the scheduler regularly scans the folder and finds the new added DAG file and reads it.
- Then the scheduler creates a DagRun, which is an instance at a given time.
- Then it marks the DagRun run an queued in the meta database.
- If the task is ready, the scheduler marks the DagRun as running in the meta database.
- Creates a TaskInstance corresponding to the task and marks the instance marked as scheduled in the meta database.
- Then the Scheduler send the TaskInstance to the executer and the status is queued in the meta database.
- The executer creates a process corresponding to the worker and these worker takes the TaskInstance and marks it as running and ones tasks are completed the worker marks the instance as completed in the meta database as success or failed