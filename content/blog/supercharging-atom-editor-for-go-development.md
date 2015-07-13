+++
date = "2015-07-07T15:24:59-04:00"
title = "Supercharging the Atom Editor for Go Development"
type = "post"
ogtype = "article"
+++

After years of using Integrated Development Environments (IDE) during my Windows progamming days, such as Visual Basic IDE, Borland Delphi IDE, Visual C++ and more recent Visual Studio, I have ditched all of those when I switched to Mac OS X about 10 years ago.

My initial transition in the Mac programming world was using excellent Textmate editor at the time. A fast coding editor with great highlight syntax, extension modules and code snippets that made me feel productive again. After the decline of TextMate several years ago, since the application wasn't getting software updates, a lot of people ended up switching to Sublime Text editor and the traditional VIM editor.

I have tried Atom when it first came out a while back, but it wasn't ready for prime time. When they officially launched version 1.0 a few days ago, I have decided to give it another try. And I am really happy that I did.

https://atom.io/

## Installing a Theme

You are going to spend most of your day looking at code and staring at this environment, so you should always find a theme that pleases your eyes and that have a natural color balance. I believe this is very personal, and you should find one that you like the most.

What I have been using is the combination of the One Dark UI Theme and the Monokai-Seti Syntax theme. I really enjoy the aesthetics of this duo.

![atom-theme](/img/atom-theme.png)

Here is how it looks like on my environment:

![atom-editor](/img/atom-editor.png)

#### monokai-seti
- https://atom.io/packages/monokai-seti

## Installing a Programmers Font

One of the first things I wanted to do when opening Atom the first time, was to install and use my preferred programming font. I have been using the free "Inconsolata" font for a while.

You can find and download it here: http://www.levien.com/type/myfonts/inconsolata.html

You can easily change the font being used in the editor view through the standard editor settings.

## Installing Languages

The standard packages that come with Atom cover most of your language needs. There are 2 packages that I have missed for my Go development, one is the support for Dockerfile syntax and Google Protobuf syntax, which both I use in a lot of my projects.

- language-docker: https://atom.io/packages/language-docker
- language-protobuf: https://atom.io/packages/language-protobuf

## Installing Packages

#### go-plus
- https://atom.io/packages/go-plus

This package provides almost all of Go Language support in Atom for tools, build flows, linters, vet and coverage tools. It also contains many code snippets and other features.

Make sure you have all the golang tools installed, by using the following command from your shell:

```bash
go get -u golang.org/x/tools/cmd/...
go get -u github.com/golang/lint/golint
```

There are many features in go-plus. But my favorite while developing my Go code is that I have instant feedback on my syntax and build errors. As soon as I save a file, go-plus runs in the background a miriad of tools like go vet, go oracle, go build, etc, and displays in the bottom of your editor any errors and warnings you may have. This is totally awesome and speeds up your dev cycles dramatically.

![atom-go-plus-linter](/img/atom-go-plus-linter.png)

It has also the ability to display on the editor gutter an indication of any build errors on that line, so you can easily spot which lines has an errors. Errors are displayed in red and warnings in yellowish.

![atom-go-plus-gutter-errors](/img/atom-go-plus-gutter-errors.png)

#### go-rename
- https://atom.io/packages/go-rename

This package provides intelligent variable, methods and struct safe renaming by plugging into the Go rename tool. You can easily initiate a rename refactoring dialog by pressing ```ALT-R``` when you have an identifier selected.

## Making it a little more similar to VIM

You may ask "Why not using VIM instead?". The fact is that I have for more than a year, but I wanted to try out Atom, and I am really enjoying the experience, specially since I have all the VIM commands I have been using here in the Atom Editor.

The fact is that I am a basic VIM user, and most of the benefits for me is about caret navigation, insertion, text replacements, line deletions, line jumps, etc. Once you have installed the nice ```vim-mode``` package by the Atom team, you have all that, and lot more commands that I haven't really used yet from VIM.

