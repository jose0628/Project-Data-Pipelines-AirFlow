## Project Summary
A Music Streaming Startup would like to get insight of the users activity on their music streaming app and get data insights to improve their services .
This project focuses on Extracting-Transforming-Loading (ETL) process and data modeling for the songs data in a star schema. 

The project is developed under Amazon Redshift and Apache AirFlow technologies. The project will allow helping as well to bring answers to business questions based on the data warehouse (i.e, most listened songs etc.).


## AirFlow Operators

#### Stage Operator
The stage operator loads any JSON formatted files from S3 to Amazon Redshift. 
The operator creates and runs a SQL COPY statement based on the parameters provided. 
The operator's specifies where in S3 the file is loaded and what is the target table.
The parameters allow distinguishing between JSON files. Another important feature of the stage operator contains 
a templated field that allows it to load timestamped files from S3 based on the execution time and run backfills.
    

#### Fact and Dimension Operators
With dimension and fact operators, I take advantage of the provided SQL helper class to run data transformations. 
Most of the logic is within the SQL transformations and the operator is expected to take as input a SQL statement 
and target database on which to run the query against. 


Dimension loads are often done with the truncate-insert pattern where the target table is emptied before the load.
 Thus, having a parameter that allows switching between insert modes when loading dimensions. 
 Fact tables are usually so massive that they should only allow append type functionality.

#### Data Quality Operator
The final operator to create is the data quality operator, which is used to run checks on the data itself. 
The operator's main functionality is to receive one or more SQL based test cases along with the expected results and execute the tests. 
For each the test, the test result and expected result needs to be checked and if there is no match,
 the operator should raise an exception and the task should retry and fail eventually.
 
Quality checks:

```sql
{
    'sql': 'SELECT COUNT(*) FROM songplays WHERE songid IS NULL;',
    'op': 'eq',
    'val': 0
},
{
    'sql': 'SELECT COUNT(*) FROM songplays;',
    'op': 'gt',
    'val': 0
}
```

## Project Structure

    .
    ├── dags     
    │   └── sparktify_etl_dag.py   # DAG definition for AirFlow
    └── plugins                   
        ├── helpers                
        │   └── sql_queries.py     # All queries needed to create tables, update and read tables
        └── operators
            ├── data_quality.py    # DataQualityOperator definition
            ├── load_dimension.py  # LoadDimensionalOperator definition
            ├── load_fact.py       # LoadFactOperator definition
            └── stage_redshift.py  # StageToRedshiftOperator definition    
            
## How to run
1. Run your Apache AirFlow instance and let load the DAGs and dependencies
2. Look for the DAG -> sparktify_etl_dag and start the DAG

## Requirements
It assumes that you have set up the following environments

- AWS Redshift Cluster
- Apache AirFlow with Redshift Credentials
- S3 service
