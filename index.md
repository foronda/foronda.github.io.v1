---
#
# Use the widgets beneath and the content will be
# inserted automagically in the webpage. To make
# this work, you have to use › layout: frontpage
#
layout: frontpage
title: "Feeling Responsive – A Jekyll Theme Based On Foundation"
header:
   image_fullwidth: "unsplash_brooklyn-bridge_header.jpg"
widget-1:
    title: "Online resume!"
    url: 'http://brylianforonda.com/resume'
    text: 'Highly motivated IT professional and web application developer with a missino to work for todays top technology companies where I can utilize my knowledge and skill to solve real-world problems.'    
    image: unsplash_9-302x182.jpg
widget-2:
    title: "Destiny - Skirmish"
    url: 'https://www.youtube.com/watch?v=rld97UeDm-s'
    text: 'Skirmish on Asylum. When the Hawkmoon was actually better than the Thorn...'
    video: '<a href="#" data-reveal-id="videoModal"><img src="http://brylianforonda.com/images/start-video-feeling-responsive-302x182.jpg" width="302" height="182" alt=""></a>'
widget-3:
    title: "About Me"
    url: 'http://brylianforonda.com'
    text: 'Part IT, Part Web Developer.'
    image: github-303x182.jpg
---


<div id="videoModal" class="reveal-modal large" data-reveal="">
  <div class="flex-video widescreen vimeo" style="display: block;">
    <iframe width="1280" height="720" src="https://www.youtube.com/embed/rld97UeDm-s" frameborder="0" allowfullscreen></iframe>
  </div>
  <a class="close-reveal-modal">&#215;</a>
</div>