- vim-mode: https://atom.io/packages/vim-mode
- vim-surround: https://atom.io/packages/vim-surround
- last-cursor-position: https://atom.io/packages/last-cursor-position

##### Mapping a few commands in the Keymap File

```cson
'atom-text-editor:not(mini).autocomplete-active':
  'ctrl-p': 'core:move-up'
  'ctrl-n': 'core:move-down'

'.vim-mode.command-mode:not(.mini)':
  'ctrl-f': 'core:page-down'
  '/': 'find-and-replace:show'
```

One VIM module I miss is Easy-Motion. There was one package for Atom but is currently not compatible with Atom 1.0. I am sure somebody will update or create a version soon.

## Customizing your TreeView

#### file-icons
- https://atom.io/packages/file-icons

At first, I thought this package would make my treeview way too colorful with a bunch of different colored icons for my filetypes in the TreeView, Tabs, and Fuzzy finder dialogs. But I decided to try it out and now I think this is really cool. In fact, it does help me find, at a quick glance, the file I am looking for specially in the root of my project. There are several settings in the package to control whether to show color-less icons and/or to only colorize icons if a file has been modified.

Below you can see how your TreeView would look like once you have this packaged installed. Give it a try, I am sure you will like it.

![file-icons](/img/file-icons.png)

#### Applying some CSS modifications

The default line height for the TreeView is usually a little bit too tall, so I wanted to reduce the padding between the lines. Another modification was to use the same fixed font I use in my editor to keep the style consistent. I usually use the ```Inconsolata``` font.

```
// style the background color of the tree view
.tree-view {
    font-family: "Inconsolata";
    font-size: 12px;
}

.list-group li:not(.list-nested-item),
.list-tree li:not(.list-nested-item),
.list-group li.list-nested-item > .list-item,
.list-tree li.list-nested-item > .list-item {
    line-height:18px;
}

.list-group .selected:before,
.list-tree .selected:before {
    height:18px;
}

.list-tree.has-collapsable-children .list-nested-item > .list-tree > li,
.list-tree.has-collapsable-children .list-nested-item > .list-group > li {
    padding-left:12px;
}
```

### Code Snippets

Most modern programming editors and IDEs comes with code-completion for common keywords and code structures based on the language of choice. It usually contains most of the common ones, but it is far from complete. There lots of rooms for improvements here in the standard stock packages, specially the support for Go via the standard Atom Go langauge package and go-plus package.

But Atom allows you to create your own code snippets repository in the ```snippets.cson``` file. Open the Snippets file from the Atom menu, and you can start creating your own snippets library.

You will have to create your entries under a ```.source.go``` scope, so your snippets will only be suggested by the auto-completion feature when you are editing Go files.

Here are a few that I have added recently:

```cson
'.source.go':
    'return nil and error':
      'prefix': 'rne'
      'body': 'return nil, err'

    'return false and error':
      'prefix': 'rfe'
      'body': 'return false, err'

    'Return True and Nil':
      'prefix': 'rte'
      'body': 'return true, nil'

    'Import logrus':
      'prefix': 'logrus'
      'body': 'log "github.com/Sirupsen/logrus"'
```

Once you have defined your snippets, they immediately show up in your auto-completion suggestions. I love this feature, and I try to add common snippets of my Go code to really speed up typing.

![atom-complete-snippets](/img/atom-complete-snippets.png)


### Dash

Dash is a fantastic commercial application for Mac OS X that gives you instant offline access to 150+ API documentation sets from many languages and frameworks. I have been using it for a few years with Ruby development and now with Go.

You can find more about it here: https://kapeli.com/dash

![atom-dash](/img/atom-dash.png)

There is an Atom package that integrates your editor shortcuts via ```CTRL-H``` once you have a text selected and opens up directly in the Dash application. Pretty hand when you want to quickly jumpt to a method declaration documentation.

