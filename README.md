Note: This repo is largely a snapshop record of bring Wikidata
informaiton in line with Wikipedia, rather than code specifically
deisgned to be reused.

The code and queries etc here are unlikely to be updated as my process
evolves. Later repos will likely have progressively different approaches
and more elaborate tooling, as I try to tweak something each time
through

---------

Step 1: Tracking page
=====================

Starting point: https://www.wikidata.org/w/index.php?title=Talk:Q6866033&oldid=1232183430

(Wikidata only knows of a single officeholder, in 1990)

Step 2: Scraper
===============

Comparison/source = https://en.wikipedia.org/wiki/Minister_of_Agriculture_(New_Zealand)

`scraper.rb` is fairly standard version: only needed the URL changed
from previous equivalent scraper.

Run as:

    bundle exec ruby scraper.rb| tee wikipedia.json`

Step 3: Get local copy of Wikidata information
==============================================

    wd sparql office-holders.js Q6866033 | tee wikidata.json

Step 4: Create missing P39s
===========================

    bundle exec ruby new-P39s.rb wikipedia.csv wikidata.json |
      wd ee --batch --summary "Add missing P39s from https://en.wikipedia.org/wiki/Minister_of_Agriculture_(New_Zealand)"

These were created in edit group: https://tools.wmflabs.org/editgroups/b/wikibase-cli/c8d6edbb5fc5f/

Step 5: Add missing qualifiers
==============================

Usually done similarly to step 4, but here the only existing P39 is
already complete, so there's nothing to add, as we can see by getting no
output from

    bundle exec ruby new-qualifiers.rb wikipedia.csv wikidata.json







