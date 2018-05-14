---
layout: post
title: "Using Plover, VLC, and VSCode for Transcription"
description: "A guide detailing my setup for using Plover, VLC, and VSCode to write timestamped transcriptions."
tags:
- steno
---

This past weekend, I tried transcribing for a second time. I had such a blast with my new setup using only free software that I really wanted to document it so that others could emulate it for themselves.

First, I want to talk about what I did the first time I tried transcribing, and what did and didn't work about that setup.

## ExpressScribe

The first time I tried transcribing, I used the well-recommended ExpressScribe for Mac. This is an all-inclusive application for transcribing. You open up the audio file in ExpressScribe itself. You get a global shortcut to insert the current timestamp as well as the ability to map shortcuts for skipping ahead, behind, and chaneing the playback speed. The application also has a text editor built-in, but I didn't use it because I found it pretty ugly. ExpressScribe also didn't support the HiDPI/Retina screen scaling on Mac, so it looked pretty terrible on my screen.

What's good about ExpressScribe is that it's very friendly to typists/keyboarding users, and I think that's something that my system wouldn't fix. My new solution is very tied to controlling everything with Plover, which I really enjoy as a Plover user, but a keyboard transcriptionist probably wouldn't get the same benefits.

Because I didn't use ExpressScribe's text input box, I had to substitute it. At the time, I used Sublime Text, which I trusted as my fastest text editor apart from vim. However, Sublime really struggled with the lack of line breaks. I opted to keep speakers on separate lines and just word wrap for the bulk the text. Sublime did end up chugging when using this style, unfortunately. Maybe there could be some optimizations, but at the time I was just trying to get the work done for the 24-hour deadline.

Setting up the timestamp pasting from ExpressScribe was okay. I had to add the formatting in the ExpressScribe side, not the steno side, because Plover was too fast for ExpressScribe. Say that my global shortcut to paste the timestamp was F6, then the Plover translation `{[^}{#F6}{^]}` would output `[]00:00:01` instead of the desired `[00:00:01]`. This was mitigated by just configuring that within the ExpressScribe side which, again, is better for keyboardists.

## VLC Commands

The reason I decided to reevaluate my setup is because Benoit Pierre, Plover developer extraordinare, developed [a Plover VLC commands plugin](https://pypi.org/project/plover-vlc-commands/) to control VLC over its HTTP interface. This means you can skip, pause, play, and get timestamps directly from the context of Plover translations.

### Controlling playback

The setup for VLC was as follows:

1. In VLC settings, click "Enable HTTP Interface" and set a password.
1. Open Plover 4.x, go to the Plugins Manager, find and install VLC Commands, then restart Plover.
1. Create a `vlc.json` following the example in the plugin documentation, changing "password" to the password I just set. You have to put this file next to your plover.cfg
1. Restart Plover one last time and you should be all set up to control VLC whenever it's running.

Here are the strokes that I created for playing and skipping:

- `KPH-FPG`: `{PLOVER:VLC_seek:-4s}`

    This skips the file back 4 seconds. Rather than pausing when I fall behind, I find it much better to just skip back. This keeps the momentum up and lets you review your work as you'll inevitably sometimes back up a little too far.
- `KPH*FPG`: `{PLOVER:VLC_toggle_pause}`

    I *try* not to pause, but when someone calls or I have to take an hourly break to walk around, you do need to pause and then resume when you come back. This command takes care of that.

*On foot pedals:* I do not use a foot pedal, nor do I think one is necessary for transcribing when you are using a steno machine. Strokes are so readily available at any given time that I don't think having a separate device and limb to handle backing up is necessary. Plus, if you use strokes, you can create more and more creative uses and vary up functions more easily. Foot pedals tend to just have a single action and that's a pretty big disadvantage.

### Timestamps and Speaker Strokes

You could have a dedicated stroke to paste a timestamp, but as the podcast I was transcribing didn't have any particular need for timestamps of a regular interval, I just placed one on every speaker change. For that reason, it was super convenient to integrate the speaker and the timestamp in the same stroke. Here's what I used for my two-speaker setup:

- `STPHAO`: `{^\n\n[^}{:VLC_timestamp}{^]}SPEAKER 1:{-|}`
- `EUFPLT`: `{^\n\n[^}{:VLC_timestamp}{^]}SPEAKER 2:{-|}`

Of course, real names were included instead of just placeholders. I found the bank-style speaker outlines (top row plus vowels for either side) worked really well for me. I visualized one speaker to my left and one to my right and it made it really trivial to just insert the appropriate speaker stroke whenever the voice changed. The speaker strokes also automatically insert the paragraph breaks, so I never actually had to insert a line break manual, which I found pretty satisfying.

Finally, the Plover dictionary comes with a stroke…

- `TPHAUBL`: `(inaudible)`

…which is very useful to use to mark places that you need to revisit or can't understand.

## Visual Studio Code

VSCode is a free text editor released by Microsoft. I've found it really useful for general-purpose programming, and was curious as to whether I could use it for taking transcriptions.

VSCode does have extensions for vi-style bindings, but I found that its presence interfered with Plover's rapid editing speed on Mac, unfortunately. So for the purpose of taking down text, I disabled vi bindings.

### Workspace Settings

As I use VSCode to program, I didn't want to change everything and ruin what I had going from the coding side. VSCode has a feature called workspaces where you can save a particular set of settings and enabled extensions without affecting the rest of your workspaces.

I went ahead and created a workspace and disabled pretty much every single plugin in order to get the most performance out of the editor that I could. Then, I disabled any sort of code-completing features as they are very distracting while taking transcription. In order to accomplish this, I made this Workspace Settings object, which is done in JSON:

```json
{
 "folders": [
  {
   "path": "."
  }
 ],
 "settings": {
  "files.autoSave": "afterDelay",
  "editor.wordWrap": "on",
  "editor.autoClosingBrackets": false,
  "editor.autoIndent": false,
  "editor.quickSuggestions": false,
  "editor.parameterHints": false,
  "editor.formatOnType": false,
  "editor.suggestOnTriggerCharacters": false,
  "editor.fontFamily": "Noto Sans"
 }
}
```

Of note, I also set the font family to Noto Sans, which is not a fixed-width font. Fixed-width is great for programming but when you use emdashes and Unicode ellipses, they appear collapsed and crowded. Noto Sans is a free body font that can be [downloaded from Google Fonts](https://fonts.google.com/specimen/Noto+Sans) and I enjoyed it very much for this job.

The above settings also contain autosave, which defaults to once a second after edits are made. I also stored my files in Google Drive so that I got some revisions/backups.

## The Result

Now my setup looks something like this:

{% asset transcribing_screenshot.png alt='Screenshot of VSCode with an in-progress transcript, Plover in the enabled state, and VLC playing a podcast' %}

*Note 1: VSCode has both light and dark themes, which I'd change to match the lighting in my office.*

*Note 2: I don't actually have Plover and VLC visible right next to my transcribing window. I usually just minimize them and focus fully on the transcript. This screenshot was just for demo purposes.*

I think that I will continue to use this setup for transcribing. If I make any significant changes, I'll definitely post an update here with my findings. The podcast I was transcribing and that's pictured here is [episode 1 of the Fringe Games History Podcast](http://fringe.games/episodes/ep1-kirk-israel.html) which I recommend checking out if you have any interest in programming or 8-bit gaming.

Some questions that I kind of want to answer for the feature:

- How can I integrate spell-checking into this setup (largely not necessary because I'm stenoing, but it could come up)
- How can I calculate how much time I spend working on the audio to figure out my ratio of time spent playing the file versus the actual file time?