---

title: 'Arduino Project 3: Accelerometer Primer'
date: 2013-01-21 09:00:21.000000000 -06:00



categories:
- Fun Electronics Projects
- Fun IT Projects
- How-to Guides
tags:
- accelerometer
- arduino
meta:
  _wpas_done_all: '1'
  _publicize_pending: '1'
  jabber_published: '1358780530'
  email_notification: '1358780612'
  publicize_twitter_user: alexdglover
  _wpas_done_1477652: '1'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:297624509;b:1;}}
  _wpas_done_1477650: '1'
  reddit: a:2:{s:5:"count";i:0;s:4:"time";i:1361201106;}
---
<p>This project is more of a warm-up than a full-blown project. I wanted to get a feel for the basic workings of an accelerometer in preparation for a larger undertaking (no spoilers!). I borrowed this project idea from a forum about Arduino and accelerometers but can't remember which forum, so I can't credit back to the original poster - sorry about that.</p>
<p>Before we get to the how, let's take a look at the what. Basically we have an accelerometer and 5 LEDs mounted on a breadboard. Four of the LEDs are mounted to correspond with the X and Y accelerometer axes and one LED is in the middle.   When the accelerometer is level, only the middle LED is illuminated. If you tilt the accelerometer to the side, the LED on that side is illuminated.</p>
<p>[vimeo https://vimeo.com/57800076]</p>
<p><!--more--></p>
<p>Remember the accelerometer measures force, not tilt - we can demonstrate this by quickly moving the accelerometer to one side, creating lateral force. The LED on the opposite side relative to the direction we move the board will illuminate - that is, if we move the board to the left, the LED on the right will illuminate.</p>
<p>[vimeo https://vimeo.com/57800077]</p>
<p>Let's move on to the how. Here are the parts I used:</p>
<ul>
<li><a href="http://www.amazon.com/gp/product/B0066XLWDE/ref=as_li_ss_tl?ie=UTF8&amp;tag=alexdgloverwo-20&amp;linkCode=as2&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B0066XLWDE">Virtuabotix MMA7361 Three Axis Accelerometer Module</a> - $10</li>
<li><a href="http://www.amazon.com/gp/product/B006H06TVG/ref=as_li_ss_tl?ie=UTF8&amp;tag=alexdgloverwo-20&amp;linkCode=as2&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B006H06TVG">Arduino Uno R3</a> - $26</li>
<li><a href="http://www.amazon.com/gp/product/B004RXKWDQ/ref=as_li_ss_tl?ie=UTF8&amp;tag=alexdgloverwo-20&amp;linkCode=as2&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B004RXKWDQ">400-point Breadboard with Jumper Wires</a> - $9</li>
<li><a href="http://www.amazon.com/gp/product/B004JO2PVA/ref=as_li_ss_tl?ie=UTF8&amp;tag=alexdgloverwo-20&amp;linkCode=as2&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B004JO2PVA">5mm LEDs with Resistors (5 Colors, Pack of 25)</a> - $7</li>
</ul>
<p>Total project cost of $52, plus you'll have some leftover jumper wires and LEDs.</p>
<p>Since this is just a simple primer, I won't go into great detail. Basically we need to wire up the accelerometer as outlined in the Arduino sketch and we need to wire up each LED to an output pin and ground. If you need help with the wiring, here's a rough diagram. Note - Fritzing didn't include a piece that was identical to my accelerometer, so I used two 5 pin components - they represent one piece. Also, the wire colors don't have any logical meaning, they match my real-world build.</p>
<p><a href="http://alexdglover.files.wordpress.com/2013/01/accelerometer_primer.png"><img class="aligncenter size-full wp-image-350" alt="Accelerometer_Primer" src="{{ site.baseurl }}/assets/accelerometer_primer.png" width="595" height="562" /></a></p>
<p>Here's what it looks like up close.</p>
<p><a href="http://alexdglover.files.wordpress.com/2013/01/img_2740.jpg"><img class="aligncenter size-full wp-image-351" alt="alexdglover_accelerometer_primer_1" src="{{ site.baseurl }}/assets/img_2740.jpg" width="595" height="446" /></a></p>
<p>Here are a couple more angles of the wiring:</p>
<p><a href="http://alexdglover.files.wordpress.com/2013/01/img_2737.jpg"><img class="aligncenter size-full wp-image-352" alt="alexdglover_accelerometer_primer_2" src="{{ site.baseurl }}/assets/img_2737.jpg" width="595" height="446" /></a></p>
<p><a href="http://alexdglover.files.wordpress.com/2013/01/img_2741.jpg"><img class="aligncenter size-full wp-image-353" alt="alexdglover_accelerometer_primer_3" src="{{ site.baseurl }}/assets/img_2741.jpg" width="595" height="446" /></a></p>
<p>As far as the programming goes, I started with the example on <a href="https://www.virtuabotix.com/reference/index.php?title=Accelerometer_Sensor_Library" target="_blank">this page of  the Virtuabotix wiki</a>. Here's the my finished sktech:</p>
<blockquote><p><code><br />
/*####################################################################<br />
accelerometerPrimer<br />
Alex Glover<br />
January 2013</p>
<p>Based on autoCalibration.ino, provided by virtuabotix<br />
#######################################################################*/</p>
<p>#include "Accelerometer.h"</p>
<p>Accelerometer myAccelerometer = Accelerometer();<br />
int yPosPin = 11;<br />
int yNegPin = 13;<br />
int xPosPin = 10;<br />
int xNegPin = 9;<br />
int zPin = 12;</p>
<p>void setup()<br />
{<br />
Serial.begin(9600);</p>
<p>//Connect up the following pins and your power rail<br />
// SL GS 0G X Y Z<br />
myAccelerometer.begin(3, 4, 5, A0, A1, A2);</p>
<p>pinMode(yPosPin, OUTPUT);<br />
pinMode(yNegPin, OUTPUT);<br />
pinMode(xPosPin, OUTPUT);<br />
pinMode(xNegPin, OUTPUT);<br />
pinMode(zPin, OUTPUT);</p>
<p>//calibration performed below<br />
Serial.println("Please place the Accelerometer on a flatnLevel surface");<br />
delay(2000);//Give user 2 seconds to comply<br />
myAccelerometer.calibrate();<br />
}</p>
<p>void loop()<br />
{<br />
delay(20);//delay for readability<br />
myAccelerometer.read();<br />
if(myAccelerometer._Zgs &lt;= -0.9){ digitalWrite(zPin, HIGH); } else if(myAccelerometer._Zgs &gt; -0.9){<br />
digitalWrite(zPin, LOW);<br />
}<br />
if(myAccelerometer._Xgs &gt;= 0.5){<br />
digitalWrite(xPosPin, HIGH);<br />
}<br />
else if(myAccelerometer._Xgs &lt; 0.5){<br />
digitalWrite(xPosPin, LOW);<br />
}<br />
if(myAccelerometer._Xgs &lt;= -0.5){ digitalWrite(xNegPin, HIGH); } else if(myAccelerometer._Xgs &gt; -0.5){<br />
digitalWrite(xNegPin, LOW);<br />
}<br />
if(myAccelerometer._Ygs &gt;= 0.5){<br />
digitalWrite(yPosPin, HIGH);<br />
}<br />
else if(myAccelerometer._Ygs &lt; 0.5){<br />
digitalWrite(yPosPin, LOW);<br />
}<br />
if(myAccelerometer._Ygs &lt;= -0.5){<br />
digitalWrite(yNegPin, HIGH);<br />
}<br />
else if(myAccelerometer._Ygs &gt; -0.5){<br />
digitalWrite(yNegPin, LOW);<br />
}</p>
<p>}</code></p></blockquote>
<p>You can adjust the values to increase/decrease sensitivity. Hope you enjoyed the post, and I hope this helps you jumpstart your project with your accelerometer!</p>