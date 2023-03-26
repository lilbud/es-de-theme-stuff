## Intro:

This will be a work in progress guide on converting themes from the original RetroPie version of EmulationStation to the newer format used in EmulationStation Desktop Edition. Basically just converting the syntax. No major changes will be made to anything about the theme. 

These older themes can still be loaded as of 2.0, but the developer has stated plans to remove this compatibility, as keeping those legacy elements can present trouble going forward.

These converted themes will be basic and will have the normal set of functions possible back in the original version of ES. Any new features will have to be added in on your own. This guide is simply just to get these themes working in ES-DE so when legacy support is dropped, these themes aren't gone.

In this example, I’ll be using [es-theme-carbon](https://github.com/RetroPie/es-theme-carbon). The default theme for ES, created by Rookervik.

ES-DE Theme Syntax is [here](https://gitlab.com/es-de/emulationstation-de/-/blob/master/THEMES.md)

---

## Removing extra=”true”:

This tag is an option that is no longer needed. ES had a limited definition of elements that could be used, so indicating “extra” was a way around this. ES-DE removes that limitation, so any images can be used at all. Simply remove this from any element header.

---

## ForceUppercase:

An option used for any text strings. True was to force it to all be uppercase, false was to disable it, and keep the original case as is. This has been replaced with a much simpler “letterCase” option, which accepts values to change the capitalization differently. Options include: 

-   “uppercase” - same as 1 in the original theming
-   “lowercase” - forces all text to be lowercase
-   “capitalize” - capitalizes the first letter of each word
-   “none” - retains the original case of the text

To fix this, simply change any “forceUppercase” tag to “letterCase”. In addition, pay attention to the original value, and convert as needed. True changes to upper, false changes to none. Can also be changed as desired 

-   `<forceUppercase>1</forceUppercase>`
	-   Becomes: `<letterCase>uppercase</letterCase>`
-   `<forceUppercase>0</forceUppercase>`
	-   Becomes: `<letterCase>none</letterCase>`


## Alignment

Another legacy option, used in the gamelist (and other textboxes) to dictate the position of the game names in the box. This has been replaced with <horizontalAlignment> and <verticalAlignment>. Same as the letterCase option, just more control than a simple binary option. 

To fix, replace any “alignment” tag with the proper alignment. For gamelists (in 95% of cases) this gets replaced with “horizontalAlignment”. Simply change the tag in the <> brackets. 

## Video

The following options have been removed

`<showSnapshotNoVideo>true</showSnapshotNoVideo>`

`<showSnapshotDelay>false</showSnapshotDelay>`

Simply remove them as needed.

Also worth mentioning, video is supported by default now. In earlier versions of EmulationStation, when new features would be added, they might not be compatible with older versions (See FormatVersion below). So, any features outside of basic/detailed would need to be wrapped in a `<feature supported="[NAME]">` tag. No longer needed, as video was supported from the getgo in ES-DE.

## FormatVersion:

Remove as needed, was originally used when newer features got added to ES and the theme format needed to be updated. They are backwards compatible but not forwards. (EX. Grid view was version 4, wouldn’t work in version 3). They’re on version 6 or something now I think.

## Unused Elements

Before, if you were designing a theme and didn't have a need to show a particular metadata field (like publisher, or a label for rating). You still had to use it, but position it at `<pos>1 1</pos>`. Otherwise, ES would fail to load if something wasn't specified. This is not needed anymore, and can be removed from any themes.

## Views and Variants (probably the longest part so far):

These were tags to be used to determine various views like “basic” and “detailed”. These are no longer needed, being replaced by a generic “gamelist” setting. HOWEVER, removing this outright will likely break many themes, unless you convert stuff to the newer format. I have created a “compatibilities.xml” file which emulates the view switching of the original ES. This file will be in the repo with this guide. Pretty much working as follows:

	- First, checks if you’ve used the scrapers to get videos for your games. 
	Most themes have the same layout for both video and detailed views. Just replacing the “image” with a video. 

	- If no videos are found for a certain system, then it falls through to the “detailed” view. 
	Which is all the metadata, plus an image for each.

	- If no scraped media is found at all, then it falls through to the original “basic” view. 
	Which is just a gamelist showing all your games. 

In order to switch these over, any references to “basic/detailed/video” (or any combo of the three). Should be switched to “variant”. ALSO IMPORTANT, add a “view name=”gamelist”” tag underneath this header tag. And make sure to include the closing tags.

Some examples:

```
<view name="detailed">
	<image name="md_image">
		<pos>0.763 0.400</pos>
		<maxSize>0.453 0.436</maxSize>
		<origin>0.5 0.5</origin>
	</image>
</view>
```
becomes

```
<variant name="detailed">
	<view name="gamelist">
		<image name="md_image">
			<pos>0.763 0.400</pos>
			<maxSize>0.453 0.436</maxSize>
			<origin>0.5 0.5</origin>
		</image>
	</view>
</variant>
```

## Metadata and Image Types

ES-DE changes the handling of metadata. Before, it only mattered what you named your elements, and ES would know to display certain information. ES-DE on the other hand, is much more open about it. Text and Image elements can be named whatever you wish, all that is needed is either a "metadata" field (for text), and an "imageType" field (for images). This allows more flexibility, and leaving behind needing "md_image" in your theme files just to get box art. Make sure to check the theme documentation for all possible values, below I'll show an example of each.

```
<image name="md_image">
	<imageType>image</imageType> <!--ADD THIS LINE-->
	<pos>0.763 0.400</pos>
	<maxSize>0.453 0.436</maxSize>
	<origin>0.5 0.5</origin>
</image>
```
image is a catch all. And is the safest bet for theme creation. This allows ES several possible images to look for before simply displaying nothing. The documentation explains the order of importance.

```
<text name="md_genre">
	<metadata>genre</metadata> <!--ADD THIS LINE-->
	<pos>0.53 0.66</pos>
	<size>0 0.033</size>
</text>
```
This metadata line must be added to every metadata element. Otherwise, ES-DE won't know what to display, and will simply show nothing.

## Carousel and Infobar Code 

One last thing which might be new to those coming from legacy theme development. The carousel is now fully customizable! Now, when converting pretty much any ES theme, this won't present much of an issue. As it wasn't until relatively later on that carousel could be edited. Though for some themes that took advantage of it may this present an issue.

The code below should be put in the `<view name="system">` section of your theme.xml. This will recreate the carousel as it was by default in ES. Replace the `[PATH TO LOGO]` text with the path to each systems logo. Older themes had a folder for each system, so that path will be something like `./${system.theme}/art/system.svg`. This is not a guarantee, and should be kept in mind for every new theme. Take note of all the paths and recreate them as needed.

```
<carousel name="systemCarousel">
	<type>horizontal</type>
	<staticImage>[PATH TO LOGO]</staticImage>
	<imageInterpolation>linear</imageInterpolation>
	<unfocusedItemOpacity>0.5</unfocusedItemOpacity>
	<pos>0 0.383796</pos>
	<size>1 0.232407</size>
	<fontSize>0.055</fontSize>
</carousel>

<text name="gameCounter">
	<origin>0.5 0.5</origin>
	<pos>0.5 0.6437</pos>
	<size>1 0.056</size>
	<systemdata>gamecount</systemdata>
	<fontSize>0.035</fontSize>
	<horizontalAlignment>center</horizontalAlignment>
	<backgroundColor>FFFFFFBB</backgroundColor>
	<letterCase>uppercase</letterCase>
	<zIndex>51</zIndex>
</text>
```

## Closing and Other Notes

Thanks for reading. This might be updated in the future with new info as I think of it (or as its brought up to me). This should cover most of it, always look at the official documentation as it goes much more indepth on changes and whatnot.

If this guide is followed, the end result should be a legacy theme properly converted to the new ES-DE format. Some tweaking might be needed after the fact, as unknown things may pop up with the literal thousands of themes out there. But this guide should get you like 90% of the way there. 
