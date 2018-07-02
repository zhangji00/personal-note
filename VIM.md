# vim - 编辑器之神 
1. 使用vim的原因:
      <br>在使用vim之前，其实我一直是使用IDE的(原先是做嵌入式用的keil也用过VS, sbulime, eclipse, Intellij总之是个图形界面党)。为什么用vim呢？主要还是和工作有关，因工作关系转向了linux平台, 第一天老板就让我学习elixir, erlang 和 vim, 对于一个嵌入式c来说是懵逼的。一开始用vim内心还无比抗拒，在我也中它只是个编辑器，即便号称编辑器之神，它也是用来码代码的，干什么要费尽心思记辣么多命令只为用一个文本编辑器？直到现在我也不会用它来写c#开发图形界面, 为什么？因为术业有专攻，vim的强大指代的是强大的命令编辑和脱离鼠标的快捷操作，但是我不会认为做界面应该脱离鼠标（拖拽控件这么好用的功能当然不能丢咯）

2. 使用vim的好处:
   <br>对于一个windows使用者来说初次接触linux的命令行, 早已习惯鼠标点点点，突然要在一个黑框框中敲一堆不明其意的英文字母,可以想象是怎样的一种酸爽。但是，熬过了初期的煎熬才能发现拨云见日般的明朗。关键是培养使用命令行和脚本的习惯和思考方式。我在学习使用vim的痛苦挣扎中是收获颇丰的，譬如有些vim中的 操作是可以在命令行操作中继续使用的，比方说"/"这个命令在vim中是搜索，使用"/关键词"可以在当前编辑的文档中搜索关键词，其实这个"/"命令是可以在类Unix系统中的其他软件中使用的比如说less、more、man这些指令,在打开的文本中我们仍然可以使用"/"来搜索。一个简单的操作就是当不记得某些命令的某个参数时，你可能不愿意阅读man帮助文档的整个描述， 不过我们可以使用"/"来搜索关键字实现快速跳转，阅读自己需要的那部分。包括跳转指令gg和shift-g迅速跳转到文档的开头结尾同样可以用。所以有些命令记一遍就不单单可以在vim中使用了，不会增加记忆负担，相反的当熟练到一定程度反而加快工作效率。当脱离了鼠标后你的手只要在键盘上的一小片区域 就可以完成所有的文档编辑任务，相当的节约时间。想想那些黑客敲键盘时的英姿飒爽，是不是你也跃跃欲试！

