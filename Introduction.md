# Table of Contents

1. [What is Airflow](#1-what-is-airflow)
2. [Benefits of Airflow](#2-benefits-of-airflow)
   - [Dynamic](#dynamic)
   - [Python-based](#python-based)
   - [Dynamic Tasks](#dynamic-tasks)
   - [Dynamic Workflows](#dynamic-workflows)
   - [Branching](#branching)
   - [Flexibility and Scalability](#flexibility-and-scalability)
   - [Fully Functional and User Interface](#fully-functional-and-user-interface)
3. [Airflow Components](#3-airflow-components)
   - [3.1 The Web Server](#31-the-web-server)
   - [3.2 The Scheduler](#32-the-scheduler)
   - [3.3 Meta Database](#33-meta-database)
   - [3.4 The Triggerer](#34-the-triggerer)
   - [3.5 The Executor](#35-the-executor)
      - [Types of Executors](#types-of-executors)
   - [3.6 The Queue](#36-the-queue)
   - [3.7 The Worker](#37-the-worker)
4. [Airflow Core Concepts](#4-airflow-core-concepts)
   - [4.1 The DAG (Directed Acyclic Graph)](#41-the-dag-directed-acyclic-graph)
   - [4.2 Operator](#42-operator)
      - [Best Practices when using Operators](#best-practices-when-using-operators)
   - [4.3 Tasks / Task Instance](#43-tasks--task-instance)
   - [4.4 Workflow](#44-workflow)
5. [Different Architectures](#5-different-architectures)
   - [5.1 Single Node Architecture](#51-single-node-architecture)
   - [5.2 Multi Node Architecture](#52-multi-node-architecture)
6. [DAG Execution Flow](#6-dag-execution-flow)
7. [DAG Skeleton](#7-dag-skeleton)
   - [7.1 Creating a RDBMS Table](#71-creating-a-rdbms-table)
   - [7.2 Checking for API Availability](#72-checking-for-api-availability)
   - [7.3 Extract and Process Users](#73-extract-and-process-users)
   - [7.4 Hooks](#74-hooks)
   - [7.5 Datasets](#75-datasets)
      - [Extra](#extra)

## 1- What is Airflow
- An open source tool to programmatically author, schedule and monitor workflows.
- It's like a very smart to-do list for your workflow that runs itself.
- Apache Airflow helps you to:
  - Set the order of your tasks.
  - Make sure each task starts only when the previous ones are done.
  - Control the timing of your entire data process.

## 2- Benefits of Airflow
### Dynamic
- Airflow can adapt and change based on what's happening.
- **Python-based:** Easy-to-use and powerful, write your workflows in Python.
- **Dynamic Tasks:** Generate tasks based on dynamic input.
- **Dynamic Workflows:** Generate workflows based on static inputs.
- **Branching:** Execute a different set of tasks based on a condition or result.

### Flexibility and Scalability
- Connect to many data sources and tools.
- Start small and grow as your projects get bigger.
- Customize your work to fit your exact needs.

### Fully Functional and User Interface
- Airflow has a visual dashboard where you can see and control your tasks and workflows.
- Monitor and troubleshoot your workflows.
- Highlight relations between workflows and tasks.
- Identify bottlenecks with performance metrics.
- Manage users and roles of your Airflow instance.

## 3- Airflow Components
### 3.1- The Web Server
- Provides a user interface when you use Airflow.
- Built by Flask.
- Allows you to view, manage, and monitor your workflow through a web browser.

### 3.2- The Scheduler
- The heart of Airflow.
- Responsible for determining when tasks should run and which resources to run on.

### 3.3- Meta Database
- This is a database that stores information about your tasks and their status.
- It keeps track of all the important details about your workflow instances.

### 3.4- The Triggerer
- Responsible for managing deferrable tasks that are waiting for external events.
- Efficiently handles tasks that depend on external factors without blocking other processes.

### 3.5- The Executor
- Determines how your tasks are run.
- Manages the execution of the tasks, deciding whether to run them in sequence or in parallel and on which system.

#### Types of Executors:
##### Sequential
- The default executor when installing Airflow manually.
- Database has to be SQLite.
- Runs one task at a time.
- Used when experimenting and debugging issues.

##### Local
- Can execute multiple tasks at the same time, but on the same machine. Same Airflow instance but with a different database.
- Use all types of databases, not SQLite databases.
- Doesn't scale very well as it depends on the machine on which it runs.

##### Celery
- Executes multiple tasks on multiple machines.

### 3.6- The Queue
- List of tasks waiting to be executed.
- Helps to manage the order of task execution, especially when there are many tasks to run.

### 3.7- The Worker
- The processes that actually perform the tasks.
- They do the actual work defined in your tasks.

## 4- Airflow Core Concepts
### 4.1- The DAG (Directed Acyclic Graph)
- Is a collection of all the tasks you want to run, organized in a way that reflects their dependencies.
- It helps you define the structure of your entire workflow, showing which tasks need to happen before others.

### 4.2- Operator
- Defines a single, ideally idempotent task in your DAG.
- Allows you to break down your workflow into discrete, manageable pieces of work.
- Airflow has thousands of operators:
  - `PythonOperator` to execute a Python script or function.
  - `BashOperator` to execute a bash script or command.
  - `SQLExecuteQueryOperator` to execute a SQL query to a database.
  - `FileSensor` to wait for a file.

#### There are 3 types of operators:
- **Action Operators:** Execute an action.
- **Transfer Operators:** Transfer data.
- **Sensor Operators:** Wait for a condition to be met.

#### Best practices when using operators:
- Define one task in your data pipeline in each operator.
- Why? If processing data fails, then only that operator will be restarted.

### 4.3- Tasks / Task Instance
- Specific instance of an operator. When an operator is assigned to a DAG, it becomes a task.
- Tasks are the actual units of work that get executed when your DAG runs.

### 4.4- Workflow
- The entire process defined by your DAG, including all the tasks and their dependencies.
- Represents your entire data pipeline, showing how all the pieces fit together to achieve your goal.

## 5- Different Architectures
- A node is a single computer or server.

### 5.1- Single Node Architecture
- A single node architecture means all components of Airflow are running on one machine.

#### Why use single node:
- Great to get started or for small workflows.
- Simple to set up and manage.

### 5.2- Multi Node Architecture
- Running Airflow across multiple computers or servers.

#### Why use multi-node:
- Can add more workers to handle more tasks.
- If one machine fails, others can take over its work.
- By distributing work across machines, you can process more tasks simultaneously.

## 6- DAG Execution Flow
- Write your DAG file and place it in the DAG folder.
- Then the scheduler regularly scans the folder and finds the newly added DAG file and reads it.
- Then the scheduler creates a `DagRun`, which is an instance at a given time.
- Then it marks the `DagRun` as queued in the meta database.
- If the task is ready, the scheduler marks the `DagRun` as running in the meta database.
- Creates a `TaskInstance` corresponding to the task and marks the instance as scheduled in the meta database.
- Then the Scheduler sends the `TaskInstance` to the executor, and the status is queued in the meta database.
- The executor creates a process corresponding to the worker, and these workers take the `TaskInstance` and mark it as running. Once tasks are completed, the worker marks the instance as completed in the meta database as success or failed.

## 7- DAG Skeleton
- The following is a basic DAG code:

```python
# Imports DAG library from Airflow
from airflow import DAG

# Imports datetime library from datetime
from datetime import datetime

with DAG('DAG Id',
          start_date = datetime(2025,1,2),
          schedule_interval = '@daily',
          catchup = False
) as dag: None