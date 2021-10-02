# svn-utilities
Some convenient Subversion (SVN) commandline utilities I've been working on.

Documentation will be coming soon.

Installation
==
Installation is very easy. Just do:
```sh
source ./install
```
and everything will be set up for you. To update, just run `./install` again.

By default, this installs in your home directory using `~/.mcfx2svn` as a home base. This can safely be changed in the install script if desired.

Updating
==
Updating is the exact same as installation. Just make sure if you changed the name in the install script, you do it again.


Usage
==

Some notes, first:
- All commands apply to the current working directory and all subdirectories unless stated otherwise. Generally speaking, you will want to run these from the top level of your SVN repository (`trunk/`).
- All commands assume that the current working directory is within a valid SVN repository, and that you have a CLI version of SVN installed. If `svn st` doesn't work, these commands won't work either.
- All commands were tested on WSL and assume Linux as a general rule. Unfortunately, the SVN Linux CLI does not correctly store credentials and [some hacks had to be used instead.](https://github.com/MCFX2/svn-utilities/issues/5) At the moment, this means that if you configured your CLI to store your credentials in plaintext, you will be prompted for your password on some operations even though you shouldn't be.
- [This script is not POSIX-compliant.](https://github.com/MCFX2/svn-utilities/issues/11) It will not work correctly without Bash.

General Utility
--
These are just some tools that I am deeply confused about SVN not having already.

```sh
svn-dump-unversioned
```
Print an easily-parsed list of every file in the current directory and subdirectories that isn't currently known to SVN. This means they are not being included in commits, and they have not been set as ignored.

```sh
svn-add-all
```
Add all unversioned files to the repository. After this, just `svn ci` to commit them. It will print every file that was added. Use this instead of `svn add * --force`, for the love of God.

```sh
svn-bisect 
```
NOT IMPLEMENTED

Ignore List Management
--
The Subversion CLI doesn't really provide good tools for managing the ignore list. The entire ignore list is all-or-nothing, and it's based on the current directory. This set of  utilities provides some functions for managing it more easily.

Commands:

```sh
svn-replace-globalignores <filename>
# example:
svn-replace-globalignores .svnignore
```
Replace the current ignore list with the contents of a file (`.svnignore` in this example). This file works exactly the same as `.gitignore`- it'll ignore anything that contains a match for the regex provided on any line. For more details, [you can read about how `.gitignore` is formatted here.](https://git-scm.com/docs/gitignore)
If successful, there will be no output.

```sh
svn-list-ignores
```
Print the current ignore list in its entirety, in an easy-to-parse format. Keep in mind this is specific to the _exact directory_ you are running this from. Ignoring something in `trunk/test` will NOT change the ignore list for `trunk/test/sub`, even though the ignore list will still apply there.

```sh
svn-ignore <pattern>
# example:
svn-ignore "garbage-directory/*.vcxproj"
```
Add a pattern to the ignore list. This can also be a filename. Keep in mind that it will ignore any file in the current directory AND subdirectories that match (but it will only appear in `svn-list-ignores` for the current directory). Generally it is recommended to ignore everything from the top level directory (typically `trunk`) and simply specify the desired subdirectory as part of the pattern.
If successful, it will print only the following:
```
property 'svn:global-ignores' set on '.'
```
You can further confirm that it worked with `svn-list-ignores`.

```sh
svn-notice <pattern>
# example:
svn-notice cool-file.txt
```
Remove something from the ignore list. _It has to match an actual item in the ignore list, not just be the name of an item that it affects._ If successful, it will print `Noticed <input>` to the console. If it doesn't print this, it didn't work. If your goal is to add one specific file that's currently ignored, then just use `svn add --no-ignore`.

Code review tools
--
UNDER CONSTRUCTION
