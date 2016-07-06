
GETTING STARTED

Directions to get the repository up and running. This assumes the repo is
checked out as directory $root.

1. Install the packages that the NIEM.GitHub.io website requires, which are
   installed as submodules.

   $ cd $root
   $ git submodule init
   $ git submodule update

2. Install the Ruby packages that GitHub Pages uses:

   $ sudo ruby gem install --verbose github-pages

3. Run jekyll

   $ cd $root
   $ jekyll serve

4. Point your browser to http://127.0.0.1:4000/technical/json/guidance/

DOCUMENT STYLE GUIDE

The ellipsis character (&hellip;) looks great in justified text, but terrible in
monospace text. Type ellipsis as:

  * Within justified text, use &hellip;.
  * Within monspace text, use three periods (...).

ACTION ITEMS

* Section 2.10: Fix text about NULL / empty / missing simple content: "null" probably isn't
  the right way to represent something without a simple value. Just omit the
  rdf:value. null means the same thing. "" is right out
* Section 2.15: Fix metadata: do an easy button transform for metadata. put an "oh well" for
  relationshipMetadata
* Section 2.16: Move in-depth adapter text into its own section in "advanced topics" ... (move
  most of this to an an advanced topics section. this section just does a simple
  custom mapping. Make the JSONLD full example correspond to this)
* Section 3: Show a *simple* XSLT transforming XML to JSON-LD.
* Appendix A: Edit references to follow standard format
* Add a deeper dive on aliasing terms and rdf:value