3. 废话多了还是介绍配置吧， 毕竟空白的vim总还是不尽如人意的
   <br>vim的插件管理器vundle，一条命令搞定安装
      
      <br>$ git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

   <br>有了插件管理器，当然还要有插件
      <br>(1). tagbar: 显示当前文件中标签
      <br>(2). NERDTREE: 文件浏览器
      <br>(3). Powerline: 状态栏
      <br>(4). Conque-shell: vim shell
      等等你可以个性化定制的插件，以下是我目前的配置文件
   ```
      set nocompatible              " be iMproved, required
      filetype off                  " required
      
      " set the runtime path to include Vundle and initialize
      set rtp+=~/.vim/bundle/Vundle.vim
      call vundle#begin()
      " alternatively, pass a path where Vundle should install plugins
      " call vundle#begin('~/some/path/here')
      "
      " let Vundle manage Vundle, required
      " 一下就是要安装的插件，可以在github中搜索vim 插件能找到很多有用的差价，可自行尝试
      Plugin 'VundleVim/Vundle.vim'
      "Plugin 'thinca/vim-ref'
      Plugin 'elixir-lang/vim-elixir'
      Plugin 'slashmili/alchemist.vim'
      "Plugin 'mattreduce/vim-mix'
      Plugin 'scrooloose/nerdtree'
      Plugin 'majutsushi/tagbar'
      Plugin 'Lokaltog/vim-powerline'
      Plugin 'nicoraffo/conque'
      "Plugin 'awetzel/elixir.nvim'
      "
      " The following are examples of different formats supported.
      " Keep Plugin commands between vundle#begin/end.
      " kaltog/vim-powerline
      "
      " plugin on GitHub repo
      """""""Plugin 'tpope/vim-fugitive'
      " plugin from http://vim-scripts.org/vim/scripts.html
      "Plugin 'L9'
      " Git plugin not hosted on GitHub
      """"""Plugin 'git://git.wincent.com/command-t.git'
      " git repos on your local machine (i.e. when working on your own plugin)
      "Plugin 'file:///home/gmarik/path/to/plugin'
      " The sparkup vim script is in a subdirectory of this repo called vim.
      " Pass the path to set the runtimepath properly.
      """"""Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
      " Install L9 and avoid a Naming conflict if you've already installed a
      " different version somewhere else.
      """"""Plugin 'ascenator/L9', {'name': 'newL9'}
      "
      " All of your Plugins must be added before the following line
      call vundle#end()            " required
      filetype plugin indent on    " required
      " To ignore plugin indent changes, instead use:
      " filetype plugin on
      "
      " Brief help
      " :PluginList       - lists configured plugins
      " :PluginInstall    - installs plugins; append `!` to update or just
      " :PluginUpdate
      " :PluginSearch foo - searches for foo; append `!` to refresh local cache
      " :PluginClean      - confirms removal of unused plugins; append `!` to
      " auto-approve removal
      " 
      " see :h vundle for more details or wiki for FAQ
      " Put your non-Plugin stuff after this line
      let mapleader=";"
      "set viminfo='1000,f1,<500
      syntax enable 
      syntax on
      set number 
      set nowrap
      set pastetoggle=<F12>
      "let g:netrw_winsize = 30
      nmap <leader>e :Explore!<cr>
      nmap <leader>f :NERDTreeToggle<cr>
      "let g:alchemist_tag_disable = 1
      let g:alchemist_tag_map = '<C-]]>'
      let g:alchemist_tag_stack_map = '<C-TT>'
      "set tags = "/root/fifa2/tags"
      "下一行是为了让tagbar能显示elixir的模块函数
      let g:tagbar_type_elixir = {'ctagstype': 'elixir', 'kinds':['f:functions:0:0', 'c:callbacks:0:0', 'd:delegates:0:0', 'e:exceptions:0:0','i:implementations:0:0', 'a:macros:0:0', 'o:operators:0:0', 'm:modules:0:0','p:protocols:0:0', 'r:records:0:0'], 'sro': '.', 'kind2scope': {'m':'modules'}, 'scope2kind': {'modules': 'm'}}
      "let g:tagbar_winsize = 1
      nmap <leader>t :TagbarToggle<cr>
      "nmap <leader><leader>t :TagbarClose<cr>
      hi Cursorline term=reverse ctermbg=242 guibg=DarkGrey cterm=NONE "这样可以使得cursorline的下滑线去掉
      hi Cursorcolumn term=reverse ctermbg=242 guibg=DarkGrey cterm=NONE
      nmap <Leader>c :set cursorline! cursorcolumn! <cr>
      nmap <Leader>sh :ConqueTermSplit bash<CR>  "用<Leader>sh作为快捷键打开Conque shell
      nmap - :resize -1<CR>  "这样就可以用加减键控制窗口的大小了
      nmap = :resize +1<CR>
      "set nocompatible   " Disable vi-compatibility
      set laststatus=2   " Always show the statusline
      set encoding=utf-8 " Necessary to show Unicode glyphs
      let g:Powerline_symbols = 'unicode' "这里如果用fancy会出现乱码
   ```   
   保存以上配置文件到根目录的.vimrc文件。打开vim，命令行运行:PluginInstall等待安装，完成后基本就能看到一个漂亮的vim了enjoy your journey!
   ![这里写图片描述](https://img-blog.csdn.net/20180611142212157?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTEwMzEyNTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

   <br>关于elixir使用ctags需要添加一下内容
   ```
      --langdef=Elixir
      --langmap=Elixir:.ex.exs
      --regex-Elixir=/^[ \t]*def(p?)[ \t]+([a-z_][a-zA-Z0-9_?!]*)/\2/f,functions,functions (def ...)/
      --regex-Elixir=/^[ \t]*defcallback[ \t]+([a-z_][a-zA-Z0-9_?!]*)/\1/c,callbacks,callbacks (defcallback ...)/
      --regex-Elixir=/^[ \t]*defdelegate[ \t]+([a-z_][a-zA-Z0-9_?!]*)/\1/d,delegates,delegates (defdelegate ...)/
      --regex-Elixir=/^[ \t]*defexception[ \t]+([A-Z][a-zA-Z0-9_]*\.)*([A-Z][a-zA-Z0-9_?!]*)/\2/e,exceptions,exceptions (defexception ...)/
      --regex-Elixir=/^[ \t]*defimpl[ \t]+([A-Z][a-zA-Z0-9_]*\.)*([A-Z][a-zA-Z0-9_?!]*)/\2/i,implementations,implementations (defimpl ...)/
      --regex-Elixir=/^[ \t]*defmacro(p?)[ \t]+([a-z_][a-zA-Z0-9_?!]*)\(/\2/a,macros,macros (defmacro ...)/
      --regex-Elixir=/^[ \t]*defmacro(p?)[ \t]+([a-zA-Z0-9_?!]+)?[ \t]+([^ \tA-Za-z0-9_]+)[ \t]*[a-zA-Z0-9_!?!]/\3/o,operators,operators (e.g. "defmacro a <<< b")/
      --regex-Elixir=/^[ \t]*defmodule[ \t]+([A-Z][a-zA-Z0-9_]*\.)*([A-Z][a-zA-Z0-9_?!]*)/\2/m,modules,modules (defmodule ...)/
      --regex-Elixir=/^[ \t]*defprotocol[ \t]+([A-Z][a-zA-Z0-9_]*\.)*([A-Z][a-zA-Z0-9_?!]*)/\2/p,protocols,protocols (defprotocol...)/
      --regex-Elixir=/^[ \t]*Record\.defrecord[ \t]+:([a-zA-Z0-9_]+)/\1/r,records,records (defrecord...)/
      --regex-Elixir=/^[ \t]*test[ \t]+\"([a-z_][a-zA-Z0-9_?! ]*)\"*/\1/t,tests,tests (test ...)/
   ```

    到根目录下的.ctags文件中，这样就可以使用ctags -R *， 来生成tags文件以方便跳转定义
    
    
4. 使用vim看代码时， ctags是不可或缺的辅助工具， 有了ctags就可以在函数、宏定义等的声明之间跳转了，vim中所说的ctags指定的是exuberant ctags可以使用ctags --version来查看版本

    ```
       Exuberant Ctags  5.8， Copyright (C) 1996-2009 Darren Hiebert
       Compiled: Sep 16 2017， 13:24:32
       Addresses: <dhiebert@users.sourceforge.net>， http://ctags.sourceforge.net
       Optional compiled features: +wildcards， +regex
    ```
   <br>得到如上输出基本就没有问题了，如果提示illegal option 那基本上可以确定不是Exuberant Ctags比如说mac 版的ctags就是这样，此时可以使用brew install ctags-exuberant 来进行安装(linux 使用apt-get install exuberant-ctags)，安装完成后可以使用which -a ctags，得到如下信息

    ```
       /usr/local/bin/ctags
       /usr/bin/ctags
    ```

   <br>/usr/bin/ctags 是系统自带的， /usr/local/bin/ctags是安装完成的Exuberant Ctags, /usr/local/bin/ctags --version 可以验证,此后便可以使用/usr/local/bin/ctags -R * 来生成tags文件， 使用ctrl-]跳转定义, 使用ctrl-t返回
     
    ```
       Ctrl-]  Find the tag under the cursor.
       Ctrl-T  Return to previous location before jump to tag (not widely implemented).
    ```
   <br>清除可以直接删掉tags文件 
