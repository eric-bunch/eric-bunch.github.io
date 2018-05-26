---
layout: post
title: "The Simpsons' Best Episode Ever by the Data"
date: 2017-08-03
blurb: Using Kaggle's Simpsons data set, we determine which episode is the definitive Best Episode Ever!
author: Eric Bunch
---



<img src="/images/simpsons_as_pixels.jpg" style="width: 100%" />

<br>
<br>

<a href="https://www.wikiwand.com/en/The_Simpsons">The Simpsons</a> is one of the <a href="https://www.wikiwand.com/en/List_of_longest-running_United_States_television_series">longest-running</a> shows on television, and a piece of American culture. I personally grew up on The Simpsons, and am still a big fan. Not too long ago, Kaggle put up a <a hfref='https://www.kaggle.com/wcukierski/the-simpsons-by-the-data'>data set</a> on The Simpsons, and I thought it would be fun to explore it!

Even if you haven't seen all 618 (as of this post!) episodes of The Simpsons, you probably know that it has passed it's prime. Below is a the graph of <a href="http://www.imdb.com/">IMDB</a> Ratings and Number of U.S. Viewers in millions. There are some curious things to note about this graph. I was surprised that the number of viewers was so high in the first few seasons, and that it has almost steadily declined since then. The curious jump in viewers around Episode 250 occurred at the start of Season 12, which is when the show switched from airing starting in September, to start in November, as well as beginning the tradition of starting the season with the Halloween "Treehouse of Horror" episode. The IMDB Ratings increase slightly over time, until a little before Episode 200--somewhere during Season 9, and then declines steadily for a while, and levels out.

In this post, we will explore some basic questions about the data, and then we will try to answer the question "Which is the best (and worst) episode of The Simpsons?" by combining the IMDB Ratings with the number of U.S. viewers.


<br><br>
<img src="/images/simpsons_ratings_and_viewers.png" style="width: 100%;" >
<br><br>

<h3>Spoken word count</h3>
Let's take a quick look at the number of lines spoken by character, as well as the location they were spoken. First, let's look at a simple bar chart counting up the totals. As we can see, the discrepancy between the number of lines Homer has far outstrips even the rest of the Simpsons family. The same can be said of the number of lines spoken at the Simpsons' home and the other locations in the show.

<br><br>

<img src="/images/simpsons_joint_char_loc_by_speaking_line.png" style="width: 100%;" >

<br><br>

In the graphs below, we can see that the counts above persist over the seasons. There are a few curious things about the two plots below. We can see that the number of speaking lines that Homer had drastically increased a little before Episode 200--somewhere around Season 9. Around the same time, the number of lines spoken at the Simpsons' Home decreased significantly. This is the same time frame where the IMDB Ratings of the show decreased significantly. One hypothesis one could form based on these graphs is that the suffering reviews were a result of more Homer-centric episodes, following the locations he goes to.

<br><br>
<img src="/images/simpsons_line_per_char_ts.png" style="width: 100%;" >
<br><br>

<img src="/images/simpsons_line_by_location_ts.png" style="width: 100%;" >
<br><br>

<h3>Episode Quality</h3>

Now, we'll try to come up with a definitive Simpsons Best Episode Ever. To do this, we will use a weighted sum of the IMDB Rating and the (scaled) U.S. viewers in millions. This is to balance out the more specialized ratings that IMDB gives with the more populist vote that people gave with their TV time. Here, we weight the IMDB rating 80% and the U.S. viewers in millions 20% to yield the metric we will call Episode "Quality".

<br><br>
<img src="/images/simpsons_quality.png" style="width: 100%;" >
<br><br>

There are a few interesting things about the Quality graph above. We can see there is a significant dip in quality a little after Episode 200 (around Season 9 or 10)-- the Best Episode Ever will be before this point. In addition, there are a few episodes that have sharp spikes downward relative to the other Episodes of their time. In the graph below, we have marked the Best Episode Ever, as well as these curious negative outliers.


<br><br>

<img src="/images/simpsons_quality_marked.png" style="width: 100%;" >

<br><br>

As the graph above shows, The Simpsons' Best Episode Ever is:

<h3><a href="https://www.wikiwand.com/en/Marge_vs._the_Monorail">Marge vs. the Monorail</a>!</h3>

This Episode aired in 1993 during Season 4, featuring Marge as the main protagonist. During the episode the town of Springfield comes into a financial windfall, and calls a town meeting to decide how to spend the money. Marge suggests they use the money to fix up their roads. Instead, the townspeople were won over by a fast-talking salesman, Lyle Lanley (voiced by <a href="https://www.wikiwand.com/en/Phil_Hartman">Phil Hartman</a>), and decided to built a Monorail. Later, Marge investigates and discovers that the salesman Lyle is a conman, the Monorail is shoddily built, and extremely unsafe.

