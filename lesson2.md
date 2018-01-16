# Lesson 2: editing actions and activating the time machine

[Lesson 1](lesson1.md) has taken us through the basic essentials of editing. Let's now take a look at how powerful Vim is for expressing *how you mean* to edit the text. We will also cover how to undo text editing actions, because mistakes get made. Oh yes.

But first, let us learn a few more navigation shortcuts. Cut down the keystrokes, fella!

## New navigation shortcuts

* `$` takes the cursor on the last character of the current line.
* `0` takes the cursor on column (character) 1 of the current line.
* `^` takes the cursor on the first non-blank character of the current line.
* `{` and `}` take the cursor to, respectively, the previous and the next set of blank lines. Handy for skipping paragraphs.
* `(` and `)` take the cursor to, respectively, the previous and next beginning of a sentence.

Enough brain cramming already!

## Editing actions

Entering insert mode is not the only manner of changing the contents of a file. From the normal mode, Vim enables a bevy of *editing actions* that allow complex editing tasks to be realized with a mere few keystrokes. To put things in proper context, think about how you will change text with a typical GUI-based, mouse-enabled editor.

### Cutting text to the clipboard

Imagine there is this chunk of text you want to replace with something else. You first must tell the editor which text to act on by *selecting* it. For just a few words, most people will do so by striking the arrow keys while pressing `Shift` down. For larger chunks, most people leave the keyboard with one of their hands to reach for the mouse, and drag an I-shaped cursor over the chunk to delineate it. I find this gesture so awfully slow! So annoying! If I ever have to grab the mouse while editing code, I most often leave the keyboard altogether and reach out for my mug and take a sip or two while the mouse work is being done. Breathe slowly, don't cringe, think slowly, move fast and accurate, done. Then I type Ctrl-X to get rid of it, but keep it in the clipboard. You never know, you know! So yeah, indicating which text takes too long and I find that unusable.

In Vim, I first figure out which text I mean to cut out, and *imagine how to move to the end of that text*. So if I want to cut out the current word, that *motion* would be `w`. If I want to cut the next three functions (which I always separate with at least one blank line), that motion would be `3}`. Once I have ascertained that movement, I simply type `d`, followed with the movement to cover the text to cut out. Thus, the two examples above would write down to
```
dw
```
and
```
d3}
```
respectively.

The mnemonic expressivity of this system fascinates me. Obviously, `d` stands for *delete*; what follows it is the object of the command. So, `dw` means *delete (one) word*; `d3}` means *delete three paragraphs/routines*. I am awestruck by the simple elegance of this command scheme.

One modification that this approach to interaction seems not to offer is the ability to delete full lines. Sure, one can delete the current line by typing `0dj`. However, deleting full lines is a thing we do so often, could there not be a way that foregoes moving to the beginning of the current line first? Well, yes there is! You delete the current line (and simultaneously send it to the Vim clipboard) by typing `dd`. How elegant! Full lines are targeting by typing the same key as the command, allowing for a quick sequence that's easy on the fingers. To delete 4 lines: `d4d`. You gotta love this.

### Copying text (to the clipboard)

If one means to merely copy text to the clipboard without removing it out, the shortcut is `y` (which stands for *yank*). So `y5w` copies the next 5 words to the clipboard; `y$` copies up to the end of the current line. Exactly like the cutting operation, you can copy full lines to the clipboard by repeating the operation key: so to copy the current line, type `yy`; to copy the next 10 lines, `y10y`.

### Changing text

I don't know about you, but when I cut lines off, it is typically to replace them with new text. In order to do this, you have the `c` (for *change*) operation. To replace the next 5 words, type `c5w`: Vim removes five words from the cursor (placing them in the clipboard) and enters insert mode so you can type in the new text. As usual, type `[Esc]` to get back to normal mode.

### Pasting text back

So, the past 3 operations put stuff in this putative place I keep calling the *clipboard*, but if I type `[Ctrl]+V` (or `[Command]+V` if you like fruit), whatever happens has nothing to do with the text shipped away to the clipboard. What gives man? What gives is that the Vim clipboard is a special internal place that has nothing to do with the system clipboard that other applications happily share. Vim is its own special snowflake, you know? Also, it expects to run in the shady environment of a shell, where applications share pipes and files instead of clipboards. Just like an opium den!

