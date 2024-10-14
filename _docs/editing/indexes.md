---
title: Indexes
categories: editing
order: 10
---

# Indexes
{:.no_toc}

* toc
{:toc}

## Introduction

A book's index – the long list of terms and page numbers at the back – is an important part of much non-fiction. Good indexes are created by professional indexers, who compile the list of entries. They do not only list words: rather, they create a taxonomy of concepts; a map of ideas in the book. For instance, a good indexer will include an entry for 'democracy' if it's discussed even if the word 'democracy' is never used. This is why good indexes cannot be generated by computers. A good index is a work of literature in itself.

Traditionally, the indexer works from the book's final layout, and notes page numbers for each concept manually. But in our workflow, where we reflow content into different formats, page numbers are never really fixed. And in epub, web and app formats they don't exist at all. So, while you might carefully manage a print PDF's pagination to allow for manual page numbers in the index, you couldn't reuse that index in another format. In the past, therefore, indexes have only been available to readers in PDF-based formats. Epub, web and app readers have had to rely on search, which is a poor substitute when you're looking for concepts rather than exact phrases.

In modern publishing, we need each page number to be a *link* to a particular point in the book. In PDF, the page numbers that appear in the index are then generated automatically. And in reflowable formats (epub, web, app) those links shouldn't even appear as page numbers, since 'pages' don't exist there. Rather, each link is simply numbered in sequence.

There are a several advantages to this approach:

- You can index early in the production process, rather than waiting for page layout to be finalised.
- The index can be useful *during the writing process*. This is especially useful for multi-author teams on large books, where contributors need to look up where concepts have already been addressed.
- When you make updates or corrections that cause text to shift, you don't need to check all the page numbers in the index.
- The index's wonderful taxonomy of concepts is now available to readers in all formats.
- When you translate a book into another language, you can translate the index at the same time, and have it work immediately.

> Note: in book and web production, the word 'index' has several meanings, in addition to meaning a back-of-the-book reference index.
> - When talking about website code, the `index` page is the starting page: the home page or the main page in a directory. So never give your book index the file name `index` (`index.md` or `index.html`). We recommend using a file name like `reference-index` for a book's index.
> - An `index` can also be a kind of database: a structured list of items for a computer to parse. For instance, this software uses Javascript indexes like this for search and for processing book-index targets and links. In these docs, we will try to use 'book index' or 'reference index' for the back-of-the-book index. And sometimes we will call the programming index a 'database', even though it's not a database in the conventional programming sense.
{:.box}

To create a dynamic index you have to complete these steps:

1. In the text, add tags containing each index entry. These are targets for the book's index to link to.
2. Create a list of book-index entries. This is the list at the back of the book, to which we'll automatically attach page numbers.
3. Run the output-script task that builds a 'database' from your tags.


## Creating the index

### How to tag indexed concepts

We use HTML comments to note where indexed concepts or phrases appear in the text. The HTML comment must start with `index:`. An HTML comment between block elements (e.g. between paragraphs, lists, or blockquotes) can contain any number of index entries that appear in the element that follows the comment. E.g.:

``` md
<!--index:
Holmes, Sherlock
Adler, Irene
-->

To Sherlock Holmes she is always THE woman. I have seldom heard him mention her under any other name.
```

In the final index, the link in web, app and epub formats, and the page number in PDF, will point to the start of that *following* element. In the above example, the links will point to the beginning of the paragraph starting 'To Sherlock…'.

This way of adding index entries in text keeps your markdown neat. The trade-off is that it's possible that, in PDF, the index could refer (i.e. link) to the page number on which the paragraph starts, while the concept you intended to index actually appears later in the paragraph on the following page. Generally speaking, this trade-off will be worth it.

Then, if you absolutely must have the page number exactly right, then you can add your HTML comment inline, like this:

``` md
To <!--index: Holmes, Sherlock --> Sherlock Holmes <!--index: Adler, Irene --> she is always THE woman. I have seldom heard him mention her under any other name.
```

