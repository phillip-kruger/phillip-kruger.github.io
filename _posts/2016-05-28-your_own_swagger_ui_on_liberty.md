---
layout: post
title: Your own Swagger UI on Liberty
tags: [IBM Websphere Liberty, Java EE, JAX-RS, Project Lombok, REST, Swagger, Webjars]
bigimg: "/images/Your_own_Swagger_UI_on_Liberty/ibm_swagger.png"
image: "/images/ibmliberty_logo.png"
aliases:
    - /2016/05/your-own-swagger-ui-on-liberty.html
---
[IBM Websphere Liberty](https://developer.ibm.com/wasdev/websphere-liberty/) comes with great out of the box support for [Swagger](http://swagger.io/), including their own branded [Swagger UI](http://swagger.io/swagger-ui/). You might, however, want to create your own branded UI.
(If you are not using Liberty, check out [Apiee](http://www.phillip-kruger.com/2017/06/apiee-easy-way-to-get-swagger-on-java-ee.html) for other options)

# Liberty Swagger support.
You need to still enable swagger in Liberty so that your swagger.json (and swagger.yaml) file get generated when you deploy your JAX-RS Application.

[This article](https://developer.ibm.com/wasdev/blog/2016/02/17/exposing-liberty-rest-apis-swagger/) explains in detail how to do that, but in short:

Use installUtility to install the feature:

```bash
installUtility install apiDiscovery-1.0
```

Enable the feature in server.xml

```xml
<feature>apiDiscovery-1.0</feature>
```

And that is it !
If you now point your browser to https://<host>:<https_port>/ibm/api/explorer you should see the branded Swagger UI.

# Example JAX-RS application
(a simple application that can store reminder notes)

In your **pom.xml**

```xml
<dependency>
    <groupid>javax</groupid>
    <artifactid>javaee-api</artifactid>
    <version>7.0</version>
</dependency>
<dependency>
    <groupid>io.swagger</groupid>
    <artifactid>swagger-annotations</artifactid>
    <version>1.5.8</version>
</dependency>
```

Java code:
**ApplicationConfig.java**

```java
package com.phillipkruger.example.swaggerui;

import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;

@ApplicationPath("/api")
public class ApplicationConfig extends Application{
}
```

**NotesService.java**

```java
package com.phillipkruger.example.swaggerui;

import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import javax.validation.constraints.NotNull;
import javax.ws.rs.Consumes;
import javax.ws.rs.DELETE;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.PUT;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Api(value = "Notes service",consumes = "json",produces = "json")
@Path("/note")
@Produces(MediaType.APPLICATION_JSON) @Consumes(MediaType.APPLICATION_JSON)
public class NotesService {

    private static final Map<String,Note> NOTE_DB = new HashMap<>();

    @POST
    @Path("/{title}")
    @ApiOperation(value = "Create a new note", notes = "This will create a new note under a certain title, only if it does not exist already")
    public Note createNote(@NotNull @PathParam("title") String title,@NotNull String text) throws NoteExistAlreadyException{
        if(exist(title))throw new NoteExistAlreadyException();
        Note note = new Note(title, text);
        save(note);
        return note;
    }

    @GET
    @Path("/{title}")
    @ApiOperation(value = "Retrieve a note", notes = "This will find the note with a certain title, if it exist")
    public Note getNote(@NotNull @PathParam("title") String title) throws NoteNotFoundException{
        if(!exist(title))throw new NoteNotFoundException();
        return NOTE_DB.get(title);
    }

    @DELETE
    @Path("/{title}")
    @ApiOperation(value = "Delete a note", notes = "This will delete the note with a certain title, if it exist")
    public void deleteNote(@NotNull @PathParam("title") String title){
        if(exist(title)){
            NOTE_DB.remove(title);
        }
    }

    @PUT
    @ApiOperation(value = "Update a note", notes = "This will update the note with a given new note, if it exist")
    public Note updateNote(@NotNull Note note) throws NoteNotFoundException{
        if(!exist(note.getTitle()))throw new NoteNotFoundException();
        save(note);
        return note;
    }

    @GET
    @Path("/exists/{title}")
    @ApiOperation(value = "Check if note exist", notes = "This will check if a note with a certain title exist")
    public boolean exist(@NotNull @PathParam("title") String title){
        return NOTE_DB.containsKey(title);
    }

    @GET
    @ApiOperation(value = "Get all the note titles", notes = "This will return all current note titles")
    public List getNoteTitles(){
        return new ArrayList<>(NOTE_DB.keySet());
    }

    private void save(Note note){
        NOTE_DB.put(note.getTitle(), note);
    }

}
```

**Note.java**

```java
package com.phillipkruger.example.swaggerui;

import java.io.Serializable;
import java.util.Date;
import javax.validation.constraints.NotNull;
import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlAttribute;
import javax.xml.bind.annotation.XmlRootElement;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data @NoArgsConstructor @AllArgsConstructor
@XmlRootElement
@XmlAccessorType(XmlAccessType.FIELD)
public class Note implements Serializable {
    private static final long serialVersionUID = -8531040143398373846L;

    @NotNull @XmlAttribute(required=true)
    private Date created = new Date();
    @NotNull @XmlAttribute(required=true)
    private Date lastUpdated = new Date();
    @NotNull @XmlAttribute(required=true)
    private String title;
    @NotNull @XmlAttribute(required=true)
    private String text;

    public Note(@NotNull String title, @NotNull String text){
        this.created = new Date();
        this.lastUpdated = new Date();
        this.title = title;
        this.text = text;
    }

}
```

**Notes:**

* I am using [Project Lombok](https://projectlombok.org/) for boilerplate java code
* There is more source code, but to keep it short I left some out. To see all source go to [https://bitbucket.org/phillip_kruger/blog/src](https://bitbucket.org/phillip_kruger/blog/src) and select swaggerui

If you deploy this code to a Liberty instance, it will by default create the Swagger as in the screenshot above.

# Your own Swagger UI
I use [Webjars](http://www.webjars.org/) whenever I need Javascript libraries in my Java EE applications. If you are not using Webjars, check it out. Webjars allows you to treat your javascript dependencies like any other java dependency.

Add the swagger-ui webjars as a runtime dependency.

**pom.xml**

```xml
<properties>
    <swagger.ui.version>2.1.8-M1</swagger.ui.version>
</properties>

<dependency>
    <groupId>org.webjars.bower</groupId>
    <artifactId>swagger-ui</artifactId>
    <version>${swagger.ui.version}</version>
    <scope>runtime</scope>
</dependency>
```

**index.html**
Make a copy of the [default swagger index.html](https://raw.githubusercontent.com/swagger-api/swagger-ui/v2.1.8-M1/dist/index.html) page and copy it to **/src/main/webapp/**

```bash
wget -O <your_project_root>/src/main/webapp/index.html https://raw.githubusercontent.com/swagger-api/swagger-ui/v2.1.8-M1/dist/index.html
```

We are going to push this file though a [resource filter](http://maven.apache.org/plugins/maven-war-plugin/examples/adding-filtering-webresources.html) with maven, so you can use variables from your pom file

Now edit it to reference the webjar resource and other UI changes you want. Example:

* Change the title to Note Service API (or your own name:) )
* Change all relative paths to resources to **webjars/swagger-ui/${swagger.ui.version}/dist/**
* Add **<link rel="stylesheet" href="theme.css">**
* Replace
  * url = "http://petstore.swagger.io/v2/swagger.json";  with
  * url = "/${build.finalName}/swagger.json";
* Add other info you want (maybe the build number or last build date)
* I also added links to the json and yaml files

```html
<!DOCTYPE html>
<html>
<head>
  <title>Note Service</title>
  <link href='webjars/swagger-ui/${swagger.ui.version}/dist/css/typography.css' media='screen' rel='stylesheet' type='text/css'/>
  <link href='webjars/swagger-ui/${swagger.ui.version}/dist/css/reset.css' media='screen' rel='stylesheet' type='text/css'/>
  <link href='webjars/swagger-ui/${swagger.ui.version}/dist/css/screen.css' media='screen' rel='stylesheet' type='text/css'/>
  <link href='webjars/swagger-ui/${swagger.ui.version}/dist/css/reset.css' media='print' rel='stylesheet' type='text/css'/>
  <link href='webjars/swagger-ui/${swagger.ui.version}/dist/css/screen.css' media='print' rel='stylesheet' type='text/css'/>
  <script src="webjars/swagger-ui/${swagger.ui.version}/dist/lib/shred.bundle.js" type="text/javascript"></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/jquery-1.8.0.min.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/jquery.slideto.min.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/jquery.wiggle.min.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/jquery.ba-bbq.min.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/handlebars-2.0.0.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/underscore-min.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/backbone-min.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/swagger-client.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/swagger-ui.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/highlight.7.3.pack.js' type='text/javascript'></script>
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/marked.js' type='text/javascript'></script>

  <!-- enabling this will enable oauth2 implicit scope support -->
  <script src='webjars/swagger-ui/${swagger.ui.version}/dist/lib/swagger-oauth.js' type='text/javascript'></script>
  <link rel="stylesheet" href="theme.css">

  <script type="text/javascript">
    $(function () {
      var url = window.location.search.match(/url=([^&]+)/);
      if (url && url.length > 1) {
        url = decodeURIComponent(url[1]);
      } else {
        url = "/${build.finalName}/swagger.json";
      }
      window.swaggerUi = new SwaggerUi({
        url: url,
        dom_id: "swagger-ui-container",
        supportedSubmitMethods: ['get', 'post', 'put', 'delete', 'patch'],
        onComplete: function(swaggerApi, swaggerUi){
          if(typeof initOAuth == "function") {
            /*
            initOAuth({
              clientId: "your-client-id",
              realm: "your-realms",
              appName: "your-app-name"
            });
            */
          }
          $('pre code').each(function(i, e) {
            hljs.highlightBlock(e)
          });
        },
        onFailure: function(data) {
          log("Unable to Load SwaggerUI");
        },
        docExpansion: "none",
        sorter : "alpha"
      });

      function addApiKeyAuthorization() {
        var key = $('#input_apiKey')[0].value;
        log("key: " + key);
        if(key && key.trim() != "") {
            log("added key " + key);
            window.authorizations.add("api_key", new ApiKeyAuthorization("api_key", key, "query"));
        }
      }

      $('#input_apiKey').change(function() {
        addApiKeyAuthorization();
      });

      // if you have an apiKey you would like to pre-populate on the page for demonstration purposes...
      /*
        var apiKey = "myApiKeyXXXX123456789";
        $('#input_apiKey').val(apiKey);
        addApiKeyAuthorization();
      */

      window.swaggerUi.load();
  });
  </script>
</head>

<body class="swagger-section">
<div id='header'>
  <div class="swagger-ui-wrap">
    <a id="logo" href="http://swagger.io">swagger</a>
<!--    <form id='api_selector'>
      <div class='input'><input placeholder="http://example.com/api" id="input_baseUrl" name="baseUrl" type="text"/></div>
      <div class='input'><input placeholder="api_key" id="input_apiKey" name="apiKey" type="text"/></div>
      <div class='input'><a id="explore" href="#">Explore</a></div>
    </form>-->
  </div>
</div>

<!--<div id="message-bar" class="swagger-ui-wrap">&nbsp;</div>-->
<div id="swagger-ui-container" class="swagger-ui-wrap"></div>
<div id="swagger-ui-footer">
    <p>
        <a class="rawbutton" href="swagger.json" target="_blank">json</a>
        <a class="rawbutton" href="swagger.yaml" target="_blank">yaml</a>
    </p>
</div>
</body>
</html>
```

**theme.css**

```css
body {
    background-image: url("logo.png");
    background-repeat: no-repeat;
    background-attachment: fixed;
    background-position: bottom right;
}
```

**logo.png**
Include your company logo, example
![logo](/images/Your_own_Swagger_UI_on_Liberty/logo.png)

**Resource filter in maven**
Add this to your pom.xml, to replace all variables in the html and css

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>2.6</version>
            <configuration>
                <webResources>
                    <resource>
                        <directory>${basedir}/src/main/webapp</directory>
                        <filtering>true</filtering>
                        <includes>
                            <include>*.css</include>
                            <include>*.html</include>
                        </includes>
                    </resource>
                </webResources>
            </configuration>
        </plugin>
    </plugins>
</build>
```

When browsing to [http://localhost:9080/swaggerui-1.0.0/](http://localhost:9080/swaggerui-1.0.0/) you now have your own swagger UI.
![screen](/images/Your_own_Swagger_UI_on_Liberty/own_swagger.png)

#  Themes
You can now customize the CSS and HTML as needed. Another nice library to check out is [swagger-ui-themes](https://github.com/ostranme/swagger-ui-themes)

You can include this using webjars again:

```xml
<dependency>
    <groupId>org.webjars.bower</groupId>
    <artifactId>swagger-ui-themes</artifactId>
    <version>${swagger.ui.theme.version}</version>
    <scope>runtime</scope>
</dependency>
```

Now simply replace the **screen.css** with the theme you want, example ([Muted theme](https://github.com/ostranme/swagger-ui-themes#muted)):

```html
<!--<link href='webjars/swagger-ui/${swagger.ui.version}/dist/css/screen.css' media='screen' rel='stylesheet' type='text/css'/>-->
<link href="webjars/swagger-ui-themes/${swagger.ui.theme.version}/themes/theme-muted.css" media='screen' rel='stylesheet' type='text/css'/>
```

![screen2](/images/Your_own_Swagger_UI_on_Liberty/own_swagger2.png)

# Reuse
You can reuse your theme for all your services. Just change the project to a jar in the pom.xml, and the location for your static resource to be classes/META-INF/resources. Also remove the JAX-RS (so in my example Note Service) and just keep the static resource in the jar.
Replace the maven-war-plugin with this to do the resource filtering and the correct path. Then a war project can simply include this jar as a runtime dependency to get the swagger ui.

```xml
<build>
    <!-- Copy all web content files META-INF folder, and push it though a filter to replace maven properties -->
    <resources>
        <resource>
            <directory>${basedir}/src/main/webapp</directory>
            <targetPath>${project.build.directory}/classes/META-INF/resources</targetPath>
            <filtering>true</filtering>
            <includes>
                <include>*.css</include>
                <include>*.html</include>
            </includes>
        </resource>
        <resource>
            <directory>${basedir}/src/main/webapp</directory>
            <targetPath>${project.build.directory}/classes/META-INF/resources</targetPath>
            <filtering>false</filtering>
            <excludes>
                <exclude>*.css</exclude>
                <exclude>*.html</exclude>
            </excludes>
        </resource>
    </resources>
</build>
```
