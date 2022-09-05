# Project - Data Lake
______
## Summary
A music streaming startup, Sparkify, has grown their user base and song database even more and want to move their data warehouse to a data lake. Their data resides in S3, in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.


In this project, I'll build an ETL pipeline for a data lake hosted on S3. I will load data from S3, process the data into analytics tables using Spark, and load them back into S3. 
I'll deploy this Spark process on a cluster using AWS.

## Get started
_________
1. Configure `dl.cfg` file
```
KEY=YOUR_AWS_ACCESS_KEY
SECRET=YOUR_AWS_SECRET_KEY
```
2. To deploy application to development enviroment, move project directory from local to EMR
```yaml
 scp -i <.pem-file> <Local-Path> <username>@<EMR-MasterNode-Endpoint>:~<EMR-path>
```
3. Run spark job (Before running job make sure EMR Role have access to s3)
```yaml
spark-submit etl.py --master yarn --deploy-mode client --driver-memory 4g --num-executors 2 --executor-memory 2g --executor-core 2
```

## ELT pipeline
1.  Ingest data from S3
    
    -   Song data:  `s3://udacity-dend/song_data`
    -   Log data:  `s3://udacity-dend/log_data`
    
    The script reads song_data and load_data from S3.
    
2. Process data using spark on EMR
    
    Transforms dataset and create five different tables listed below : 
    #### Fact Table
	 **songplays**  - records in log data associated with song plays i.e. records with page  `NextSong`
     ``songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent``

	#### Dimension Tables
	 **users**  - users in the app
		Fields -   _user_id, first_name, last_name, gender, level_
		
	 **songs**  - songs in music database
    Fields - _song_id, title, artist_id, year, duration_
    
	**artists**  - artists in music database
    Fields -   _artist_id, name, location, lattitude, longitude_
    
	  **time**  - timestamps of records in  **songplays**  broken down into specific units
    Fields -   _start_time, hour, day, week, month, year, weekday_
    
4.  Write result data to S3
    
    Writes them to partitioned parquet files in table directories on S3.