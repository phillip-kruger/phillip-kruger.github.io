---
date: 2017-06-14T20:04:40.407Z
title: WebSockets example and the birth of Stompee
tags: ["Java EE", "Stompee", "Webjars", "WebSockets","IBM Websphere Liberty", "Payara", "TomEE", "Wildfly"]
image: "images/Stompee/cigarette-butts.jpg"
share: true
authorlocation: "Centurion, South Africa"
aliases:
    - /2017/06/websockets-example-and-birth-of-stompee.html
---
A while back I decided to play a bit with WebSockets ([JSR 356](https://www.jcp.org/en/jsr/detail?id=356)), but I wanted to do more than a "Hello World", so [Stompee](https://github.com/phillip-kruger/stompee) was born.

Stompee allows you to view the log output on the web. The idea is that you can fine tune what you are looking for in the log output, and then only send those down to your browser. This might help in debugging issues in your system.

It's not meant to replace the log, but give you a more consumable part of the log output.

#  Development
You have no compile time dependency on stompee, all you need is to include the latest version of stompee-core as a runtime dependency in (for example) your pom.xml of your web project, so that the stompee-core jar end up in your war file.

```xml
<dependency>
    <groupId>com.github.phillip-kruger</groupId>
    <artifactId>stompee-core</artifactId>
    <version>1.1.2</version>
    <scope>runtime</scope>
</dependency>
```

##  Default settings
(Optional) You can set the default logger to use, and the level to set when you start the output. You do this by including a file named **stompee.properties** in **/src/main/resources/**. Example:

```bash
logger=com.github.phillipkruger.stompee.example
level=FINEST
```

# Runtime
Stompee contains an `@ServerEndpoint` that allows you to establish a websocket connection to your server. On deploy of your app, this endpoint is registered with the application server.

It contains a small web GUI written in [Semantic UI](https://semantic-ui.com/), and is available via your app context because with any Servlet 3 compatible container, the jars that are in the **WEB-INF/lib** directory are automatically made available as static resources. This works because anything in a **META-INF/resources** directory in a JAR in **WEB-INF/lib** is automatically exposed as a static resource.
(similar to how [webjars](https://www.webjars.org/) expose javascript and css resources)

So after you deploy your application, you can access stompee UI on **/stompee**
```
http://<your.host>:<your.port>/<your.context>**/stompee**
```
![screen1](images/Stompee/stompee-ui-1.png)

When the page loads, the javascript of the stompee UI establishes a connection to the websocket endpoint, and replies with your application name (above it's stompee-example in the top right corner)

Stompee will present you with a dropdown of all registered loggers on your system. You can select the logger and hit Enter or click the play button.

This will send a message containing your logger name to the server.

The server then adds a custom **java.util.logging.Handler** to that logger. That handler basically creates a Json representation (using [Json Processing - JSR 374](https://www.jcp.org/en/jsr/detail?id=374)) of the **java.util.logging.LogRecord** and sends it to the UI.

![screen2](images/Stompee/stompee-ui-2.png)

The UI can now create a nice colorful representation, with collapsible Stacktraces to condense the log and sortable columns to make following a thread or finding something specific message easier.

#  Settings
You can now also (by clicking on the gear icon), change the log level of your chosen log.

Other options like only showing log records that contain a stacktrace, or allowing you to filter on the log message, makes the log even more consumable.

The level, filter and exception_only options actually happen in the Log Handler on the server side, making the data that gets sent to the UI more efficient.

![screen3](images/Stompee/stompee-ui-3.png)

The log will continue to stream to your browser via websocket until you stop, or close / refresh the browser. When this happens, stompee will restore the log level to the original level and remove the custom handler, before disconnecting the websocket connection.

#  Deployment
When I wrote this, I had a microservice deployment model in mind, and that is why you include the jar in your war, and the assumption is that you would have one war per application server. In a case where you have multiple apps on your application server, you can also just deploy the stompee-standalone.war to that server. (stompee-standalone is an empty war containing stompee-core)

I have tested Stompee on the following Java EE 7 application servers

* [Wildfly 10.0.1](http://wildfly.org/)
* [Payara 172](http://www.payara.fish/) (including Payara Micro)
* [Liberty 17.0.0.1](https://developer.ibm.com/assets/wasdev/#asset/runtimes-wlp-javaee7)
* [TomEE 7.0.3](http://tomee.apache.org/)

Any feedback, bugs, suggestion, comments etc. are welcome.

> **Why stompee ?**
> The name stompee comes from stomp + ee.
> Stomp is the Afrikaans word for log (wooden) and ee for Java EE
> Stompie is the Afrikaans word for cigarette butt, hence the logo.


<a class="icon-github" href="https://github.com/phillip-kruger/stompee" target="_blank">Star</a>
