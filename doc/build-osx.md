Notas e instruções de compilação no Mac OS X
====================================
Este guia irá mostrar como compilar criptoreald no OSX.

Notas
-----

* Testado no OS X 10.7 até a versão 10.11 somente em processadores 64-bit Intel.

* Todos os comandos devem ser executados em um aplicativo de Terminal. O nativo está localizado em `/Applications/Utilities`.

Preparação
-----------

Você precisa instalar o Xcode com todas as opções marcadas para que o compilador e tudo mais esteja disponível em /usr not just /Developer. Xcode deve estar disponivel em sua mídia de instalação do OS X, mas, caso não esteja, você pode obter a versão atual em https://developer.apple.com/xcode/. Se você instalar Xcode 4.3 ou uma versão mais recente,  você precisará instalar o utilitário de linhas de comendo. Isto pode ser feito em `Xcode > Preferences > Downloads > Components` e geralmente deve ser feito novamente cada vez que o Xcode é atualizado.

Você também deverá instalar o [Homebrew](http://brew.sh) para instalar as dependências da biblioteca.

A instalação das dependências está coberta nas instruções abaixo.

Instruções: Homebrew
----------------------

#### Instalar dependências usando Homebrew

    brew install autoconf automake berkeley-db4 libtool boost miniupnpc openssl pkg-config protobuf libevent qt

NOTA: Compilar com Qt4 ainda é suportado, entretanto, pode resultar em UI quebrada. Portanto, compilar com Qt5 é recomendado. Esteja atento que Qt5 5.7+ requer suporte ao compilador C++11.

### Compilando Criptoreal Core

1. Copie a árvore do GitHub para pagar o código fonte e chegar ao diretório.

        git clone https://github.com/criptoreal/criptoreal.git
        cd criptoreal

2.  Compilar Criptoreal Core:
    Isto irá configurar e copiar os binários criptoreal assim como a gui (se Qt for encontrado).
    Você pode desativar a compilaçãod da gui informando `--without-gui` para configurar.

        ./autogen.sh
        ./configure
        make

3.  Também é uma boa ideia para compilar e executar os testes de unidade:

        make check

4.  (Opcional) Você também pode instalar criptoreald no seu caminho:

        make install

Use Qt Creator as IDE
------------------------
Você pode usar o Qt Creator como IDE, para depurar e manipular formulários, etc.
Faça o download do Qt Creator em https://www.qt.io/download/. Faça o download da "community edition" e instale somente o Qt Creator (desmarque o restante durante o processo de instalação).

1. Tenha certeza de que você instalou tudo através do Homebrew mencionado acima
2. Execute um ./configure --enable-debug
3. No Qt Creator escolha "Novo Projeto" -> Importar Projeto -> Importar Projeto Existente
4. Digite "criptoreal-qt" como nome do projeto, informe src/qt como local
5. Deixe a seleção do arquivo como está
6. Confirme a "página de resumo"
7. Na aba "Projetos" selecione "Gerenciar Kits..."
8. Escolha o kit "Desktop" padrão e selecione "Clang (x86 64bit in /usr/bin)" como compilador
9. Selecione LLDB como depurador (talvez seja necessário configurar o caminho para sua instalação)
10. Comece a depurar com o Qt Creator

Criando uma versão de lançamento
------------------------
Você pode ignorar isto se estiver compilando `criptoreald` para uso próprio.

criptoreald/criptoreal-cli binaries não estão incluídos no Criptoreal-Qt.app.

Se você está compilando `criptoreald` ou `Criptoreal Core` para outros, sua máquina de compilação deve ser configurada da seguinte forma para compatibilidade máxima:

Todas as dependências devem ser compiladas com estas flags:

 -mmacosx-version-min=10.7
 -arch x86_64
 -isysroot $(xcode-select --print-path)/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.7.sdk

Uma vez que as dependências estejam compiladas, veja [doc/release-process.md](release-process.md) para saber como o pacote do Criptoreal Core é fechado e assinado para criar a imagem de disco .dmg que é distribuída.

Executando
-------

Está disponível agora em `./criptoreald`, desde que você ainda esteja no diretório `src`. No entanto, devemos primeiro criar o arquivo de configuração RPC.

Execute `./criptoreald` para obter o nome do arquivo onde deve ser colocado, ou apenas teste estes comandos:

    echo -e "rpcuser=criptorealrpc\nrpcpassword=$(xxd -l 16 -p /dev/urandom)" > "/Users/${USER}/Library/Application Support/Criptoreal/criptoreal.conf"
    chmod 600 "/Users/${USER}/Library/Application Support/Criptoreal/criptoreal.conf"

Na próxima vez que você executá-lo, ele começara a baixar a blockchain, mas não emitirá nada enquanto estiver fazendo isso. Este processo pode levar várias horas; você pode monitorar observando o arquivo debug.log, desta forma:

    tail -f $HOME/Library/Application\ Support/Criptoreal/debug.log

Outros comandos:
-------

    ./criptoreald -daemon # para iniciar a daemon do criptoreal.
    ./criptoreal-cli --help  # para uma lista de opções de linha de comando.
    ./criptoreal-cli help    # Quando o daemon está sendo executado, para obter uma lista de comandos RPC 
