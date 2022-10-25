As you know the oracle issue has been blocking us for long time. I started to investigate oracle related issues on Monday, and had the following findings yesterday. I want to share to the team about some trouble shooting details of this problem, so when you meet similar issues, maybe you can take this as a reference.

The error message is: Could not inspect jdbc autocommit mode
Normally it’s caused when a thread want to close the jdbc connection, but the connection is already closed.

There are a few possible reasons why the connection is closed unexpectedly.

1. The application is not closing connections properly, after too many connections are opened, some connections will be closed by connection pool.
2. The connection pool proactively close the connection, this maybe because of network errors, connection timeout, connection has been idled for too long time etc.
3. The remote database shutdown the connection.

I tried to examine the possibilities one by one.

1. By making a counter in HibernateUtils class, whenever new connection is opened or old connection is closed, log the current active connections in system. — turns out all open/close operations are paired.
2. By configuring the c3p0 connection pool settings
3. By configuring oracle’s connection timeout
All the attempts do not solve the problem.

So back to the code, the symptom when the exception throws is in servlet’s context listener, and the system will crash after this error happens.
I first changed the behaviour to not let the system crash, but just log the exception when error occurs, so it’s easier to do further analysis.
Then I checked the place where exception is thrown, it’s trying to get keystore_entry from DB, so the question becomes should the error be query specific? Should it be only caused by some flow in the code? (because the DB query happens in a static block, I was guessing could it be related to thread stack size or permanent generation space size, but after adjusting the size it still doesn’t solve the problem)
With these two questions I created a Helper testing class — ConnectionTest and created a static function test() which does the most basic open/release jdbc connection, and call ConnectionTest.test() in different flows.

After the testing, I found only after calling registerAgentService.shutdownNow(); in update() function in ServletContextListener, jdbc connection is failing.

So the root cause is found: registerAgentService is an instance of ScheduledExecutorService, it schedules a RegisterAgentTask who is registering an agent to talk to CC.
After agent registration is scheduled, it calls notifyObservers to tell observers the task completion.
The oberver here is the parent thread, who is running servletContextListener, it will call update() function once it detect the status of registerAgentTask is changed.
registerAgentService.shutdownNow() will send an interrupt signal to JVM, c3p0 connection is not handling the signal properly but unexpectedly closed the connection.

The solution for this problem is just to move registerAgentService.shutdownNow() to the bottom of update() function, because DB query is already finished, the interrupt signal will not interfere with c3p0 connection pool management. After the system is up, requests will be handled by servlets on demand, connection pool works as normal.

We can try to think about different approaches when seeing complicated issues like this, for example adding additional testing classes, use equivalent libraries for comparision, try to analysis the different possibilities which can cause the problem and try to narrow down the issue. This is just for reference.

2019-05-30