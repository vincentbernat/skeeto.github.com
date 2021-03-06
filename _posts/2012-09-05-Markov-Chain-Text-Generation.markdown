---
title: Markov Chain Text Generation
layout: post
tags: [emacs, elisp, ai]
uuid: 3f808165-be65-3f4b-f485-8df6aacccd04
---

You may have been confused by
[yesterday's nonsense post](/blog/2012/09/04/). That's because it was
generated by a few
[Elisp Markov chain functions](https://github.com/skeeto/markov-text). It
was fed my entire blog and used to generate a ~1500 word post.  I
tidied up a bit to make sure the markup was valid and parenthesis were
balanced, but that's about it.

The algorithm is really simple and I was quite surprised by the
quality of the output. After feeding it *Great Expectations* and *A
Princess of Mars* (easily obtainable from
[Project Gutenberg](http://www.gutenberg.org/)) I had a good laugh at
some of the output. Some choice quotes,

> He wiped himself again, as if he didn't marry her by hand.

> I admit having done so, and the summer afternoon toned down into the
> house.

My favorite of yesterday's post was this one,

> Suppose you want to read a great story, I recommend it.

The output also looks like some types of spam, so this may be how some
spammers generate content in order to get around spam filters.

To build a Markov chain from input, the program looks at
`markov-text-state-size` words (default 3) and makes note of what word
follows. Then it slides the window forward one word and repeats. To
generate text, the last `markov-text-state-size` words outputted is
the state and the next word is selected from these notes at random,
weighted by the frequency of its appearance in the input text. Smaller
state sizes generates more random output and larger state sizes
generates better structured output. Too large and the output is the
input verbatim.

For example, given this sentence and a state size of *two* words,

> Quickly, he ran and he ran until he couldn't.

The produced chain looks like this in alist form,

    ((("Quickly," "he") "ran")
     (("he" "ran") "and" "until")
     (("ran" "and") "he")
     (("and" "he") "ran")
     (("ran" "until") "he")
     (("until" "he") "couldn't.")
     (("he" "couldn't.")))

[![](/img/diagram/markov-chain.png)](/img/diagram/markov-chain.gv)

Because there are two options for ("he" "ran"), the generator might
loop around that state for awhile like so,

> Quickly, he ran and he ran and he ran and he ran until he couldn't.

Or it might skip the section altogether,

> Quickly, he ran until he couldn't.

Also notice that the punctuation is part of the word. This makes the
output more natural, automatically forming sentences. More so, my
program also holds onto all newlines. This breaks the output into nice
paragraphs without any extra effort. Since I wrote it in Elisp, I use
`fill-paragraph` to properly wrap the paragraphs as I generate them,
so superfluous single newlines don't hurt anything.

One problem I did run into with my input text was quotes. I was using
novels so there is a lot of quoted text (character dialog). The
generated text tends to balance quotes poorly. My solution for the
moment is to strip these out along with spaces when forming
words. That's still not ideal.

I'm going to play with this a bit more, using it as a tool for other
project ideas (ERC bot, etc.). I already did this by including a
[*lorem ipsum*](http://en.wikipedia.org/wiki/Lorem_ipsum) generator
alongside the `markov-text` package. The input text is Cicero's *De
finibus bonorum et malorum*, the original source of *lorem
ipsum*. This was actually the original inspiration for this project,
after I saw `lorem-ipsum.el` on EmacsWiki and decided I could do
better.
