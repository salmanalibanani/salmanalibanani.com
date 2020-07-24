---
title: "Docker Scratchpad"
date: 2020-07-24T10:46:08+10:00
draft: true
---
This is a collection of useful Docker commands.
<h3>Containers and Images</h3>
<ul>
	<li>Show all running containers:
<pre><span style="font-family:courier;color:#339966;">docker ps</span></pre>
</li>
	<li>Shows all running and stopped containers:
<pre><span style="font-family:courier;color:#339966;">docker ps -a</span></pre>
</li>
	<li>Shows only CONTAINER IDs of all running and stopped containers:
<pre><span style="font-family:courier;color:#339966;">docker ps -aq </span></pre>
</li>
	<li>Stop all running containers:
<pre><span style="font-family:courier;color:#339966;">docker container stop $(docker container ls -aq)</span></pre>
</li>
	<li>Remove all containers:
<pre><span style="font-family:courier;color:#339966;">docker container rm $(docker container ls -aq) </span></pre>
</li>
	<li>Show all top level images:
<pre><span style="font-family:courier;color:#339966;">docker images</span></pre>
</li>
	<li>Remove all images:
<pre><span style="font-family:courier;color:#339966;">docker rmi $(docker images -aq) </span></pre>
</li>
</ul>
<h3>Dockerfile</h3>
<ul>
	<li>Build image from Dockerfile in the current directory with tag "sometag":
<pre><span style="font-family:courier;color:#339966;">docker build -t sometag .</span></pre>
</li>
	<li>Run the container interactively:
<pre><span style="font-family:courier;color:#339966;">docker run -it sometag</span></pre>
</li>
</ul>
<h3>Volumes</h3>
<ul>
	<li>List volumes:
<pre><span style="font-family:courier;color:#339966;">docker volume ls</span></pre>
</li>
	<li>Run the latest nginx image and mount the host directory D:\Volume as a volume \Volume:
<pre><span style="font-family:courier;color:#339966;">docker run -d --name test -v D:/Volume:/Volume nginx:latest</span></pre>
Run a bash session interactively in the container named "test" created above:
<pre><span style="font-family:courier;color:#339966;">docker exec -it test /bin/bash</span></pre>
Get a list of files in the volume:
<pre><span style="font-family:courier;color:#339966;">ls Volume</span></pre>
Inspect your container to varify the volume:
<pre><span style="font-family:courier;color:#339966;">docker inspect test</span></pre>
&nbsp;</li>
</ul>
