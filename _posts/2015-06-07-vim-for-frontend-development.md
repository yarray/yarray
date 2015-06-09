---
layout: post
title:  "Vim for front-end Development"
date:   2015-06-07 23:14:22
categories: vim
---

Tools for front-end development evolve a lot these years. Besides WebStorm, which in my opinion is undoubtably no.1, we also get some decent new comers like Brackets or Atom. However I always miss vim when using these tools, so that I'd even like to trade some powers offered by IDEs. Here is a brief report concluding requirements in front-end development, and my surveys on how vim and its community can possibly handle them.


# Indenting

*Notice: The testing in this section is performed a year ago, so may be a bit outdated. Updates should be taken soon.*

The first requirement is indenting. Native indenting for javascript seems not always work. [JavaScript-Indent](https://github.com/vim-scripts/JavaScript-Indent) and [pangloss/vim-javascript](https://github.com/pangloss/vim-javascript) are two frequently mentioned options. The former is quite old. According to my test the later is more or less robuster, and it offers extra highlighting which I will mention later. 

Unfortunately, it seems neither can handle all scenarios. At last I have to use jsbeautify to compensate. After using [maksimr/vim-jsbeautify](https://github.com/maksimr/vim-jsbeautify) for a short period, I switch to [Chiel92/vim-autoformat](https://github.com/Chiel92/vim-autoformat) which is more versatile and under active development.

> Not use plugin or use [pangloss/vim-javascript](https://github.com/pangloss/vim-javascript), combined with [Chiel92/vim-autoformat](https://github.com/Chiel92/vim-autoformat) to deal with complex cases.

# Highlighting

Native highlighting of vim works, including keywords, strings, and dom related globals. Several plugins claim to provide more power. [pangloss/vim-javascript](https://github.com/pangloss/vim-javascript) can highlight functions, params, jsdoc, numbers, and can be configured to hightlight dom functions and others. If you already use it to enhance indenting then that's an extra goodness. [jelera/vim-javascript-syntax](https://github.com/jelera/vim-javascript-syntax) highlight dom functions, numbers, braces, semicolons and operators. Basically I feel this kind of  extra highlights are too minor improvements to introduce a plugin.

Highlighting variables and symbols should be much more useful. However  I haven't find working solutions. Taking advantage of tags, [easytags](https://github.com/xolox/vim-easytags) is a very good  plugin to automatically generate tags using CTags compatible tools. It also provides highlighting based on tags, which is quite handy. Unfortunately the highlighting functionality only works for some programming languages like python and c++, and doesn't include javascript. A more severe problem is that there is no perfect tag generator for javascript. CTags cannot handle modern js. Jsctags(doctorjs) is unmaintained for a long time and last time I try it has some bugs. The most promising one is [ramitos/jsctags](https://github.com/ramitos/jsctags), which is based on TernJs. However, just like CTags it cannot generate tags for scoped variables either, which makes it useless in terms of highlighting. There is an [issue](https://github.com/ramitos/jsctags/issues/3) about this.

There are two interesting new concepts for code highlighting. One is from Douglas Crockford, named "context coloring", which he represented in a [talk](https://www.youtube.com/watch?v=dkZFtimgAcM) about monad. Instead of coloring based on keywords and patterns, context coloring highlights code based on scopes, so that it's very easy to see how variables flow into inner scopes. [vim-js-context-coloring](https://github.com/bigfish/vim-js-context-coloring) is an implementation for vim. The plugin works but has several bugs, including weird behaviors when toggling and incorrect startup coloring. I find this highlighting mode colors too much code which tend to make me blind. And totally no keyword coloring is not that good for quickly grasping code structure. Normal syntax highlighting with coloring variables and symbols from outer scopes specifically seems ideal to me. But it requires some tweaking.
 
The other new concept is "semantic highlighting", introduced by Evan Brooks in his [post](https://medium.com/@evnbr/coding-in-color-3a6db2743a1e). It can be seen as an radical variation of highlighting based on tags. The key idea is not to highlight syntax, but to highlight every single variables in different colors. [semantic-highlight.vim](https://github.com/jaxbot/semantic-highlight.vim) implements this idea for vim. The result is not bad. However, this highlighting mode requires too many colors, which unavoidably results in color collision, and thus introduces false visual hints. In the original blogpost, the author proposes a brilliant idea that is to render similar variable names in similar colors. Unfortunately, this plugin doesn't yet take that, and I haven't seen this design in any implementation.

> Not use plugin, or take advantage of [pangloss/vim-javascript](https://github.com/pangloss/vim-javascript) if using it for indenting. Keep an eye on the subsequent development of new highlighting concepts.

# Navigating

For general between-file jumping, [ctrlp](https://github.com/kien/ctrlp.vim) and [NERDTree](https://github.com/scrooloose/nerdtree) are essential. [projectionist](https://github.com/tpope/vim-projectionist) seems quite promising but I haven't invest time to configure it. For in-file search, native vim is already quite decent. The plugin [easymotion](https://github.com/Lokaltog/vim-easymotion) may even enhance that. Its demo looks fantastic, offering the ability to jump to arbitrary matchings of key strokes. However in reality I rarely search a word existing everywhere on the screen, but rather prefer to use something more specific and end up giving up the plugin and stick to some native stuffs like ```2fc``` or ```/hello world```.

Semantic jumping is another challenge for javascript. The common  tag-based way, will not work due to the inability of tag generators, as I mentioned before. Fortunately, we have [TernJS](https://github.com/marijnh/tern) which provides an even much more superior solution than tags. With [tern_for_vim](https://github.com/marijnh/tern_for_vim) we can jump to definitions using ```:TernDef```, precisely, within nodejs modules, within requirejs modules. I personally map ```<c-]>``` to this so the jumping can be more intuitive, although tag stack related commands will still not work. Tern is a big player in this post and will show his muscle repetitively in subsequent sections.

> For general navigation use [ctrlp](https://github.com/kien/ctrlp.vim) and [NERDTree](https://github.com/scrooloose/nerdtree). For semantic navigation use [tern_for_vim](https://github.com/marijnh/tern_for_vim).

# Linting

[Syntactic](https://github.com/scrooloose/syntastic) is the de-factor validation plugin for vim. For javascript it supports many checkers including closure compiler, eslint, flow, gjslint (Closure Linter), jscs, jshint, jsl, jslint, jsxhint, and standard. Among them I choose jshint as basic linter, since it's fast, widely used (which means good community support), and not bothering that much like jslint. You may need a .jshintrc file under your project to make it recognizing es6 features or browserify. Basically jshint is a good validator to keep the code in good style.

What's unexpected is that here is an rather advanced tool: flow.  [Flow](https://github.com/facebook/flow) from Facebook with an ungooglable name is very impressing. To be short, it's perhaps the best type inference engine capable for plain javascript. TernJs is also good at type inference, but there are currently not validators based on it yet. The flow checker with Syntactic as a full revolution for javascript coding, is really powerful. The underlying type system is rather "Haskell" (also ML?) and even better than normal imperial languages. Even only the nullable check is sufficient for me to take it. The only not-so-good thing is that type annotations for flow is different from all other type inference engine like the closure compiler and TernJs, which both use (enhanced) JsDoc. Generally I think JsDoc is better since there's a rather complete toolchain around it. The inline comment style annotations used by Flow are also a little visually obtrusive:

{% highlight javascript linenos %}
var renderArray = function(
    data /*: [any] */ ,
    template /*: Element */
) /*: [?Element] */ {
    return data.map(function(d) {
        return render(d, template);
    });
};
{% endhighlight %}

The JsDoc equalvalence is like:

{% highlight javascript linenos %}
/**
* @param {[Object]} data 
* @param {Element} template
* @return {[?Element]}
*/
var renderArray = function(data, template) {
    return data.map(function(d) {
        return render(d, template);
    });
};
{% endhighlight %}

Flow team said in an [issue](https://github.com/facebook/flow/issues/3) that they will never adopt JsDoc style since they use different type systems. There's also an official [vim-flow](https://github.com/facebook/vim-flow) plugin which also offers on the fly type checking. However the display is not as good as Syntasitc so I disable this functionality.

Some other tools to mention:

[Closure compiler](https://developers.google.com/closure/compiler/) from Google supports type check given JsDoc style type annotations. However in my experiment I cannot produce warnings as strong as Flow. The Syntastic checker offers no opportunity to open the "checkType" option. Maybe I will do more experiments later. gjslint as linter of closure compiler doesn't contain type check. There's a promising project [tern-lint](https://github.com/angelozerr/tern-lint), which validates code based on TernJs's type inference system. Currently its functionalities are still very limited. 

> Use [Syntactic](https://github.com/scrooloose/syntastic) with jshint. The flow checker is highly recommended (need to install [Flow](https://github.com/facebook/flow)).

# Autocompleting

A.k.a intellisense. Unexpected for there are several competitors at every part. For general auto completion engine for vim, we have [YouCompleteMe](https://github.com/Valloric/YouCompleteMe) and [neocomplete.vim](https://github.com/Shougo/neocomplete.vim). The purpose of these plugins is to collect completions from multiple sources, and auto pop when needed in a user-friendly manner. Previously I use neocomplcache which is the predecessor of neocomplete, and later I switch to YouCompleteMe for its speed, native support for Ultisnips, semantic completion for c/c++ based on llvm, and for python based on jedi. As I know neocomplete also has most of the features. But it seems slower and not as easy to set up. (Yes YouCompleteMe requires compilation and it is slow. But during the compilation you can just go taking coffee, or tweak yourself to get used to the weird name. (As I know it DOES need tweaking  in Windows, but maybe it's also an option to tweak oneself to use *nix including Mac OS X)

YouCompleteMe itself is not capable to do javascipt semantic completion. But both [tern_for_vim](https://github.com/marijnh/tern_for_vim) and [vim-flow](https://github.com/facebook/vim-flow) can naturally "hook" into the completion engine and display their results. Both are really good since they can display semantic results, which is rarely seen in dynamic typed languages. Both need some type annotations to perfectly work. Since I already use flow style annotations for validation, I stick to flow for completion, otherwise JsDoc annotation with TernJs may be a better choice.

> Use [YouCompleteMe](https://github.com/Valloric/YouCompleteMe), and [vim-flow](https://github.com/facebook/vim-flow) if using Flow to lint, or [tern_for_vim](https://github.com/marijnh/tern_for_vim) otherwise. [neocomplete.vim](https://github.com/Shougo/neocomplete.vim) may be considered.

# Snippet(ing)

[Ultisnips](https://github.com/SirVer/ultisnips) with [some pre bundled snippets](https://github.com/honza/vim-snippets) is very handy. Compared to [SnipMate](https://github.com/garbas/vim-snipmate) as my earlier option, it offers YouCompleteMe and neocomplete support, has no dependencies, and under more active development.

[emmet-vim](https://github.com/mattn/emmet-vim) is emmet support in vim. Emmet is a syntax like ```div.cname>ul+ul>li*3``` which will expand to

{% highlight html linenos %}
<div class="cname">
    <ul></ul>
    <ul>
        <li></li>
        <li></li>
        <li></li>
    </ul>
</div>
{% endhighlight %}

Having Ultisnips I seldom use it, but when needed it's very handy to quickly produce a lot of boilplates.

> Use [Ultisnips](https://github.com/SirVer/ultisnips), maybe also [emmet-vim](https://github.com/mattn/emmet-vim)

# Debugging

Just not worth it. Debug with browser or switch to IDE when debugging if it really matters that much. If preprocessing is used be sure to generate sourcemaps.

> Debug with browser (with sourcemap).

# Misc

[vim-css-color](https://github.com/skammer/vim-css-color) is a very handy syntax plugin to show colors in css. Consider [ColorV](https://github.com/Rykka/colorv.vim) if you need full power to manage colors in vim. Personally I feel it’s an overkill and use [this alfred workflow](http://www.alfredforum.com/topic/805-colors%E2%80%94convert-color-formats-access-the-os-x-color-panel/) to pick & input color code (well done, alfred).

[vim-css3-syntax](https://github.com/hail2u/vim-css3-syntax) offers support for css3 keywords like flex. I personally don’t use less or sass.

> Use [vim-css-color](https://github.com/skammer/vim-css-color) to display color. Choose some external color picker tools if you like. Use [vim-css3-syntax](https://github.com/hail2u/vim-css3-syntax).

# Companions

* Git on command line. There’s [fugitive](https://github.com/tpope/vim-fugitive) for vim but I find it not that necessary for my workflow.
* Gulp on command line. It perfectly works to replace project management tools in IDE.

