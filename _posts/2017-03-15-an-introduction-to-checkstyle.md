---
id: 270
title: An Introduction to Checkstyle
date: 2017-03-15T20:55:23+00:00
author: Mike Conroy
layout: post
guid: https://mikepconroy.com/?p=270
permalink: /2017/03/an-introduction-to-checkstyle/
categories:
  - Computing
  - Technology
tags:
  - DevOps
  - Java
  - programming
---
<center>
  <a href="http://checkstyle.sourceforge.net/"><img class="alignnone size-full wp-image-273" src="https://mikepconroy.com/wp-content/uploads/2017/03/checkstyle_logo.png" alt="" width="261" height="98" /></a>
</center>


  
Checkstyle is a tool that can be used by developers in order to help teams follow coding conventions on a Java project. I recently used this whilst developing a <a href="https://github.com/mikecon94/Battleships" target="_blank">Battleships</a> game in Java with a group of others as part of a university module (maybe a post for another day?..).

<p style="text-align: center;">
  <strong>Why use Checkstyle?</strong>
</p>

* * *

Coding conventions and standards are a way to ensure that code is formatted consistently across a project, team or organisation. The benefits of using them are pretty well known and well documented but it essentially comes down to making the code more readable and accessible to yourself and other developers.

Checkstyle helps developers to follow a coding convention by pointing out errors or warnings within the IDE based on an XML configuration file that you provide in the project. The configuration file&#8217;s format is <a href="http://checkstyle.sourceforge.net/config_coding.html" target="_blank">well documented</a>, easy to read and easy to edit. On our project we chose to base our standards on Google&#8217;s guidelines (<a href="https://github.com/checkstyle/checkstyle/blob/master/src/main/resources/google_checks.xml" target="_blank">google_checks.xml</a>) which we then adapted to suit our own coding style (<a href="https://github.com/mikecon94/Battleships/blob/master/checkstyle.xml" target="_blank">checkstyle.xml</a>). We also used <a href="https://travis-ci.org/" target="_blank">Travis CI</a> to build our project each time someone pushed new code and if compilation and tests passed then we would get a notification in our team&#8217;s Slack channel, similarly if anything went wrong we would get a notification to let us know we needed to fix something. As we were using Checkstyle with the Maven plugin this meant that if code was committed which didn&#8217;t comply with our standards then the build would fail<sup>1</sup>.

<p style="text-align: center;">
  <strong>How to install & use Checkstyle</strong>
</p>

* * *

Installing and getting Checkstyle to work with Maven initially took a few attempts and gave quite the headache but I put that down to my lack of experience with Maven and in the end it was worth it. I&#8217;m hoping that by documenting the process here others will be able to save time by not having to follow my process of trial and error!

**Maven**

In the pom.xml put the following block of code into the <plugins> section.

<pre class="brush: xml; title: ; notranslate" title="">&lt;plugin&gt;
	&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
	&lt;artifactId&gt;maven-checkstyle-plugin&lt;/artifactId&gt;
	&lt;version&gt;2.17&lt;/version&gt;
	&lt;executions&gt;
		&lt;execution&gt;
			&lt;id&gt;validate&lt;/id&gt;
			&lt;phase&gt;validate&lt;/phase&gt;
			&lt;configuration&gt;
				&lt;encoding&gt;UTF-8&lt;/encoding&gt;
				&lt;configLocation&gt;checkstyle.xml&lt;/configLocation&gt;
				&lt;consoleOutput&gt;true&lt;/consoleOutput&gt;
				&lt;failsOnError&gt;true&lt;/failsOnError&gt;
				&lt;linkXRef&gt;false&lt;/linkXRef&gt;
			&lt;/configuration&gt;
			&lt;goals&gt;
				&lt;goal&gt;check&lt;/goal&gt;
			&lt;/goals&gt;
		&lt;/execution&gt;
	&lt;/executions&gt;
	&lt;dependencies&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;com.puppycrawl.tools&lt;/groupId&gt;
			&lt;artifactId&gt;checkstyle&lt;/artifactId&gt;
			&lt;version&gt;7.6&lt;/version&gt;
		&lt;/dependency&gt;
	&lt;/dependencies&gt;
&lt;/plugin&gt;
</pre>

Note &#8211; the configLocation field can be removed and Checkstyle will use the default config (<a href="http://maven.apache.org/plugins-archives/maven-checkstyle-plugin-2.14/config/sun_checks.html" target="_blank">sun_checks.xml</a>) but as mentioned earlier on our project we used a custom configuration and simply named it &#8220;checkstyle.xml&#8221; in the root of our project.
  
It&#8217;s also worth mentioning the dependencies section where we explicitly tell Maven to use version 7.6. Without this section Maven will use version 6.11.2. This was something I only solved recently but it prevented us from using some of the newer checks available during development.

After setting up the configuration file (or choosing to use the default) running &#8216;mvn validate&#8217; in the command line should give you the following output.

<pre>...
[INFO] --- maven-checkstyle-plugin:2.17:check (validate) @ Battleships ---
...</pre>

Below that line you will see a list of errors or warnings if your code does not follow the defined convention.

That&#8217;s it, your project is now set up to run Checkstyle with Maven!

**Eclipse**

There is also an [Eclipse Checkstyle Plugin](http://eclipse-cs.sourceforge.net) available for Checkstyle which will allow Eclipse to display errors and warnings to the user in the &#8216;problems&#8217; tab. This makes it easy for developers to see jump to the offending pieces of code and fix it to comply with the coding standards in the configuration file.<figure id="attachment_297" style="width: 625px" class="wp-caption aligncenter">

[<img src="https://mikepconroy.com/wp-content/uploads/2017/03/eclipse_checkstyle-1024x696.png" alt="Checkstyle throwing a variety of errors in Eclipse." width="625" height="425" class="size-large wp-image-297" srcset="https://mikepconroy.com/wp-content/uploads/2017/03/eclipse_checkstyle-1024x696.png 1024w, https://mikepconroy.com/wp-content/uploads/2017/03/eclipse_checkstyle-300x204.png 300w, https://mikepconroy.com/wp-content/uploads/2017/03/eclipse_checkstyle-768x522.png 768w, https://mikepconroy.com/wp-content/uploads/2017/03/eclipse_checkstyle-624x424.png 624w, https://mikepconroy.com/wp-content/uploads/2017/03/eclipse_checkstyle.png 1291w" sizes="(max-width: 625px) 100vw, 625px" />](https://mikepconroy.com/wp-content/uploads/2017/03/eclipse_checkstyle.png)<figcaption class="wp-caption-text">Checkstyle throwing a variety of errors in Eclipse.</figcaption></figure> 

Happy Coding!

* * *

<sup>1</sup> The severity of &#8220;non-compliance&#8221; could be configured to either throw a warning or an error based on which rule had been broken. For example a line length of greater than 100 may throw an error but an extra whitespace somewhere may just cause a warning.

&nbsp;