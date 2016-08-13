Setting everything up
=====================

Before we start working on our example and dive into Elm, let's set up all the tools that help you writing Elm. One of the major benefits of statically typed languages is that a computer has a certain degree of understanding of your source code, which makes it much easier to build nice, interactive tools to give you feedback while you develop. Additionally, because Elm is still a relatively small community with a preference of one good tool instead of many mediocre ones, it is relatively quick to setup everything to get started and you don't waste many hours figuring out which boilerplate setup to use.

Elm itself
----------

The first thing you need is the elm compiler itself. Download the version for your operating system and install it (at the time of writing this, Elm version 0.17 is the current version - this is also the version all examples will use). If everything went well you should be able to open a terminal (on osx/linux) or cmd window (on windows) and enter `elm repl` and should be presented with a prompt. You can exit with Ctrl+d. If you get an error, elm was probably not added to your path correctly. If it persists after restarting, google how to add stuff to your path in your OS and add the path of the bin subdirectory of your elm installation or ask for help on the Elm Slack in the beginner channel.

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

Creating your first Elm file
----------------------------

To finish our setup, we now need to add some content to our Elm file and compile it. Add this code into Main.elm (using the editor you have setup for Elm):

```elm
import Html exposing (text)

main =
  text "Hello from the MovieSearch app!"
```

Nice! There are now two ways to play with this. One is `elm reactor`, the on-the fly compilation tool of Elm. If you fire it up you can then open a browser and point it to `http://localhost:8000` and open Main.elm and see your text. Reactor has live reloading, so if you change the text in the Main.elm and save you should see it update automatically in reactor.

If you want to publish your work on a webserver though, you need some way to create a javascript file from your Elm code that you can include in your html file or include with a server side programming language. In this case, you want to use `elm make` to create a javascript file with a given name, like so:

```bash
elm make Main.elm -o MovieSearch.js
```

It should work and generate a MovieSearch.js file for you. Now, create a file called  MovieSearch.html and paste the following barebones HTML content in there that references our generated javascript file:

```html
<!DOCTYPE html>
<html>
 <head>
   <meta charset="UTF-8">
   <title>Elm movie domino</title>
 </head>
 <body>
   <div class="container">
     <div id="moviedomino"></div>
   </div>
 </body>
 <script src="MovieSearch.js"></script>
 <script>
   var node = document.getElementById('moviedomino');
   var app = Elm.MovieDomino.embed(node);
 </script>
</html>
```

Now open the MovieSearch.html file in a browser (just double click the file) and verify that you see our greeting message from above in your browser.

Excellent progress! In the next chapter we will look at functions in Elm and soon after that we will start to create a little application to search for movie actors using a web service. Onwards!
