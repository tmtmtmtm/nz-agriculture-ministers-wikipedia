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

    bundle exec ruby scraper.rb| tee wikipedia.csv

Step 3: Get local copy of Wikidata information
==============================================

    wd sparql office-holders.js Q6866033 | tee wikidata.json

Step 4: Create missing P39s
===========================

I manually removed Damien O'Connor from the file, as there's a large gap
before him, and I want to think about the replaces/replaced-bys etc
first, and then ran

    bundle exec ruby new-P39s.rb wikipedia.csv wikidata.json |
      wd ee --batch --summary "Add missing P39s from https://en.wikipedia.org/wiki/Minister_of_Agriculture_(New_Zealand)"

These were created in edit group: https://tools.wmflabs.org/editgroups/b/wikibase-cli/28605d7e0f7ed/

Step 5: Add missing qualifiers
==============================

    bundle exec ruby new-qualifiers.rb wikipedia.csv wikidata.json

This gave three changes that could be applied, all of which looked OK,
so I selected those and then ran:

    pbpaste | wd aq --batch --summary "Add missing qualifiers, from https://en.wikipedia.org/wiki/Minister_of_Agriculture_(New_Zealand)"

(With a larger set, and the need to pick and choose, I'd send to file
and edit)

Those were added as https://tools.wmflabs.org/editgroups/b/wikibase-cli/224dfec2a8ab

Step 6: Return to Damien O'Connor
=================================

After giving the QueryService time to catch up, I rebuilt the local copy
of what's in Wikidata:

    wd sparql office-holders.js Q6866033 | tee wikidata.json

and then restored the full wikipedia.csv file again (this time to
include Damien O'Connor)

`new-qualifiers` now returns nothing to be amended (as expected):

    be ruby new-qualifiers.rb wikipedia.csv wikidata.json 

`new-P39s` suggests an addition for O'Connor of:

    add_P39.js Q5212493 2017-10-26 '' Q6128819 '' 34

But I think I want to drop the 'replaces' from that, and add it just as

    wd ee add_P39.js Q5212493 2017-10-26 '' '' '' 34

Not sure if this is quite correct, but I'll leave it as-is for now, and
someone else can decide later.

Step 7: Refresh the Tracking Page
=================================

We finish with: https://www.wikidata.org/w/index.php?title=Talk:Q6866033&oldid=1232235758

