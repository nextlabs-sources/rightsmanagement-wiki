# Monitor for Database Reachability

The aim of  Database availability monitoring is to ensure that the database is always reachable from the current system.

Before creating the item, must ensure that libzbxpgsql is properly installed in the system. 

###  Create MACRO

To check the connectivity of the database, the system will try to log in the database with pre-defined credentials, aka MACROs defined in templates. For example in template Router database connectivity, open tab Macros,

1. create Macro {$PG_CONN}
    - value as: host=the_real_db_address port=5432 user=username_for_router password=password_for_router connect_timeout=10
    - **NOTE** keep the space between every pair of key and values
2. {$PG_CONN}
    - value as: router

###  Create Item

1. Click "Create item" button

2. Add the following:
 	
    - Name: PostgreSQL Server responding

    - Type: Zabbix agent

    - Key: pg.connect[{$PG_CONN},{$PG_DB}]

    - Type of information: Decimal

    - Update interval (in sec): 60

    - Applications: PostgreSQL Server Reachability
    
    - Description: Returns 1 on successful connection, otherwise 0. 
Accepts only the default connection parameters.
NOTE: If you are using connection pooling, this parameter should bypass the connection pooler and connect directly to your PostgreSQL server. Create a second item key to check the availability of your connection pooler.
Requires libzbxpgsql v1.0.0 or above.

### Create Trigger

1. Click "Create trigger" button

2. Add the following:

    - Name: PostgreSQL Server on {HOST.NAME} is unreachable for the last 3 polls

    - {Template Router Database Connectivity:pg.connect[{$PG_CONN},{$PG_DB}].max(#3)}<1
    
    - Multiple PROBLEM events generation: tick
    
    - Severity: High
    
# References

* [libzbxpgsql Documentation](http://cavaliercoder.com/libzbxpgsql/documentation/)