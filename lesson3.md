# Lesson 3: search and ~~destroy~~ replace

We know how to move around, change stuff and undo mistakes. Great! Now let's get even better at moving around, selecting and fixing things with the old white magic of search.

## Moving (up) to a character on the line

Before we get into the most serious search features, let's take a look at a nifty motion that acts as a character-level search. When, on a line, you type `f` followed by any character, Vim moves the cursor to the next occurrence of this character on the line (to the right of the cursor). If the line does not contain that character, the cursor does not move. When using Vim to edit source code, this can be extremely useful to quickly get around certain lines of code -- certainly better than typing `w` 33 times. For instance, to navigate to the next argument to a function call, `f,`[.](#ofcourse) Similarly, `F` followed by some character moves the cursor to the previous occurrence of this character on the line, unless none appear to the left of the cursor. There are also `t` and `T`, which act like `f` and `F`, but move to the character respectively before and after the one typed.

These motions are super useful for region selection when using editing actions such as `d` and `c`. Say you would like to delete everything until the next string, in a language where strings are delimited by double-quotes: type `dt"`. Change the text up to the second next open parenthesis: type `c2f(`.

And there is more! Motion `;` repeats the last `f`, `F`, `t` or `T` move. So, you want to move to some parameter within a function call, but commas are hard to count. Type: `f,`, then `;` until you get where you want to go. Similarly, motion `,` repeats the last `f`...`T` command, but going in the opposite direction... So it inverts a `f` to a `F` (and vice-versa), and `t` to `T`. Developing the reflex to use motions `f`, `;` and family greatly facilitates the navigation and edition of long lines.

## Incremental search

Before we start looking at Vim's powerful search features, let's set the `hlsearch` and `incsearch` options. Simply type the command:

```
:set hlsearch incsearch
```

and type `<Enter>` to get Vim to swallow it (much like you use the `:w` command in order to save your file). This strengthens the search feature awesomely. If you ever want to check out what it's like to search without this option, run the command

```
:set nohlsearch noincsearch
```

Let's say that you need to search for the string `writeText` in the file you have open. Start typing `/` -- this puts Vim in command mode, as if you had struck `:`. Then start typing. After `w`, the cursor moves to the next occurrence of `w`. After `r`, you get to the next occurrence of `wr`, and these characters are highlighted. By the time you have written `write`, your cursor stands on the beginning of the next occurrence of this word in your file, and the word is highlighted. You can guess that once you have typed the whole `writeText` word, the cursor will stand at the beginning, and it will be highlighted. At this stage, type `<Enter>` and Vim returns to normal mode. You are ready to work right where you need to be.

This miracle of technological design is called *incremental search*, and it is one of the many addictive drugs dispensed by Vim. Once you get used to incremental search, you can never go back. But it's free! so abuse as much as you care to. When used commonly, incremental search becomes a fast and efficient way to move about a document. In particular, when editing Python or Ruby source code, a quick type of `/def myfunction<Enter>` is all you need to move to some function. During the search process, you can delete erroneous characters, and you will navigate back, closer to the original position of the cursor; at any moment, cancelling the search with `<Esc>` will return the cursor where it first was. In addition, the search process highlights all the partial results in the text, and this highlighting persists after returning to normal mode. To turn off the highlight of search results, run command `:nohl`.

In addition to `/`, the character `?` triggers incremental search. However, in this case, the search is run backwards from the cursor, that is, towards the beginning of the file. You would also like to try out motions `n` and `N` -- these respectively move the cursor to the next and previous match of the last search. Funnily, their semantics depend on whether the last search was done using `/` or `?`: for a forward search, `n` moves towards the end of the file, and `N` moves towards the beginning of the file; for a backward search, it is the opposite. Finally, try out motions `*` and `#`: these respectively search forward and backward for the word under the cursor.

It is interesting to realize that searching using `/`, `?` is considered a motion. This means that it can be used to select a region to target with an action. For instance, to delete everything up to word `mark`, type `d/mark<Enter>`. Please remark that this fails to delete the `mark` frontier, since the end of the motion is the beginning of that word, and search motions are [exclusive](#exclusive). Should you like to include the limit word into the region as well, type instead `d/mark/e<Enter>`. The terminating `/e` position the cursor on the last character of the search result instead of on its first one, and make the motion [inclusive](#exclusive).

The cherry that tops this delicious cake is that the search string that follows `/` or `?` is read as a multiline regular expression. Yes! You can thus navigate very efficiently even when you are not sure of the exact spelling of what you are looking for. The suffix `/i` to the regular expression makes it case-insensitive, for the less sensitivity-minded among us (you know who you are -- if you don't mention it we won't either).

## Search and replace

Now, the pièce de résistance, the one command that any Vimmer worth their salt knows besides `:e` and `:w` -- the substitution command, `:s`. This guy is simple:

```
:s/re/st/fl
```

`re` denotes a regular expression that should match the strings to replace; `st` is the string to replace it with; `fl` denotes flags that affect the operation's execution.

First, a word about what the command typed as above does not show: it will only perform the search and replace *on the first match* of the regular expression, and *on the current line*. This is a rather limited scope. We wish for way more! This is because all commands take a *range* of lines that target them (except file-wide commands such as `:e` and `:w` -- how convenient!). This range is stated by two line numbers separated with a comma. When we omit the range, the command defaults to targetting only the current line, as if we had typed for range `.,.` (knowing that `.` aliases to the number of the current line). Knowing that `$` is an alias for the number of the last line of the file, we can have a search-and-replace command target the whole file by typing `:1,$s`... However, typing `1,$` takes four keystrokes -- this is way too long! For whiny typists like me, Vim provides `%` as a range alias to `1,$`. So your typical search-and-replace command will go `:%s`.

However, this does not address the failure to replace every match of the regular expression -- the command as expressed is still limited to the first match met on any line. To fix this, add the `g` flag at the end of the command. So your file-wide all-replacing search-replace command has become

```
%s/re/st/g
```

You're welcome. Two details bear going over in further details. First, you know how, more often than you'd like, you're not at all that sure about a search-replace operation? How sometimes you'd like to explicitly vet each replacement, one by one? Vim has you covered -- add flag `c` after the `g`, or the last `/`.

Second, this command supports the `\N` format for grabbing bits of the matched pattern into the replacement string. For you unfamiliar with this trope, you can see that Vim provides grouping regular expression patterns between `\(` and `\)`. When a match for the whole pattern is found, the bits of it matched between these delimiters respectively become associated to the strings `\1`, `\2`, etc. Nothing better than an example: let's say I am coding in Python and I have named a concept a `file`. I now realize than this should rather be called a `text`. Unfortunately, my code already has a bunch of methods `writeFile`, `readFile`, `newFile`, `freeFile`, and so on. So! I'm going to search-replace all these methods:

```
%s/\([a-z]+\)File/\1Text/g
```

Alakazam! Now they're `writeText`, `readText`, etc. I'll leave it as an exercise to experiment with multiple matches, as well as nested matches. Try it!

Until next time. As homework, get used to getting around quickly using the character find motions, as well as forward and backward search. The usage philosophy of Vim is to manage to navigate and edit with keystroke and cognitive economy. Search has become part of my fingers' mechanical memory, and it has been working awesomely for me. I hope you can find similar comfort.

## Actuallies

1. <a name="ofcourse"></a>Of course, this command does not parse nested function calls -- it merely zips over to the next comma instance.
2. <a name="exclusive"></a>The character where an *exclusive* motion terminates is omitted from the region targeted by the command. Reciprocally, it is made part of the target region when it is defined by an *inclusive* motion.