If the entry in the actual index contains italics, do not add any italic markup to your `index:` comments. For example, if your index contains an italic entry for the ship *HMS Queen Elizabeth*, your index entry would still just be:

```md
<!-- index:
HMS Queen Elizabeth -->
```


### Nested entries

Complex indexes will often group, or nest, concepts. For example, the index for our Holmes story might list 'marriage' as a main entry, and then 'unhappy' and 'secret' as subentries beneath it.

You create a subentry by including its parent entry, then double backslash `\\`, then the subentry term. E.g.

``` md
<!--index:
marriage \\ unhappy
-->
```

A few very big indexes might include sub-subentries. For instance:

``` md
<!--index:
occupations \\ clergy \\ Nonconformist
-->
```


### Indexing concept ranges

Concepts listed in an index are sometimes discussed in a book over long passages, rather than in one word-specific location. For instance, in a book on French literature, several pages might be dedicated to discussing the work of Albert Camus. The index might list 'Camus, Albert 25–28'.

When you tag concepts in the book's text, and you want to reference a range like this, you need to note where the range begins and ends. Do this by adding a tilde at the end or start of your tag's entry. You'd tag the start of the Camus section like this:

``` md
<!--index:
Camus, Albert~
-->
```

And you'd tag the end of the discussion like this:

``` md
<!--index:
~Camus, Albert
-->
```

To end a range for a nested entry, the tilde goes at the start of the entire line, not the subentry term:

``` md
<!--index:
~cases \\ Scandal in Bohemia
-->
```

It's important to always close your ranges! And remember that a tag between block elements (paragraphs, blockquotes, etc.) targets the element *following* the tag. So you'd place your range-closing tag immediately *before* the last paragraph about Camus.


### Creating the index list

Then in the markdown file for the index itself, you create a list of entries, using exactly the phrases you used when tagging concepts in HTML comments. You do not need to add any links or page references. The list must have a `{:.reference-index}` class tag. E.g.:

``` md
- Adler, Irene
- Holmes, Sherlock
{:.reference-index}
```

Don't confuse the syntax of this list with that used for your tags. You do not include the hyphens used for concept-range tags. And you must include subentries as nested list items, not using double backslashes. For instance, an index with the nested entries and page ranges might look like this:

``` md
- Camus, Albert
- existentialists
  - Camus, Albert
{:.reference-index}
```


### Generating the index database

Before the page numbers and links will work in your book, you need to generate the index's 'database' file.

Use the output script and choose the 'refresh indexes' option for each output format you're publishing. The script will generate an 'index database' as a Javascript file. This is necessary for the system to populate your index list with links when you output your book. For instance, for print-PDF, this will create a file called `book-index-print-pdf.js`. In version control (e.g. Git), you should commit this generated file in the same way you do any book content.

And you should re-run the 'refresh indexes' option each time your indexing tags have changed.

When you then generate an ouput (e.g. a website or PDF), your book index will include links and/or page numbers, depending on the output format.


