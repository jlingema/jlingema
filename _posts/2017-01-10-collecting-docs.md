---
title: Collecting documentation
excerpt: Fragmented documentation is hard to find. This is our solution.
categories: code snippet
layout: post
permalink: doc-collecting
---

### Premise

The FCC software is fragmented across several repositories (partly to facilitate easier code re-use, partly due to bad habits). Documentation was therefore spread across Doxygen-pages, TWiki-pages and markdown documents residing in the different repositories.

The idea is bring all this together in a website that integrates everything and can be generated as part of our nightly builds.


### Preparation

The first step was to migrate our CERN TWiki pages to markdown. Part of this was automatically done with [pandoc](http://pandoc.org/) but touch-ups were necessary to convert some of the links, etc. However, tha TWiki pages were anyway out-dated and had to be partly re-written. These markdown pages are now being collected in a dedicated [Tutorials repository](https://github.com/HEP-FCC/fcc-tutorials).

### Collecting pages from all repositories

 I wrote a python script which uses the github API to discover all repositories owned by the HEP-FCC organisation. It gets the file-trees:

 ```python
 def get_repo_contents(repo, commit, repo_contents):
    """ get the tree of the repository contents """
    url = 'https://api.github.com/repos/{repo}/git/trees/{sha}?recursive=1'.format(repo=repo, sha=commit)
    fobj = urllib.urlopen(url)
    repo_contents[repo+commit] = json.loads(fobj.read())
```

Which are then searched for any markdown files:

```python
def find_files_of_type(repo, commit, suffix=".md"):
    """ return a list of markdown files in the repo """
    if repo+commit not in repo_contents.keys():
        get_repo_contents(repo, commit)
    contents = repo_contents[repo+commit]
    return [GithubFile(elt["url"], elt["path"]) for elt in contents["tree"] if elt["path"].endswith(suffix)]
```

Now, we could just pre-fix them with some front-matter and run them through jekyll, but that will result in many broken links, since the documentation relies heavily on relative paths within the various repositories.

We use some regular expression trickery to get those links out and transform them in a way that they also work in the generated website:

```python
def convert_markdown_links(match):
    """ for relative links: convert *.md to *.html (for the links to work both on github and in jekyll) """
    label = match.group(1)
    url = match.group(2)
    ret_string = "[{label}]({url})"
    # naive capture of non-local links (e.g. directly to github):
    if not url.startswith("http"):
        url = url.replace('.md', '.html')
    elif url.startswith("https://github.com/HEP-FCC/") and url.endswith(".md"):
        url = url.replace('.md', '.html')
        url = url.replace('https://github.com/HEP-FCC/', '../')
        url = url.replace('/tree/master/', '/')
        url = url.replace('/blob/master/', '/')
    return ret_string.format(label=label, url=url)

content = re.sub("\[(.+)\]\(([^\)]*)\)", convert_markdown_links, content)
```

Now everything works both within the repositories and also on our [new website](http://fccsw.web.cern.ch/fccsw/) generated with jekyll and bootstrap.
