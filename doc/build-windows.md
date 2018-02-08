NOTAS DE COMPILAÇÃO DO WINDOWS
====================

Algumas notas em como compilar Criptoreal Core no Windows.

A maioria dos desenvolvedores usa a compilação cruzada do Ubuntu para criar executáveis para o Windows. Isto também é usado para compilar os binários de lançamento.

Compilar no Windows é possível (por exemplo usando msys / mingw-w64), mas ninguém documentou as etapas para fazer este processo. Se você está fazendo isto, contribua com a comunidade.

Compilação Cruzada
-------------------

Estas etapas podem ser realizadas em, por exemplo, uma VM do Ubuntu. O sistema dependente também funcionará em outras distribuições Linux, entretanto, os comandos para instalar a cadeia de ferramentas serão diferentes.

Primeiro instale as toolchains:

    sudo apt-get install g++-mingw-w64-i686 mingw-w64-i686-dev g++-mingw-w64-x86-64 mingw-w64-x86-64-dev

Para compilar executáveis para Windows 32-bit:

    cd depends
    make HOST=i686-w64-mingw32 -j4
    cd ..
    ./configure --prefix=`pwd`/depends/i686-w64-mingw32
    make

Para compilar executáveis para Windows 64-bit:

    cd depends
    make HOST=x86_64-w64-mingw32 -j4
    cd ..
    ./configure --prefix=`pwd`/depends/x86_64-w64-mingw32
    make

Para mais documentação no sistema de dependências veja [README.md](../depends/README.md) no diretório depends.

