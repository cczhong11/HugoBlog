---
title: "Undertow configuration"
date: "2018-04-27"
description: "This blog will tell you my experience using Undertow."
categories:
    - "Web services"
    - "Cloud computing"
    - "Tips"
---

# Undertow

This is a very fast Java web server. When using this web server, I met some problems but I could not find enough answers even on the stackOverflow. Therefore, I decided to write my experience down.

## How to set parameters to each Servlet?

It is necessary for us to pass some parameters to Serlet functions. One way to do this is to use `addInitParam` when adding Servlet. Like the following code.

```java
DeploymentInfo servletBuilder = deployment()
                    .setClassLoader(ServletServer.class.getClassLoader())
                    .setContextPath(MYAPP)
                    .setDeploymentName("test.war")
                    .addServlets(
                            servlet("MessageServlet", MessageServlet.class)
                                    .addInitParam("message", "Hello World")
                                    .addMapping("/*"),
                    )
```

In the example, we set parameter `message` to "Hello Word". Then how to we read that in the Servlet funtion? We could set in the `init`.

```java
//MessageServlet.java
    public static final String MESSAGE = "message";

    private String message;

    @Override
    public void init(final ServletConfig config) throws ServletException {
        super.init(config);
        message = config.getInitParameter(MESSAGE);
    }
```

## Why we could not get any new request from `doGet`

There is a very strange question happened when we used Undertow. Although we got requests from load generator continuously, we could not see any from the `doGet` function. 

It is because all listeners are pending at this time and we could not get any requests now. You could learn more from [here](http://undertow.io/undertow-docs/undertow-docs-2.0.0/index.html#xnio-workers). There is a limited threads opened for Undertow, if all `WORKER_IO_THREADS` threads were used, we will lose all incoming requests. We could set parameters for Undertow using the following codes.

```java
XnioWorker worker = xnio.createWorker(OptionMap.builder()
    .set(Options.WORKER_IO_THREADS, ioThreads)
    .set(Options.WORKER_TASK_CORE_THREADS, workerThreads)
    .set(Options.WORKER_TASK_MAX_THREADS, workerThreads)
    .set(Options.TCP_NODELAY, true)
    .getMap());
```

## Connecting to MySQL problems

We could use `PreparedStatement` to perform SQL query.

```java
PreparedStatement statement = connection.prepareStatement(
             "SELECT * FROM World");
ResultSet resultSet = statement.executeQuery()
```

We also need to close them after using them. If you did not close them, there would occur out of memory problem.
[reference](https://stackoverflow.com/questions/14546592/do-i-need-to-close-preparedstatement).
