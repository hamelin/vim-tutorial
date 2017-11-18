# Lesson 1: First steps with navigation and editing

## Starting and exiting the editor

Vim is simply started from the shell, typing `vim`, optionnally followed by
the path to the file to edit. The first thing to disconcert many newcomers is
that they don't see a menu, and the usual `Ctrl+Q` or `Ctrl+X` key shortcuts
to leave an editor don't seem to work. So, if you're lost and need to exit
Vim:

1. Type the `Esc` two or three times to make sure you're in *normal mode*
   (I'll say a few words about that in the [next section](two-modes)).
1. <a name="leave-panic"></a>Type:
   ```
   :qa!
   ```
   and then the `Return` key to swallow the command. Bam! you're out of Vim
   and back at the shell. Bullet dodged!

### <a name="two-modes"></a>The [two modes](#vim-modes) of Vim

When using Vim, you will alternate between two *modes* of usage: the *normal* mode, and the *insert* mode. The former is the one where you control what to do next:

* Navigating the file you are editing.
* Searching for stuff.
* Copying, moving and deleting text.
* Pretty much everything else.

The latter is where you will type text into the file. Way simpler!

## First steps on navigating a document

When you start Vim, or open a new file, you are in normal mode. From there, let us consider a few things we can do by typing various keys:

* `l, h`: move the cursor one character to the right or left, respectively.
* `j, k`: move the cursor one line below or above, respectively.
* `w, b`: move the cursor to the beginning of the next or previous word, respectively. Words are split by spaces, tabs and line breaks, as [usual](#word-splitting).
* `Ctrl+f, Ctrl+b`: move the cursor and scroll the screen by one "page" (as many lines as the screen can fit) down or up, respectively.
* `z.` (typed in quick sequence -- `z`, then the period): scroll the screen so that the line the cursor is on gets at the center of the screen. If you get these sore necks from craning to peer at the bottom of your screen all the time, this will save a lot on your weekly Tylenol costs (so you can buy more Skittles).

"Why of why don't you simply use the arrow keys, tutor?" asks the confounded. "They work!" Of course they do, dear confounded. However, they force me to move my right hand over there! One of the non-obvious beauties of Vim is that it was designed by a touch typist for fellow touch typists. We keyboard afficionados have been taught to position our hands on the central row, with our left and right index fingers resting respectively on `f` and `j`. Now look what's under my right hand: the basic navigation keys `h`, `j`, `k` and `l`! Vim attempts to help me never move my hands away from the central row, respecting my touch typist's desire for movement minimization. Of course it fails badly at forcing my left hand towards the `Esc` key all the damn time, but this is where I cop to my hypocrisy and overlook. I'm complex, ok!

Also notice something interesting: when you know you want to repeat a certain movement, you can just type the number of times you want to do it before the move key. In other words, if I want to go 15 lines down, you have three options:

1. Type: `jjjjjjjjjjjjjjj` -- I don't know about you, but this tires me after like, 3 `j`s.
2. Hold the `j` key just the right number milliseconds so that the cursor lands exactly 15 lines down. This is clearly the best solution, but me, I can't count milliseconds. I tried, but for consistency's sake, I need an alternative for my feeble human brain and motor skills.
3. Type: `15j`

Obviously, `6z.` will try to scroll the screen to position the cursor in the center 6 times in a row. A little pointless. But for most actions you can do in visual mode, this repeating feature can be marvelous.

## Editing basics

In order to add new text to a document, you have to enter *insert* mode. The easiest way to do so is to simply type
```
i
```
while in normal mode. You will then see `INSERT` written in the status bar at the bottom of the Vim window, and can type away all the text you want. You will see it get inserted behind the cursor. There are even poor man's navigation features while in insert mode, as you can use the arrow keys to move the cursor around by small steps. In addition to inserting text, you can delete some: use the `Backspace` and `Delete` keys to erase behind and below the cursor, respectively. To go back to normal mode, simply type `Esc`.

This poses a problem, however: how do you append text to the very end of the file? Indeed, entering insert mode by typing `i` while the cursor stands over the end of the last character of the file effectively inserts the new text before this last character, forcing you to retype, then erase this character below the cursor using the `Delete` key while in insert mode. This is stupid! So, in order to *append* text, enter insert mode using the `a` (mnemonic!) command. This will advance the cursor beyond the character it's standing on before entering insert mode. Pretty much anywhere, this is no different than typing the space bar (which advances to the character to the left, unless the cursor is at the end of a line, in which case the cursor advances to the next line), then `i`. But! at the end of the file, this starts appending text.

# Saving your work

To write the buffer being edited to a file, we have to input a *command*. Vim is controlled through both key shortcuts (which we have discussed until now) and commands. These are short strings input after typing the `:` (colon) key. This puts Vim in *command mode*. Yes, another mode already! You can type `Esc` to get back to normal mode, should you get confused with your command. Hurray for consistency!

So the command to save the current file is
```
:w
```
after which you type `Return` to get Vim to execute the command and go back to normal mode. However, this works only if you have been editing a named file all along (by starting Vim with the shell command `vim path/to/file`). To name the current file, simply append the path to save it to to the command, just so:
```
:w path/to/new/file
```

## Exiting Vim revisited

Remember how we use [command](#leave-panic) `:q!` to leave in case of panic? This command leaves without saving the current file, which can be convenient if we've erased all of it by mistake. Yes, that happens to me all the time. Damn you `:%d`, and also damn my fat fingers. An alternative to quitting without bothering to save is the `:q` command (without exclamation mark). This will not let you go unless you have saved, insisting you use `:q!` if you really mean to leave without saving. So, to leave, you may input commands `:w`, and then `:q`. Since this is common, Vim allows you to mash the two by typing instead
```
:wq
```
Since this involves typing 4 keys (colon, `w`, `q` and `Return`), there strictly has to be a shortcut. We don't have all day, Vim! That shortcut is the
```
:x
```
command. I use this one all the time, I have never bothered to learn `:wq`. That saved key is precious to me, I guess?

## Practice!

Skillful Vim practitioners try to navigate and perform their editing tasks while minimizing the number of keystrokes they perform. So start practicing by using Vim for real coding or writing work. Keep the following in mind:
* Memorize the key commands we have learned in this lesson.
* Learn to use the `:w`, `:q!` and `:x` commands.
* Try to type as little as possible to navigate. If you realize you have been stroking a certain key repeatedly, think of a way to perform the same task using less keystrokes. The repeat numbers are your friends...

## Actuallies

* <a name="vim-modes"></a>There are actually *6* modes to Vim, oy! But we can concern ourselves with only two at first. Less confusion!
* <a name="word-splitting"></a>The `iskeyword` option actually determines what characters really split words.