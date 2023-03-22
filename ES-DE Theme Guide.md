# ES-DE Theme Creation Guide
### Good practices and tips/tricks

## Intro

This guide will serve as a dumping ground of all the theming related knowledge I either picked up over the years or have done on my own. Not saying this is stuff you *have* to do or anything, I'm a text file, not a cop. Using the various things presented in this guide will make theme creation easier (at least the coding part, I can't be creative for you). This will also *especially* make maintaining the theme a ton easier for both you and anyone else who wants to contribute.

## Folder and File Structure

Typically, you'd want to keep it simple. As it makes maintaining the theme easier. Plus it leads to a smaller filesize. 

Subjective but a good one to follow is:
```
art (or assets, or _inc, whatever)/
	logos/
		svg logos here (can have separate for system and gamelist if needed)
	fonts/
		font files here
	img/
		extra images here, ratings icons, etc.
```

Of course, this structure is up to your needs (more or less folders as needed). Only one I'd definitely recommend is a folder for logos, since 100+ of those cluttering up your assets folder will be a bit much. If you have more than 2 or 3 of something, stick it in a folder. Better for organization as well.

Also a thing of note to make things easier, when naming any system specific files like logos, use the default `system.theme` variable. These are short strings or even acronyms for system names. Which means they'll be good for filenames and they're short and don't contain any special characters.

## Variables, Variables, Variables

This is kind of an important one, as it goes a long way to making updating the theme as simple as possible. 

Pretty much up until variables were introduced to ES in the beginning of 2017, themes had a massively bloated file structure. Every system to be supported needed its own folder with its own theme.xml. Times that by the 100+ systems supported and you understand the pain of maintaining themes.

And, the more system your theme supports, the more people will want to use it. Worst thing to see will be a blank screen and missing logos. This got to be ridiculously difficult to maintain after a while. Retropie supports something like 100+ systems, have fun updating all those xml files!

Well, this isn't needed anymore. Thanks to variables, themes can now be a fraction of the file count and size. As well as not needing a whole afternoon to make significant changes to a theme. 

There are two kinds of variables to mention, ones for paths, and ones for colors and such.

A good thing to do is to create a "variables.xml" file in the root of your theme folder. Here is where you can put anything that you'll be using repeatedly, typically hex color codes. Simply wrap them in a descriptive tag (like "systemColor"), and that in a `<variables>` tag. 

To use these variables, make sure your theme.xml looks like this

```
<theme>
	<include>./variables.xml</variables>
	...
	<image name="logo">
		...
		<color>${systemColor}</color>
		...
	</image>
</theme>
```

This way, you can define color variables and the like once. And to use them, simply type the variables name like so

```
${VARIABLENAME}
```

This takes the place of whatever the variable points to. And updating colors or anything is as simple making one edit in the variables file.

I should make a note here, while you can technically define variables for paths in this file, I haven't had luck with it. No matter what, the variables get mixed around and point to the wrong path after being included. You can still define these variables, just that you have to do it in the theme.xml file like so:

```
<theme>
	<include>./variables.xml</variables>
	
	<variables>
		<systemLogo>./art/logos/${system.theme}.svg</systemLogo>
	</variables>
</theme>
```

Some variables are predefined, like `system.theme` (As well as `system.name` and `system.fullName`). These DON'T have to be specified by you. These variables go off of the information in your `es_systems.cfg`. As mentioned, `system.theme` is best for things like logos or platform-specific graphics. I'd recommend sticking with these.

## Box.png

I'd say this is one of the more important things I learned while theme making. 

On the Raspberry Pi (the main RetroPie platform), it suffered from a terminal case of not being very powerful. Having limited amounts of RAM and CPU power. So, it was important to design your themes to be lighter so that earlier/less powerful Pis wouldn't use too much VRAM. If you do this, you get a fun error called "black/white screen of death". It means your theme is simply too much theme for the Pi and you should cut it back a bit. One way is to make as much of your system use small images or vector SVG files (as these take up less memory to scale, and can scale infinitely).

Now lets talk about box.png

