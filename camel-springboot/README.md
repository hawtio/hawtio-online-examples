# Hawtio-Enabled Camel Spring Boot Example

This sample application shows how to make it hawtio-enabled with Camel Spring Boot. Once deployed on OpenShift/Kubernetes, it will be discovered by Hawtio Online.

## Highlights

- [pom.xml](pom.xml)

This project uses JKube [kubernetes-maven-plugin](https://eclipse.dev/jkube/docs/kubernetes-maven-plugin/) to build a container image and deploy it to a Kubernetes/OpenShift cluster.

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

Run with:

```console
mvn spring-boot:run
```

## How to deploy it to Kubernetes/OpenShift

This example is intended to be used by deploying to a Kubernetes/OpenShift cluster.

To deploy it to a cluster, firstly change the container image name in [pom.xml](pom.xml) to fit your development environment. (The default image name is `quay.io/hawtio/hawtio-online-example-camel-springboot:latest`, which should be pushed to the `hawtio` organisation on [Quay.io](https://quay.io/).)

```xml
    <jkube.container-image.name>quay.io/hawtio/%a:latest</jkube.container-image.name>
```

Then build the project and container image:

```console
mvn package k8s:build
```

and push the image to the container registry so that Kubernetes/OpenShift can pull it at deployment:

```console
mvn k8s:push
```

To deploy it to Kubernetes/OpenShift:

```console
mvn k8s:resource k8s:deploy
```

After deployment is successful and the pod has started, the application log can be seen on the cluster like this:

```console
$ hawtio-online-example-camel-springboot
+ hawtio-online-example-camel-springboot-7d456c879-pk945 › spring-boot
Starting the Java application using /opt/jboss/container/java/run/run-java.sh ...
INFO exec -a "java" java -XX:MaxRAMPercentage=80.0 -XX:+UseParallelGC -XX:MinHeapFreeRatio=10 -XX:MaxHeapFreeRatio=20 -XX:GCTimeRatio=4 -XX:AdaptiveSizePolicyWeight=90 -XX:+ExitOnOutOfMemoryError -javaagent:opt/jolokia-agent-jvm-2.1.1-javaagent.jar=protocol=https,host=*,port=8778,useSslClientAuthentication=true,caCert=/var/run/secrets/kubernetes.io/serviceaccount/service-ca.crt,clientPrincipal.1=cn=hawtio-online.hawtio.svc,extendedClientCheck=true,discoveryEnabled=false -cp ".:/deployments/*" org.springframework.boot.loader.launch.JarLauncher
INFO running in /deployments
 _________________________________________ 
< Hawtio Online Camel Spring Boot Example >
 ----------------------------------------- 
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||

2024-10-24T19:03:59.663Z  INFO 1 --- [           main] i.h.o.e.s.SampleSpringBootService        : Starting SampleSpringBootService using Java 21.0.5 with PID 1 (/deployments/hawtio-online-example-camel-springboot-1.0.0.jar started by 1000710000 in /deployments)
2024-10-24T19:03:59.673Z  INFO 1 --- [           main] i.h.o.e.s.SampleSpringBootService        : No active profile set, falling back to 1 default profile: "default"
I> No access restrictor found, access to any MBean is allowed
Jolokia: Agent started with URL https://10.128.0.27:8778/jolokia/
2024-10-24T19:04:02.027Z  INFO 1 --- [           main] org.xnio                                 : XNIO version 3.8.16.Final
2024-10-24T19:04:02.037Z  INFO 1 --- [           main] org.xnio.nio                             : XNIO NIO Implementation Version 3.8.16.Final
2024-10-24T19:04:02.063Z  INFO 1 --- [           main] org.jboss.threads                        : JBoss Threads version 3.5.0.Final
2024-10-24T19:04:02.459Z  INFO 1 --- [           main] o.a.c.impl.engine.AbstractCamelContext   : Apache Camel 4.8.0 (SampleCamel) is starting
2024-10-24T19:04:02.506Z  INFO 1 --- [           main] o.a.c.component.quartz.QuartzComponent   : Setting org.quartz.scheduler.jmx.export=true to ensure QuartzScheduler(s) will be enlisted in JMX
2024-10-24T19:04:02.529Z  INFO 1 --- [           main] org.quartz.impl.StdSchedulerFactory      : Using default implementation for ThreadExecutor
2024-10-24T19:04:02.531Z  INFO 1 --- [           main] org.quartz.simpl.SimpleThreadPool        : Job execution threads will use class loader of thread: main
2024-10-24T19:04:02.538Z  INFO 1 --- [           main] org.quartz.core.SchedulerSignalerImpl    : Initialized Scheduler Signaller of type: class org.quartz.core.SchedulerSignalerImpl
2024-10-24T19:04:02.538Z  INFO 1 --- [           main] org.quartz.core.QuartzScheduler          : Quartz Scheduler v.2.3.2 created.
2024-10-24T19:04:02.539Z  INFO 1 --- [           main] org.quartz.simpl.RAMJobStore             : RAMJobStore initialized.
2024-10-24T19:04:02.542Z  INFO 1 --- [           main] org.quartz.core.QuartzScheduler          : Scheduler meta-data: Quartz Scheduler (v2.3.2) 'DefaultQuartzScheduler-SampleCamel' with instanceId 'NON_CLUSTERED'
  Scheduler class: 'org.quartz.core.QuartzScheduler' - running locally.
  NOT STARTED.
  Currently in standby mode.
  Number of jobs executed: 0
  Using thread pool 'org.quartz.simpl.SimpleThreadPool' - with 10 threads.
  Using job-store 'org.quartz.simpl.RAMJobStore' - which does not support persistence. and is not clustered.

2024-10-24T19:04:02.542Z  INFO 1 --- [           main] org.quartz.impl.StdSchedulerFactory      : Quartz scheduler 'DefaultQuartzScheduler-SampleCamel' initialized from an externally provided properties instance.
2024-10-24T19:04:02.542Z  INFO 1 --- [           main] org.quartz.impl.StdSchedulerFactory      : Quartz scheduler version: 2.3.2
2024-10-24T19:04:02.557Z  INFO 1 --- [           main] o.a.c.component.quartz.QuartzEndpoint    : Job Camel_SampleCamel.cron (cron=0/10 * * * * ?, triggerType=CronTriggerImpl, jobClass=CamelJob) is scheduled. Next fire date is 2024-10-24T19:04:10.000+0000
2024-10-24T19:04:02.579Z  INFO 1 --- [           main] o.a.c.component.quartz.QuartzEndpoint    : Job Camel_SampleCamel.simple (cron=null, triggerType=SimpleTriggerImpl, jobClass=CamelJob) is scheduled. Next fire date is 2024-10-24T19:04:02.577+0000
2024-10-24T19:04:02.596Z  INFO 1 --- [           main] o.a.c.impl.engine.AbstractCamelContext   : Routes startup (total:2)
2024-10-24T19:04:02.596Z  INFO 1 --- [           main] o.a.c.impl.engine.AbstractCamelContext   :     Started cron (quartz://cron)
2024-10-24T19:04:02.596Z  INFO 1 --- [           main] o.a.c.impl.engine.AbstractCamelContext   :     Started simple (quartz://simple)
2024-10-24T19:04:02.596Z  INFO 1 --- [           main] o.a.c.impl.engine.AbstractCamelContext   : Apache Camel 4.8.0 (SampleCamel) started in 136ms (build:0ms init:0ms start:136ms)
2024-10-24T19:04:02.597Z  INFO 1 --- [           main] o.a.c.i.debugger.DefaultBacklogDebugger  : Enabling Camel debugger
2024-10-24T19:04:02.598Z  INFO 1 --- [           main] o.a.c.component.quartz.QuartzComponent   : Starting Quartz scheduler: org.quartz.impl.StdScheduler@3fdede3a
2024-10-24T19:04:02.598Z  INFO 1 --- [           main] org.quartz.core.QuartzScheduler          : Scheduler DefaultQuartzScheduler-SampleCamel_$_NON_CLUSTERED started.
2024-10-24T19:04:02.599Z  INFO 1 --- [           main] i.h.o.e.s.SampleSpringBootService        : Started SampleSpringBootService in 3.266 seconds (process running for 3.738)
Hello Camel! - simple
2024-10-24T19:04:02.618Z  INFO 1 --- [gerJMXConnector] o.a.c.i.d.DebuggerJmxConnectorService    : Debugger JMXConnector listening at: service:jmx:rmi:///jndi/rmi://localhost:1099/jmxrmi/camel
Hello Camel! - cron
Hello Camel! - simple
Hello Camel! - cron
Hello Camel! - simple
```
