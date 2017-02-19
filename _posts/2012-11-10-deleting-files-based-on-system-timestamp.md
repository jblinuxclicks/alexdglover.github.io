---

title: Deleting Files Based on System Timestamp
date: 2012-11-10 08:07:08.000000000 -06:00



categories:
- How-to Guides
- Utilities And Other Useful Things
tags:
- helpful
- unix
- unix command line
- unix find
meta:
  _wpas_done_all: '1'
  _publicize_pending: '1'
  jabber_published: '1352556429'
  _wpas_done_1477650: '1'
  publicize_reach: a:2:{s:7:"twitter";a:1:{i:1477652;i:25;}s:2:"wp";a:1:{i:0;i:3;}}
  publicize_twitter_user: alexdglover
  _wpas_done_1477652: '1'
  _publicize_done_external: a:1:{s:7:"twitter";a:1:{i:297624509;b:1;}}
  email_notification: '1352556429'
  _wpas_skip_1477652: '1'
  _wpas_skip_1477650: '1'
  reddit: a:2:{s:5:"count";i:0;s:4:"time";i:1353505636;}
---
<p>Recently ran into an issue at work where a single database was creating A LOT of trace files. 54 gigabytes of trace files to be specific. Well, this was a problem because the system disk was only 56GB, preventing me from building new databases. No problem, its a dev environment, I'll just delete the trace files that are older than 3 days. Except that the trace file naming convention wasn't consistent, so there was no way for me to delete the oldest files based only on file name.</p>
<p>What about the system timestamp on each file? It seemed like a good solution, but I didn't want to write a shell script to do something so minor. Then I got lucky on Google, and found this useful little gem:</p>
<p>[sourcecode language="bash"]<br />
find /full/path/to/directory -type f -mtime +3 -exec rm -f {} ;<br />
[/sourcecode]</p>
<p>I'll translate - it will find all files in a given directory that have system timestamps from 3 days ago or greater. Clarifying note - the system timestamp reflects when a file was created OR when it was last modified. For each file that it finds that matches that criteria, it will execute 'rm -f {file name}' and delete the file!</p>
<p><!--more But wait, there's more...--></p>
<p>You can either use the full path to the directory you want to delete files in, or if you're already in that directory, simply enter a single period, like this:</p>
<p>[sourcecode language="bash"]<br />
find . -type f -mtime +3 -exec rm -f {} ;<br />
[/sourcecode]</p>
<p>In my case, I wanted to delete all files older than 3 days. If you wanted to keep 30 days worth of trace files, you would just enter +30 instead.</p>
<p>[sourcecode language="bash"]<br />
find . -type f -mtime +30 -exec rm -f {} ;<br />
[/sourcecode]</p>
<p>Back to my example. I executed the above command and then... nothing. The Unix command line is often an unforgiving wasteland of lack of information. As I was deleting hundreds of thousands of trace files, the system hung for some time, and I couldn't tell what kind of progress, if any, was being made. Fortunately the command was working, it just wasn't obvious to me.</p>
<p>Luckily, the Unix 'find' binary allows you to call multiple command executions for each file that is found. To do so, terminate your first execution statement (the commands that follow '-exec') with ';' and add another -exec statement. Now, if we simply add another execution statement that calls a command to print a period (or any other string), we'll be able to see the progress in the terminal. The printf binary will work quite nicely for this:</p>
<p>[sourcecode language="bash"]<br />
find . -type f -mtime +3 -exec rm -f {} ; -exec printf &quot;.&quot; ;<br />
[/sourcecode]</p>
<p>Hope this helps!</p>
<p>Credit to <a href="http://www.justskins.com/forums/how-to-del-files-110106.html" target="_blank">Just Skins web development forum</a> and <a href="http://unstableme.blogspot.com/2009/09/execute-multiple-commands-with-exec-and.html" target="_blank">UnstableMe</a> for getting me started.</p>