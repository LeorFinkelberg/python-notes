Для удобства можно поставить следующие плагины Vim:
- Fugitive: строка состояния Git,
- Tagbar: панель, упрощающая переход к функциям, методам и классам.

Чтобы установить эти плагины, нужно внести изменения в конфигурационный файл `~/.vimrc`
```vim
"Расположение конфигурационного файла: ~/.vimrc
syntax on
set nocompatible  "Отключить совместимость с Vi; ОБЯЗАТЕЛЬНО!
filetype off  "ОБЯЗАТЕЛЬНО!
"Установить путь среды выполнения для включения Vundle
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()  " -- BEGIN

Plugin 'VundleVim/Vundle.vim'  "ОБЯЗАТЕЛЬНО!
"Команды Plugin должны располагаться между vundle#begin/end
Plugin 'tpope/vim-fugitive'
Plugin 'majutsushi/tagbar'

call vundle#end()  " -- END
filetype plugin indent on  "ОБЯЗАТЕЛЬНО!
"Автоматически открывать панель tagbar в C-файлах не обязательно
autocmd FileType c call tagbar#autoopen(0)
"Автоматически открывать панель tagbar в Python-файлах не обязательно
autocmd FileType python call tagbar#autoopen(0)

set laststatus=2
set statusline=%{FugitiveStatusline()}
```

Чтобы загрузить и установить эти плагины, выполняем 
```bash
$ vim +PluginInstall +qall
```

Простой поиск по тексту не отличит вызов или определение функции от реализации. Однако можно воспользоваться приложением [ctags](http://ctags.sourceforge.net). Чтобы проиндексировать заголовки CPython для всех файлов C и Python в стандартной библиотеке, выполняем
```bash
# for Unix
$ ./configure --with-pydebug && make -j
```

>[!WARNING]
>В некоторых случаях может потребоваться изменить формат файла `./configure` (и других связанных с ним файлов) с `dos` на `unix`. Проще всего это сделать с помощью утилиты `dos2unix`
>```
>$ dos2unix -f file_name
>```
>Для ОС Windows и MacOS дополнительно может потребоваться обновить `ctags`
>```
># для MacOS
>$ brew install ctags
># для Windows
>$ choco install ctags
>```

При запуске редактора Vim должен быть доступен Tagbar
![[tagbar.PNG]]