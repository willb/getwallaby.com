---
date: '2010-12-10 09:57:01'
layout: post
slug: extending-wallaby-with-a-python-client-library
status: publish
title: Extending Wallaby with a Python client library
wordpress_id: '17'
---

In [my previous post](http://chapeau.freevariable.com/2010/10/extending-the-wallaby-shell.html), we saw how to extend wallaby by writing Ruby classes that use a client library to extend the wallaby shell.  If you're comfortable with Ruby, this is a great way to build functionality on top of the wallaby API in an idiomatic way.  However, Python programmers shouldn't have to learn Ruby just to interact with wallaby.

The wallaby source repository now includes [a Python client library](http://git.fedorahosted.org/git/?p=grid/wallaby.git;a=blob;f=schema/wallaby.py) to interact with a wallaby service.  This library does a few things to make your life easier:

  1. Raw QMF object proxies are wrapped in Python client objects.
  2. Object references returned by QMF methods are automatically wrapped by client objects.
  3. QMF methods that return error statuses will raise exceptions in the client library.
  4. Client classes include docstrings for QMF methods.


See below for a transcript illustrating these features.  Note that you'll need to have the following packages installed to use the Python wallaby client library:  Red Hat Enterprise MRG 1.3 (or the `python-qmf` package from a recent Fedora release) and Wallaby 0.9.18 or later (0.10.0 to access all of the features exposed through the client library).

{% codeblock Example interaction with the Wallaby Python client library.  lang:python %}
from qmf.console import Session
import wallaby

# create a new console object
console = Session()

# connect to the broker (on localhost:5672, by default)
console.addBroker()
# => Broker connected at: localhost:5672

# find the QMF object for the wallaby service
raw_store, = console.getObjects(_class="Store")

# wrap it up in a client object
store = wallaby.Store(raw_store, console)

# now, interact with it!
node = store.addNode("barney.local.")
feature = store.addFeature("Example feature")
param = store.addParam("EXAMPLE_PARAM")

# most "options" arguments are indeed optional
feature.modifyParams("ADD", {"EXAMPLE_PARAM":"example value"})
store.getDefaultGroup().modifyFeatures("ADD", ["Example feature"])

node.getConfig()
# => {u'WALLABY_CONFIG_VERSION': u'0', u'EXAMPLE_PARAM': u'example value'}

# What does the activateConfiguration method return?
help(store.activateConfiguration)

# Help on method activateConfiguration in module wallaby:
# 
# activateConfiguration(self) method of wallaby.Store instance
#     Returns a tuple consisting of:
#     * A map containing an explanation of why the configuration isn't valid, or an empty map if the configuration was successfully activated.
#     * A set of warnings encountered during configuration activation.

store.activateConfiguration()
# => ({}, [])

node.getConfig()
# => {u'WALLABY_CONFIG_VERSION': u'1292513031757418', u'EXAMPLE_PARAM': u'example value'}

# try something that will cause an error

feature = store.addFeature("Example feature")

# Traceback (most recent call last):
#   File "<stdin>", line 2, in <module>
#   File "wallaby.py", line 404, in addFeature
#     raise ClientError(result.status, result.text)
# wallaby.ClientError: (67109121, u'Feature name Example feature is already taken')
{% endcodeblock %}

(Cross-posted from [Chapeau](http://chapeau.freevariable.com/).)
