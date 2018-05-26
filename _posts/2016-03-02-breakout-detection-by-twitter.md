---
layout: post
title: "Breakout Detection by Twitter"
date: 2016-03-02
blurb: Describing Twitter's breakout detection package.
author: Eric Bunch
---




<p>
This post is about Twitter's <a href="https://github.com/twitter/BreakoutDetection">breakout detection</a> package. This is a statistical package originally written in R that detects breakouts, which are also called level shifts or jumps in time series data. A level shift in time series data is not something that is extremely well defined, but is more or less a significant change in mean in the time series data that persists for a significant amount of time. I have been looking at some time series data lately, and there is a need to systematically detect when level shifts occur; I am looking at thousands of time series', and need to check for new breakouts each day. This is something that would be great if it could be automated. </p>

<p>
There are multiple libraries in R that can detect level shifts, including <a href="https://cran.r-project.org/web/packages/tsoutliers/index.html"><b>tsoutliers</b></a> and <a href="https://cran.r-project.org/web/packages/changepoint/index.html"><b>changepoint</b></a> in addition to <a href="https://github.com/twitter/BreakoutDetection"><b>BreakoutDetection</b></a>. I have done some comparative analysis of these few packages $\text{(although there are surely more)}$, and I have found that the breakout detection package is the best suited for my purposes. I'm not going to go into the comparison in this post, but will simply focus on the breakout detection package. </p>

<p>
I won't go into the math behind breakout detection, but it's pretty interesting. The method combines <a href="http://www.sciencedirect.com/science/article/pii/S0378375813000633">E-statistics</a> $\text{(Energy Statistics)}$, and <a href="https://en.wikipedia.org/wiki/Robust_statistics">Robust Statistics</a>. In section 8 of this <a href="/static/Szekely_estats.pdf">paper</a> gives a brief discussion on the reasoning behind energy statistics.
</p>

<p>
Now I'll talk a bit about how to utilize the package. I'll describe how to use the version ported over to Python. First, clone the project from github <a href='https://github.com/roland-hochmuth/BreakoutDetection'>here</a>. Before the next step, I recommend creating a virtual environment and activating	it before installing the breakout detection package. After the repository is cloned, cd into the directory <code>BreakoutDetection/Python</code>. Then execute the following three commands $\text{(this is also on the github repository under Python: How to get started)}$
</p>

<pre><code>
swig -python -c++ breakout_detection.i <br>python setup.py build_ext -I../src build <br>sudo python setup.py build_ext -I../src install</code></pre>

$\text{(if you are in a virtualenv, then leave off the sudo of the last command)}$. Then to use this package in a python script, you must `import breakout_detection`


<p>
The following is an example of how to use the breakout detection package. It's using the breakout detection package on the S&P500 index to detect breakouts. The index is tracked from Jan. 4, 2010.
</p>

<pre><code class='python'>
import pandas as pd <br>import breakout_detection <br>import pandas.io.data as web <br>import matplotlib.pyplot as plt <br><br>plt.style.use('fivethirtyeight')<br>edm_multi = breakout_detection.EdmMulti() <br>snp = web.DataReader('^GSPC', 'yahoo') <br>max_snp = max(max(snp['Open']),1) <br>Z = [x/float(max_snp) for x in snp['Open']] <br>edm_multi.evaluate(Z, min_size=64, beta=0.0001, degree=1) <br>snp['Open'].plot(figsize=(30,10)) <br>for i in edm_multi.getLoc(): <br>    plt.axvline(snp['Open'].index[i], color='#FF4E24')</code></pre>

<p>
This gives the following graph
<br><br><br>
<img class='breakout_graph' src='/images/breakout_detection_snp.png' style="width: 100%;">

<br><br><br>

 I think it works pretty well! The beta parameter is a kind of penalization threshold. Higher values will result in less breakout points. The min_size parameter acts like a kind of periodicity constraint. Once a breakout point is detected, another cannot be detected within min_size units of time $\text{(or whatever is on the x-axis)}$. This also means that a breakout point cannot be detected in the first min_size units on the x-axis. I chose 64 for min_size because that's roughly the amount of days in a quarter, and I figured a meaningful shift in the S&P500 index would happen in a quarter. The degree parameter can take values 0, 1, or 2. Degree = 0 will not impose any penalization of detection of breakout points--it will even cancel out the beta parameter. Degree = 1 will have a penalization effect, and will not have two breakout points too close to another. Degree = 2 will have a heavier penalization for closeness of breakout points.
</p>
