---
title: "Markdown cheatsheet"
sub_title: "Quick lookup table for all markdown elements supported by the so-simple theme"
tags:
  - markdown
image:
  path: http://timjames.me/img/jekyll/feature.jpg
  thumbnail: http://timjames.me/img/jekyll/feature.jpg
---

_Attribution of this content goes to the folks at so-simple: [https://github.com/mmistakes/so-simple-theme](https://github.com/mmistakes/so-simple-theme)_

Enable table of contents on post or page by adding `{%raw %}{% include toc %}{% endraw %}` where you'd like it to appear.

{% include toc %}

---

A variety of common HTML elements to demonstrate the theme's stylesheet and verify they have been styled appropriately.

# Header one

## Header two

### Header three

#### Header four

##### Header five

###### Header six

## Blockquotes

Single line blockquote:

> Stay hungry. Stay foolish.

Multi line blockquote with a cite reference:

> People think focus means saying yes to the thing you've got to focus on. But that's not what it means at all. It means saying no to the hundred other good ideas that there are. You have to pick carefully. I'm actually as proud of the things we haven't done as the things I have done. Innovation is saying no to 1,000 things.
>
> <footer><strong>Steve Jobs</strong> &mdash; Apple Worldwide Developers' Conference, 1997</footer>

Quoted text inline using `<q>` element:

<p>Luke continued, <q>And then she called him a <q>scruffy-looking nerf-herder</q>! I think I’ve got a chance!</q> The poor naive fool&hellip;</p>

## Tables

| Employee         | Salary |                                                              |
|------------------|--------|--------------------------------------------------------------|
| [John Doe](#)    | $1     | Because that's all Steve Jobs needed for a salary.           |
| [Jane Doe](#)    | $100K  | For all the blogging she does.                               |
| [Fred Bloggs](#) | $100M  | Pictures are worth a thousand words, right? So Jane × 1,000. |
| [Jane Bloggs](#) | $100B  | With hair like that?! Enough said.                           |

| Header1 | Header2 | Header3 |
|:--------|:-------:|--------:|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|-----------------------------|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|=============================|
| Foot1   | Foot2   | Foot3   |

## Definition Lists

Definition List Title
: Definition list division.

Startup
: A startup company or startup is a company or temporary organization designed to search for a repeatable and scalable business model.

#dowork
: Coined by Rob Dyrdek and his personal body guard Christopher "Big Black" Boykins, "Do Work" works as a self motivator, to motivating your friends.

Do It Live
: I'll let Bill O'Reilly [explain](https://www.youtube.com/watch?v=O_HyZ5aW76c "We'll Do It Live") this one.

## Unordered Lists (Nested)

  * List item one
      * List item one
          * List item one
          * List item two
          * List item three
          * List item four
      * List item two
      * List item three
      * List item four
  * List item two
  * List item three
  * List item four

## Ordered List (Nested)

  1. List item one
      1. List item one
          1. List item one
          2. List item two
          3. List item three
          4. List item four
      2. List item two
      3. List item three
      4. List item four
  2. List item two
  3. List item three
  4. List item four

## Buttons

Make any link standout more when applying the `.btn` class.

```html
<a href="#" class="btn--success">Success Button</a>
```

[Default Button](#){: .btn}
[Primary Button](#){: .btn .btn--primary}
[Success Button](#){: .btn .btn--success}
[Warning Button](#){: .btn .btn--warning}
[Danger Button](#){: .btn .btn--danger}
[Info Button](#){: .btn .btn--info}
[Inverse Button](#){: .btn .btn--inverse}
[Light Outline Button](#){: .btn .btn--light-outline}

```markdown
[Default Button Text](#link){: .btn}
[Primary Button Text](#link){: .btn .btn--primary}
[Success Button Text](#link){: .btn .btn--success}
[Warning Button Text](#link){: .btn .btn--warning}
[Danger Button Text](#link){: .btn .btn--danger}
[Info Button Text](#link){: .btn .btn--info}
[Inverse Button](#link){: .btn .btn--inverse}
[Light Outline Button](#link){: .btn .btn--light-outline}
```

[X-Large Button](#){: .btn .btn--primary .btn--x-large}
[Large Button](#){: .btn .btn--primary .btn--large}
[Default Button](#){: .btn .btn--primary }
[Small Button](#){: .btn .btn--primary .btn--small}

```markdown
[X-Large Button](#link){: .btn .btn--primary .btn--x-large}
[Large Button](#link){: .btn .btn--primary .btn--large}
[Default Button](#link){: .btn .btn--primary }
[Small Button](#link){: .btn .btn--primary .btn--small}
```

## Notices

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice}` class.
{: .notice}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--primary}` class.
{: .notice--primary}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--info}` class.
{: .notice--info}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--warning}` class.
{: .notice--warning}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--success}` class.
{: .notice--success}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--danger}` class.
{: .notice--danger}


## Address element

<address>
  1 Infinite Loop<br /> Cupertino, CA 95014<br /> United States
</address>

## Anchor element (aka. Link)

This is an example of a [link](http://apple.com "Apple").

## Abbreviation element

The abbreviation CSS stands for "Cascading Style Sheets".

*[CSS]: Cascading Style Sheets

## Cite element

"Code is poetry." ---<cite>Automattic</cite>

## Code element

You will learn later on in these tests that `word-wrap: break-word;` will be your best friend.

## Strike element

This element will let you <strike>strikeout text</strike>.

## Emphasize element

The emphasize element should _italicize_ text.

## Insert element

This element should denote <ins>inserted</ins> text.

## Keyboard element

This scarcely known element emulates <kbd>keyboard text</kbd>, which is usually styled like the `<code>` element.

## Preformatted element

This element styles large blocks of code.

<pre>
.post-title {
	margin: 0 0 5px;
	font-weight: bold;
	font-size: 38px;
	line-height: 1.2;
	and here's a line of some really, really, really, really long text, just to see how the PRE element handles it and to find out how it overflows;
}
</pre>

## Quote element

<q>Developers, developers, developers&#8230;</q> &#8211;Steve Ballmer

## Strong element

This element shows **bold text**.

## Subscript element

Getting our science styling on with H<sub>2</sub>O, which should push the "2" down.

## Superscript element

Still sticking with science and Isaac Newton's E = MC<sup>2</sup>, which should lift the 2 up.

## Variable element

This allows you to denote <var>variables</var>.

## Twitter embeds

---

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">At Content Marketing World, <a href="https://twitter.com/AndrewAndPete?ref_src=twsrc%5Etfw">@AndrewAndPete</a> show how to make your content stand out -- and host a weird-ass game show to prove their point. Awesome session! <a href="https://twitter.com/hashtag/CMWorld2018?src=hash&amp;ref_src=twsrc%5Etfw">#CMWorld2018</a> <a href="https://twitter.com/hashtag/CMWorld?src=hash&amp;ref_src=twsrc%5Etfw">#CMWorld</a> <a href="https://t.co/3rQahrub2r">pic.twitter.com/3rQahrub2r</a></p>&mdash; Neil MacKinnon (@neilmack99) <a href="https://twitter.com/neilmack99/status/1037745018369437701?ref_src=twsrc%5Etfw">September 6, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

---

# Syntax highlighting

## GFM Code Blocks

GitHub Flavored Markdown [fenced code blocks](https://help.github.com/articles/creating-and-highlighting-code-blocks/) are supported by default with Jekyll. You may need to update your `_config.yml` file to enable them if you're using an older version.

```yaml
kramdown:
  input: GFM
```

Yet another code snippet for demonstration purposes:

```ruby
module Jekyll
  class TagIndex < Page
    def initialize(site, base, dir, tag)
      @site = site
      @base = base
      @dir = dir
      @name = 'index.html'
      self.process(@name)
      self.read_yaml(File.join(base, '_layouts'), 'tag_index.html')
      self.data['tag'] = tag
      tag_title_prefix = site.config['tag_title_prefix'] || 'Tagged: '
      tag_title_suffix = site.config['tag_title_suffix'] || '&#8211;'
      self.data['title'] = "#{tag_title_prefix}#{tag}"
      self.data['description'] = "An archive of posts tagged #{tag}."
    end
  end
end
```

## Jekyll Highlight Liquid Tag

Jekyll also has built-in support for syntax highlighting of code snippets using either Rouge or Pygments, using a dedicated Liquid tag as follows:

```liquid
{% raw %}{% highlight scss %}
.highlight {
  margin: 0;
  padding: 1em;
  font-family: $monospace;
  font-size: $type-size-7;
  line-height: 1.8;
}
{% endhighlight %}{% endraw %}
```

And the output will look like this:

{% highlight scss %}
.highlight {
  margin: 0;
  padding: 1em;
  font-family: $monospace;
  font-size: $type-size-7;
  line-height: 1.8;
}
{% endhighlight %}

Here's an example of a code snippet using the Liquid tag and `linenos` enabled.

{% highlight html linenos %}
{% raw %}<nav class="pagination" role="navigation">
  {% if page.previous %}
    <a href="{{ site.url }}{{ page.previous.url }}" class="btn" title="{{ page.previous.title }}">Previous article</a>
  {% endif %}
  {% if page.next %}
    <a href="{{ site.url }}{{ page.next.url }}" class="btn" title="{{ page.next.title }}">Next article</a>
  {% endif %}
</nav><!-- /.pagination -->{% endraw %}
{% endhighlight %}

## Code Blocks in Lists

Indentation matters. Be sure the indent of the code block aligns with the first non-space character after the list item marker (e.g., `1.`). Usually this will mean indenting 3 spaces instead of 4.

1. Do step 1.
2. Now do this:

   ```ruby
   def print_hi(name)
     puts "Hi, #{name}"
   end
   print_hi('Tom')
   #=> prints 'Hi, Tom' to STDOUT.
   ```

3. Now you can do this.

## GitHub Gist Embed

GitHub Gist embeds can also be used:

```html
<script src="https://gist.github.com/mmistakes/77c68fbb07731a456805a7b473f47841.js"></script>
```

Which outputs as:

<script src="https://gist.github.com/mmistakes/77c68fbb07731a456805a7b473f47841.js"></script>

---

# Hero image

Add a horizontal image using the following YAML Front Matter:

```yaml
image:
  path: /images/eder-oliveira-180877.jpg
```

Hero images can also be assigned more succinctly when `thumbnail` or `caption` are not used.

```yaml
image: /images/eder-oliveira-180877.jpg
```

Tall images will push content down the page. `1600 x 600` is a good middle-ground size to aim for.

---
