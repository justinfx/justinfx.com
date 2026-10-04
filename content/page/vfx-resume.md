+++
author = "Justin Israel"
date = "2010-04-07"
meta_keywords = ["justin israel", "vfx", "resume"]
showDate = false
title = "VFX Resume"
url = "/vfx-resume/"

+++
Direct Link: <a href="https://drive.google.com/open?id=0B9Jy7IiM00tZNDkzNDYyZTUtNTZkNC00YWVhLWJiYWQtNWJkMWY0ZDNjNDBj" target="_blank">PDF</a>

This is a one page summary. My <a href="https://www.linkedin.com/in/justinisrael/" target="_blank" rel="noopener noreferrer">LinkedIn profile</a> has my complete professional history.

<iframe id="resume-frame" src='/public/resume/index.html'
scrolling='no'
frameborder='0'
width="100%"
style="height: 300vh;">
</iframe>
<script>
  (function () {
    var f = document.getElementById('resume-frame');
    function fit() {
      try { f.style.height = f.contentDocument.documentElement.scrollHeight + 'px'; } catch (e) {}
    }
    f.addEventListener('load', function () {
      fit();
      new ResizeObserver(fit).observe(f.contentDocument.documentElement);
    });
  })();
</script>