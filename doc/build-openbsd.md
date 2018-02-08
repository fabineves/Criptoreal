Guia de compilação do OpenBSD
======================
(atualizado para OpenBSD 6.0)

Este guia descreve como compilar criptoreald e utilitários de linha de comando no OpenBSD.

Como o OpenBSD é mais comum como sistema operacional de um servidor, não nos preocuparemos com a GUI.

Preparação
-------------

Execute os seguintes comandos como root para instalar as dependências da base para a compilação:

```bash
pkg_add gmake libtool libevent
pkg_add autoconf # (escolha a última versão, e.g. 2.69)
pkg_add automake # (escolha a última versão, e.g. 1.15)
pkg_add python # (escolha a última versão, e.g. 3.5)
```

O compilador C++ padrão que vem com o OpenBSD 5.9 é o g++ 4.2. Esta versão é antiga (de 2007), e não é capaz de compilar a versão atual do Criptoreal Core, principalmente por não possuir suporte ao C++11, mas mesmo antes deste período já eram encontrados problemas. Então, nós iremos instalar um compilador mais novo.

GCC
-------

Você irá instalar uma nova versão do gcc com:

```bash
pkg_add g++ # (escolha a versão mais nova 4.x, e.g. 4.9.3)
```

Este compilador não irá substituir o compilador do sistema, este será instalado como `egcc` e `eg++` em `/usr/local/bin`.

### Boost de compilação

Não use `pkg_add boost`! A versão do boost instalada desta forma é compilada usando o compilador `g++` não o `eg++`, que irá resultar em um conflito entre `/usr/local/lib/libestdc++.so.XX.0` e `/usr/lib/libstdc++.so.XX.0`, resultando em um "crash" no teste:

    test_criptoreal:/usr/lib/libstdc++.so.57.0: /usr/local/lib/libestdc++.so.17.0 : WARNING: symbol(_ZN11__gnu_debug17_S_debug_me ssagesE) size mismatch, relink your program
    ...
    Segmentation fault (core dumped)

Isto torna necessária a criação de um boost, ou pelo menos as partes usadas pelo Criptoreal Core, manualmente:


```
# Escolha um caminho para instalar o boost, aqui criamos um diretório dentro do diretório criptoreal
BITCOIN_ROOT=$(pwd)
BOOST_PREFIX="${BITCOIN_ROOT}/boost"
mkdir -p $BOOST_PREFIX

# Escolha um caminho para instalar o boost, aqui criamos um diretório dentro do diretório criptoreal
CRIPTOREAL_ROOT=$(pwd)
BOOST_PREFIX="${CRIPTOREAL_ROOT}/boost"
mkdir -p $BOOST_PREFIX

# Procure a fonte e verifique se ela não é adulterada
curl -o boost_1_61_0.tar.bz2 http://heanet.dl.sourceforge.net/project/boost/boost/1.61.0/boost_1_61_0.tar.bz2
echo 'a547bd06c2fd9a71ba1d169d9cf0339da7ebf4753849a8f7d6fdb8feee99b640  boost_1_61_0.tar.bz2' | sha256 -c
# MUST output: (SHA256) boost_1_61_0.tar.bz2: OK
tar -xjf boost_1_61_0.tar.bz2

# Boost 1.61 precisa de um pequeno patch para o OpenBSD
cd boost_1_61_0
# Aqui também: https://gist.githubusercontent.com/laanwj/bf359281dc319b8ff2e1/raw/92250de8404b97bb99d72ab898f4a8cb35ae1ea3/patch-boost_test_impl_execution_monitor_ipp.patch
patch -p0 < /usr/ports/devel/boost/patches/patch-boost_test_impl_execution_monitor_ipp

# Compilar com a configuração mínima necessária para o criptoreal
echo 'using gcc : : eg++ : <cxxflags>"-fvisibility=hidden -fPIC" <linkflags>"" <archiver>"ar" <striper>"strip"  <ranlib>"ranlib" <rc>"" : ;' > user-config.jam
config_opts="runtime-link=shared threadapi=pthread threading=multi link=static variant=release --layout=tagged --build-type=complete --user-config=user-config.jam -sNO_BZIP2=1"
./bootstrap.sh --without-icu --with-libraries=chrono,filesystem,program_options,system,thread,test
./b2 -d2 -j2 -d1 ${config_opts} --prefix=${BOOST_PREFIX} stage
./b2 -d0 -j4 ${config_opts} --prefix=${BOOST_PREFIX} install
```

