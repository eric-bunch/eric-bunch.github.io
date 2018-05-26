---
layout: post
title: "Setting up an Amazon Ubuntu EC2 instance and configuring with Python2.7.9 and SciPy stack"
date: 2015-11-15
blurb: I go through how to how to set up an Amazon EC2 instance and setting up an environment for scientific computation.
author: Eric Bunch
---
Here, I will go through the steps to set up an Amazon EC2 instance that runs Ubuntu 14.04 LTS, install Python2.7.9, and install important modules like numpy and SciPy. So let's get started. First, sign in to Amazon Web Services, and go to the EC2 dashboard. Click on the 'Running instances' option, and the click 'Launch instance'. We will be using the Ubuntu AMI $\text{(currently they are offering 14.04 LTS)}$ flavor for this tutorial, as well as using the t2 micro instance because it is free with a one year free trial. Select this option and click 'Review and launch'. On the next screen, click 'Launch'. There are security options that we can change, but for simplicity we will leave them as default and change them later if we desire. A popup screen will come up asking about a key pair. If you do not have a key pair, follow the instructions to obtain one. Once you have, select 'Launch instance'.

Next select 'View instances' to view the image you just created. It will probably need a minute or so to instantiate. Wait until the entry in the 'Instance state' column says 'running'. Once the instance is running, select the box on the left side of the row where the instance information is displayed. Then click the button that says 'Connect' above the list of instances. A popup box will appear with instructions on how to connect to your instances. We will be connecting through our command line. The command needed to connect to your instance will look like

<pre><code>
ssh -i "whatever_your_key_pair_is.pem" ubuntu@xx.xx.xxx.x
</code></pre>

This command needs to be executed while in the same directory as your .pem file holding the key pair, or you will need to pre-append the full directory path to <code>"whatever_your_key_pair_is.pem"</code>. The `.pem` file is the file with your key pair we just obtained, and the x's will be numbers in the IP address for your EC2 instance.

<p> This next part is optional. We can crate an alias command for the above command to connect to the EC2 instance, so that we don't have to remember or look up the command each time we wish to connect. To do so, in the command line type <code>nano ~/.bash_aliases</code>. This will open up a file containing the alias commands you already have. If you haven't created aliases before, this wil be blank. To this file, add the line

<pre><code>
alias ec2-connect="ssh -i "whatever_your_key_pair_is.pem" ubuntu@xx.xx.xxx.x"
</code></pre>

Of course, you can name your alias something other than ec2-connect if you like. Type Ctrl+x to exit, and type 'y' and then Enter to save these changes. Then to activate this alias, in the command line type <code>source ~/.bashrc</code>, and hit Enter.


Now connect to the EC2 instance by typing either the <code>ssh -i...</code> command or the alias you created, if you did create one. You will be asked if it is okay to connet to the IP address if your EC2 instance; agree to proceed. You will then be connected with the EC2 instance. To disconnect, hit Ctrl+d. We can see that if we type <code>python</code> and hit enter, we open up a Python terminal. On the images currently hosted, the default Python version is 2.7.6. For the project that I am using my EC2 instance for, I attempted to run a script that employed some things from the <code>urllib3</code> module. When I did this, I received an Insecure Platform Warning. In the <a href="http://urllib3.readthedocs.org/en/latest/security.html"><b>documentation</b></a> about this warning, it is strongly recommended to upgrade to Python 2.7.9 or greater. In the next steps, we will detail how to get Python 2.7.9 set up in a virtual environment, and install all the relevant modules.

<p> First, we will install Python 2.7.9

<ol>
<li> Update and upgrade:
<pre><code>
sudo apt-get update && sudo apt-get upgrade
</code></pre>
</li>

<li> Install some dependencies:
<pre><code>
sudo apt-get install build-essential
</code></pre>

<pre><code>
sudo apt-get install libreadline-gplv2-dev libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev
</code></pre>
</li>

<li> Create a Downloads directory, then change to that directory and download the files for Python 2.7.9
<pre><code>
mkdir Downloads
cd Downloads
wget https://www.python.org/ftp/python/2.7.9/Python-2.7.9.tgz
</code></pre>

</li>

<li> Extract the files, and go into the directory Python-2.7.9 that was created
<pre><code>
tar -xvf Python-2.7.9.tgz
cd Python-2.7.9
</code></pre>
</li>

<li> Install Python
<pre><code>
./configure make sudo make install
</code></pre>
</li>

<li> Return to the main directory and update and upgrade again

<pre><code>
cd sudo apt-get update && sudo apt-get upgrade
</code></pre>


At some point during the upgrade process, a menu will appear, asking about which version of a file to use. Select the option that says something to the effect of 'use package maintainer's version'. Now that Python 2.7.9 is installed, it can be called by typing <code>python2.7</code> into the command line
</li>

