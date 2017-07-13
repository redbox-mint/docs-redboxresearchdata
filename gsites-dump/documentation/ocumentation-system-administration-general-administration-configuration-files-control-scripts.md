In your installation's '`server`' directory you'll find the control scripts required to start/stop the server and perform data harvests. Both ReDBox and Mint work the same way, and generally the functioning of the scripts are identical for all OSs (some differences under Windows are noted below). The key concern for a Systems Administrator is the environment script (`server/tf_env.sh` or `server/tf_env.bat`). 
Because the other scripts call these environment scripts before executing this is the central location you need to make changes to any configuration. The major points you want to change immediately following installation: 
* The path to your installation.
 * The externally facing URL of the server (including port if not 80).
 * The port to start on (this might not be the same as the external port if behind a local Apache proxy or similar).
 * Local data, such as an SMTP host and administrator's email address.
 
 * ReDBox: The URL of your Mint server is required by ReDBox.
 
At the top of the environment script you'll find a block of variables you can use to change these values: 
 `export SERVER_URL="http://localhost:9001/mint/"`
 `export LOCAL_PORT="9001"`
 `export PROJECT_HOME="C:/tf2/mint-bare"`
 `export AMQ_PORT="9201"`
 `export AMQ_STOMP_PORT="9202"`
 `export SMTP_HOST="localhost"`
 `export ADMIN_EMAIL="admin@localhost"`
  
The AMQ ports don't generally need to be changed, unless you are integrating with other systems. This is not part of a typical ReDBox/Mint deployment however. 
#### **[]()Shell Scripts
 
Starting and stopping the server is achieved with the '`tf.sh`' script and a parameter. There are a number of available parameters of use: 
 `./tf.sh start`
 `./tf.sh stop`
 `./tf.sh restart`
 `./tf.sh status`
  
Once the system is online, you can execute a harvest using the harvest script: 
 `./tf_harvest.sh {harvest_source}`  
The details of using this script can be [found here](documentation-system-administration-administering-mint-loading-data). 
#### **[]()Windows
 
The Windows batch scripts are fairly similar, but the start/stop process is less comprehensive since they are mainly used by current users for development work only. There is a single '`tf_start.bat`' script that will start a server, and it is stopped (gracefully) by sending a kill signal (CTRL+C) from the terminal. 
Harvesting is identical, using '`tf_harvest.bat`'. 
#### **[]()Proxy Details
 
If your server is behind a corporate proxy the control scripts will try to ensure this is accounted for by examining the environment. You may however prefer to set this (or disable this) manually by modifying this line (or its corresponding Windows equivalent): 
 `PROXY_OPTS="-Dhttp.proxyHost=$PROXY_HOST -Dhttp.proxyPort=$PROXY_PORT -Dhttp.nonProxyHosts=localhost"` 
In particular '`nonProxyHosts`' may require specific configuration for your system.