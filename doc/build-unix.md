NOTAS SOBRE A COMPILAÇÃO NO UNIX
====================
Algumas notas sobre como compilar o Criptoreal Core no Unix.

(para instruções específicas do OpenBSD, veja [build-openbsd.md](build-openbsd.md))

Nota
---------------------
Sempre use caminhos absolutos para configurar e compilar o criptoreal e suas dependências,
por exemplo, ao especificar o caminho da dependência:

	../dist/configure --enable-cxx --disable-shared --with-pic --prefix=$BDB_PREFIX

Aqui o BDB_PREFIX deve ser um caminho absoluto - é definido usando o $(pwd) que garante o uso do caminho absoluto.

Para Compilar
---------------------

```bash
./autogen.sh
./configure
make
make install # optional
```

Isto também irá compilar o criptoreal-qt se as dependências forem atendidas.

Dependências
---------------------

Estas dependências são obrigatórias:

 Biblioteca  | Objetivo         | Descrição
 ------------|------------------|----------------------
 libssl      | Crypto           | Geração de números aleatórios, Criptografia de curva elíptica
 libboost    | Utilidade        | Biblioteca para encadeamento, estrutura de dados, etc
 libevent    | Networking       | Rede assíncrona independente do Sistema Operacional

Dependências Opcionais:

 Biblioteca  | Objetivo         | Descrição
 ------------|------------------|----------------------
 miniupnpc   | Suporte UPnP     | Suporte de Firewall-jumping 
 libdb4.8    | Berkeley DB      | Armazenamento da carteira (necessário somente quando a carteira é ativada)
 qt          | GUI              | Conjunto de ferramentas GUI (necessário somente com GUI ativo)
 protobuf    | Pagamentos em GUI| Formato de intercâmbio de dados usado para o protocolo de pagamento (necessário somente com GUI ativo)
 libqrencode | QR codes em GUI  | Opcional para gerar QR codes (necessário somente quando a GUI está habilitada)
 univalue    | Utilidade        | Análise e codificação JSON  (A versão do bundle será usada a menos que --with-system-univalue seja configurado)
 libzmq3     | notificação ZMQ  | Opcional, permite a geração de notificações ZMQ  (requer versão ZMQ >= 4.x)

Para as versões usadas no lançamento, veja [release-process.md](release-process.md) em *Obter e compilar entradas*.

Requisitos de sistema
--------------------

Os compiladores C++ são devoradores de memória. É recomendado ter ao menos 1 GB de memória disponível ao compilar o Criptoreal Core.. Com 512MB de memória ou menos, a compilação levará muito mais tempo devido ao swap thrashing.

Instruções de compilação de dependência: Ubuntu e Debian
----------------------------------------------
Requisitos para compilação:

    sudo apt-get install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils

Ao menos no Ubuntu 14.04+ e no Debian 7+ existem nomes genéricos para cada pacote individual de desenvolvimento do boost, de todo modo, as instruções a seguir só podem ser usadas apenas para instalar as partes necessárias do boost:

    sudo apt-get install libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev

Se isto não funcionar, você pode instalar todos os pacotes de desenvolvimento do boost com:

    sudo apt-get install libboost-all-dev

