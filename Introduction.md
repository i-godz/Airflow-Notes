# Table of Contents

1. [What is Airflow](#what-is-airflow)
2. [Benefits of Airflow](#benefits-of-airflow)
   - [Dynamic](#dynamic)
   - [Python-based](#python-based)
   - [Dynamic Tasks](#dynamic-tasks)
   - [Dynamic Workflows](#dynamic-workflows)
   - [Branching](#branching)
   - [Flexibility and Scalability](#flexibility-and-scalability)
   - [Fully Functional and User Interface](#fully-functional-and-user-interface)
3. [Airflow Components](#airflow-components)
   - [3.1 The Web Server](#31-the-web-server)
   - [3.2 The Scheduler](#32-the-scheduler)
   - [3.3 Meta Database](#33-meta-database)
   - [3.4 The Triggerer](#34-the-triggerer)
   - [3.5 The Executor](#35-the-executor)
      - [Types of Executors](#types-of-executors)
   - [3.6 The Queue](#36-the-queue)
   - [3.7 The Worker](#37-the-worker)
4. [Airflow Core Concepts](#airflow-core-concepts)
   - [4.1 The DAG (Directed Acyclic Graph)](#41-the-dag-directed-acyclic-graph)
   - [4.2 Operator](#42-operator)
      - [Best Practices when using Operators](#best-practices-when-using-operators)
   - [4.3 Tasks / Task Instance](#43-tasks--task-instance)
   - [4.4 Workflow](#44-workflow)
5. [Different Architectures](#different-architectures)
   - [5.1 Single Node Architecture](#51-single-node-architecture)
   - [5.2 Multi Node Architecture](#52-multi-node-architecture)
6. [DAG Execution Flow](#dag-execution-flow)
7. [DAG Skeleton](#dag-skeleton)
   - [7.1 Creating a RDBMS Table](#71-creating-a-rdbms-table)
   - [7.2 Checking for API Availability](#72-checking-for-api-availability)
   - [7.3 Extract and Process Users](#73-extract-and-process-users)
   - [7.4 Hooks](#74-hooks)
   - [7.5 Datasets](#75-datasets)
      - [Extra](#extra)


## 1- What is Airflow.
- Am open source tool to programmatically author, schedule and monitor workflows. 
- it like a very smart to-do list for your workflow that runs itself.
Apache Airflow helps you to 
- Set the order of your tasks.
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
- Built by flask.
- Allows you to view, manage and monitor your workflow through a web browser.
### 3.2- The Scheduler 
- The heart of Airflow.
- Responsible for determining when tasks should run and which resources to run on.
### 3.3- Meta Database 
- This is a database that stores informations about your tasks and their status.
- It keeps track of all the important details about your workflows instance.
### 3.4- The Triggerer 
- Responsible for managing deferrable tasks, that are waiting for external events.
- Efficiently handles tasks that depends on external factors without blocking other processes.
### 3.5- The Executor
- Determines how your tasks your run.
- Managing the execution of the tasks, deciding whether to run them in sequence or in parallel and on which system.
#### Types of executers:
##### Sequential 
- The default executer when installing airflow manually.
- Database has to be SQLite.
- Runs one task at a time.
- Used when experimenting and debugging issues.
##### Local 
- Can execute multiple tasks at the same time, but on the same machine. Same airflow instance but with a different database.
- Use all types of database, not SQLite databases.
- Doesn't scale very well as it depends on the machine on which it runs.
##### Celery 
- Executes multiple tasks on multiple machines.
### 3.6- The Queue
- List of tasks waiting to be executed.
- Helps to manage the order of tasks execution, especially when there are many tasks to run.
### 3.7- The Worker
- The processes that actually perform the tasks.
- They do the actual work defined in your tasks.
## 4- Airflow Core Concepts
![](https://i.imgur.com/ixZUYg3.png)
### 4.1- The DAG (Directed Acyclic Graph)
- Is a collection of all the tasks you want to run, organized in a way that reflects their dependencies.
- It helps you define the structure of your entire workflow, showing which tasks needed to happen before others. task
![](https://i.imgur.com/sFeJ4CY.png)
### 4.2- Operator 
- Defines a single, ideally idempotent tasks in your DAG.
- Allow you to break down your workflow into discrete, manageable pieces of work. 
- Airflow has thousands of operators:
	- PythonOperator to execute a python script or function.
	- BashOperator to execute a bash script or command.
	- SQLExecuteQueryOperator to execute a SQL query to a database.
	- FileSensor to wait for a file. 
	
	#### There are 3 types of operators. 
    **Action Operators:** Execute an action.
    **Transfer Operators:** Transfer data.
	**Sensor Operators:** Wait for a condition to be met.
	
	#### Best practices when using operators 
- Defines one task in your date pipeline in each operator.
- Why? if processing data fails then only that operator will be restarted.

![](https://i.imgur.com/N61VILA.png)
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
![](https://i.imgur.com/UY0JmrS.png)
#### Why use single node 
- Greta to get started or small workflows.
- SImple to setup and manage.
### 5.2- Multi Node Architecture
- Running airflow across multiple computers or servers.
![](https://i.imgur.com/ejaj2k9.png)
#### Why use single node 
- Can add more workers to handle more tasks.
- If one machine fails, other can take over its work.
- By distributing work across machines, you can process more tasks simultaneously.
## 6- DAG Execution Flow 
![](https://i.imgur.com/22XNZGX.png)

- Write your DAG file and place it in the DAG folder.
- Then the scheduler regularly scans the folder and finds the new added DAG file and reads it.
- Then the scheduler creates a DagRun, which is an instance at a given time.
- Then it marks the DagRun run an queued in the meta database.
- If the task is ready, the scheduler marks the DagRun as running in the meta database.
- Creates a TaskInstance corresponding to the task and marks the instance marked as scheduled in the meta database.
- Then the Scheduler send the TaskInstance to the executer and the status is queued in the meta database.
- The executer creates a process corresponding to the worker and these worker takes the TaskInstance and marks it as running and ones tasks are completed the worker marks the instance as completed in the meta database as success or failed.
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
) as dag : None 
```
- While we call a DAG instance from the Airflow, we must identify the following parameters:
	- **DAG Id:** the unique identifier or the name of our DAG. Must be unique across all the instances.
	- **start_date:** defines the date our DAG starts being scheduled. 
	- **schedule_interval:** the frequency at which our DAG is triggered. 
	- **catchup:** by default it is set up to true, where by between now and the start date it runs all the non triggered.
	- **None:** it is added since we dont have any task successfully initiated.
### 7.1- Creating a RDMBs table 
```python
# Imports DAG library from Airflow 
from airflow import DAG

# Imports datetime library from datetime 
from datetime import datetime 

# Imports PostgresOperator library 
from airflow.providers.postgres.operators.postgres import PostgresOperator

with DAG('user_processing',  
          start_date = datetime(2025, 1, 2), 
          schedule_interval = '@daily',
          catchup = False
) as dag:

    create_table = PostgresOperator(
        task_id = 'create_table',
        postgres_conn_id = 'postgres',
        sql = '''
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
### 7.2- Checking for API availability.
- The following are different parameters while using the sensor operator.
#### poke_interval 
- This is a parameter which defines 60 seconds by default, so each 60 seconds it checks if the condition is true or not before executing the next task.
#### timeout 
- This is another parameter that is defined to 7 days by default.
```python
# Imports DAG library from Airflow 
from airflow import DAG

# Imports datetime library from datetime 
from datetime import datetime 

# Imports PostgresOperator library 
from airflow.providers.postgres.operators.postgres import PostgresOperator

# Imports HttpsSensor library
from airflow.providers.http.sensors.http import HttpSensor

# Imports SimpleHttpOperator
from airflow.providers.http.operators.http import SimpleHttpOperator

with DAG('user_processing',  
          start_date = datetime(2025, 1, 2), 
          schedule_interval = '@daily',
          catchup = False
) as dag:

    create_table = PostgresOperator(
        task_id = 'create_table',
        postgres_conn_id = 'postgres',
        sql = '''
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
                    
    is_api_available = HttpSensor(
        task_id = 'is_api_available',
        http_conn_id = 'user_api',
        endpoint = 'api/',
    )

```
### 7.3- Extract and Process users
```python
# Imports DAG library from Airflow 
from airflow import DAG

# Imports datetime library from datetime 
from datetime import datetime 

# Imports PostgresOperator library 
from airflow.providers.postgres.operators.postgres import PostgresOperator

# Imports HttpsSensor library
from airflow.providers.http.sensors.http import HttpSensor

# Imports SimpleHttpOperator
from airflow.providers.http.operators.http import SimpleHttpOperator

# Imports PythonOperator 
from airflow.operators.python import PythonOperator

# Imports json 
import json

# Imports json_normalize 
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
    processed_user.to_csv('/Users/godzilla/Desktop/output.csv', index = None, header = False) 

with DAG('user_processing',  
          start_date = datetime(2025, 1, 2), 
          schedule_interval = '@daily',
          catchup = False
) as dag:

    create_table = PostgresOperator(
        task_id = 'create_table',
        postgres_conn_id = 'postgres',
        sql = '''
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
                    
    is_api_available = HttpSensor(
        task_id = 'is_api_available',
        http_conn_id = 'user_api',
        endpoint = 'api/',
    )
 
    extract_user = SimpleHttpOperator(
        task_id = 'extract_user',
        http_conn_id = 'user_api',
        endpoint = 'api/',
        method = 'GET',
        response_filter = lambda response: json.loads(response.text),
        log_response = True
    )

    process_user = PythonOperator(
        task_id = 'process_user',
        python_callable = _process_user
    )

#   This creates dependencies between both tasks
    extract_user >> process_user
```
### 7.4 - Hooks
- Allows us to interact with external services where it abstracts all the complexity of the interactive component.
### 7.5 - Datasets 
- Acts as a logical grouping of a data as a file or a sql table.
- Consists of two parameters:
#### URI:
- Unique identifier of your data.
- Path to your data.
- Must be composed of only ASCII characters.
- The URI schema cannot be airflow. 
- Case sensitive.
```python
from airflow import Dataset

# valid datset:
schemeless = Dataset("/path/file.txt"
csv_file = Dataset("file.csv)

# invalid datasets:
reserved = Dataset("airflow://file.text")
not_ascii = Dataset("file_dàtaset")						 
```

#### EXTRA
- Json dictionary to attach additional information.

```python
from airflow import Dataset

my_file = Dataset(
					"s3://dataset/file.csv,
					extra = {'owner : 'zyad}",
)				 
```