#### dash
https://atom.io/packages/dash


## Styling your Editor

One of the biggest advantages of Atom editor compared to other native editors, is its ability to completely customize the interface using Cascade Stylesheets (CSS). Almost all aspects of the editor can be tweaked and improved if it doesn't appeal you.

### Changing Symbol View appearance

One of the areas that really bothered me was the stock Symbols-View dialog, the lines were too tall and could barely fit many rows in the lookup window. I liked the way Sublime implemented and I decided to customize it to resembles more of what Sublime had.

```
.symbols-view {
  &.select-list ol.list-group li .primary-line,
  &.select-list ol.list-group li .secondary-line {

    font-family: Inconsolata;
    font-size: 14px;

    // let lines wrap
    text-overflow: initial;
    white-space: initial;
    overflow: initial;

    // reduce line-height
    line-height: 1.0em;
    padding-top: .1em;
    padding-bottom: .0em;

    // make sure wrapped lines get padding
    // padding-left: 21px;
    &.icon:before {
      margin-left: -21px;
    }

    .character-match {
        color: rgb(200, 200, 10) !important;
    }
  }
}

.symbols-view {
  &.select-list ol.list-group li .secondary-line {
      float: right;
      margin-top: -12px;
      padding-top: 0em;
      padding-bottom: 0em;
      font-size: 12px;
      color: rgba(200, 200, 10, 0.8) !important;
  }
}
```

The end result is a much slicker Symbols-View. When you press ```Command+R``` the symbols view appear, and you can see that line-heights are shorter and the line numbers aligned to the right of the view.

![atom-symbol-view-styles](/img/atom-symbol-view-styles.png)

### Styling Line Selection

There is an interesting package called Hightlight-Line:  
https://atom.io/packages/highlight-line

This package allows customization of the line selection styles. In my case, I have added a dashed yellow border to the bottom and top my selection. I like the way it looks and helps me determine the range of selection specially at the last line where it could be a partial selection.

![atom-highlight-selected](/img/atom-highlight-selected.png)

You can replace 'solid', with 'dashed' or 'dotted' on the CSS selector depending of what you have
set in the package settings page.

```
atom-text-editor::shadow {

    .line.highlight-line {
        background: rgba(255, 255, 255, 0.05) !important;
    }

    .line.highlight-line-multi-line-dashed-bottom {
        border-bottom-color: yellow !important;
    }

    .line.highlight-line-multi-line-dashed-top {
        border-top-color: yellow !important;
    }
}
```

## Generating Ctags for your Go code

Auto-completion is a very important feature when programming, and is usually very intrusive to the developers. So the suggestions it should make needs to be very good or it will annoy you really quick. Missing suggestions is one of the most frustating things when it comes to auto-completion functionality.

When you know a structure, interface or method name you want to use the Auto-completion so you can type fast and jump to the next work. When you are constantly not finding the items that should be there your developer happiness level suffers tremendously.

There is an awesome tool called ```gotags``` that is ctags compatible generator for Go Language. It utilizes the power of AST and Parsing classes in the Go standard library to really understand and capture all the structure, interfaces, variables and methods names. It generates a much better ctags list than the standard ctags standard tools.

- gotags: https://github.com/jstemmer/gotags

You can install it by running the following commands:

```bash
go get -u github.com/jstemmer/gotags
```

And then generate tags simply by invoking it like this from the root of your source code:

```bash
gotags -tag-relative=true -R=true -sort=true -f="tags" -fields=+l .
```

This will generate a new tags file in the root of your code, and Atom auto-completion will be much more smarter about your Go code.

![atom-go-tags](/img/atom-go-tags.png)

## Conclusion

Whether you like Sublime Text or have been a VIM fanatic, you should give Atom a shot now that they have reached version 1.0 a couple of weeks ago.

Especially since it is an open-source project backed by Github.com, there is a lot of activity and the community is growing incredibly fast.

Give it a try !!