The other four episodes marked are
- <a href="https://www.wikiwand.com/en/Another_Simpsons_Clip_Show">Another Simpsons Clip Show</a> $\text{(1994, Season 6)}$
- <a href="https://www.wikiwand.com/en/All_singing,_all_dancing">All Singing, All Dancing</a> $\text{(1998, Season 9)}$
- <a href="https://www.wikiwand.com/en/Gump_Roast">Gump Roast</a>$\text{(2002, Season 13)}$
- <a href="https://www.wikiwand.com/en/Lisa_Goes_Gaga">Lisa Goes Gaga</a> $\text{(2012, Season 23)}$

Three of these shows $\text{(Another Simpsons Clip Show, All Singing, All Dancing & Gump Roast)}$ are essentially clip shows, so understandably had lower quality. The Episode Lisa Goes Gaga is in fact the Episode with the lowest quality $\text{(Worst Episode Ever!)}$. This Episode has featured guest Lady Gaga, who comes to Springfield and takes it upon herself to cheer up the whole town. She takes a particular interest in Lisa. Lady Gaga eventually helps Lisa learn to appreciate herself for who she is, and then the two perform a duet together.

Below are the top 10 Episodes by quality $\text{(my personal favorite comes in at #4: You Only Move Twice)}$.

<br><br>


<div style="overflow: scroll">
<table border="1" class="dataframe" >
  <thead>
    <tr style="text-align: right;">
      <th>Title</th>
      <th>Original air date</th>
      <th>Season</th>
      <th>Episode # in Season</th>
      <th>US viewers (M)</th>
      <th>IMDB Rating</th>
      <th>Quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Marge vs. the Monorail</td>
      <td>1993-01-14</td>
      <td>4</td>
      <td>12</td>
      <td>23.0</td>
      <td>9.0</td>
      <td>7.660</td>
    </tr>
    <tr>
      <td>Last Exit to Springfield</td>
      <td>1993-03-11</td>
      <td>4</td>
      <td>17</td>
      <td>22.4</td>
      <td>9.0</td>
      <td>7.648</td>
    </tr>
    <tr>
      <td>Treehouse of Horror V</td>
      <td>1994-10-30</td>
      <td>6</td>
      <td>6</td>
      <td>22.2</td>
      <td>9.0</td>
      <td>7.644</td>
    </tr>
    <tr>
      <td>You Only Move Twice</td>
      <td>1996-11-03</td>
      <td>8</td>
      <td>2</td>
      <td>13.9</td>
      <td>9.2</td>
      <td>7.638</td>
    </tr>
    <tr>
      <td>The Springfield Files</td>
      <td>1997-01-12</td>
      <td>8</td>
      <td>10</td>
      <td>20.9</td>
      <td>9.0</td>
      <td>7.618</td>
    </tr>
    <tr>
      <td>Cape Feare</td>
      <td>1993-10-07</td>
      <td>5</td>
      <td>2</td>
      <td>20.0</td>
      <td>9.0</td>
      <td>7.600</td>
    </tr>
    <tr>
      <td>Homer's Enemy</td>
      <td>1997-05-04</td>
      <td>8</td>
      <td>23</td>
      <td>11.8</td>
      <td>9.2</td>
      <td>7.596</td>
    </tr>
    <tr>
      <td>Homer the Heretic</td>
      <td>1992-10-08</td>
      <td>4</td>
      <td>3</td>
      <td>19.3</td>
      <td>9.0</td>
      <td>7.586</td>
    </tr>
    <tr>
      <td>Who Shot Mr. Burns? (Part One)</td>
      <td>1995-05-21</td>
      <td>6</td>
      <td>25</td>
      <td>15.0</td>
      <td>9.1</td>
      <td>7.580</td>
    </tr>
    <tr>
      <td>Homer Badman</td>
      <td>1994-11-27</td>
      <td>6</td>
      <td>9</td>
      <td>17.0</td>
      <td>9.0</td>
      <td>7.540</td>
    </tr>
  </tbody>
</table>
</div>

<br><br>


Below we have the plot of IMDB Ratings, U.S. viewers in millions, and Quality aggregated by Season and averaged. Below the graph we can have the top 10 Seasons ranked by IMDB Rating, U.S. viewers in Millions, and Quality.

<br><br>

<img src="/images/simpsons_ratings_viewers_quality_by_season.png" style="width: 100%;" >
<br><br>

<div class="row">
<div class="col-md-4" style="padding-bottom: 25px;">
Top 10 Seasons by IMDB Rating

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Season</th>
      <th>IMDB Rating</th>
      <th>US viewers (M)</th>
      <th>Quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>5</td>
      <td>8.336364</td>
      <td>18.940909</td>
      <td>7.047909</td>
    </tr>
    <tr>
      <td>7</td>
      <td>8.324000</td>
      <td>14.660000</td>
      <td>6.952400</td>
    </tr>
    <tr>
      <td>6</td>
      <td>8.312000</td>
      <td>15.596000</td>
      <td>6.961520</td>
    </tr>
    <tr>
      <td>4</td>
      <td>8.268182</td>
      <td>22.395455</td>
      <td>7.062455</td>
    </tr>
    <tr>
      <td>8</td>
      <td>8.220000</td>
      <td>14.600000</td>
      <td>6.881091</td>
    </tr>
    <tr>
      <td>3</td>
      <td>8.154167</td>
      <td>21.766667</td>
      <td>6.958667</td>
    </tr>
    <tr>
      <td>2</td>
      <td>8.040909</td>
      <td>24.404545</td>
      <td>6.920818</td>
    </tr>
    <tr>
      <td>9</td>
      <td>7.844000</td>
      <td>10.008000</td>
      <td>6.475360</td>
    </tr>
    <tr>
      <td>1</td>
      <td>7.807692</td>
      <td>27.807692</td>
      <td>6.802308</td>
    </tr>
    <tr>
      <td>10</td>
      <td>7.569565</td>
      <td>8.737391</td>
      <td>6.230400</td>
    </tr>
  </tbody>
</table>
</div>



<div class="col-md-4" style="padding-bottom: 25px;">
Top 10 Seasons by U.S. viewers (M)

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Season</th>
      <th>IMDB Rating</th>
      <th>US viewers (M)</th>
      <th>Quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>7.807692</td>
      <td>27.807692</td>
      <td>6.802308</td>
    </tr>
    <tr>
      <td>2</td>
      <td>8.040909</td>
      <td>24.404545</td>
      <td>6.920818</td>
    </tr>
    <tr>
      <td>4</td>
      <td>8.268182</td>
      <td>22.395455</td>
      <td>7.062455</td>
    </tr>
    <tr>
      <td>3</td>
      <td>8.154167</td>
      <td>21.766667</td>
      <td>6.958667</td>
    </tr>
    <tr>
      <td>5</td>
      <td>8.336364</td>
      <td>18.940909</td>
      <td>7.047909</td>
    </tr>
    <tr>
      <td>6</td>
      <td>8.312000</td>
      <td>15.596000</td>
      <td>6.961520</td>
    </tr>
    <tr>
      <td>12</td>
      <td>7.361905</td>
      <td>15.485714</td>
      <td>6.199238</td>
    </tr>
    <tr>
      <td>7</td>
      <td>8.324000</td>
      <td>14.660000</td>
      <td>6.952400</td>
    </tr>
    <tr>
      <td>8</td>
      <td>8.220000</td>
      <td>14.600000</td>
      <td>6.881091</td>
    </tr>
    <tr>
      <td>14</td>
      <td>7.077273</td>
      <td>14.378182</td>
      <td>5.949382</td>
    </tr>
  </tbody>
</table>
</div>

<div class="col-md-4" style="padding-bottom: 25px;">
Top 10 Seasons by Quality

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Season</th>
      <th>IMDB Rating</th>
      <th>US viewers (M)</th>
      <th>Quality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>4</td>
      <td>8.268182</td>
      <td>22.395455</td>
      <td>7.062455</td>
    </tr>
    <tr>
      <td>5</td>
      <td>8.336364</td>
      <td>18.940909</td>
      <td>7.047909</td>
    </tr>
    <tr>
      <td>6</td>
      <td>8.312000</td>
      <td>15.596000</td>
      <td>6.961520</td>
    </tr>
    <tr>
      <td>3</td>
      <td>8.154167</td>
      <td>21.766667</td>
      <td>6.958667</td>
    </tr>
    <tr>
      <td>7</td>
      <td>8.324000</td>
      <td>14.660000</td>
      <td>6.952400</td>
    </tr>
    <tr>
      <td>2</td>
      <td>8.040909</td>
      <td>24.404545</td>
      <td>6.920818</td>
    </tr>
    <tr>
      <td>8</td>
      <td>8.220000</td>
      <td>14.600000</td>
      <td>6.881091</td>
    </tr>
    <tr>
      <td>1</td>
      <td>7.807692</td>
      <td>27.807692</td>
      <td>6.802308</td>
    </tr>
    <tr>
      <td>9</td>
      <td>7.844000</td>
      <td>10.008000</td>
      <td>6.475360</td>
    </tr>
    <tr>
      <td>10</td>
      <td>7.569565</td>
      <td>8.737391</td>
      <td>6.230400</td>
    </tr>
  </tbody>
</table>
</div>
</div>
