#Running a Blog with blosxom as a Tor hidden service
##Tor + Blosxom = love


Blosxom is a very little blogging application. Being so small 
and simple it can be easily tweaked to work well as a Tor Hidden 
service. By well I mean that it must protect the server and client 
from identity leaks.


##Configuring tor as a hidden service</h3>
The first step is configuring Hidden Serivice and getting apache up (for a 
detailed guide on how to configure a hidden service 
<a href="http://www.torproject.org/docs/tor-hidden-service.html.en">See here</a>)
I will just give some pointers on how to avoid identity leaks in apache.

You want to disable the default apache error messages, because they contain
information on your machine. To so so add this to your hidden service 
virtual host:


        ErrorDocument 300 " "
        ErrorDocument 301 " "
        ErrorDocument 302 " "
        ErrorDocument 303 " "
        ErrorDocument 304 " "
        ErrorDocument 305 " "
        ErrorDocument 306 " "
        ErrorDocument 307 " "
        
        ErrorDocument 500 " "
        ErrorDocument 501 " "
        ErrorDocument 502 " "
        ErrorDocument 503 " "
        ErrorDocument 504 " "
        ErrorDocument 505 " "
        ErrorDocument 506 " "
        ErrorDocument 507 " "
        ErrorDocument 509 " "
        ErrorDocument 510 " "
        
        ErrorDocument 400 " "
        ErrorDocument 401 " "
        ErrorDocument 402 " "
        ErrorDocument 403 " "
        ErrorDocument 404 " "
        ErrorDocument 405 " "
        ErrorDocument 406 " "
        ErrorDocument 407 " "
        ErrorDocument 408 " "
        ErrorDocument 409 " "
        ErrorDocument 410 " "
        ErrorDocument 411 " "
        ErrorDocument 412 " "
        ErrorDocument 413 " "
        ErrorDocument 414 " "
        ErrorDocument 415 " "
        ErrorDocument 416 " "
        ErrorDocument 417 " "
        ErrorDocument 418 " "
        ErrorDocument 422 " "
        ErrorDocument 423 " "
        ErrorDocument 424 " "
        ErrorDocument 425 " "
        ErrorDocument 426 " "

What it does is it replaces the standard apache error messages
with blank error strings. This avoids browser fingerprinting and
disclosure of possible sensisitive information regarding the server.
##Setting up Blosxom



Blosxom comes as just one .cgi file written in perl. To get it
to work with apache you need libapache2-mod-perl2.
On debian:
    apt-get install libapache2-mod-perl2


Then you need it to recognize .cgi as a cgi-script so you should
add this to /etc/apache2/apache2.conf

    AddHandler cgi-script .cgi .pl

By default blosxom will not use https on all links and will not work as
a hidden service.
Edit your bloxsom.cgi file in the following way:

    $blog_title = "YOUR BLOG NAME";
    $blog_description = "BLOG DESCRIPTION";
    $datadir = "PLACE WERE U WILL STORE YOUR POSTS";
    $static_dir = "PLACE WERE STATIC RENDERING HAPPENS";

Be sure to place your static dir and datadir outside of your public accessible directory, and ensure that files inside are not
writable by others. If an attacker manages to write your blog entries the least he can do is carry on a 
<a href="http://en.wikipedia.org/wiki/Cross-site_scripting">XSS attack</a>.



To make it work with tor make these changes to 
the blob of html code at the end of the file:

    $url = "/index.cgi"
    html foot &lt;p /&gt;&lt;center&gt;&lt;a href="https://www.blosxom.com/"&gt;&lt;img src="/blosxomandtor.png" border="0" /&gt;&lt;/a&gt;&lt;/body&gt;&lt;/html&gt;


This makes all the links relative, so that they will work with .onion urls. This also works when using
the blog from tor2web. The only issue is when you try and browse the page from x.tor2web.org/<onion_url>;.
This is not that important as &lt;onion_url&gt;.tor2web.org works fine, yet I am looking for a workaround.
