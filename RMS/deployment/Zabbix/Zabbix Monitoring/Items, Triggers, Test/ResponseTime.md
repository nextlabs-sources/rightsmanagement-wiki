# Monitor Response time of a URL

The aim is to ensure that an alarm is sent to the notifier if the response time of the URL exceeds 5 seconds for 1 min. The server will ping the agent after every 15 min to check the response time of the url

**Create Application**

Purpose of creating an application is to group the items

1. Go to Configuration --> Hosts

2. Click on the link Applications depending on your host

3. Click "Create Application" button

4. Provide the name e.g. WEBSITES and Add it

This application will be associated with that particular host

**Create Item**

1. Go to Configuration --> Hosts

2. Click on the link Items depending on your host

3. Click "Create item" button

4. Add the following:
 	
    - Name: Response Time

    - Type: Zabbix agent

    - Key: web.page.perf[visible name of the host]

    - Host interface: ip address of agent:10050

    - Type of information: Numeric (float)   because the response time result is in float type

    - Update interval (in sec): 900

    - Applications: WEBSITES (as created above)

**Create web Scenario**

Creating web scenario, enables web monitoring. See more: [https://www.zabbix.com/documentation/2.4/manual/web_monitoring](https://www.zabbix.com/documentation/2.4/manual/web_monitoring)

1. Go to Configuration --> Hosts

2. Click on the link Web depending on your host

3. Click "Create web scenario" button

4. Add the following:
 	
   - Under the "Scenario" tab add the following:

     - Name: Response time RMS_API (for eg)
 
     - Application: WEBSITES

     - Update interval (in sec): 900

     - Agent: Zabbix

   - Under the "Steps" tab add the following:
    
     - Name: RMS_API (for eg)

     - Timeout: 5 sec

     - URL: [https://rmtest.nextlabs.solutions/rms/rs/upgrade?tenant=skydrm](https://rmtest.nextlabs.solutions/rms/rs/upgrade?tenant=skydrm)

     - Status code: 200

**Create Trigger**

1. Go to Configuration --> Hosts

2. Click on the link triggers depending on your host

3. Click "Create trigger" button

4. Add the following:

    - Name: response time exceeding 5s for RMS_API

    - Expression: { name of host: web.test.time[name of scenario,name of steps,resp].avg(60)
}>5

		For example : {Zabbix server:web.test.time[Response time RMS_API,RMS_API,resp].avg(60)}>5

**Test**

1. Go to Monitoring --> Latest data

2. Search for the host name on the top right corner

3. Under "Hosts", select the appropriate Latest data depending on your host name

4. Under "WEBSITES", you should see the response time you want to monitor for that url. You can also view the data in the form of graph 