<li> Before we install <a href="https://virtualenv.readthedocs.org/en/latest/"><b>virtualenv</b></a>, we will install some prerequisites. </li>


<pre><code>
sudo apt-get install build-essential python2.7-dev python-dev python-pip liblapack-dev libblas-dev libatlas-base-dev gfortran libpng-dev libjpeg8-dev libfreetype6-dev libqt4-core libqt4-gui libqt4-dev libzmq-dev
</code></pre>

For this part of the tutorial, I am taking the above command from this <a href="https://www.quantstart.com/articles/Quick-Start-Python-Quantitative-Research-Environment-on-Ubuntu-14-04"><b>article</b></a>.

<li>Install virtualenv

<pre><code>sudo pip install virtualenv</code></pre>
</li>

<li>Now that we have virtualenv installed, we will create a virtual environment to keep our Python 2.7.9 stuff in--and to keep that stuff separate from the Python 2.7.6 stuff.

<ol>
<li> First, make a folder to hold all of our virtual environments that we wish to create in the future. This step is not essential, but is common practice. We will call the folder <code>virtualenvs</code>.

<pre><code>
mkdir virtualenvs
</code></pre>
</li>

<li> Make a virtual environment where the default is Python 2.7.9. The information for a virtual environment will be stored in a directory that we will store in the <code>virtualenvs</code> directory. We will call the directory for the virtual environment we are about to create <code>py-2-7-9_scipy_stack</code>. In order to identify what kind of versions and packages we will have in this virtualenv.

<pre><code>virtualenv -p python2.7 virtualenvs/py-2-7-9_scipy_stack</code></pre>
</li>

<li> Move into the directory where the virtualenv information we just created is stored, and then activate the virtualenv

<pre><code>
cd virtualenvs/py-2-7-9_scipy_stack source bin/activate
</code></pre>

 If we wish to deactivate the virtualenv, use the command <code>deactivate</code>. </li>

 <li> Now that we are our virtual environment, we can begin to install the Python packages we want. The main one we want is SciPy, and since that one is trickier to install than the others, we will detail how to install it. First, SciPy requires numpy, so install numpy; this will take some time.

<pre><code>
pip install numpy
</code></pre>

Next, we wish to install the SciPy module. But because this module is so large, and we are using a micro EC2 instance, we run into problems with memory if we simply try <code>pip install scipy</code>. To overcome this, we will add some <a href="https://www.linux.com/news/software/applications/8208-all-about-linux-swap-space"><b>swap</b></a> space to our memory. We will add a 2048kb swap file. The following instructions come from <a href="https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-ubuntu-12-04">here</a>. First, create the swap file

<pre><code>
sudo dd if=/dev/zero of=/swapfile bs=1024 count=2048k
</code></pre>

 Next prepare the swap file by creating a linux swap area

 <pre><code>
 sudo mkswap /swapfile
 </code></pre>

 Then activate the swap file

<pre><code>
sudo swapon /swapfile
</code></pre>

This file will be available until you log out. In order to make the swap permanent, we need to add the following to the fstab file. Open the fstab file

<pre><code>
sudo nano /etc/fstab
</code></pre>

Paste in the following line
<pre><code>
/swapfile       none    swap    sw      0       0
</code></pre>


Next we will set the swappiness to 10

<pre><code>
echo 10 | sudo tee /proc/sys/vm/swappiness echo vm.swappiness = 10 | sudo tee -a /etc/sysctl.conf
</code></pre>


Finally, we correct the permissions on the swap file by using the following commands

<pre><code>
sudo chown root:root /swapfile sudo chmod 0600 /swapfile
</code></pre>
</li>

<li>Now we can finally proceed with installing SciPy and other modules. Below, I have code to install SciPy and a number of other modules. It should be noted that SciPy takes quite some time to install, as do the <code>pandas</code> and <code>scikit-learn</code> modules.

<pre><code>
pip install scipy
pip install matplotlib
pip install pandas
pip install scikit-learn
pip install ipython
pip install pyzmq
pip install pygments
pip install patsy
pip install statsmodels
</code></pre>
</li>
</ol>

<li> That's it! Whenver you want to use Python 2.7.9 on your EC2 instance, activate the virtual environment as we did above, and you will have available Python 2.7.9 as well as any modules you installed while active in that virtual environment. If you would like to use a different version of Python, say Python 3.x, on your EC2 instance, you can follow largely the same steps. Instead of installing Python 2.7.9, you will have to install Python 3.x. In step 7, you must change the package <code>python2.7-dev</code> to the package <code>python3.4-dev</code>; or 3.3 if you desire. Finally, when you create your virtualev, instead of passing the argument <code>-p python2.7</code>, you will pass <code>-p python3.4</code>. </li> </li> </ol> </p>
