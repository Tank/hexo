title: Shell Script to Get The Time Difference
date: 2015-10-27 16:56:01
updated: 2015-10-27 16:56:01
tags:
- Bash
- Shell Script
categories:
- Shell Script
---

## Shell Script to Get The Time Difference
I was trying to estimate what is the time spent for a curl command.
So I made a simple shell script for this.

__First__  
create a script name `timediff.sh`  
```sh
$ vim timediff.sh
```

__Second__  
Append text as follows:
```sh
#!/bin/bash
echo "==========START=========="
date
START=$(date +%s%3N) # %3N means in miliseconds
# Start your script work here
echo "processing..."
sleep 2 # sleep for 2 seconds.
# End
END=$(date +%s%3N)
echo
echo "==========END============"
date
DIFF=$(( $END - $START ))
echo "It took $DIFF ms."
```
__Third__  
Save and execute the following script:  
```sh
$ chmod +x timediff.sh
```
__Finally__  
Execute the script:
```sh
$ ./timediff.sh
```
__Output:__
```sh
==========START==========
二 10月 27 09:32:50 UTC 2015
processing...
==========END==========
二 10月 27 09:32:52 UTC 2015
It took 2002 ms.
```