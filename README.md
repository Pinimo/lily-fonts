Alternative Notation Fonts for GNU LilyPond
-------------------------------------------

As of version 2.19.12 the [GNU LilyPond}(http://lilypond.org) musical typesetter
can make use of alternatives to the built-in notation font *Emmentaler*. For the
current stable version 2.18.2 there is a patch available that can be applied
reasonably simple.

The functionality has been provided by [Abraham Lee](http://leighverlag.blogspot.de/)
who has also provided a comprehensive array of notation fonts of different styles.
Details about the matter as well as samples and usage instructions can be obtained from
[http://fonts.openlilylib.org](http://fonts.openlilylib.org). Regarding a simpler
to use interface please have a look at the 
[`Stylesheets`](https://github.com/openlilylib/openlilylib/tree/master/ly/stylesheets) 
library inside [`openLilyLib`](https://github.com/openlilylib/openlilylib).

`install-lily-fonts` is a Python script that makes the maintenance of your
collection of alternative fonts within any number of LilyPond installations a
mostly automatic process. It is written and maintained by Urs Liska (ul@openlilylib.org)
and released under the GPL version 3 or later. See COPYING for details.


## Concept

For the time being LilyPond expects its notation fonts in a specific folder
*within* its installation directory. In order to alleviate some issues this
causes with multiple LilyPond installations and/or updates it is advisable to
store the font files in a custom location and only create links to them inside
the LilyPond installation. While this is already an improvement keeping the
"installation" of fonts up-to-date for multiple LilyPond versions is a tedious
process.

This is where `install-lily-fonts` comes in: it maintains a local font repository with
a catalog of installed fonts and versions. From this it can detect additions and
pdates on the server, download and extract the archives and manage the links 
in the LilyPond installation. 

**Note:** So far this program only works on Linux and Mac OS because Python seems not
able to create symbolic links on Windows. Any help regarding this issue is greatly
appreciated.

## Usage

The basic invocation of `install-lily-fonts` is:

```
python install-lily-fonts [options]
```

You can run `python install-lily-fonts --help` at any time to get information on the
available command line options, which is up to date even if this README should not
have been updated.


### Main Configuration

##### `-d --font-directory`  

This option specifies the location of your local font repository, that is, a
directory where you want to keep the physical copies of the fonts. After the first
successful run of the program this directory will contain a `CATALOG` file 
containing information on the installed fonts.

The directory can be passed as an absolute or as a relative path, the latter being
interpreted relative to the current working directory from which the program is
called.

If the resulting path doesn't point to an existing directory you are prompted to choose
between creating the given directory (if you intend to create a new font repository)
or aborting the program (if you misspelled the path). 

If the directory exists but no font catalog file is present the program you are prompted
to choose between creating it (if you intend to create a new font repository) or aborting 
the program (if you misspelled the path). However, if you created the directory in the
previous step the program assumes you want to create the font catalog too.

*TODO: verify that the program actually behaves like this.*


##### `-t --targets`  

`install-lily-fonts` can handle a single or multiple LilyPond installations in one run.
So this option is used to pass one or more paths to LilyPond installations to the program.
As with the `--directory` option paths can be given as absolute or relative paths.
LilyPond installations are detected when the path points to either their root directories
or to their binary executable. If this detections fails for any reason the program exits
without doing anything.

The option accepts one or more arguments to denote one or more LilyPond installations, e.g.

```
--targets ~/lilyponds/stable ~/lilyponds/devel ~/lilyponds/current-git
```

Alternatively the argument can point to a file listing LilyPond installations, which is
useful when you have multiple installations to maintain.
A targets file lists one LilyPond path per line, empty lines or lines starting with `#`
are silently ignored. Such a file could look like this:

```
# My LilyPond installations handled by install-lily-fonts

# Current stable release (binary)
~/lilyponds/current-stable

# Current development release (binary)
~/lilyponds/current-devel

# Build from current master
~/git/lilypond/lilypond-builds/current
```

*[For your convenience there is an entry ignoring any files whose name start with `target` 
in the script directory's `.gitignore` directory, so when running this program from its
Git repository you can add targets files in that practical location without affecting the
versioning behaviour.]*

### Controlling the Behaviour

Some options can be set to control the behaviour of the program:

- `-l --local`  
If this option is passed no attempt is made to update the font catalog from the remote
server. This can be used to repair broken links or when you add a new LilyPond installation
and are reasonably sure that there are no remote updates.
- `-f --force`  
- If this option is passed the local and the remote catalogs are not compared, instead the
local versions for all fonts are taken as out of date.
- `-b --batch`  
Run the program in batch mode, without any user interaction. Basically this means
that any error condition will immediately abort the script with an error

### Operation In Detail

**TODO:** Explain the three steps download/extration/linking and when and what actually happens.
