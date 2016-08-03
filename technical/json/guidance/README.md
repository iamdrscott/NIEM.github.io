---
title: README
---

Notes for the NTAC JSON-LD Guidance repository

* placeholder
{:toc}

## Getting Started

Directions to get the repository up and running. This assumes the repo is
checked out as directory `$root`.

1. Install the packages that the NIEM.GitHub.io website requires, which are
   installed as submodules.

   ```bash
$ cd $root
$ git submodule init
$ git submodule update
```

2. Install the Ruby packages that GitHub Pages uses:

   ```bash
$ sudo ruby gem install --verbose github-pages
```

3. Run jekyll

   ```bash
$ cd $root
$ jekyll serve
```

4. Point your browser to [http://127.0.0.1:4000/technical/json/guidance/](http://127.0.0.1:4000/technical/json/guidance/)

## Document Style Guide

The ellipsis character (&hellip;) looks great in justified text, but terrible in
monospace text. Type ellipsis as:

  * Within justified text, use `&hellip;`.
  * Within monspace text, use three periods (`...`).

## Action Items

1. [Section 2.10](./#elements-with-empty-and-nilled-content): Fix text about
   NULL / empty / missing simple content: "null" probably isn't the right way to
   represent something without a simple value. Just omit the `rdf:value`. null
   means the same thing. "" is right out
2. [Section 2.15](./#metadata): Fix metadata: do an easy button transform for
   metadata. put an "oh well" for `relationshipMetadata`
3. [Section 2.16](./#adapter-elements): Move in-depth adapter text into its own
   section in "advanced topics" ... (move most of this to an an advanced topics
   section. this section just does a simple custom mapping. Make the JSONLD full
   example correspond to this)
4. Section 3: Show a *simple* XSLT transforming XML to JSON-LD.
5. Appendix A: Edit references to follow standard format
6. Add a deeper dive on aliasing terms and rdf:value
7. action items from the NTAC 7/20 call : Updates to Non-Normative Guidance in
   Using NIEM with JSON
    1. Status: Add sentence stating this is a working draft, subject to future
       changes and additions
       * DONE
    2. Section 1: Add reference/link to Using JSON with NIEM paper
       * DONE
    3. Section 1.4: Add reference in to JSON-LD best practices:
      [http://json-ld.org/spec/latest/json-ld-api-best-practices](http://json-ld.org/spec/latest/json-ld-api-best-practices)
       * Highlight the Best Practices in section 4. Also say that we're not trying
         to teach JSON or JSON-LD.
       * Point 1.4 into Section 4 for JSON/LD details.
       * ALL DONE
    4. Section 1.4: Add a few sentences describing XML to JSON-LD, same RDF graph
      as in NDR
        * Scott's notes: say something about our intent: RDF from NDR should be same
          as JSON-LD guidance. May entail deltas to NDR when required.
        * ^ DONE
        * "if you follow our JSON guidance you get RDF"
        * ^ DONE
        * (Section 1.3 paragraph 1: say ~ "the NDR tells how to convert NIEM XML to
          RDF" & we're using that to generate corresponding LD.
        * ^ALREADY DONE? BY LAST SENTENCE IN PP #1
        * (last sentence of Section 1.3 paragraph 1: add ~ "JSON-LD is an RDF
          representation, and what we're doing is building RDF from the NDR using
          JSON-LD as the syntax")
        * ^ ALREADY DONE? BY LAST SENTENCE IN PP #1
    5. Section 2.5: Add text stating this applies to repeated elements with
      complex content as well as simple content
       * ok
    6. Section 2.8: Explain rationale for `rdf:value` (for case of simple
      content with no attributes), borrowing from (to be deleted) 5.5.2; present
      alternative of no `rdf:value`, explain tradeoff
       * ok
       * Make `rdf:value` as-needed, optional, and caveat emptor to the reader.
    7. Section 2.11: Add statement that there must be at least one other pair (or
      @id will be interpreted as a reference rather than an identified node as
      described in section 2.12)
       * section 4 should have an additional subsection with an example of the
         merging different things with the same IDs. the XML ID/REF mechanism
         isn't how JSON-LD thinks about IDs.
       * section 2.11 should reference that.
    8. Section 2.14: Remove comments
        * ok
    9. Section 5: Remove subsections except 5.1 and 5.4; renumber 5.4 to 5.2
       * 5.2 is covered by 2.16. delete 5.2; it's covered.
       * 5.3 is a placeholder. move to section 4 & fill it out.
       * prune 5.5, resurrect later if needed.

## NDR Changes

1. Refactor NIEM ID/REF to correspond to linked data methodology:
   1. Stand down `structures:ref`
   2. Add `structures:uri`

