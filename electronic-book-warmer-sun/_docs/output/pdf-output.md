---
title: PDF output
categories: output
order: 2
---

# Print output
{:.no_toc}

* Page contents
{:toc}

## Introduction

We use [PrinceXML](http://princexml.com/) to turn Jekyll's HTML into beautiful, print-ready book files. We haven't found anything as good as Prince, so we reckon it's worth its price tag. And you can use the trial version to get your print output perfect before committing to the price. So our CSS files for print are designed specifically for Prince.

You can output print PDFs for sending to high-end printers, or screen PDFs, for digital distribution and reading on screen.

## Quick output

Run the template's `run-` output script for your operating system, and follow the prompts. For these to work, you must already have Jekyll and Prince installed and working.

* On Windows, run `run-windows.bat` by double-clicking it from your file explorer.
* On Linux, run `run-linux.sh`. You may have to run it from a terminal, and first enter `chmod +x run-linux.sh` to give it permissions, then `./run-linux.sh`.
* On Mac OSX, double-click `run-mac.command` in Finder. You may need to give the file permission to run first. To do this, in a Terminal in the same folder as the script, type `chmod +x run-mac.command`.

## Lightning Source output

We often have to create print PDFs for Lightning Source and similar print-on-demand providers. Their needs are unusual and specific. In most cases, you can get a Lightning Source-compatible PDF by turning on the Lightning Source setting in your book's `print-pdf.scss` file:

``` scss
$print-page-setup-lightning-source: true;
```

This is set off (`false`) by default. This setting:

- removes crop marks
- sets bleed to 3mm
- makes sure page elements don't bleed into the facing page's bleed area.

In addition, also in your book's `print-pdf.scss` file, make sure your PDF profile is set to PDF/X-1a:

``` scss
$pdf-profile: "PDF/X-1a:2001"
```

Also see the section on [colour profiles](../layout/colour-profiles).

## Refining print layout

You will doubtless want to refine your print layout by editing your markdown and adding custom CSS to the `print.scss` file for your book.

For instance, to save widows and orphans, you can tighten letter-spacing by adding `{:.tighten-5}` and `{:.loosen-5}` tags to the lines after paragraphs. The number in the tag refers to how many thousands of an em you're affecting letter-spacing by. For instance, `{:.tighten-10}` will tighten letter-spacing by 10/1000 em. In good typography, you should avoid tightening or loosening by more than 10/1000 wherever possible.

And to add manual hyphens to improve spacing, use discretionary hyphens (aka soft hyphens) by adding the HTML entity `&shy;` where you want the soft hyphen.

### Faster print refinement

When refining print layout (e.g. fixing widows and orphans), this happens:

1. You edit the markdown.
2. Jekyll generates new HTML.
3. You convert to PDF in Prince.
4. You open the new PDF to see your changes.

There are two main delays in this process:

1. Jekyll can take several seconds to regenerate the entire book (even with `--incremental` enabled).
2. You have to click 'Convert' and Prince must regenerate the file(s).
3. If your PDF viewer locks the file you're viewing, you have to close it before generating a new PDF in Prince.

To speed this up:

1.  Temporarily stop Jekyll generating any files you aren't working on. In `_config.yml`, add a line that excludes those files. In this example, we exclude the CSS folder and all the book's chapters, and then commented out with `#` the file we're working on, so that Jekyll does regenerate that one file:

    ~~~
    exclude: [
    css,
    book-one/0-0-cover.md, 
    book-one/0-1-half-title.md, 
    book-one/0-2-titlepage.md, 
    book-one/0-3-copyright.md,
    book-one/0-4-contents.md,
    # book-one/1.md,
    book-one/2.md,
    book-one/3.md,
    ]
    ~~~

    An `exclude` list can also be written as a list:

    ~~~
    exclude:
      - css
      - book-one/0-0-cover.md
      - book-one/0-1-half-title.md
      - book-one/0-2-titlepage.md
      - book-one/0-3-copyright.md
      - book-one/0-4-contents.md
      # - book-one/1.md
      - book-one/2.md
      - book-one/3.md
    ~~~

    And you can use wildcards. For instance, here we exclude all the prelims and the whole of `book-two`, but not the first chapter in `book-one`:

    ~~~
    exclude:
      - css
      - book-one/0*
      # - book-one/1*
      - book-one/2*
      - book-one/3*
      - book-two*
    ~~~

2.  We should now only pass Prince the file(s) we're actually generating. In the `files` list in `_data/meta.yml`, comment out the lines listing files you're not working on.
3.  If you've used Javascript not required for PDF, make sure it's wrapped in a `if site.output == ""` tag that doesn't include it in `print-pdf` output.
4.  Use a PDF Viewer that [doesn't lock the file](http://superuser.com/questions/599442/pdf-viewer-that-handles-live-updating-of-pdf-doesnt-lock-the-file). 
	*	On Windows, [Sumatra](http://www.sumatrapdfreader.org/free-pdf-reader.html) is perfect for this, unlike Acrobat and Windows Preview, which locks the file. If you edit/regenerate a PDF it has open, Sumatra will allow that to happen and will automatically reload the new file, at the same page you had open.
	*	On Mac OSX, Preview does the same, though we've found it doesn't open the regenerated file to the page you were on, sending you back to the start of the book, which is a pain. A much better alternative on OSX is [Skim](http://skim-app.sourceforge.net/), an open-source PDF reader. In its Preferences > Sync, set Skim to watch for file changes and update automatically. Also set PDFs to open to the last viewed page in Preferences > General.

> ## Technical background: The output-to-PDF process
> 
> 1.    When Jekyll runs, it saves your book's HTML files in the `_site` folder.
> 2.    The output script passes a list of HTML files into Prince. This list is generated from the `files` list in `_data.meta.yml` for each PDF format, `print-pdf` and `screen-pdf`, in the order they're listed. These should only include the files you need in the relevant PDF. For instance, you'll usually leave out `index.html`. And you'll usually only include `cover` for the screen PDF.
> 3.    Prince will use your book's print CSS file for the design, which Jekyll creates from the `print-pdf.scss` or `screenpdf.scss` files in your book's folder.
> 4.  Prince will save the PDF is creates to the `_output` folder.
> 
> If you want to learn about using CSS to control print output using Prince, [this is a great tutorial](http://www.smashingmagazine.com/2015/01/designing-for-print-with-css/).
{:.box}
