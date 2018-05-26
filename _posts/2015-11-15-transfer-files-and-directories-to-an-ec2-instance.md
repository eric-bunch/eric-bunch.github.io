---
layout: post
title: "Transfer files and directories to an EC2 instance"
date: 2015-11-15
blurb: I'll show how to tansfer a file or directory from your computer to an existing EC2 instance.
author: Eric Bunch
---


<h4>Transferring files</h4>

Here we will discuss how to transfer files and directories between your computer and an existing EC2 instance, as well as how to set up aliases for the commands. To upload a file named 'file.txt' in the directory ~/Documents/ from your computer into the directory ~/data/ on your EC2 instance, use the following command. All of it goes on one line.



<pre><code>
scp -i path/to/key-pair-file/your-key-pair-file.pem ~/Documents/file.txt ubuntu@ec2-xx-xxx-xxx-xx.compute-1.amazonaws.com:~/data/
</code></pre>

Here, instead of x's, you will use the numbers of the IP address for you EC2 instance. Notice that there are dashes separating the numbers, and not dots.

To download a file named <code>file.txt</code> in the directory <code>~/data/</code> from your EC2 instance to the directory ~/Downloads/ on your computer, use the following command Aagain, one line


<pre><code>
scp -i path/to/key-pair-file/your-key-pair-file.pem ubuntu@ec2-xx-xxx-xxx-xx.compute-1.amazonaws.com:/data/file.txt ~/Download/
</code></pre>


Again, the x's are the numbers in the IP address of your EC2 instance, separated by dashes, not dots.


<h4>Alias for transferring files</h4>


If we don't want to remember or look up these commands each time we want to transfer files between your EC2 instance, we can make aliases for these commands. First, open the <code>./bash_aliases</code> file



<pre><code>
nano ~/.bash_aliases</code></pre>


Then add the following two functions at the end of the file; you can name them whatever you like.

<pre><code>
ec2_upload_func() { scp -i path/to/key-pair-file/your-key-pair-file.pem "$1" ubuntu@ec2-xx-xxx-xxx-xx.compute-1.amazonaws.com:"$2" }
</code></pre>

<pre><code>
ec2_download_func() { scp -i path/to/key-pair-file/your-key-pair-file.pem ubuntu@ec2-xx-xxx-xxx-xx.compute-1.amazonaws.com:"$1" "$2" }
</code></pre>

Then, after those two functions, create two aliases that call these functions. Add the following two lines to .bash_aliases (again, call the aliases whatever you like)

<pre><code>
alias ec2-push=ec2_upload_func alias ec2-pull=ec2_download_func
</code></pre>

Next, save and close the .bash_aliases file, and run the command <code>source ~/.bashrc</code>. Now to upload a file from your computer to your EC2 instance, we can use the command


<pre><code>
ec2-push path/to/file/file.txt path/of/destination/in/ec2
</code></pre>



Likewise, in order to download a file from your EC2 instance to your computer, run the command


<pre><code>
ec2-pull path/to/ec2/file/file.txt path/to/desination/on/comp/
</code></pre>


<h4>Transferring directories</h4>

The commands for transferring whole directories needs is a slight modification of the command to transfer a single file. To transfer a file and its contents from your computer to your EC2 instance, use the command

<pre><code>
scp -i path/to/key-pair-file/your-key-pair-file.pem -r path/to/directory/ ubuntu@ec2-xx-xxx-xxx-xx.compute-1.amazonaws.com:path/to/destination/directory
</code></pre>



To transfer a file and its contents from your EC2 instance, use the command


<pre><code>
scp -i path/to/key-pair-file/your-key-pair-file.pem -r path/to/ec2/directory ubuntu@ec2-xx-xxx-xxx-xx.compute-1.amazonaws.com:path/to/destination/directory
</code></pre>

<h4>Alias for transferring directories</h4>

To create aliases for the above two commands, we need to add two more functions to .bash_aliases. Open .bash_aliases by using the command <code>nano ~/.bash_aliases</code>. Then add the following functions to the end of this file; call them whatever you like:


<pre><code>
ec2_upload_dir_func() { scp -i path/to/key-pair-file/your-key-pair-file.pem -r "$1"  ubuntu@ec2-xx-xxx-xxx-xx.compute-1.amazonaws.com:"$2" }
</code></pre>

<pre><code>
ec2_download_dir_func() { scp -i path/to/key-pair-file/your-key-pair-file.pem -r  ubuntu@ec2-xx-xxx-xxx-xx.compute-1.amazonaws.com:"$1" "$2" }
</code></pre>

Then after these two functions, add the two following lines to .bash_aliases

<pre><code>
alias ec2-push-dir=ec2_upload_dir_func alis ec2-pull-dir=ec2_download_dir_func
</code></pre>

Then save and close the <code>.bash_aliases</code> file. Then use the command <code>source ~/.bashrc</code> to activate these aliases. Now in order to upload a directory and its contents from your computer to your EC2 instance, use the following command

<pre><code>
ec2-push-dir path/to/directory/on/comp path/to/ec2/destination
</code></pre>

Likewise, to download a directory and its contents from your EC2 instance to your computer, use the command

<pre><code>
ec2-pull-dir path/to/ec2/dir path/to/destination/dir/on/comp
</code></pre>
