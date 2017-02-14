---
title: Python Docstring
excerpt: Sublime Text snippet for an extended function docstring.
categories: code snippet
layout: post
---


Use-case and actual snippet of an extended auto-complete function head in python.
The extended description shows concisely what the function returns and what it expects as input.

### Use-Case

Imagine you have a rather long function body. Possibly with multiple return paths. Sometimes it can be hard to determine what it is exactly the function returns.

At the same time, sometimes (especially in python) the arguments the function expects are not exactly clear. While you can optimise with good variable names the type may still be somewhat arbitrary.

How can we solve this? With good documentation, of course. The code snippet on the right is similar to something I saw in a [kaggle.com](http://kaggle.com) competition submission. The docstring is quite long and maybe too long for most functions you write. But in cases as discussed above it could be really helpful and you could profit from it:

```python

def convert_input_vector_to_strings(vec, vhdl_dict, mu_type):
    """
    converts the input std::vector<l1t::L1TRegionalMuonCandidate>
    into two strings with one containing one muon per line and the
    other encoding the tracks of three muons per line.
    TAKES:
        vec:        the input vector
        vhdl_dict:  as defined in ../tools/vhdl.py
        mu_type:    BAR, OVL, FWD
    RETURNS: pair of strings:
        [0] muon strings (see get_muon_line)
        [1] muon track lines (see get_track_line)
    """
    # A rather long function body...

    return mu_string, track_string

```


One thing that struck me: In the code I saw, every function had such a header. Through using the same format every time, the documentation and with it, the code became very clear.



### Sublime Snippet

Upon typing deff <tab> this snippet creates a docstring template as shown above. Now only the usual documentation has to be actually typed.

```xml
<snippet>
	<content><![CDATA[
def ${1:function}(${2}):
	"""
	${3:docstring}
	TAKES:
		${2/, /(\n\t\t)/g}
	RETURNS:
		${4:void}
	"""
	pass
]]></content>
	<tabTrigger>deff</tabTrigger>
	<scope>source.python</scope>
	<description>
		Function snippet for a more detailed
	  	docstring that explains the signature
	</description>
</snippet>
```
