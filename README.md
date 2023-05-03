# SnowFlake_S3_integration

This repository contains all the code used to extract data from my S3 bucked and query it from snowflake setting up the storage integration object in Snowflake that specified the S3 bucket location and AWS credentials, and configuring it with appropriate settings such as file formats and allowed locations. 

create database DEMO;
create schema demo1;
create
or replace storage integration aws_s3_integration type = external_stage storage_provider = 'S3' enabled = true storage_aws_role_arn='arn:aws:iam::412824560285:role/s3_full2' storage_allowed_locations =('s3://dans-snowflake-demo/');
show integrations;
desc integration aws_s3_integration;
grant usage on integration aws_s3_integration to role accountadmin;
create or replace file format demo_format 
type = 'CSV' field_delimiter = ',' skip_header = 1 error_on_column_count_mismatch=false;
create or replace stage demo_aws_stage 
storage_integration = aws_s3_integration url = 's3://dans-snowflake-demo/';
list @demo_aws_stage;
create or replace temporary table demo_us_500 (
        first_name String,
        last_name String,
        email String,
        gender String,
        ip_address String,
        id String
    );
select * from demo_us_500 limit 1000;
copy into demo_us_500
from @demo_aws_stage/us_500.csv
file_format=(format_name=demo_format)
on_error='continue';
