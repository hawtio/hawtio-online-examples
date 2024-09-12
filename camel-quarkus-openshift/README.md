# Hawtio-Enabled Camel Quarkus Example for OpenShift

This sample application shows how to make it _hawtio-enabled_ with Camel Quarkus. Once deployed on OpenShift, it will be discovered by Hawtio Online.

## Highlights

- [pom.xml](pom.xml)

This project uses the Quarkus [OpenShift](https://quarkus.io/guides/deploying-to-openshift) extension to build a container image and deploy it to an OpenShift cluster. Should the target cluster be a different version of Kubernetes then an [alternative example](https://github.com/hawtio/hawtio-online-examples/camel-quarkus-openshift) is available.

The most important part in terms of the _hawtio-enabled_ configuration is defined in the `<properties>` section. To make it _hawtio-enabled_, the Jolokia agent must be attached to the application with HTTPS and SSL client authentication configured. The client principal should match those the Hawtio Online instance provides (the default is `hawtio-online.hawtio.svc`).

```xml
<properties>
    <jolokia.protocol>https</jolokia.protocol>
    <jolokia.host>*</jolokia.host>
    <jolokia.port>8778</jolokia.port>
    <jolokia.useSslClientAuthentication>true</jolokia.useSslClientAuthentication>
    <jolokia.caCert>/var/run/secrets/kubernetes.io/serviceaccount/service-ca.crt</jolokia.caCert>
    <jolokia.clientPrincipal.1>cn=hawtio-online.hawtio.svc</jolokia.clientPrincipal.1>
    <jolokia.extendedClientCheck>true</jolokia.extendedClientCheck>
    <jolokia.discoveryEnabled>false</jolokia.discoveryEnabled>
</properties>
```

## How to run locally

Run in development mode with:

```console
mvn compile quarkus:dev
```

Or build the project and execute the runnable JAR:

```console
mvn package && java -jar target/quarkus-app/quarkus-run.jar
```

### Running with Jolokia agent locally

You can run this example with Jolokia JVM agent locally as follows:

```console
java -javaagent:target/quarkus-app/lib/main/org.jolokia.jolokia-agent-jvm-2.0.1-javaagent.jar -jar target/quarkus-app/quarkus-run.jar
```

## How to deploy it to OpenShift

This example is intended to be used by deploying to an OpenShift cluster.

To deploy it to a cluster, ensure that the console has access to the OpenShift cluster by logging into it. Then execute the following:

```console
mvn clean package -Dquarkus.kubernetes.deploy=true
```
After deployment is successful and the pod has started, the application log can be seen on the cluster like this:

```console
$ oc log hawtio-online-example-camel-quarkus-os-xxxxxxxxx-xxxxx
INFO exec -a "java" java -XX:MaxRAMPercentage=80.0 -XX:+UseParallelGC -XX:MinHeapFreeRatio=10 -XX:MaxHeapFreeRatio=20 -XX:GCTimeRatio=4 -XX:AdaptiveSizePolicyWeight=90 -XX:+ExitOnOutOfMemoryError -javaagent:lib/main/org.jolokia.jolokia-agent-jvm-2.1.0-javaagent.jar=protocol=https,host=*,port=8778,useSslClientAuthentication=true,caCert=/var/run/secrets/kubernetes.io/serviceaccount/service-ca.crt,clientPrincipal.1=cn=hawtio-online.hawtio.svc,extendedClientCheck=true,discoveryEnabled=false -cp "." -jar /deployments/quarkus-run.jar
INFO running in /deployments
I> No access restrictor found, access to any MBean is allowed
Jolokia: Agent started with URL https://10.217.0.156:8778/jolokia/
__  ____  __  _____   ___  __ ____  ______
 --/ __ \/ / / / _ | / _ \/ //_/ / / / __/
 -/ /_/ / /_/ / __ |/ , _/ ,< / /_/ /\ \   
--\___\_\____/_/ |_/_/|_/_/|_|\____/___/   
2024-09-12 13:37:27,570 INFO  [org.apa.cam.qua.cor.CamelBootstrapRecorder] (main) Apache Camel Quarkus 3.14.0 is starting
2024-09-12 13:37:27,628 INFO  [org.apa.cam.mai.MainSupport] (main) Apache Camel (Main) 4.7.0 is starting
2024-09-12 13:37:27,740 INFO  [org.apa.cam.mai.BaseMainSupport] (main) Auto-configuration summary
2024-09-12 13:37:27,741 INFO  [org.apa.cam.mai.BaseMainSupport] (main)     [MicroProfilePropertiesSource] camel.context.name=SampleCamel
2024-09-12 13:37:27,848 INFO  [org.apa.cam.imp.eng.AbstractCamelContext] (main) Detected: camel-debug JAR (Enabling Camel Debugging)
2024-09-12 13:37:27,994 INFO  [org.apa.cam.imp.eng.AbstractCamelContext] (main) Apache Camel 4.7.0 (SampleCamel) is starting
2024-09-12 13:37:28,801 INFO  [org.apa.cam.com.qua.QuartzComponent] (main) Setting org.quartz.scheduler.jmx.export=true to ensure QuartzScheduler(s) will be enlisted in JMX
2024-09-12 13:37:28,869 INFO  [org.apa.cam.com.qua.QuartzEndpoint] (main) Job Camel_SampleCamel.cron (cron=0/10 * * * * ?, triggerType=CronTriggerImpl, jobClass=CamelJob) is scheduled. Next fire date is 2024-09-12T13:37:30.000+0000
2024-09-12 13:37:28,992 INFO  [org.apa.cam.com.qua.QuartzEndpoint] (main) Job Camel_SampleCamel.simple (cron=null, triggerType=SimpleTriggerImpl, jobClass=CamelJob) is scheduled. Next fire date is 2024-09-12T13:37:28.987+0000
2024-09-12 13:37:29,140 INFO  [org.apa.cam.mai.BaseMainSupport] (main) Property-placeholders summary
2024-09-12 13:37:29,140 INFO  [org.apa.cam.mai.BaseMainSupport] (main)     [MicroProfilePropertiesSource] quartz.cron=0/10 * * * * ?
2024-09-12 13:37:29,140 INFO  [org.apa.cam.mai.BaseMainSupport] (main)     [MicroProfilePropertiesSource] quartz.repeatInterval=10000
2024-09-12 13:37:29,141 INFO  [org.apa.cam.imp.eng.AbstractCamelContext] (main) Routes startup (total:2)
2024-09-12 13:37:29,141 INFO  [org.apa.cam.imp.eng.AbstractCamelContext] (main)     Started cron (quartz://cron)
2024-09-12 13:37:29,141 INFO  [org.apa.cam.imp.eng.AbstractCamelContext] (main)     Started simple (quartz://simple)
2024-09-12 13:37:29,142 INFO  [org.apa.cam.imp.eng.AbstractCamelContext] (main) Apache Camel 4.7.0 (SampleCamel) started in 1s147ms (build:0ms init:0ms start:1s147ms)
2024-09-12 13:37:29,142 INFO  [org.apa.cam.imp.deb.DefaultBacklogDebugger] (main) Enabling Camel debugger
2024-09-12 13:37:29,143 INFO  [org.apa.cam.com.qua.QuartzComponent] (main) Starting Quartz scheduler: org.quartz.impl.StdScheduler@44af588b
Hello Camel! - simple
2024-09-12 13:37:29,211 INFO  [org.apa.cam.imp.deb.DebuggerJmxConnectorService] (Camel (camel-1) thread #1 - DebuggerJMXConnector) Debugger JMXConnector listening at: service:jmx:rmi:///jndi/rmi://localhost:1099/jmxrmi/camel
Hello Camel! - cron
Hello Camel! - simple
Hello Camel! - cron
Hello Camel! - simple
```