> ### Technical explanation
>
> To generate the dynamic 'page' references in a book index, we rely on a number of processing steps and scripts. These differ depending on the output format we're working with.
>
> 1. A person must create a list of index entries. See the 'Dynamic indexes' section of the Samples book for an example. We think of each entry as a term that references a concept in the book. Let's call them 'concept terms'.
> 2. A person must note in the book's content where each concept appears, using HTML comments that begin with `index:`. Where a single comment includes more than one concept term, each term should be on its own line. This is described in the guidance above.
> 3. Our scripts use the concept terms in the tags to generate a 'database' of concept terms and their locations. This is generated by the 'Refresh book index' option in the output script, which builds the book HTML and then uses Node and Puppeteer (launched by `render-book-index.js`) to scrape the content, looking for `index:` comments. This generates a file for each book output, which contains a Javascript object containing all the concept terms (e.g. `book-index-print-pdf.js`) as a kind of concept 'database'. This is a similar process to that used to generate a search index for web and app outputs. The 'database' (we're avoiding using the word 'index' to avoid confusion) is then available on each page within the `bundle.js` script.
> 4. Next, we need to turn the HTML comments into anchor targets that we can link to. And we need to add links (clickable 'page' references) to each concept term in the book index. This is done in two different ways, depending on the output format:
>    - For web and app output, we use client-side Javascript. The `index-targets.js` script finds all the `index:` comments on the page and creates `<a>` tags. And the `index-lists.js` script looks for `.reference-index` lists, and when it finds one, compares each item on the list with the concept terms in the `book-index-*.js` 'database'. When a concept term matches an entry in the database, the script inserts a link to the relevant anchor-tag ID after the concept term. The script also manages concept ranges ('from' and 'to') and nested entries.
>    - For PDF and epub outputs, we can't use client-side Javascript. PrinceXML doesn't support some of the Javascript required for comment handling. And in epub, internal links that are dynamically generated client-side are not clickable for security reasons. So, for these output formats, we pre-process the HTML to add the targets and links. After the output script runs Jekyll to build the HTML in `_site`, it processes each file in the book with gulp tasks. These tasks create anchor-tag targets in the book content from `index:` HTML comments, and populate the `.reference-index` with links. These tasks are logical equivalents of the client-side scripts described above for web and app outputs.
> 5. In PDF output, we use Prince functions in CSS to generate page references. These Prince functions not only create the page ranges (e.g. '23–25') but also avoid adding duplicate page references. This is because, if a concept term appears twice on the same page, there will be two links pointing to that page, but we want only one instance of that page number to show in the book index.
{:.box}


## The legacy, manual method

Before it was possible to generate index references from tags, we manually added anchor-targets and links to the content and index lists. This old process is documented here for legacy projects.


### How to tag indexed phrases

To tag a phrase in the text, we make it a link, and we give that link:

* a target that points to the index
* a class (`.indexed`) to control what it looks like
* an ID (e.g. `#democracy-1`) to uniquely identify it.

The link target should point to the index entry for that word. This way, clicking the word in the text will take you to the right place in the index. And clicking the page reference in the index will take you to the word in the text.

Here's an example of the link in the text:

~~~
Late that night, [Bob](reference-index.html#bob-1){:.indexed #bob-1} realised the key was in his pocket.
~~~

And then when Bob appears later in the book: 

~~~
Eventually, [Bob](reference-index.html#bob-2){:.indexed #bob-2} called her to confess.
~~~

Note that the ID must be unique for every instance of Bob.


### How to create the index list

Then in the index itself, you create a list of entries. After each entry, you add links to each instance of that entry you've tagged in the text. And you give each link the ID that you've pointed to in your tagged word's link target.

> Tech tip: To make it easy for us to manage, we use the same ID for the tagged word and the index entry. You don't have to us the same ID. For instance, your tagged-word's ID might be `#text-bob-1` and your index entry's link `#index-bob-1`. If like us you use the same ID in both cases, remember that your index must be in a separate file from your text, since IDs must be unique within each file.

Here is what your markdown for the index might look like. Here we've included examples of sub-entries, and the tag to use for the entire list to style it as an index: `{:.reference-index}`.

~~~
* Alice
[1](1.html#alice-1){:#alice-1}
[2](3.html#alice-2){:#alice-2}
* Bob
[1](1.html#bob-1)
[2](9.html#bob-2)
* key
[1](4.html#key-1)
    - private
    [1](4.html#key-private-1)
    - public
    [1](4.html#key-public-1)
{:.reference-index}
~~~

In this example, we've used numbers as the link text. On screen, these will stay numbers, e.g.:

> Alice 1, 2

The stylesheet will add the commas between entries (so you could globally replace with semicolons or otherwise). In print output (using PrinceXML), the stylesheet will replace those numbers with page references.