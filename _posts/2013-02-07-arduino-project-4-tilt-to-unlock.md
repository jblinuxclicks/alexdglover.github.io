---

title: 'Arduino Project 4: Tilt To Unlock'
date: 2013-02-07 17:52:52.000000000 -06:00



categories:
- Fun Electronics Projects
- How-to Guides
tags:
- accelerometer
- arduino
- diy
- door latch
- lock box
- relay
- solenoid
meta:
  _wpas_done_all: '1'
  _publicize_pending: '1'
  jabber_published: '1360281172'
  _wpas_done_1477650: '1'
  publicize_twitter_user: alexdglover
  _wpas_done_1477652: '1'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:297624509;b:1;}}
  email_notification: '1360281174'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
  reddit: a:2:{s:5:"count";i:0;s:4:"time";i:1361201105;}
---
<p>This is my favorite Arduino project to date. After some basic experimenting with an accelerometer, I had an idea. What if you used the accelerometer to look for a combination of movements? Instead of using a combination lock or a traditional key lock, you could just tilt the object in a set pattern to unlock it. Here's what I ended up with:</p>
<p>[vimeo http://vimeo.com/59062708]</p>
<p>Pretty sweet if you ask me.</p>
<p><!--more--></p>
<p>Note - in spite of my terrible, coffee-fueled, shakey-handed videography, a lot of this post will be in video form.</p>
<p>Now this post is based on my <a title="Arduino Project 3: Accelerometer Primer" href="http://alexdglover.wordpress.com/2013/01/21/arduino-project-3-accelerometer-primer/">Acclerometer Primer</a>, so if you're following along and want to build one of these for yourself you should go back and build the primer.</p>
<h3>Parts Used (beyond what was used in the primer)</h3>
<ul>
<li><a href="http://www.amazon.com/gp/product/B005FOTJF8/ref=as_li_ss_tl?ie=UTF8&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B005FOTJF8&amp;linkCode=as2&amp;tag=alexdgloverwo-20">Amico DC 12V Open Frame Type Solenoid for Electric Door Lock</a></li>
<li><a href="http://www.amazon.com/gp/product/B0057OC5O8/ref=as_li_ss_tl?ie=UTF8&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B0057OC5O8&amp;linkCode=as2&amp;tag=alexdgloverwo-20">SainSmart 4-Channel 5V Relay Module</a> (any 5v relay will work, we won't use all 4 channels)</li>
<li>12v Power Supply (you can use a battery or wall adapter)</li>
<li>Some sort of box/container - something larger than 4in x 4in x 4in, otherwise you may have trouble getting all of the components in</li>
</ul>
<h3>Phase 1 - Adapting the Primer To Recognize a Combination</h3>
<p>Before we try to add any new hardware, let's modify the Accelerometer Primer code to look for a pattern of movements. We've already got the mechanism for recognizing simple single movements, so all we need is to add some code to track multiple movements relative to each other. I just added a new integer variable called "currentMove." currentMove will get incremented each time a correct move in the sequence is made. To thwart people from just shaking the box or trying random patterns, each incorrect move will reset currentMove to 1. Let's see it in action:</p>
<p>[vimeo http://vimeo.com/59029783]</p>
<p>Here's my code:</p>
<blockquote><p><code><br />
/*####################################################################<br />
Tilt To Unlock<br />
Alex Glover<br />
February 2013<br />
Combination to unlock box is Y+ (left), X+ (forward), Y+ (left), X- (back), Y+ (forward)<br />
Based on autoCalibration.ino, provided by virtuabotix<br />
#######################################################################*/<br />
#include "Accelerometer.h"</code></p>
<p>Accelerometer myAccelerometer = Accelerometer();<br />
int yPosPin = 11;<br />
int yNegPin = 13;<br />
int xPosPin = 10;<br />
int xNegPin = 9;<br />
int zPin = 12;<br />
int currentMove = 1;<br />
int resetTrigger = 0;</p>
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
myAccelerometer.calibrate();</p>
<p>}</p>
<p>void loop()<br />
{<br />
delay(20);//delay for readability<br />
resetTrigger++; //increment the reset trigger<br />
myAccelerometer.read();<br />
if(myAccelerometer._Zgs &lt;= -0.9){ digitalWrite(zPin, HIGH); } else if(myAccelerometer._Zgs &gt; -0.9){<br />
digitalWrite(zPin, LOW);<br />
}<br />
if(myAccelerometer._Xgs &gt;= 0.5){<br />
digitalWrite(xPosPin, HIGH);<br />
if(currentMove==2){<br />
Serial.println("Second move successful");<br />
currentMove++;<br />
delay(1000);<br />
}<br />
else if(currentMove==5){<br />
Serial.println("Fifth move successful - opening");<br />
currentMove++;<br />
delay(1000);<br />
}<br />
else{<br />
Serial.println("WRONG MOVE!");<br />
currentMove=1;<br />
}<br />
}</p>
<p>else if(myAccelerometer._Xgs &lt; 0.5){<br />
digitalWrite(xPosPin, LOW);<br />
}<br />
if(myAccelerometer._Xgs &lt;= -0.5){ digitalWrite(xNegPin, HIGH); if(currentMove==4){ Serial.println("Fourth move successful"); currentMove++; delay(1000); } else{ Serial.println("WRONG MOVE!"); currentMove=1; } } else if(myAccelerometer._Xgs &gt; -0.5){<br />
digitalWrite(xNegPin, LOW);<br />
}<br />
if(myAccelerometer._Ygs &gt;= 0.5){<br />
digitalWrite(yPosPin, HIGH);<br />
if(currentMove==1){<br />
Serial.println("First move successful");<br />
currentMove++;<br />
delay(1000);<br />
}<br />
else if(currentMove==3){<br />
Serial.println("Third move successful");<br />
currentMove++;<br />
delay(1000);<br />
}<br />
else{<br />
Serial.println("WRONG MOVE!");<br />
currentMove=1;<br />
}<br />
}<br />
else if(myAccelerometer._Ygs &lt; 0.5){<br />
digitalWrite(yPosPin, LOW);<br />
}<br />
if(myAccelerometer._Ygs &lt;= -0.5){ digitalWrite(yNegPin, HIGH); Serial.println("WRONG MOVE!"); currentMove=1; } else if(myAccelerometer._Ygs &gt; -0.5){<br />
digitalWrite(yNegPin, LOW);<br />
}</p>
<p>if(resetTrigger==3000){ //do this every minute (3000 iterations * 0.02 seconds = 60 seconds)<br />
currentMove=1; //reset currentMove back to 1, so user can try to do the sequence again<br />
resetTrigger=0; //set the resetTrigger back to 0 so it can start another iteration to 3000</p>
<p>}</p>
<p>}</p></blockquote>
<p>Perfect! Now we can recognize a combination of movements. Now all we need to do is something meaningful when the combination is recognized.</p>
<h3>Phase 2 - Incorporating The Cigar Box, Relay, and 12v Door Latch</h3>
<p>Time for the hardware. Now, at this point we should be pretty confident in our code (we are confident in our code, right?) so we can get rid of the LEDs and all of the associated jumper wires. Next, switch your <a href="http://www.amazon.com/gp/product/B0066XLWDE/ref=as_li_ss_tl?ie=UTF8&amp;camp=1789&amp;creative=390957&amp;creativeASIN=B0066XLWDE&amp;linkCode=as2&amp;tag=alexdgloverwo-20">Virtuabotix Accelerometer</a> from using the 5v power from the Arduino to using the 3.3v power from the Arduino (you'll need to change the connection at both ends, since the accelerometer has connections for both). We need to switch to 3.3v because our relay is going to use the 5v power from the Arduino. That being said, connect the 5v power to the VCC pin on the relay and also connect the ground from your Arduino to the relay. Next, choose one of the digital pins from your Arduino (doesn't matter which) and connect it to the relay control connections - this pin will control the opening and closing of your relay circuit. In my case, I had to use a ribbon cable as an adapter between the male pins on the relay and the male jumper wires. In the picture below, I've removed all of the LEDs and connected the relay (white wire is 5v power to relay, blue is ground, and red wire connects pin 10 to the 3rd relay control).</p>
<p><a href="http://alexdglover.files.wordpress.com/2013/02/img_27691.jpg"><img class="aligncenter size-full wp-image-402" alt="alexdglover_tiltToUnlock" src="{{ site.baseurl }}/assets/img_27691.jpg" width="595" height="446" /></a></p>
<p>Now might be a good time to mount your 12v door latch. Once mounted, take the ground wire from the door latch and solder/connect it directly to the ground of your 12v power supply. Next, take the voltage-in wire from the door latch and connect it to the middle 'port' of one of your relays. This middle port is the 'common' connection, and it is always connected to the circuit. The other two 'ports' are normally open (NO) and normally closed (NC). Normally open means that the circuit is open (no current flowing through) when the relay is not powered; once the relay is powered by the 5v connection from the Arduino, the circuit closes and current flows through. The normally closed port is the opposite - current is flowing through until the relay gets a 5v signal, at which point it breaks (opens) the circuit. In our case, we want to connect the positive wire from the 12v power supply to the normally open contact. Here's a quick diagram to help:</p>
<p><a href="http://alexdglover.files.wordpress.com/2013/02/relay_wiring.jpg"><img class="aligncenter size-full wp-image-403" alt="alexdglover_tiltToUnlock_relay_wiring" src="{{ site.baseurl }}/assets/relay_wiring.jpg" width="595" height="446" /></a></p>
<p>We've just covered a lot so here's a quick recap video to bring it all together:</p>
<p>[vimeo http://vimeo.com/59029784]</p>
<p>Alright, if you've managed to get all of the hardware put together, all you need is a little more code to interact with the relay and door latch. Here's my code:</p>
<blockquote><p><code><br />
/*####################################################################<br />
tiltToUnlock<br />
Alex Glover<br />
February 2013</code></p>
<p>Combination to unlock box is Y+ (left), X+ (forward), Y+ (left), X- (back), Y+ (forward)</p>
<p>Based on autoCalibration.ino, provided by virtuabotix<br />
#######################################################################*/</p>
<p>#include "Accelerometer.h"</p>
<p>Accelerometer myAccelerometer = Accelerometer();<br />
/*int yPosPin = 11;<br />
int yNegPin = 13;<br />
int xPosPin = 10;<br />
int xNegPin = 9;<br />
int zPin = 12;<br />
We don't need any of these pins anymore, as we're not using LEDs*/<br />
int currentMove = 1;<br />
int resetTrigger = 0;<br />
int relayPin = 10;</p>
<p>void setup()<br />
{<br />
Serial.begin(9600);</p>
<p>//Connect up the following pins and your power rail<br />
// SL GS 0G X Y Z<br />
myAccelerometer.begin(3, 4, 5, A0, A1, A2);</p>
<p>/*pinMode(yPosPin, OUTPUT);<br />
pinMode(yNegPin, OUTPUT);<br />
pinMode(xPosPin, OUTPUT);<br />
pinMode(xNegPin, OUTPUT);<br />
pinMode(zPin, OUTPUT);<br />
We don't need any of these pins anymore, as we're not using LEDs*/</p>
<p>pinMode(relayPin, OUTPUT);//Set the pin that will control the relay in OUTPUT mode<br />
digitalWrite(relayPin, HIGH);//Write a high voltage signal to the relay on startup - this will break the circuit</p>
<p>//calibration performed below<br />
Serial.println("Please place the Accelerometer on a flatnlevel surface");<br />
delay(2000);//Give user 2 seconds to comply<br />
Serial.println("Calibration complete");<br />
myAccelerometer.calibrate();</p>
<p>}</p>
<p>void loop()<br />
{<br />
delay(20);//delay for readability<br />
resetTrigger++; //increment the reset trigger<br />
myAccelerometer.read();<br />
if(myAccelerometer._Zgs -0.9){<br />
//digitalWrite(zPin, LOW);<br />
}<br />
if(myAccelerometer._Xgs &gt;= 0.5){<br />
//digitalWrite(xPosPin, HIGH);<br />
if(currentMove==2){<br />
Serial.println("Second move successful");<br />
currentMove++;<br />
delay(1000);<br />
}<br />
else if(currentMove==5){<br />
Serial.println("Fifth move successful - opening");<br />
currentMove=1;<br />
digitalWrite(relayPin, LOW);<br />
delay(2000);<br />
digitalWrite(relayPin, HIGH);<br />
delay(1000);<br />
}<br />
else{<br />
Serial.println("WRONG MOVE!");<br />
currentMove=1;<br />
}<br />
}</p>
<p>else if(myAccelerometer._Xgs &lt; 0.5){<br />
//digitalWrite(xPosPin, LOW);<br />
}<br />
if(myAccelerometer._Xgs -0.5){<br />
//digitalWrite(xNegPin, LOW);<br />
}<br />
if(myAccelerometer._Ygs &gt;= 0.5){<br />
//digitalWrite(yPosPin, HIGH);<br />
if(currentMove==1){<br />
Serial.println("First move successful");<br />
currentMove++;<br />
delay(1000);<br />
}<br />
else if(currentMove==3){<br />
Serial.println("Third move successful");<br />
currentMove++;<br />
delay(1000);<br />
}<br />
else{<br />
Serial.println("WRONG MOVE!");<br />
currentMove=1;<br />
}<br />
}<br />
else if(myAccelerometer._Ygs &lt; 0.5){<br />
//digitalWrite(yPosPin, LOW);<br />
}<br />
if(myAccelerometer._Ygs -0.5){<br />
//digitalWrite(yNegPin, LOW);<br />
}</p>
<p>if(resetTrigger==3000){ //do this every minute (3000 iterations * 0.02 seconds = 60 seconds)<br />
currentMove=1; //reset currentMove back to 1, so user can try to do the sequence again<br />
resetTrigger=0; //set the resetTrigger back to 0 so it can start another iteration to 3000</p>
<p>}</p>
<p>}</p></blockquote>
<p>Awesome. One thing I almost forgot to mention - the 12v door latch needs something to latch on. You may have to get creative with this part, depending on what kind of box you are using. In my case, the cigar box lid actually slid off (rather than opening on hinges) so all I had to do was carve a notch into the lid for the latch to catch on.</p>
<p><a href="http://alexdglover.files.wordpress.com/2013/02/img_2780.jpg"><img class="aligncenter size-full wp-image-404" alt="alexdglover_tiltToUnlock_lid" src="{{ site.baseurl }}/assets/img_2780.jpg" width="595" height="793" /></a></p>
<p>Now when I slide the lid of the box again, it pushes the latch down until it lines up with the notch and then *click*, it latches and locks the box closed.</p>
<p>Whew. Long write-up. If I missed anything or if you have any questions, feel free to comment and I'll be happy to help!</p>
<p><strong>Update: </strong>By connecting both the Arduino and the door latch to the ground and power rails on the breadboard, I was able to do away with the 9v battery altogether. Now you don't have to worry about the battery dying and the box being locked forever.</p>