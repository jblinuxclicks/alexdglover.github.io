---

title: 'Arduino Project 5: Internet Control without Ethernet Shield'
date: 2013-02-18 09:00:30.000000000 -06:00



categories:
- Fun IT Projects
- How-to Guides
- Utilities And Other Useful Things
tags:
- arduino
- COM
- ethernet shield
- home automation
- internet controls
- php
- script
- serial port
- USB
meta:
  _wpas_done_all: '1'
  jabber_published: '1361199790'
  reddit: a:2:{s:5:"count";i:0;s:4:"time";i:1361201104;}
  publicize_twitter_user: alexdglover
  email_notification: '1361199790'
  _wpas_done_1477652: '1'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:297624509;b:1;}}
  _wpas_done_1477650: '1'
  tagazine-media: a:7:{s:7:"primary";s:0:"";s:6:"images";a:0:{}s:6:"videos";a:0:{}s:11:"image_count";i:0;s:6:"author";s:8:"34836694";s:7:"blog_id";s:8:"34954019";s:9:"mod_stamp";s:19:"2013-02-17
    03:20:07";}
  _s2mail: 'yes'
  _edit_last: '1'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
---
<p>I'm getting ready to start an Arduino home automation project, so I started looking at ways to interface with an Arduino across the internet. That way I'll be able to control all of the lights, locks, etc. anywhere I have internet access.</p>
<p>Now, the obvious answer was to buy an Ethernet shield, but I already run a home web server so that seemed unnecessary. I saw a few solutions using Processing or Python scripts, but that seemed unnecessarily complicated. It took a fair amount of digging and brainstorming, but I've found an ultra-easy, ultra-flexible, and ultra-fast solution. <strong>Ultra</strong>.</p>
<p><!--more--></p>
<p>There are a few major assumptions here.</p>
<ol>
<li>You have a computer running a web server that is accessible from the open internet, using a static IP address or a Dynamic DNS name. Your computer is either directly connected to your ISP and using a public IP address or your router is set up for port forwarding. If you don't have this set up yet, just Google it - there are a lot of tutorials that explain how to set up a home web server.</li>
<li>Your home web server has PHP. If not, this will still work, you'll just have to re-write it in Java or whatever server-side language you're using.</li>
<li><span style="line-height: 13px;">The Arduino will be plugged into the web server via USB.</span></li>
</ol>
<p>Here's the basic concept: the Arduino can't read files from the server via the USB serial connection, so the server will have to "push" the message. The server side code (PHP, Java, whatever you choose) cannot talk directly to the serial COM port, so we need a local script on the web server that can talk to the serial (USB) port. Last, the Arduino sketch has to be written so it can "catch" and process the message.</p>
<p>In this example, we'll just create a barebones web page for controls that will turn an LED on and off via the Arduino. Let's start with the sketch. The sketch is just listening on the serial connection for a 1 or 0. If it receives a 1 it will turn the LED on, if it receives a 0 it will turn the LED off.</p>
<pre class="lang:arduino decode:true">/*
Alex Glover
February 2013
*/

void setup() {
Serial.begin(9600);
//set the LED pin to OUTPUT
pinMode(13, OUTPUT);
}

void loop() {
//wait until the serial connection is open
while (Serial.available() ==0);

//read from the serial connection; the - '0' is to cast the values as the int and not the ASCII code
int val = Serial.read() - '0';

//print to the console for testing
Serial.println(val);

//if we've recieved a '1', turn on the LED and print a message
if(val==1){
Serial.println("Received a 1");
digitalWrite(13, HIGH);
}
//if we've recieved a '0', turn off the LED and print a message
if(val==0){
Serial.println("Received a 0");
digitalWrite(13, LOW);
}
}</pre>
<p>Pretty straightforward, right? OK, now we need two scripts, one to send a '1' and one to send a '0'. In Windows, simply create a text file (call it whatever you want), and give it a .bat extension. In my setup, my files are called serial_out_0.bat and serial_out_1.bat. Each script has only one line of code.</p>
<pre class="lang:default decode:true">ECHO 0 &gt; COM3:</pre>
<p>and</p>
<pre class="lang:default decode:true">ECHO 1 &gt; COM3:</pre>
<p>Note that you might have to change the COM designation. You can check which COM your Arduino is connected to by looking in the Arduino IDE under Tools --&gt; Serial Port. If you're not using Windows, you should be able to do this pretty easily in a shell script. At this point you can test to see if the batch scripts will turn the LED off and on. Also ensure that the web server will be able to execute these scripts (don't assign user-specific privileges or save them in protected directories). If you're unsure, just put these scripts in the same web root directory where you'll host your web page.</p>
<p>Easy right? Alright, the last piece is the web form. All you need are buttons within a form that will then execute the batch scripts we wrote earlier. Easiest solution is to use 'submit' buttons and then check for which post variables are set. The rest of the code is very straightforward so I'll let it speak for itself.</p>
<pre class="lang:xhtml decode:true">&lt;html&gt;
&lt;head&gt;
&lt;?php
if(isset($_POST['submitOn'])) {
exec(‘C:xampphtdocsHome_Automationserial_out_1.bat’);
}
else if(isset($_POST['submitOff'])) {
exec(‘C:xampphtdocsHome_Automationserial_out_0.bat’);
}
?&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;form action=”control.php” method=”post”&gt;
&lt;input type=”submit” name=”submitOn” value=”Submit On”&gt;
&lt;input type=”submit” name=”submitOff” value=”Submit Off”&gt;
&lt;/form&gt;
&lt;/body&gt;

&lt;/html&gt;</pre>
<p>You'll have to change the paths to correspond with the location of your scripts, but otherwise that's it. Add some CSS if you want to add some polish to your controls. This is a very simple example, but you should be able to adapt this code to any project.</p>