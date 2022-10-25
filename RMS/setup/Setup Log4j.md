## Setup Log4j

- Go to your project folder(e.g., `D:\codebase\rmsng`),  run `cp setenv.bat $CATALINA_HOME/bin/setenv.bat`. If you already have `setenv.bat` in your tomcat folder, add in this line `set "CATALINA_OPTS=%CATALINA_OPTS% -Drms_shared_dir=%RMS_SHARED_DIR%"`.

- Run  
		
		cp log4j2_rms.xml $RMS_SHARED_DIR/conf/log4j2_rms.xml
		cp log4j2_router.xml $RMS_SHARED_DIR/conf/log4j2_router.xml
        cp log4j2_viewer.xml $RMS_SHARED_DIR/conf/log4j2_viewer.xml

       Log4j has a feature to monitor the changes of the configuration file. You can change the value of `monitorInterval` for some debug cases. The unit is second.

- New log files will be saved into folder `$RMS_SHARED_DIR/logs/${Host Name}`. Log files for current day will be named as `xxx.log`(e.g., `rms.log`, `router.log`, `viewer.log`). On the next day, old logs will be renamed and saved into `xxx.yyyy-MM-dd.log`(e.g., `rms.2018-07-18.log`). `xxx.log` files will delete all contents and start to store the current day logs.