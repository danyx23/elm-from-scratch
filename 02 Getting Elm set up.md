Setting everything up
=====================

Before we start working on our example and dive into Elm, let's set up all the tools that help you writing Elm. One of the major benefits of statically typed languages is that a computer has a certain degree of understanding of your source code, which makes it much easier to build nice, interactive tools to give you feedback while you develop. Additionally, because Elm is still a relatively small community with a preference of one good tool instead of many mediocre ones, it is relatively quick to setup everything to get started and you don't waste many hours figuring out which boilerplate setup to use.

Elm itself
----------

The first thing you need is the elm compiler itself. Download the version for your operating system and install it (at the time of writing this, Elm version 0.17 is the current version - this is also the version all examples will use). If everything went well you should be able to open a terminal (on osx/linux) or cmd window (on windows) and enter `elm repl` and should be presented with a prompt. You can exit with Ctrl+d. If you get an error, elm was probably not added to your path correctly. If it persists after restarting, google how to add stuff to your path in your OS and add the path of the bin subdirectory of your elm installation.

An editor
---------

If you are already in love with an editor, check out what support there is for Elm. If you are flexible with your editor choice, I would recommend to start with Atom for Elm development. It has a pretty nice integration and exists on all platforms. If you want to go with Atom, here is how to proceed:

* Install the [atom editor](https://atom.io/)
* Install the [language-elm](https://atom.io/packages/language-elm) and the [linter-elm-make](https://atom.io/packages/linter-elm-make) plugin. This gives you syntax highlighting and the ability to see errors right inside the code editor.
* Download elm-oracle and put it somewhere in your PATH as described at the [elm-language readme](https://atom.io/packages/language-elm) (you can put it into your Elm bin directory where elm-make is located if you like - like with elm repl, if you can run elm-oracle from a terminal and get some output from the elm-oracle program you did it correctly). This gives you auto-suggest when you start typing the name of a function (if it is in scope).
* Download [elm-format](https://github.com/avh4/elm-format) and put it somewhere in your PATH (as above). Then install the [elm-format](https://atom.io/packages/elm-format) plugin. This gives you a nice way to format your source code in the default formatting for Elm.

Git
---

If you don't know git or mercurial yet, I really recommend installing it, reading a [short introduction](http://rogerdudler.github.io/git-guide/) of git and using it as we go along. Try to commit often, and make an effort to give good commit messages. A graphical user interface like [SourceTree](https://www.sourcetreeapp.com/) may help you understand what is happening better and lower the barrier of entry. It will be invaluable on bigger projects.

An initial project directory
----------------------------

To get started, create a new directory and then run the following to install the core Elm dependencies:
```bash
elm package install elm-lang/core
```

Obsolete content - gettings started from minimal repo
------------------------------------------------------
A minimal web application consists of a html file the provides the initial DOM scaffold and an Elm file that will be compiled by the Elm compiler to a javascript file. The html file will reference the generated javascript file so that our code can be executed and our styles be used.

I have created a small starter package for our app that you can clone like so:
```bash
git clone ...
```

Let's have a look at what it contains:
```
.gitignore
package.json
movie-search.html
MovieSearch.elm
```

The .gitignore file contains a line to tell git to ignore the elm-stuff directory that will be created when we use the elm compiler. MovieSearch.elm is empty so far and is the file we will be working on. package.json contains instruction about which packages elm should install for us. Finally, movie-search.html is a bit of HTML scaffold that references the MovieSearch.js file that we will generate shortly. Note that the elm file itself is not referenced since a browser can only work with the generated javascript file, not the Elm file itself.

Creating your first Elm file
----------------------------

To finish our setup, we now need to add some content to our Elm file and compile it. Add this code into MovieSearch.elm (using the editor you have setup for Elm):

```Elm
import Html exposing (text)

main =
  text "Hello from the MovieSearch app!"
```

Nice! Now compile it from the command line at your project directory with
```bash
elm make MovieSearch.elm -o MovieSearch.js
```

You should get an error message like this:
```
Could not find module Html
```

This tells us that we have not yet installed the necessary modules. Let's do that, using the elm package manager:

```bash
elm package install
```

And try the compile command again. Now it should work and generate a MovieSearch.js file for you. Open movie-search.html in a browser (just double click the file) and verify that you see our greeting message from above in your browser.

Excellent progress! In the next chapter we will look at functions in Elm and soon after that we will start to create a little application to search for movie actors using a web service. Onwards!
