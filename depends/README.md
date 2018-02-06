### Uso

Para compilar dependências para o arch+OS atual:

    make

Para compilar para outro arch/OS:

    make HOST=host-platform-triplet

Por exemplo:

    make HOST=x86_64-w64-mingw32 -j4

Será gerado um prefiox apropriado para se conectar a configuração do Criptoreal. No exemplo acima, um diretório chamado x86_64-w64-mingw32 será criado. Para usá-lo para Criptoreal:

    ./configure --prefix=`pwd`/depends/x86_64-w64-mingw32

Plataformas comuns `host-platform-triplets` para compilação cruzada são:

- `i686-w64-mingw32` para Win32
- `x86_64-w64-mingw32` para Win64
- `x86_64-apple-darwin11` para MacOSX
- `arm-linux-gnueabihf` para Linux ARM

Não são necessárias outras opções, os caminhos são configurados automaticamente.

Opções de dependência:
O seguinte pode ser configuradoo ao executar: make FOO=bar

    SOURCES_PATH: As fontes baixadas serão colocadas aqui
    BASE_CACHE: os pacotes compilados serão colocados aqui
    SDK_PATH: Caminho onde sdk's podem ser encontrados (usados por OSX)
    FALLBACK_DOWNLOAD_PATH: Se um arquivo de origem não puder ser obtido, tente por aqui antes de desistir
    NO_QT: Não faça download/compilação'/cache qt e suas dependências
    NO_WALLET: Não faça download/compilação/cache de libs necessários para habilitar a carteira
    NO_UPNP: Não faça download/compilação/cache de pacotes necessários para habilitar upnp
    DEBUG: desabilita algumas otimizações e habilita uma maior verificação de tempo de execução
    
Se alguns pacotes não forem compilados, por exemplo `make NO_WALLET=1`, as opções apropriadas serão passadas para a configuração do Criptoreal Core's. Neste caso, `--disable-wallet`.

Alvos adicionais:

    download: execute 'make download' para buscar todas as fontes sem compilá-las
    download-osx: execute 'make download-osx' para buscar todas as fontes necessárias para as compilações osx
    download-win: execute 'make download-win' para obter todas as fontes necessárias para compilações windows
    download-linux: execute 'make download-linux' para buscar todas as fontes necessárias para as compilações do linux

### Outras documentações

- [description.md](description.md): Descrição geral do sistema dependente
- [packages.md](packages.md): Passos para adicionar pacotes

