# Lab Report 5: FZF 
Hello! Today, extending on Lab Report 3, I'm going to explore one of the most powerful commands that I genuinely use nearly every single day: `fzf`

FZF is a "fuzzy-finder" -- that means, like `grep` it searches through some input, and returns input that matches. However, unlike `grep`, it gives you an interactive interface where you can type in what you search and see the results update in real time. This makes it very useful for creating "launcher" type scripts, but also is a great addition to code editors like `vim` (or `emacs` if you're _that_ kind of person :D ).

`fzf` can be downloaded from it's Github page here: https://github.com/junegunn/fzf#installation

But on most Linux distributions, you should simply be able to install it using your package manager, something like `sudo pacman -S fzf` or `sudo apt install fzf`.

## Launcher Scripts
The most common way I use `fzf` is probably as a launcher script, to show me some list of files, and then interactively open one of them. Here's a simple example script I used for searching through papers I'd downloaded as part of my high school capstone project:

```bash
cd $HOME/Documents/capstone/papers
filename=$(find -name "*.pdf" | fzf)

nohup zathura "$filename" &

sleep 1
```


Note that I'm using `find` to get all of the pdf files in the `papers` folder, and then I'm using `fzf` to select one, and then opening it in `zathura`, which is a pdf viewer. `fzf`s default behavior is simply to print the selected line to standard output. Here's a recording of me using it (notice that I can both search for some text, and also navigate through the options with `Ctrl-n` and `Ctrl-p`, similar to vim).

<script async id="asciicast-XQl0kvjsc7sfXwFXSkEyKAWKq" src="https://asciinema.org/a/XQl0kvjsc7sfXwFXSkEyKAWKq.js"></script>

Unfortunately, `zathura` doesn't show up in the Asciinema recording (it just records the terminal), so you'll have to trust me that it did in fact open the PDF. But that should give you an idea of how you can use `fzf`.

I use similar scripts _all the time_ -- here's an example of a script I have called `conf`, which opens one of my dotfiles from the `config` directory. (`fd` is just a Rust version of `find` with somewhat nicer syntax).
```bash
#!/bin/bash

cd $HOME/config

name=$(fd -H -E .git -t f | fzf -q "$1")

echo $name

echo "Opening " $name

nvim $name
```

And here's an example of me using it to open up my NeoVim configuration:

<script async id="asciicast-b9bSfj1I1kIfkYQeF6bBD4a86" src="https://asciinema.org/a/b9bSfj1I1kIfkYQeF6bBD4a86.js"></script>

This also shows us the first useful `fzf` option we'll learn about! `-q`, or `--query`, according to the man page (`man fzf`), "Start[s] the finder with the given query" -- essentially, it just takes the argument (which in my `conf` script is `$1`), and put's that in the search string. This is really nice, because if I'm already looking to edit a particular file, say my NeoVim configuration, I can just type `conf vim<enter>`, and then `<enter>` again once I select the right file in `fzf`, instead of having to type `conf<enter> vim`, and then select the right file, and then press `<enter>` again, which feels a bit more awkard.


Another really useful `fzf` option is `-m` or `--multi`, which lets you select multiple options from the output at once. I don't tend to use this _as much_, but I could imagine it might be really useful for bulk-renaming files for example, or maybe just a more interactive way to select several files (to view them or delete them or anything else). Here's an example of me playing around with it in my music library (don't judge, half these songs were downloaded as a joke  :D). 

<script async id="asciicast-jBKiUgUnRRafJdP2yYaFsBoUj" src="https://asciinema.org/a/jBKiUgUnRRafJdP2yYaFsBoUj.js"></script>

Finally, a few command line arguments that are completely useless but fun: `--prompt` lets you change your prompt from a plain `>` to something funny like `blah>`. I don't know, maybe you care about such things. You can also use `--reverse` to put the search bar at the top. Again, I don't care, but maybe you'll find a use for it! Most of the other options in the `man` page are even more subtle or obscure -- it really is quite an intuitive program, the default settings usually just work. 

Here's a few more really cool and useful things you can do with it!

## Vim extension
Back to programming!

`fzf` has a great vim extension, which is sometimes _leagues_ better than anything comparable. Although we haven't discussed `vim` much as part of this class, `fzf` really is a fancy version of `find` so I'm considering it relevant! Here are the relevant lines from the `init.vim` (or `.vimrc` if you're using regular Vim instead of NeoVim)

```vim
call plug#begin($BASE.'/plugged')

Plug 'junegunn/fzf.vim'

call plug#end()

...

" FZF
nmap <Leader>f :GFiles<CR>
nmap <Leader>F :Files<CR>
nmap <Leader>b :Buffers<CR>
nmap <Leader>C :Colors<CR>
nmap <Leader>a :Rg<CR>
```

Essentially, this is just defining a bunch of shortcuts to run various `fzf` commands -- `:GFiles` lets you open any file in a Git repo, `:Files` opens any arbitrary file, `:Buffers` lets you cycle through the open buffers, `:Colors` lets you change the colorscheme, and `:Rg` uses `ripgrep` (a fancy Rust version of `grep`) to search through a large repository. Here's a demo of me using these on the PhysBAM codebase, a 300k LOC nearly entirely undocumented C++ codebase that I had the (mis)fortune of working on for some time! I would not have survived without `fzf`.

<script async id="asciicast-rIxNnM6Ae2zBrkOYke4ulOH9z" src="https://asciinema.org/a/rIxNnM6Ae2zBrkOYke4ulOH9z.js"></script>

Notice how quickly I can jump to files -- imagine how much slower it would be if I had to tab complete for every single nested folder, or if I had to run `find`, then copy the output, and then open that in `vim`, or manually click through the folders in a file explorer. Fuzzy-finding is a necessity on large codebases.

## Terminal search
Finally, you can add `fzf` to your terminal (`bash` or `zsh` should work, I expect most others will too but I haven't tested them). This is incredible and basically lets me grab commands I wrote once like 3 months ago and then forgot about.

To set it up, I just added 
```bash
source  /usr/share/fzf/shell/key-bindings.zsh
```
to my `.zshrc` file (you'd do the same with `key-bindings.bash` or `key-bindings.fish` on those shells -- note that different distros store the files in subtly different places, on my previous Arch install, they were in `/usr/share/fzf/`, while on Fedora, they're in the `shell` subdirectory).

With that, you can do amazing things like this by typing `Ctrl-r` in your terminal:
<script async id="asciicast-ZDy6crJhPVT9Vx6gB4nyeprVW" src="https://asciinema.org/a/ZDy6crJhPVT9Vx6gB4nyeprVW.js"></script>

It's incredibly handy to not have remember that one command you found a year ago that fixed the problem -- on my old laptop, whenever the time would get out of sync, I'd just `Ctrl-r` and grab the relevant `ntpdate` command, cause I could never remember the syntax for it!.

## Closing Remarks
I'm gonna be perfectly honestly, probably 95% of this stuff VSCode's Ctrl-P can do out of the box, but I'm a boomer who still uses Vim, so `fzf` is still a lifesaver for me -- hopefully you got something out of it too!

