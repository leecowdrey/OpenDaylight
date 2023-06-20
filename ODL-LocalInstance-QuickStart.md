# OpenDaylighty Local-Instance Quick Start

## Cheat-sheet for building your own ODL instance quickly

```bash
cat /proc/cpuinfo | grep processor
#Be sure you have at least 2, but 4 is better
cat /proc/meminfo | grep MemTotal
#Be sure you have 16G or more

sudo apt -y update && sudo apt -y upgrade && sudo apt -y clean
sudo apt -y install openjdk-11-jdk-headless wget
cd ~
wget 
https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/opendaylight/0.13.4/opendaylight-0.13.4.tar.gz
tar -zxvf opendaylight-0.13.4.tar.gz
cd opendaylight-0.13.4/
# If you have 32G, double these to 16G:
echo "JAVA_MAX_MEM=8G" | tee -a bin/setenv
echo "JAVA_MIN_MEM=8G" | tee -a bin/setenv
#...more is better, up to 50% of Total RAM if you're going to do a lot.
bin/start && sleep 10
ps -ef | grep java
# Should see a massive PID line, which will include your Heap sizing as -Xms and -Xmx
bin/client "feature:install -v odl-netconf-topology"
bin/client "feature:install -v odl-restconf"
# NOTE: For any Product/Production systems, this would be: bin/client "feature:install -v odl-netconf-clustered-topology", which is mutually-exclusive from the non-clusterable version we're using in this lab.
# Optional, to see every bit of the modeled data in the netconf SSH connections:
bin/client "log:set TRACE org.opendaylight.netconf"
tail -F data/log/karaf.log
  #when done: bin/client "log:set DEFAULT org.opendaylight.netconf"
```

## now switch to Postman for actual testing interactions via RESTCONF

### Callhome

`https://docs.opendaylight.org/projects/netconf/en/latest/user-guide.html#netconf-call-home`


## Magnesium alternative (Swagger 2) 

`https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/opendaylight/0.12.3/opendaylight-0.12.3.tar.gz`