So, when you put text away in the clipboard, the commands to paste it back into the editor are (from normal mode) `p` and `P`. In principle, `p` pastes *after* the cursor, while `P` inserts *before* the cursor. Say you've removed 3 lines through command `d3d`. If you paste with `p`, you will see your cut-off lines appear on the line after the one on which the cursor stands; if you type `P`, the cut-off lines will be pasted so the cursor now stands on the the first of them, and the line the cursor was on has now shifted down.

To be honest, while I do use both `p` and `P`, I kind of keep getting their proper usage wrong, which is not worrisome, thanks to the [undo](#undo) feature.

### Doing multiple times vs. selecting multiple objects

So, in [lesson 1](lesson1.md), we discussed how prefixing any movement command with a number would multiply its effect -- like `5w` would advance the cursor by five words. In this lesson, I just said that verb-object commands like `d`, `y` and `c` can use an article in front of the object, in the form of a number, to select a bunch of text. Can these two idioms combine? Yes, my accounting-inclined friend, they absolutely can.

This means that you get the same effect from typing [`34dd`](#stop) and `d34d`, or `y5w` and `5yw`. Vim is clever enough to understand that `5yw` does not mean "yank the next word into the clipboard five times," which would be kind of stupid. Instead, when multiplying a verb-object command, Vim automatically *combines* the effects of repeating the command, so that Vim goes "repeating yank word is -- ah! -- yanking multiple words -- got it user!" In theory, the number before the command and the number between the command and its object have distinct semantics; in practice, the automatic combination makes them interchangeable. You can even type stuff like `5d3w` in order to cut off 15 words to the clipboard -- but let's leave that to people trying to mess with the minds of botnet masters running keyloggers on their machines. 

## <a name="undo"></a>Undo, your best friend that [never forgets](#something-else) anything you did

One and a half lesson in, we see that Vim, for all the elegance of its interaction paradigm, is a hard beast to tame. You may think it would be likely for you to let a finger slip and delete a particularly beautiful couplet to the [song you were writing](#hint), or a super clever piece of code. My friend, with Vim you can have no fear, because it has **UNLIMITED UNDO**. I know it's a bit silly to frame that as a selling point in [2017](#what-time), when all GUI editors and programs under the sun have unlimited undo and redo. However, when Vim was introduced, it 1-upped good old Vi, which has only 1-level undo. I don't now about you, but I *do* count my lucky stars never having had to code with that sort of limitation.

Anyhow, whenever you edit your file and do something you regret, type `u` to undo it. If you then regret undoing it, type `[Ctrl]+r` to redo it. The funny thing with these commands is that the multiplier prefix applies to them,  making it super easy to travel through to past versions of your text, and back to the present. I find this quite when modifying, say, a big piece of code. There is this one initialization line that has to be deleted early on in the modification work. Then I do a bunch of other things, and then realize that this initialization line bore an information that I forgot about, but need right now. How do I get it back? I type `u` several times, until that line reappears. **I DO NOT CHANGE ANYTHING** and note the piece of info I need on a scratch paper. Then I type `999[Ctrl]+r` and bam! I'm back at the top of my edit work.

You may have noticed how I insisted that I don't change anything while browsing the past contents of the file. When you think about it, redoing operations you just undid only make sense as long as these operations are applied to the exact same text as the first time. Therefore, any editing operation, including the innocuous `y`ank command, clear off the redo queue. You can move around while in the "past," but not change the text nor the clipboard, otherwise the redo queue will be lost.

This concludes lesson 2. Now practice these new tricks!

## Actuallies

* <a name="something-else"></a>Actually, the redo queue is [cleared](#redo-clear) whenever a new editing action is performed after undoing a few others. Therefore, some stuff you do can be forgotten if you go back to past "versions" of your file and start editing from there. Just like in Back to the Future!
* <a name="stop"></a>I know what you're thinking. That's crooked, reader! Stop before everybody blushes.
* <a name="hint"></a>Come on, write songs and poems using a quill on parchment, like any self-respecting poet does. Even your drug-addled guitar-on-the-back-wearing hippies do that. I think.
* <a name="what-time"/>Or 2018. Or 2019. Or 3489 -- I don't know what time it is for you, dear reader! Maybe you even brought that text on your laptop computer in your backpack in your time machine.