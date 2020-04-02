## Steps
1)Log into the node for which you wish to set up a load alert email.

2)Go to the location:
``````
[root@server ]# cd /usr/bin/
``````
3)Create a file.
````
vi <filename.sh>
``````
4)Add the below script, make sure to replace the test@test.com with your email address :
``````
#This script will send you the alert whenever the server load goes above the value set for trigger
#!/bin/bash

trigger=10

load=`cat /proc/loadavg | awk '{print $1}'`

response=`echo | awk -v T=$trigger -v L=$load 'BEGIN{if ( L > T){ print "trigger"}}'`

if [ "$response" > "$trigger" ]; then

SUBJECT="CPU Load on $(hostname)"

MESSAGE="/tmp/data.txt"

TO=test@test.com;

  echo "CPU Current Usage is: $load%" >> $MESSAGE

  echo "" >> $MESSAGE

  echo "******************************************************************************************" >> $MESSAGE

  echo "TOP PROCESSES THAT NEEDS TO BE CHECKED" >> $MESSAGE

  echo "******************************************************************************************" >> $MESSAGE

  echo "$(ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head )" >> $MESSAGE

  echo "******************************************************************************************" >> $MESSAGE
  
  mail -a /tmp/data.txt -s "$SUBJECT" "$TO" < $MESSAGE

  rm /tmp/data.txt

  fi
  ```````
  5)Save the file.
  
  6)Change the ownership to root.
  ````
  chown root:root <filename.sh>
  ````
  7)Change the file permission
  ````
  chmod 0755 <filename.sh>
  ````
  8)We will need to set up the cron so that this will run for every minute. 
  
  9)Edit the crontab
  ````
  crontab -e
  ````
  10)Add the cronjob
  ````
  #Server load alert
   * * * * * /usr/bin/filename.sh >/dev/null 2>&1
   ````
   11)That's all, You will receive the notifiation whenver load is above threshold value. Which will be like this:
  ``````````
  CPU Current Usage is: 0.97%

******************************************************************************************
TOP PROCESSES THAT NEEDS TO BE CHECKED
******************************************************************************************
   PID   PPID CMD                         %MEM %CPU
  6330  81211 /usr/cerneresm/sentinel/sen  2.7  9.3
 35062      1 cer_exe/srvjavadriver B170   1.4  0.2
 31350      1 cer_exe/srvjavadriver B170   1.4  0.3
 35157      1 cer_exe/srvjavadriver B170   1.3  0.2
 31364      1 cer_exe/srvjavadriver B170   1.3  0.3
 35266      1 cer_exe/srvjavadriver B170   1.3  0.2
 30232      1 cer_exe/srvjavadriver B170   1.2  1.3
 30235      1 cer_exe/srvjavadriver B170   1.2  1.2
 30253      1 cer_exe/srvjavadriver B170   1.1  0.3
******************************************************************************************
