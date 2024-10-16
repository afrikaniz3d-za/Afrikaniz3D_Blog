---
title: Maven Hospital Challenge
author: Ntobeko Sosibo [ Afrikaniz3D ]
date: '2024-08-04'
slug: maven-hospital-challenge
categories:
  - project
tags:
  - analytics
  - cte
  - databricks
  - dataset
  - data science
  - data analysis
  - data exploration
  - eda
  - big query
  - sql
  - maven analytics
  - maven hospital challenge
  - maven challenge
  - mgh 
  - massachusetts general hospital
  - healthcare analytics
  - healthcare
draft: no
image: 'img/portfolio/W14/w14000.webp'
showonlyimage: yes
---
  
I took part in this year's Maven Hospital Challenge. I used Databrick's Community Edition. The exploration was mostly SQL-based. I opted for Databricks over Big Query for the ability to upload large files.  
  
Although I completed the exploration in time I chose to opt out of submitting because I didn't have a dashboard solution at the time that I was happy with. I'm currently learning Shiny. It suits my set-up and I have a lot of the infrastructure already that makes it easy to work offline when I need to.  
  
Below is my final notebook for the challenge. There are sections included that were not part of the tasks outlined in the challenge, but they were helpful for me in getting the most out of the exercise.  
&nbsp;  

<iframe id="databricks-notebook" src="/notebooks/N14/n14.html" width="100%" height="600px" style="border:none;"></iframe>

<button onclick="toggleFullScreen()">View Fullscreen</button>

<script>
function toggleFullScreen() {
    var iframe = document.getElementById("databricks-notebook");
    if (iframe.requestFullscreen) {
        iframe.requestFullscreen();
    } else if (iframe.mozRequestFullScreen) { // Firefox
        iframe.mozRequestFullScreen();
    } else if (iframe.webkitRequestFullscreen) { // Chrome, Safari and Opera
        iframe.webkitRequestFullscreen();
    } else if (iframe.msRequestFullscreen) { // IE/Edge
        iframe.msRequestFullscreen();
    }
}
</script>

&nbsp;  
  
<iframe id="databricks-dashboard" src="/notebooks/N14/n14_1.html" width="100%" height="600px" style="border:none;"></iframe>

<button onclick="toggleFullScreen()">View Fullscreen</button>

<script>
function toggleFullScreen() {
    var iframe = document.getElementById("databricks-dashboard");
    if (iframe.requestFullscreen) {
        iframe.requestFullscreen();
    } else if (iframe.mozRequestFullScreen) { // Firefox
        iframe.mozRequestFullScreen();
    } else if (iframe.webkitRequestFullscreen) { // Chrome, Safari and Opera
        iframe.webkitRequestFullscreen();
    } else if (iframe.msRequestFullscreen) { // IE/Edge
        iframe.msRequestFullscreen();
    }
}
</script>

&nbsp;  
  
Community Edition doesn't have as may options as the full suite, but it had enough to visualise off the notebook.
