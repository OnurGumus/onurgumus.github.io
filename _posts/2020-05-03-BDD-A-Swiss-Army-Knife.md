---
layout: default
title: "BDD A swiss army knife"
date: 2020-05-03-00:00:00 -0100
comments:true
---

# BDD is a swiss army knife

One of the things I have been attracted in BDD, that it is a great tool to generate your ubiquitous language. In addition, it can be used together in conjunction an event storming session hence you can easily discover your domain events, nouns and commands. If you are unfamiliar with concepts like event storming, don't worry. I will share a comprehensive example.



    {% if page.comments %} 
    <div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://onurgumus-github-io.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
                            
    {% endif %}
