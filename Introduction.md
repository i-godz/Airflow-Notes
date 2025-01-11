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
   - [The Web Server](#the-web-server)
   - [The Scheduler](#the-scheduler)
   - [Meta Database](#meta-database)
   - [The Triggerer](#the-triggerer)
   - [The Executor](#the-executor)
      - [Types of Executors](#types-of-executors)
   - [The Queue](#the-queue)
   - [The Worker](#the-worker)
4. [Airflow Core Concepts](#4-airflow-core-concepts)
   - [The DAG (Directed Acyclic Graph)](#the-dag-directed-acyclic-graph)
   - [Operator](#operator)
      - [Best Practices when using Operators](#best-practices-when-using-operators)
   - [Tasks / Task Instance](#tasks-task-instance)
   - [Workflow](#workflow)
5. [Different Architectures](#5-different-architectures)
   - [Single Node Architecture](#single-node-architecture)
   - [Multi Node Architecture](#multi-node-architecture)
6. [DAG Execution Flow](#6-dag-execution-flow)
7. [DAG Skeleton](#7-dag-skeleton)
   - [Creating a RDBMS Table](#creating-a-rdbms-table)
   - [Checking for API Availability](#checking-for-api-availability)
   - [Extract and Process Users](#extract-and-process-users)
   - [Hooks](#hooks)
   - [Datasets](#datasets)
      - [Extra](#extra)

## 1. What is Airflow
- An open-source tool to programmatically author, schedule, and monitor workflows. 
- It's like a very smart to-do list for your workflow that runs itself.

Apache Airflow helps you to:
- Set the order of your tasks.
- Make sure each task starts only when the previous ones are done.
- Control the timing of your entire data process.

## 2. Benefits of Airflow

### Dynamic
Airflow can adapt and change based on what's happening.

- **Python-based:** Easy-to-use and powerful, write your workflows in Python.
- **Dynamic Tasks:** Generate tasks based on dynamic input.
- **Dynamic Workflows:** Generate workflows based on static inputs.
- **Branching:** Execute a different set of tasks based on a condition or result.

### Flexibility and Scalability
- Connect to many data sources and tools.
- Start small and grow as your projects get bigger.
- Customize your work to fit your exact needs.

### Fully Functional and User Interface
Airflow has a visual dashboard where you can see and control your tasks and workflow.

- Monitor and troubleshoot your workflows.
- Highlight relations between workflows and tasks.
- Identify bottlenecks with performance metrics.
- Manage users and roles of your Airflow instance.

## 3. Airflow Components

### The Web Server
- Provides a user interface when you use Airflow.
- Built with Flask.
- Allows you to view, manage, and monitor your workflow through a web browser.

### The Scheduler
- The heart of Airflow.
- Responsible for determining when tasks should run and which resources to run on.

### Meta Database
- Stores information about your tasks and their status.
- Keeps track of all the important details about your workflow instance.

### The Triggerer
- Responsible for managing deferrable tasks that are waiting for external events.
- Efficiently handles tasks that depend on external factors without blocking other processes.

### The Executor
- Determines how your tasks run.
- Manages the execution of the tasks, deciding whether to run them in sequence or in parallel and on which system.

#### Types of Executors:

##### Sequential
- The default executor when installing Airflow manually.
- Database must be SQLite.
- Runs one task at a time.
- Used for experimenting and debugging issues.

##### Local
- Executes multiple tasks at the same time, but on the same machine.
- Uses all types of databases except SQLite.
- Doesn't scale well as it depends on the machine it runs on.

##### Celery
- Executes multiple tasks on multiple machines.

### The Queue
- A list of tasks waiting to be executed.
- Helps manage the order of task execution, especially when there are many tasks to run.

### The Worker
- The processes that actually perform the tasks.
- They do the actual work defined in your tasks.

## 4. Airflow Core Concepts

### The DAG (Directed Acyclic Graph)
- A collection of all the tasks you want to run, organized in a way that reflects their dependencies.
- Helps define the structure of your entire workflow, showing which tasks need to happen before others.

### Operator
- Defines a single, ideally idempotent task in your DAG.
- Allows you to break down your workflow into discrete, manageable pieces of work.

#### Best Practices when using Operators
- Define one task in your data pipeline in each operator.
- If processing data fails, only that operator will need to be restarted.

### Tasks / Task Instance
- A specific instance of an operator. When an operator is assigned to a DAG, it becomes a task.
- Tasks are the actual units of work that get executed when your DAG runs.

### Workflow
- The entire process defined by your DAG, including all the tasks and their dependencies.
- Represents your entire data pipeline, showing how all the pieces fit together to achieve your goal.

## 5. Different Architectures

### Single Node Architecture
- All components of Airflow run on one machine.

#### Why use single node
- Great for getting started or small workflows.
- Simple to set up and manage.

### Multi Node Architecture
- Running Airflow across multiple computers or servers.

#### Why use multi-node
- Can add more workers to handle more tasks.
- If one machine fails, others can take over its work.
- By distributing work across machines, you can process more tasks simultaneously.

## 6. DAG Execution Flow

1. Write your DAG file and place it in the DAG folder.
2. The scheduler regularly scans the folder, finds the new DAG file, and reads it.
3. The scheduler creates a DagRun, which is an instance at a given time.
4. It marks the DagRun as queued in the meta database.
5. If the task is ready, the scheduler marks the DagRun as running in the meta database.
6. Creates a TaskInstance corresponding to the task and marks the instance as scheduled in the meta database.
7. The scheduler sends the TaskInstance to the executor, and the status is queued in the meta database.
8. The executor creates a process corresponding to the worker. The worker takes the TaskInstance, marks it as running, and once completed, marks the instance as completed in the meta database as success or failure.

## 7. DAG Skeleton

### Creating a RDBMS Table
```python
# Imports DAG library from Airflow 
from airflow import DAG

# Imports datetime library from datetime 
from datetime import datetime 

# Imports PostgresOperator library 
from airflow.providers.postgres.operators.postgres import PostgresOperator

with DAG('user_processing',  
          start_date=datetime(2025, 1, 2), 
          schedule_interval='@daily',
          catchup=False
) as dag:

    create_table = PostgresOperator(
        task_id='create_table',
        postgres_conn_id='postgres',
        sql='''
            CREATE TABLE IF NOT EXISTS users (
                first_name TEXT NOT NULL,
                last_name TEXT NOT NULL,
                country TEXT NOT NULL,
                username TEXT NOT NULL, 
                password TEXT NOT NULL,
                email TEXT NOT NULL
            );
        '''
    )
```

### Checking for API Availability

#### Parameters

- **poke_interval:** Defines 60 seconds by default, so every 60 seconds it checks if the condition is true before executing the next task.
- **timeout:** Defined to 7 days by default.

```python
# Imports DAG library from Airflow 
from airflow import DAG

# Imports datetime library from datetime 
from datetime import datetime 

# Imports PostgresOperator library 
from airflow.providers.postgres.operators.postgres import PostgresOperator

# Imports HttpSensor library
from airflow.providers.http.sensors.http import HttpSensor

with DAG('user_processing',  
          start_date=datetime(2025, 1, 2), 
          schedule_interval='@daily',
          catchup=False
) as dag:

    is_api_available = HttpSensor(
        task_id='is_api_available',
        http_conn_id='user_api',
        endpoint='api/',
    )
```

### Extract and Process Users

```python
# Imports DAG library from Airflow 
from airflow import DAG

# Imports datetime library from datetime 
from datetime import datetime 

# Imports PythonOperator 
from airflow.operators.python import PythonOperator

import json
from pandas import json_normalize

def _process_user(ti):
    user = ti.xcom_pull(task_ids='extract_user')
    user = user['results'][0]
    processed_user = json_normalize({
        'first_name': user['name']['first'],
        'last_name': user['name']['last'],
        'country': user['location']['country'],
        'username': user['login']['username'],
        'password': user['login']['password'],
        'email': user['email']
    }) 
    processed_user.to_csv('/Users/godzilla/Desktop/output.csv', index=None, header=False) 

with DAG('user_processing',  
          start_date=datetime(2025, 1, 2), 
          schedule_interval='@daily',
          catchup=False
) as dag:

    process_user = PythonOperator(
        task_id='process_user',
        python_callable=_process_user
    )
```

### Hooks
- Allows interaction with external services, abstracting the complexity of the interaction.

### Datasets
- Acts as a logical grouping of data as a file or an SQL table.

#### URI:
- Unique identifier of your data.
- Path to your data.
- Must be composed of only ASCII characters.
- URI schema cannot be Airflow.
- Case sensitive.

```python
from airflow import Dataset

# Valid dataset:
schemeless = Dataset("/path/file.txt")
csv_file = Dataset("file.csv")

# Invalid datasets:
reserved = Dataset("airflow://file.text")
not_ascii = Dataset("file_dàtaset")