O BerkeleyDB é obrigatório para a carteira. os pacotes db4.8 estão disponíveis [aqui](https://launchpad.net/~bitcoin/+archive/bitcoin).
Você pode adicionar o repositório e instalar usando os seguintes comandos:

    sudo add-apt-repository ppa:bitcoin/bitcoin
    sudo apt-get update
    sudo apt-get install libdb4.8-dev libdb4.8++-dev

Ubuntu e Debian têm seus próprios pacotes libdb-dev e libdb++-dev, mas estes irão instalar o BerkeleyDB 5.1 ou mais recente, que pode quebrar a compatibilidade binária da carteira com os executáveis distribuídos que são baseados no BerkeleyDB 4.8. Se você não se importa com a compatibilidade da carteira, passe `--with-incompatible-bdb` para configurar.

Veja a seção "Desabilitar modo carteira" para compilar o Criptoreal Core sem a carteira.

Opcional:

    sudo apt-get install libminiupnpc-dev (see --with-miniupnpc and --enable-upnp-default)

Dependências ZMQ:

    sudo apt-get install libzmq3-dev (provides ZMQ API 4.x)

Dependências para GUI: Ubuntu & Debian
-----------------------------------------

Se você quer compilar Criptoreal-Qt, tenha certeza de que os pacotes obrigatórios para desenvolvimento Qt estão instalados. Tanto o Qt 5 ou Qt 4 são necessários para compilar GUI.
Se ambos Qt 4 e Qt 5 estão instalados, Qt 5 será usado. Passe `--with-gui=qt4` para configurar o sistema para escolher o Qt4.
Para compilar sem GUI passe `--without-gui`.

Para compilar com o Qt 5 (recomendado) você precisa do seguinte:

    sudo apt-get install libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler

Em alternativa, para compilar com o Qt 4 você precisa do seguinte:

    sudo apt-get install libqt4-dev libprotobuf-dev protobuf-compiler

libqrencode (opcional) pode ser instalado com:

    sudo apt-get install libqrencode-dev

Uma vez que estes estejam instalados, eles serão encontrados pela configuração e um executável do criptoreal-qt será compilado por padrão.

Notas
-----
Este release é compilado com GCC e então use "strip criptoreald" para remover os símbolos do debug, que reduz o tamanho do executável em cerca de 90%.


miniupnpc
---------

[miniupnpc](http://miniupnp.free.fr/) pode ser usado para mapeamento de porta UPnP.  O download pode ser feito [aqui](
http://miniupnp.tuxfamily.org/files/). O suporte UPnP é compilado e desligado por padrão.  Veja as opcões de configuração para o comportamento upnp desejado:

	--without-miniupnpc      Não é necessário um miniupnp de suporte UPnP
	--disable-upnp-default   (o padrão) suporte UPnP desativado por padrão no tempo de execução
	--enable-upnp-default    Suporte UPnP ativado por padrão no tempo de execução


Berkeley DB
-----------
É recomendado usar o Berkeley DB 4.8. Se você tem que compilar por conta própria:

```bash
CRIPTOREAL_ROOT=$(pwd)

# Escolha um caminho para instalar o BDB, aqui criamos um diretório dentro do diretório do criptoreal
BDB_PREFIX="${CRIPTOREAL_ROOT}/db4"
mkdir -p $BDB_PREFIX

# Procure a fonte e verifique se esta não foi adulterada com
wget 'http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz'
echo '12edc0df75bf9abd7f82f821795bcee50f42cb2e5f76a6a281b85732798364ef  db-4.8.30.NC.tar.gz' | sha256sum -c
# -> db-4.8.30.NC.tar.gz: OK
tar -xzvf db-4.8.30.NC.tar.gz

# Compile a biblioteca e instale no nosso prefixo
cd db-4.8.30.NC/build_unix/
#  Nota: Faça uma compilação estática para que ela possa ser incorporada dentro do executável, ao invés de ter que encontrar um .so no tempo de execução
../dist/configure --enable-cxx --disable-shared --with-pic --prefix=$BDB_PREFIX
make install

# Configure o Criptoreal para usar nossa própria instância compilada do BDB
cd $CRIPTOREAL_ROOT
./autogen.sh
./configure LDFLAGS="-L${BDB_PREFIX}/lib/" CPPFLAGS="-I${BDB_PREFIX}/include/" # (other args...)
```

**Nota**: Você precisa somente do Berkeley DB se a carteira está habilidada (veja a seção *Disable-Wallet mode* abaixo).

Boost
-----
Se você precisa compilar o Boost por conta própria:

	sudo su
	./bootstrap.sh
	./bjam install


Segurança
--------
Para tornar a sua instalação Criptoreal ainda mais segura, tornando certos ataques impossívels de explorar mesmo se uma vulnerabilidade for encontrada, os binários são endurecidos por padrão.
Isto pode ser desabilitado com:

Hardening Flags:

	./configure --enable-hardening
	./configure --disable-hardening


Hardening habilita os seguintes recursos:

* A Posição independente executável compila um código independente de posição para tirar vantagem do layout do espaço de endereços randômicos oferecido por alguns kernels. Os ataques que podem causar a execução do código em uma localização de memória arbitrária não tem sucesso e não é possível localizar algo útil. A pilha é localizada aleatoriamente por padrão, mas isto permite que a seção do código seja localizada aleatoriamente também.

    Em um processador AMD64 onde a biblioteca não foi compilada com -fPIC, isto causará um erro como: "relocation R_X86_64_32 against `......' não pode ser usado ao fazer um objeto compartilhado;"

    Para testar se você compilou o executável PIE, instale scanelf, parte do paxutils, e use:

    	scanelf -e ./criptoreal

    A saída deve conter:

     TYPE
    ET_DYN

* Pilha não executável
Se a pilha é executável, então a pilha trivial baseada em estouro de buffer pode sofrer explorações se buffers vulneráveis forem encontrados. Por padrão, criptoreal deve ser compilado com uma pilha não executável, mas se uma das bibliotecas utilizadas solicite uma pilha executável ou alguém cometa um erro e use uma extensão de compilador que necessita de uma pilha executável, este irá compilar silenciosamente um executável sem a proteção da pilha não executável.

    Para verificar se a pilha é não executável depois da compilação use:
    `scanelf -e ./criptoreal`

    a saída deve conter:
	STK/REL/PTL
	RW- R-- RW-

    The STK RW- means that the stack is readable and writeable but not executable.

Modo Disable-wallet (Desabilitar Carteira)
--------------------
Quando a intenção é apenas executar um node P2P sem uma carteira, criptoreal deve ser compilado no modo disable-wallet com:

    ./configure --disable-wallet

Neste caso não há dependência do Berkeley DB 4.8.

A mineração também é possível no modo disable-wallet, mas somente usando a chamada `getblocktemplate` RPC
não a `getwork`.

Configurações adicionais de Flags
--------------------------
Uma lista de configuração adicional de flags pode ser mostrada com:

    ./configure --help

Compilação Cruzada ARM
-------------------
Estes passos podem ser realizados em, por exemplo, uma VM do Ubuntu. O sistema dependente também funcionará em outras distribuições Linux, entretanto, os comandos para instalar a cadeia de ferramentas serão diferentes.

Primeiro instale a toolchain:

    sudo apt-get install g++-arm-linux-gnueabihf

Para compilar executáveis para ARM:

    cd depends
    make HOST=arm-linux-gnueabihf NO_QT=1
    cd ..
    ./configure --prefix=$PWD/depends/arm-linux-gnueabihf --enable-glibc-back-compat --enable-reduce-exports LDFLAGS=-static-libstdc++
    make


Para mais documentação sobre o sistema de dependências veja [README.md](../depends/README.md) no diretório depends.
