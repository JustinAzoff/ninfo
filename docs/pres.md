% nInfo: Query all the Things
% Justin Azoff
% May 8, 2014

# nInfo: Query all the Things

![](../meme.jpeg "Query all the Things")

# What is nInfo?

- nInfo is a Library
- nInfo is a CLI Tool
- nInfo is a Web Interface

# What is nInfo?

- nInfo is an API for your APIs
- nInfo is an API for external APIs
- nInfo is an API for things that don't really have an API

# Why nInfo?

nInfo provides a *uniform* API for looking up data

. . .

that caches results

. . .

and has single point of configuration

. . .

and comes with a nice web interface

# nInfo knows what type of data you are looking up

- IP Address (v4 or v6)
- CIDR Block (v4 or v6)
- MAC Address
- Hostname
- Username
- Hashes (as in md5/sha1 etc)


# What does nInfo look like?

## Used as a Library

~~~~~~~~~~~~~~~~~~ {.python}
>>> from ninfo import Ninfo
>>> n = Ninfo()
>>> print n.get_info_text("geoip", "1.2.3.4")
AU - Australia
>>> print n.get_info("geoip", "1.2.3.4")["country_name"]
Australia
~~~~~~~~~~~~~~~~~~

# What does nInfo look like?

## Used as a CLI Tool

~~~~~~~~~~~~~~~~~~
$ ninfo -p geoip 1.2.3.4 4.2.2.2
=== 1.2.3.4 ===
*** GeoIP (Geographic IP location) ***
AU - Australia

=== 4.2.2.2 ===
*** GeoIP (Geographic IP location) ***
US - United States
~~~~~~~~~~~~~~~~~~

# What does nInfo look like?

## Web Interface

Demo time :-)

# What is a plugin

A plugin is a class containing some metadata and two functions:

- setup: perform any one time initialization for the plugin
- get_info: do whatever it is this plugin does

A plugin can also contain templates:

- text: used for the cli tool
- html: used for the web interface

# Let's write a plugin for the Shodan API

Shodan happens to have a nice python wrapper already.  However, nInfo helps
with the following:

- Importing the right module
- Passing in the required configuration
- Caching the results
- Formatting the output

# Plugin class and Metadata

~~~~~~~~~~~~~~~~~~ {.python}

from ninfo import PluginBase

class shodan_plug(PluginBase):
    """This plugin returns any information from Shodan"""
    name          = 'shodan'
    title         = 'Shodan'
    description   = 'Computer Search Engine'
    cache_timeout = 60*60*2
    types         = ['ip']
    remote        = False

    #<<setup+get_info>>

plugin_class = shodan_plug

~~~~~~~~~~~~~~~~~~

# Setup function

~~~~~~~~~~~~~~~~~~ {.python}
def setup(self):
    from shodan import WebAPI
    self.api = WebAPI(self.plugin_config["api_key"])
~~~~~~~~~~~~~~~~~~

# Get Info function
~~~~~~~~~~~~~~~~~~ {.python}
def get_info(self, arg):
    info = self.api.host(arg)
    return info
~~~~~~~~~~~~~~~~~~

# HTML Template

~~~~~~~~~~~~~~~~~~ {.html}
%for s in data:
<h3> Port ${s['port']} at ${s['timestamp']}</h3>
<pre>
Banner ${s['banner']}
</pre>
%endfor
~~~~~~~~~~~~~~~~~~

# Writing your own plugins

Plugins need to be part of a python package that is setup in a very specific
way.  There is a paster template that can help set this up.  Once installed, a
new plugin package can be created with:

    paster create -t ninfo_plugin ninfo-plugin-foo


# Links

- [github.com/JustinAzoff/ninfo](https://github.com/JustinAzoff/ninfo)
- [github.com/JustinAzoff/ninfo_web](https://github.com/JustinAzoff/ninfo_web)
- [github.com/JustinAzoff/django-ninfo](https://github.com/JustinAzoff/django-ninfo)
- [github.com/JustinAzoff/ninfo-plugin-template](https://github.com/JustinAzoff/ninfo-plugin-template)
- [github.com/JustinAzoff/ninfo-client](https://github.com/JustinAzoff/ninfo-client)
- [Search github for ninfo-plugin](https://github.com/search?p=1&q=ninfo-plugins&ref=searchresults&type=Repositories)