### Compilando o BerkeleyDB

BerkeleyDB é necessário somente para a funcionalidade de carteira. Para ignorar isto, passe `--disable-wallet` para `./configure`.

Veja "Berkeley DB" em [build_unix.md](build_unix.md) para instruções em como compilar BerkeleyDB 4.8.
Você não pode usar a biblioteca do BerkeleyDB a partir de portas, pela mesma razão do boost acima (incompatibilidade g++/libstd++).

```bash
# Escolha um caminho para instalar o BDB, aqui nós criamos um diretório dentro do diretório criptoreal
BITCOIN_ROOT=$(pwd)
BDB_PREFIX="${BITCOIN_ROOT}/db4"
mkdir -p $BDB_PREFIX

# Encontre a fonte e veja se ela não foi adulterada
wget 'http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz'
echo '12edc0df75bf9abd7f82f821795bcee50f42cb2e5f76a6a281b85732798364ef  db-4.8.30.NC.tar.gz' | sha256 -c
# MUST output: (SHA256) db-4.8.30.NC.tar.gz: OK
tar -xzf db-4.8.30.NC.tar.gz

# Crie a biblioteca e instale no prefixo especificado
cd db-4.8.30.NC/build_unix/
#  Note: Do a static build so that it can be embedded into the executable, instead of having to find a .so at runtime
../dist/configure --enable-cxx --disable-shared --with-pic --prefix=$BDB_PREFIX CC=egcc CXX=eg++ CPP=ecpp
make install
```

### Compilando o Criptoreal Core

**Importante**: use `gmake`, não `make`. O `make`não GNU irá sair com um erro horrível.

Preparação:
```bash
export AUTOCONF_VERSION=2.69 # substitua isso pela versão autoconf que você instalou
export AUTOMAKE_VERSION=1.15 # substitua isso pela versão automake version que você instalou
./autogen.sh
```
tenha certeza que o `BDB_PREFIX` e o `BOOST_PREFIX` sejam definidos com os caminhos apropriados das etapas acima.

Para configurar com a carteira:
```bash
./configure --with-gui=no --with-boost=$BOOST_PREFIX \
    CC=egcc CXX=eg++ CPP=ecpp \
    LDFLAGS="-L${BDB_PREFIX}/lib/" CPPFLAGS="-I${BDB_PREFIX}/include/"
```

Para configurar sem a carteira:
```bash
./configure --disable-wallet --with-gui=no --with-boost=$BOOST_PREFIX \
    CC=egcc CXX=eg++ CPP=ecpp
```

Compilar e executar os testes:
```bash
gmake
gmake check
```

Clang (atualmente não está funcionando)
------------------------------

Usar um g++ mais recente resulta em ligar o novo código a um novo libstdc++.
Bibliotecas compiladas com o g++ antigo, irão importar a biblioteca antiga.
Isto gera conflitos, necessitando recompilar todas as dependências do aplicativo C++.

Usando clang isto pode - pelo menos em teoria - ser evitado pois ele usa o libstdc++ do sistema base.

```bash
pkg_add llvm boost
```

```bash
./configure --disable-wallet --with-gui=no CC=clang CXX=clang++
gmake
```

Entretanto, isto parece não funcionar. A compilação é bem sucedida, mas o link falha com muitos erros de 'símbolos de locais descartados':

local symbol 150: descartado na seção `.text._ZN10tinyformat6detail14FormatIterator6finishEv' from libbitcoin_util.a(libbitcoin_util_a-random.o)
    local symbol 151: descartado na seção `.text._ZN10tinyformat6detail14FormatIterator21streamStateFromFormatERSoRjPKcii' from libbitcoin_util.a(libbitcoin_util_a-random.o)
    local symbol 152: descartado na seção `.text._ZN10tinyformat6detail12convertToIntIA13_cLb0EE6invokeERA13_Kc' from libbitcoin_util.a(libbitcoin_util_a-random.o)

De acordo com outros erros semelhantes que foram relatados, este é um problema de binutils (ld) na 2.15, a versão instalada pelo OpenBSD 5.7:

- http://openbsd-archive.7691.n7.nabble.com/UPDATE-cppcheck-1-65-td248900.html
- https://llvm.org/bugs/show_bug.cgi?id=9758

Ainda não há uma solução conhecida para isto.
