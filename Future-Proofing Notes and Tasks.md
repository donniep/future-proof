# Future-Proofing Notes and Tasks

### Resources
*A big thanks to all of the following:*
- [`lervag/wiki.vim`](https://github.com/lervag/wiki.vim)
- [`vim-pandoc/vim-pandoc`](https://github.com/vim-pandoc/vim-pandoc)
- [`tibabit/vim-templates`](https://github.com/tibabit/vim-templates)
- [`dyng/ctrlsf.vim`](https://github.com/dyng/ctrlsf.vim)
- [`ctrlpvim/ctrlp.vim`](https://github.com/ctrlpvim/ctrlp.vim)
- [`iamcco/markdown-preview`](https://github.com/iamcco/markdown-preview.nvim)
- [`preservim/NERDtree`](https://github.com/preservim/nerdtree)
- [Vim and Workflow](https://www.edwinwenink.xyz/series/vim-and-workflow/)
- [Building a Note-Taking System with Vanilla Vim](https://www.edwinwenink.xyz/posts/42-vim_notetaking/)
- [Taking Notes With Vim](https://github.com/vezeli/vim-gym)

### Rationale
Jerome K. Jerome said it best in *Three Men in a Boat*:

> It always does seem to me that I am doing more work than I should do.  It is not that I object to the work, mind you; I like work: it fascinates  me.  I can sit and look at it for hours.  I love to keep it by me: the idea of getting rid of it nearly breaks my heart.  You cannot give me too much work; to accumulate work has almost become a passion with me: my study is so full of it now, that there is hardly an inch of room for any more.  I shall have to throw out a wing soon.
>
> And I am careful of my work, too.  Why, some of the work that I have by me now has been in my possession for years and years, and there isn't a finger-mark on it.  I take a great pride in my work; I take it down now  and then and dust it.  No man keeps his work in a better state of preservation than I do.  But, though I crave for work, I still like to be fair.  I do not ask for more than my proper share.  But I get it without asking for it - at least, so it appears to me - and this worries me.

Which is simply to say, my name is Donnie, and I'm an addict of systems for note-taking and task management.  Not that I actually take notes or manage tasks, mind you.

My journey has crested the wave of every imaginable craze for notes and tasks, from the simplest text file to Emacs' `org-mode`, to an absurdly complex, fully-functional, `mediawiki` site, to a text file zettelkasten complete with date/time filenames down to the minute.  Migration, anyone?  Most recently, I hit upon the [Obsidian](https://obsidian.md) Electron app and have used it for about a year (as of November 2021).  Obsidian is amazing in so many ways, particularly its cross-platform functionality including iPad and also its foundation on a folder of simple, markdown text files.  Yet, it has no native command-line interface.

My '3 Steps' (as opposed to 12) for survival in this wild of systems-hopping require (1) a git repository, (2) markdown syntax, and (3) an open source, command line interface for future-proofing.  Obsidian stumbles at Step 3.  So, the point of the present exercise is to craft a life-after-Obsidian, or, at least, a command line interface that can complement Obsidian until it inevitably gets acquired, or sherlocked, or just eliminates its free tier.

Yes, alternatives abound to the particulars I explain here.  I'll not argue them, but simply state that this is working for me.  After some exploration, what I've landed on is to stay as pure-vanilla `neovim` as possible (but `vim` on iPad iSH shell), with plugins judiciously added where vim and neovim stumble.  And, did I say, IANAP?

### Ground Rules for `init.vim`
This configuration is totally opinionated toward writing markdown files.  Also, I am using `vim-pandoc`'s filetype plugin, which handles markdown.

```
set nocompatible
set encoding=utf-8
syntax on
filetype plugin on
filetype indent on
set wrap
set linebreak
set textwidth=0 wrapmargin=0
set termguicolors
highlight Comment cterm=italic
set path+=**
set wildmenu
set suffixesadd+=.md
set includeexpr=substitute(v:fname,'.html','.md','')
set autowriteall
nnoremap <expr> j v:count ? 'j' : 'gj'
nnoremap <expr> k v:count ? 'k' : 'gk'
```

### Features Necessary to Living the Dream

#### Wiki Link Completion of the `[[]]` Variety
- Natively, `C-x C-f` *in insert mode* pulls a drop-down list of files from the root directory.  So, first, you type the opening brackets and the word to search for, then, *while still in insert mode*, you do `C-x C-f`, navigate to your desired file, hit enter, and, finally, clean it up (no file extensions, please).
- There's a special function from the `CtrlP` plugin you can put in `init.vim` that will insert a filename by doing `C-p` in normal mode and then typing to search and/or `C-k` to find, then `C-x` to paste the link into the active document:  [unfortunately] on a new line and [unfortunately] with spaces escaped, because that's how vim/neovim want it.  Again, you'll have to clean this up manually.
- Or, you could just load the very nice `wiki.vim` plugin which handles completing [[Wiki Links]] *almost* completely as expected through `NCM2`, though you still have to clean up the unwanted file extensions.

#### [[Wiki Link]] Follow
- Native `neovim` to follow a [[Wiki Link]]: Use visual mode to select the text *within* the link brackets, then type `gf`.  Note `set suffixesadd+=.md`, above.
- Native `neovim` to follow a web link: with your cursor on the link, type `gx`.
- `wiki.vim` plugin: just hit enter on the link.  And, you get the benefit of a simple backspace to return from a wiki link.

#### Open Existing File/Page
- First, `:CtrlP` or `C-p`.  Then, after finding the file, `C-v` to open in it a split (instead of hitting enter) or (with special function in `init.vim`) `C-x` to paste a link.
- `:CtrlPBuffer` searches only the open buffers, as opposed to files in the root directory (similar to `:b <tab>`).
- Remember that `:CtrlP` is a fuzzy searcher - it only needs a hint about where to go.
- `wiki.vim` plugin:  use the `:WikiFzfPages` command.
- I have `NERDtree` but rarely use it - too many files.

#### Search the Entire Wiki
- For the word under the cursor in normal mode, type the command: `:CtrlSF`.
- Alternatively, type the command `:CtrlSF` followed by a search pattern.
- `CtrlSF` navigation: `C-w h` to get back to the result window and then `M` to toggle a quickfix window.
- Use `CtrlP` or `wiki.vim` plugins, as above, to search just filenames, rather than content.

#### Task Management
Obsidian has a powerful [plugin](https://github.com/schemar/obsidian-tasks) for tasks that provides a data reporting language you can insert in an `.md` file in a backticked language block that compiles to a report when you preview.  So, the final "report" is ephemeral - it lives in preview or in an exported pdf but not in your source `.md` file.  This is important, because you don't want your tasks "duplicated" by creating reports that are also `.md` files.  And, the notes-repo-as-database approach is nice, because my experience of task management has led to a completely unstructured methodology.  Tasks are littered across the entire repo of notes, identifiable only by their markdown-native, open checkboxes:  `[ ]`.  I no longer use the `TODO`/`DONE` label approach.  All of which is to say that no vim plugin I've come across actually does it "right" (i.e., *my way*).

From a native Vim/Neovim point of view, what I want is to pull all open tasks (i.e., lines with open checkboxes from any `.md` file recursively from the root) into a "quickfix" - *preferably* filterable.  The quickfix approach facilitates seeing the tasks in their context (from whichever file) and being able to mark them done as needed.

Happily, plain old `CtrlSF` handles this mostly fine - just search for `'[ ]'` (which is why you don't want them duplicated).  For my needs, this is sufficient.  For someone with more than, say, 50 active tasks, this could get unwieldy, which is where the "filterable" part comes in.  It would be nice to be able to filter the quickfix down to, say, only files with "Project" in their name, or only files in the "journal" folder, or only lines that include a date, or only lines with certain tag(s).  You get the picture.  I'm not there yet in my understanding of what quickfix and `CtrlSF` can do.  Remember, IAMAP.

Note:  `vim-pandoc` does have a mapping to toggle or insert a checkbox - `\cb`.

#### Templates for New Files
- Vim/Neovim do have a rudimentary 'skeleton' system, but do use the `vim-templates` plugin, instead.
- Create `*.template` files in your notes' root `template` directory (or, wherever, per configuration).
- When you create/open a new file, use the `:TemplateInit diary` command ("diary" as an example) to insert `diary.template`'s text and expand its placeholders.
- Alternatively, `wiki.vim` has its own template scheme which matches on filename nicely, but lacks manual selection capability.

#### Journal Summaries
To create an annual Journal Summary of daily diary entries, use this one-liner to concatenate all journals for the year into one file - if they're all in one dedicated folder.  Note the `.file` extension.  Since you're selecting `.md` files to begin with, this prevents endless looping.  (I cribbed this from I don't remember where, but it did work.)

```
ls -1 *.md | sort | while read fn ; do cat "$fn" >> 2021JournalSummary.md.file; done
```

#### Preview and Export
- There are an absurd number of alternatives for markdown preview, but I finally landed on `iamcco/markdown-preview`, despite the fact that it's a bitch to install.  It does *live* html preview in your default browser.  With `neovim` and your browser tiled to either side of the screen, you're in high cotton.
- The `vim-pandoc` plugin handles export easily, of course.  Without any configuration, the command `:Pandoc! pdf` creates a pdf file and then opens it (in Preview.app on the Mac).
- `wiki.vim` plugin:  The `:WikiExport` command can be configured in `init.vim` to do pandoc export as well, similarly opening it in Preview.app on the Mac.

#### Platforms
I currently have the approach I've described installed with `neovim` on Mac and Fedora.  On iPad's iSH shell I have to use `vim` instead.  All platforms use git pointed to a bare git repo.

#### Totally Random Vim Commands I Tend to Forget
- `:noh` Turn off the damn, incessant, search highlighting
- `:ls` Buffer list dropdown
- `:b <tab>` Buffer list dropdown
- `:CtrlPBuffer` Buffer list dropdown
- `:bd` Close buffer
- `gf` and `gx` Follow links in normal mode
- `C-x C-f` in insert mode selects a filename from root directory
- `C-w C-v` Vertical split

### Selected Plugin Configuration from `init.vim`
See [`lervag/wiki.vim`](https://github.com/lervag/wiki.vim) for its configuration.

```
" iamcco/markdown-preview.nvim (html not pdf)
let g:mkdp_auto_close = 0
let g:mkdp_command_for_global = 1
let g:mkdp_filetypes = ['markdown', 'pandoc']

" Vim-Pandoc
let g:pandoc#toc#close_after_navigating = 1
let g:pandoc#toc#position = ["left"]
let g:pandoc#spell#enabled = 0
let g:pandoc#modules#disabled = ["folding"]

" CtrlP
if executable('rg')
  let g:ctrlp_user_command = 'rg %s --files --hidden --color=never --glob ""'
endif

" CtrlP function for inserting a markdown link with Ctrl-X
function! CtrlPOpenFunc(action, line)
   if a:action =~ '^h$'    
      " Get the filename
      let filename = fnameescape(fnamemodify(a:line, ':t'))
	  let filename_wo_timestamp = fnameescape(fnamemodify(a:line, ':t:s/\d\+-//'))

      " Close CtrlP
      call ctrlp#exit()
      call ctrlp#mrufiles#add(filename)

      " Insert the markdown link to the file in the current buffer
	  let mdlink = "[[".filename_wo_timestamp."]]"
      put=mdlink
  else    
      " Use CtrlP's default file opening function
      call call('ctrlp#acceptfile', [a:action, a:line])
   endif
endfunction

let g:ctrlp_open_func = { 
         \ 'files': 'CtrlPOpenFunc',
         \ 'mru files': 'CtrlPOpenFunc' 
         \ }

" Ripgrep for Grep
if executable("rg") 
    set grepprg=rg\ --vimgrep 
endif

" CtrlSF
if executable('rg')
  let g:ctrlsf_ackprg = 'rg'
endif


let g:ctrlp_open_func = { 
         \ 'files': 'CtrlPOpenFunc',
         \ 'mru files': 'CtrlPOpenFunc' 
         \ }
```

___
Created:: 2021-11-14
Tags:: #tech #wiki #vim
Links:: [[050-Tech-MOC]]

