---
title: FCCSW documentation
subtitle: Re-vamping
category: fcc
img: ../images/fcc-docs.png
more: http://fccsw.web.cern.ch
related: doc-collecting
---

The FCC software is fragmented across several repositories (partly to facilitate easier code re-use, partly due to bad habits). Documentation was therefore spread across Doxygen-pages, TWiki-pages and markdown documents residing in the different repositories.

I lead an effort to bring all this together in a website that integrates everything and can be generated as part of our nightly builds. The first step was to migrate our CERN TWiki pages to markdown. I wrote a python script which uses the github API to discover all repositories owned by the HEP-FCC organisation, gets the file-tree and fetches all markdown pages. In a second step hyperlinks are processed with regular expressions to make them work both when viewing the original markdown on github as well as in the generated website. Finally, these markdown pages along with a few dedicated pages are fed to jekyll and the website is generated.
