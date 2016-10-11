Driver script for distributed JMeter testing.

# Introduction
This is a fork of repository by [Santosh Marigowda](https://github.com/smarigowda) used to build images found in Docker Hub that implement and extend contents of blog [Srivaths Sankaran](http://srivaths.blogspot.co.uk/2014/08/distrubuted-jmeter-testing-using-docker.html).

# How to use
The driver.sh script will start JMeter Master and a number of JMeter slaves, making the provided JMX file available to them. The Master will control execution of the JMX script by the slaves and gathered log files will be available in a logs directory on the host on which the master is running. The driver.sh script can be invoked directly or a wrapper can be created from the runJMeterCloud.sh template.

## Using a Client Certificate for mutual SSL
Typically you will have a client.p12 certificate that has been password protected. This should be presented in the data directory mounted as /input_dir on the JMeter instance. You should then create a file in the data directory called __jmeter.options__ that will contain one option per line and these should be System Properties that will be added to the JMeter invocation and will define the certificate, for example:

```
-Djavax.net.ssl.keyStore=/input_data/client.p12
-Djavax.net.ssl.keyStorePassword=very_cryptic_password
-Djavax.net.ssl.keyStoreType=pkcs12
```

## A note about Grafana
Note that the sample JMX file, ,test_google.jmx, contains a section showing how to write to Grafana. To make use of this you will need to start an instance of __santosharakere/docker-grafana-statsd-elk__:

```
docker run -d -p 80:80 -p 81:81 -p 8125:8125/udp -p 2003 --name grafana santosharakere/docker-grafana-statsd-elk
```

If running on the same host then the IP Address will be the instance IP Address and the port will be 2003. If running on another host then the IPAddress will be that of the host and the port will be whatever port has been bound to 2003 on the instance - this can be seen by issuing a __docker ps__ command on the host and seeing something like that shown below where the instance port 2003 has been bound to host port 32768:

```
7c08ca8df676        santosharakere/docker-grafana-statsd-elk   "/usr/bin/supervisord"   2 days ago          Up 6 hours              4560/tcp, 6379/tcp, 8000/tcp, 8126/tcp, 0.0.0.0:80-81->80-81/tcp, 9200/tcp, 0.0.0.0:8125->8125/udp, 0.0.0.0:32768->2003/tcp   grafana
```
