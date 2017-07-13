[[_TOC_]]


## []()Status

This document is in DRAFT.
## []()Introduction

This how-to shows you how to setup housekeeping to run a harvest on a scheduled basis. 


This How-to was submitted by Jay van Schyndel (JCU) - thanks Jay!
## []()Background reading


* ReDBox uses the Quartz Scheduler. This link explains how to setup the frequency that you require your harvest to run - [http://www.quartz-scheduler.org/documentation/quartz-1.x/tutorials/crontrigger](http://www.quartz-scheduler.org/documentation/quartz-1.x/tutorials/crontrigger)

## []()Key files and folders


* redbox/home/system-config.json
* redbox/home/harvest

## []()Procedure

### []()1. Step 1

Edit your system-config.json file and add the following (:

                "houseKeeping": {
                    "config": {
                        "quartzConfig": "${fascinator.home}/quartz.`properties",`
                        "desktop": true,
                        "frequency": "3600",
                        "jobs": [
**`                    {`
                                "name": "*harvestEdgar*",
                                "type": "harvest",
                                "configFile": "*${fascinator.home}/harvest/*`*directoryNames-Edgar.json*",`
                                "timing": "*0 0 6 * * ?*"
                            },
**`                    {`
                                "name": "alerts-poll",
                                "type": "external",
                                "url": "http://localhost:${jetty.`port}/redbox/default/hkjobs/``alerts.script",`
                                "timing": "0 0/15 * * * ?"
                            }
                        ]
                    }
                },
Edit the italicised values as appropriate:
* name: can be whatever you deem appropriate
* type:  must be 'harvest' as we are setting up a harvest job
* configFile: this is the same .json file that exists in your redbox/home/harvest directory used for a manual harvest
* timing: This is where you set your frequency. In the example above the job is set to run at 6:00 am daily. Refer to 'Background reading' for a link explaining how to set this up.


### []()2. Step 2


1. If ReDBox is running, stop it.
1. Start up ReDBox.


### []()Notes


* In redbox/home/logs/houseKeeping.log you will find details of when you harvest is executed by house keeping.