---
title: Manual availability watcher !
layout: page
draft: true
---

In order to watch the availability of a server, I've done that little script
* ping $1 & sleep x seconds
* If $? of ping == 0 then we stop the loop & notify-send (libnotify) with option -t 0 to disable the message timeout.
<!--more-->

The code :
---------
```
#!/bin/bash

PING=`which ping`

function waitForHost
{
    if [ -n "$1" ]; 
        then
	        waitForHost1 $1;
		    else
		            echo "waitForHost: Hostname argument expected"
			        fi
				}

				function waitForHost1
				{
				    reachable=0;
				        while [ $reachable -eq 0 ];
					    do
					    echo a
					        sleep 5
						    $PING -q -c 1 $1
						        if [ "$?" -eq 0 ];
							    then
							            reachable=1
								    	notify-send -t 0 "$1 IS ALIVE !!!"
									    fi
									        done
										}
										waitForHost $1
```
