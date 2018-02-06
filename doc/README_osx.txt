Notas deterministas para os arquivos Dmg de OS X.

Os DMGs funcionais para OS X são criados em Linux combinando clang, os binutils (Id, ar, etc) da Apple e as ferramentas de autoria do DMG.

A Apple usa clang extensivamente para desenvolvimento e aumentou a funcionalidade necessária para que uma vanilla clang possa ter vantagem. Ela suporta o uso de -F, -target, -mmacosx-version-min, e --sysroot, onde todas são necessárias ao desenvolver para OS X. 

Uma versão pré-compilada do 3.2 é usada porque não estava disponível nos repositórios precisos no momento em que este trabalho foi iniciado. No futuro, ele pode ser alternado para usar pacotes de sistema.

A versão da Apple de binutils (chamada de cctools) contém uma série de funcionalidades não encontradas no binutils FSF. Além das opções adicionais para vincular frameworks e sysroots, outras ferramentas são necessárias também, tais como, install_name_tool, lipo, e nmedit. Estas não funcionam no linux, então elas foram adaptadas para funcionarem. Este trabalho foi usado como ponto de partida:
https://github.com/mingwandroid/toolchain4

Para compilar uma toolchain funcional, são necessários os seguintes pacotes da Apple: cctools, dyld, and ld64.

Estas ferramentas injetam timestamps por padrão, que produzem binários não determinados. A variável ZERO_AR_DATE é usada para desabilitar isso.

Esta versão de cctools foi modificada para ser usada com a versão atual dos cabeçalhos clang e suas libLTO.so ao invés de llvmgcc, como foi feita originalmente na toolchain4.

Para complicar um pouco mais as coisas, todas as compilações devem segmentar um SDK da Apple. Estes SDKs são livres para download, mas não podem ser redistribuídos. Para obter, registre uma conta de desenvolvedor, e faça o download de Xcode 6.1.1 dmg:
https://developer.apple.com/devcenter/download.action?path=/Developer_Tools/xcode_6.1.1/xcode_6.1.1.dmg

Este arquivo possui vários gigabytes de tamanho, somente um diretório é necessário: Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk

Infelizmente, as ferramentas linux mais comuns (7zip, hpmount, loopback mount) não conseguem abrir este arquivo. Para criar um tarball adequado para entradas Gitian, monte o dmg no OS X e crie-o com:
  $ tar -C /Volumes/Xcode/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/ -czf MacOSX10.9.sdk.tar.gz MacOSX10.9.sdk


Os descriptores Gitian compilam 2 conjuntos de arquivos: ferramentas Linux, depois os binários Apple que são criados usando estas ferramentas. O processo de compilação foi desenvolvido para evitar incluir os arquivos SDK nas saídas do Gitian. Todos os tarballs são deterministas e podem ser redistribuídos livremente.

genisoimage é usada para criar a DMG inicial. Não é originalmente determinista, então foi adaptada para tal. Uma genisoimage de sistema vai funcionar normalmente, mas não será determinista porque a ordem do arquivo mudará entre as invocações. A patch pode ser vista aqui: theuni/osx-cross-depends. Nenhum esforço foi feito para corrigir isto claramente, então provavelmente usa a memória de maneira ineficiente. Mas como é usada para apenas uma invocação, isto não se torna uma preocupação real.

genisoimage não pode compactar DMGs, então depois, a ferramenta 'dmg' do projeto libdmg-hfsplus é usada para compactá-la. Existem uma série de bugs nesta ferramenta e seu mantenedor aparentemente abandonou o projeto. Ele foi copiado e está disponível com correções em:: https://github.com/theuni/libdmg-hfsplus .

A ferramenta 'dmg' tem a habilidade de criar DMGs do zero também, mas esta funcionalidade não funciona. Somente a ferarmenta de compactação é usada atualmente. Teoricamente a criação poderia ser corrigida e a genisoimage não seria mais necessária.

Imagens de fundo e outros recursos podem ser adicionados ao arquivo DMG .DS_Store antes da criação. A maneira mais fácil de criar este arquivo é criar um DMG sem o arquivo, movê-lo para um dispositivo executando o OS X, personalizar o layout e em seguida, pegar o arquivo .DS_Store para uso posterior. Esta é a abordagem adotada aqui.

No OS X Mavericks (10.9), usar uma chave Apple para assinar binários é obrigatório para satisfazer os requisitos do Gatekeeper. Como esta chave privada não pode ser compartilhada, teremos que ser um pouco criativos para que o processo de compilação continue determinista. Funciona desta forma:

- Os usuários podem usar o Gitian para criar uma versão sem assinatura. Estas saídas não assinadas do dmg podem ser escolhidas pelos usuários para serem executadas. Ele também gera uma estrutura de um app sem assinatura na forma de tarball, que também contém todas as ferramentas que foram usadas anteriormente para criar um dmg final.
- O repositório de chaves da Apple usa este aplicativo não assinado para criar uma assinatura destacada, usando o script que também está incluso lá.
- Os compiladores alimentam o aplicativo não assinado + destacam a assinatura de volta ao Gitian. Usa as ferramentas pré-construídas para recombinar as peças em um dmg determinista.
