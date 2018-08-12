# 
$ influx -precision rfc3339
Connected to http://localhost:8086 version 1.4.x
InfluxDB shell 1.4.x
>

# create a database
create database testdb

# show database

show databases

# use database
use testdb

# write data via 
# <measurement>[,<tag-key>=<tag-value>...] <field-key>=<field-value>[,<field2-key>=<field2-value>...] [unix-nano-timestamp]
INSERT cpu,host=serverA,region=us_west value=0.64

[HTTP API]
# creating a database via the HTTP API
curl -i -XPOST http://localhost:8086/query --data-urlencode "q=CREATE DATABASE mydb"
# writing data via the HTTP API
curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary 'cpu_load_short,host=server01,region=us-west value=0.64 1434055562000000000'
# write multiple points
curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary 'cpu_load_short,host=server02 value=0.67
cpu_load_short,host=server02,region=us-west value=0.55 1422568543702900257
cpu_load_short,direction=in,host=server01,region=us-west value=2.0 1422568543702900257'
# writing points from a file
curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary @cpu_data.txt

    Example of a properly-formatted file (cpu_data.txt): 

    cpu_load_short,host=server02 value=0.67
    cpu_load_short,host=server02,region=us-west value=0.55 1422568543702900257
    cpu_load_short,direction=in,host=server01,region=us-west value=2.0 1422568543702900257

    
# quering data via the HTTP API
curl -G 'http://localhost:8086/query?pretty=true' --data-urlencode "db=mydb" --data-urlencode "q=SELECT \"value\" FROM \"cpu_load_short\" WHERE \"region\"='us-west'"
# multiple queries separated with ";" delimiter
curl -G 'http://localhost:8086/query?pretty=true' --data-urlencode "db=mydb" --data-urlencode "q=SELECT \"value\" FROM \"cpu_load_short\" WHERE \"region\"='us-west';SELECT count(\"value\") FROM \"cpu_load_short\" WHERE \"region\"='us-west'"

# other options when quering data
# timestamp format
curl -G 'http://localhost:8086/query' --data-urlencode "db=mydb" --data-urlencode "epoch=s" --data-urlencode "q=SELECT \"value\" FROM \"cpu_load_short\" WHERE \"region\"='us-west'"
# chunking results rather than as a single response
curl -G 'http://localhost:8086/query' --data-urlencode "db=deluge" --data-urlencode "chunked=true" --data-urlencode "chunk_size=20000" --data-urlencode "q=SELECT * FROM liters"
# maximum row limit to prevent InfluxDB from running out of memory
max-row-limit=100

# authentication ( enable )# ✨
    [http]
      enabled = true
      bind-address = ":8086"
      auth-enabled = true # ✨
      log-enabled = true
      write-tracing = false
      pprof-enabled = false
      https-enabled = false
      https-certificate = "/etc/ssl/influxdb.pem"
# authentication with Basic Authentication ( prefered )
curl -G http://localhost:8086/query -u todd:influxdb4ever --data-urlencode "q=SHOW DATABASES"
# authentication using query parameters
curl -G "http://localhost:8086/query?u=todd&p=influxdb4ever" --data-urlencode "q=SHOW DATABASES"
# authentication using request body
curl -G http://localhost:8086/query --data-urlencode "u=todd" --data-urlencode "p=influxdb4ever" --data-urlencode "q=SHOW DATABASES"
# authentication with the CLI via environment variables or flags or "auth"
export INFLUX_USERNAME todd
export INFLUX_PASSWORD influxdb4ever
influx
Connected to http://localhost:8086 version 1.4.x
InfluxDB shell 1.4.x

influx -username todd -password influxdb4ever
Connected to http://localhost:8086 version 1.4.x
InfluxDB shell 1.4.x

influx
Connected to http://localhost:8086 version 1.4.x
InfluxDB shell 1.4.x
> auth
username: todd
password:
>
# Authenticate Telegraf requests to InfluxDB
    [...]

    ## Write timeout (for the InfluxDB client), formatted as a string.
    ## If not provided, will default to 5s. 0s means no timeout (not recommended).
    timeout = "5s"
    username = "telegraf" #💥
    password = "metricsmetricsmetricsmetrics" #💥

    [...]
# User Types and Privileges
    Admin users:
        Database management:    
            CREATE DATABASE
            DROP DATABASE
            DROP SERIES
            DROP MEASUREMENT
            CREATE RETENTION POLICY
            ALTER RETENTION POLICY
            DROP RETENTION POLICY    
            CREATE CONTINUOUS QUERY
            DROP CONTINUOUS QUERY

        User management:    ◦      
            Admin user management:           
             CREATE USER, GRANT ALL PRIVILEGES, 
             REVOKE ALL PRIVILEGES, and SHOW USERS    ◦      
             Non-admin user management:            
             CREATE USER, GRANT [READ,WRITE,ALL], 
             REVOKE [READ,WRITE,ALL], and 
             SHOW GRANTS    ◦      
        General user management:            
            SET PASSWORD and DROP USER

    Non-admin users:
        READ
        WRITE
        ALL (both READ and WRITE access)
        // per user per database
    
    # privilege.txt included
            