I won't claim credit for it, I'm not sure who came up with it first. There is a series of small images in the original Simple theme for ES (the original default theme), But none titled box. If I had to wager a guess, it was likely Rookervik (an ES theme developer) who came up with the idea. This is just speculation however.

Box.png is a 1x1px white square. I mentioned above how the best way to not kill the Pi is to use small images, well this is that taken literally. Since its just a white square, it can easily be resized to any size needed, and fit any purpose. It takes less memory to just load this image in as opposed to a 1920px wide image for your header. Less size as well. This image can be loaded into memory and used repeatedly. My flat theme uses nothing but box.png for every shape on screen. It can only be used to draw squares/rectangles however, so dedicated images will be needed for anything more complex. Like creating a layout file with all the squares where you need it.

Tip for layout files, if it is simple squares, size the file at 400x225px. This is 16:9 and will scale up nicely even as a PNG. Change these dimensions per aspect ratio.

If you have a single layout, and its simple, use box.png. For more complex layouts, use tiny premade images with everything in place, this will save you from have to call and place box.png dozens of times.

## Avoiding Repeated Code

This section will simply be on the best ways to code a theme to avoid repeating code unnecessarily.

### theme multiple views at once

This is a simple one, if you have elements shared between system and gamelist view (like backgrounds), simply define those once under a shared tag for system and gamelist. No need to call them multiple times if it isn't needed. This goes for any headers or footers or simply anything in the same place on both views. 

### theming multiple elements at once

If you have many elements of the same type, they can be combined in the same section of code. And everything specified will apply to all of those elements. 

Best thing to do this for is text, especially if you have a ton of text elements and you want them all to be the same size and font and color. That way, all that is left is to figure out position. 

### gamelist

If using the variants I described in the conversion guide, you can have different settings for different views. Like having different gamelist sizes if you have no metadata. Basic version is to include the `compatibilities.xml` file included in this repo. Then for those layouts (basic, detailed, video), specify different settings. You can combine these views and copy the repeated code into those. Like, a gamelist might have the same position and colors in each view, so put this gamelist code under a tag for `basic,detailed,video`. Then in each of those specific sections, adjust anything different needed views. 

## Creating an easily maintainable theme

Small section on what to do and not when theme making

### Variables

These can be used to load platform specific elements like logos and even colors. In your main theme.xml, when a logo needs to be loaded, simply use `PATH/${system.theme}.svg` when needed. Same with any other images needed. That way, only a single theme.xml file is needed for a whole theme. This also means adding new systems is as easy as dropping in the new logos to the proper folder. 

If you have system specific colors/text, these can be placed in their own xml files. A collection of these are provided [here](https://gitlab.com/es-de/themes/system-metadata). This is maintained by the ES-DE devs, and each file contains appropriate colors for each platform, as well as text for each system if you want to display console info on the carousel. Its only 900kb, and its easy to include in your theme.xml file, as follows:

**Theme.xml**
```
<theme>
	<include>variables.xml</include>
	<include>./art/colors/${system.theme}.xml</include>
	...
</theme>
```

These files will provide colors for every system supported, so even missing systems from themes will have something there. These files use "systemColor" as a variable, so typing this in for any color needed will automatically choose the color based on system. Alternatively, color variables can be set for 

#### Do not's
##### Have far too many files/folders

As a good tip, use the variables and such to cut down on the number of files needed for each system. As well, you really don't need a folder for each system provided you organize your files decently. All logos and assets can be organized in a central assets folder. And adding new systems is as easy as dropping the new logos in these folder and maybe adding new xml files with colors (there is a default file with standard colors for those systems missing specific colors). This goes back to avoiding repeated code, as the old way of each system needing its own XML file led to a ton of repeated code. With all 100+ of these system xml files having the same exact code. Now, all that is needed is a theme.xml with everything pointing to variables to set things on a system basis automatically. 

## Closing and Conclusion

Hopefully this provides some things to think about in making themes. I'll probably add to this at some point, but this is good for now. Also it is 2am at the time of finishing this and I need sleep


