Processo de lançamento
======================

* Atualize traduções, veja [translation_process.md](https://github.com/criptoreal/criptoreal/blob/master/doc/translation_process.md#syncing-with-transifex)
* Atualize hardcoded [seeds](/contrib/seeds)

* * *

### Primeira vez / Novos compiladores
Verifique o código fonte na seguinte hierarquia de diretórios.

	cd /path/to/your/toplevel/build
	git clone https://github.com/criptoreal/gitian.sigs.git
	git clone https://github.com/criptoreal/criptoreal-detached-sigs.git
	git clone https://github.com/devrandom/gitian-builder.git
	git clone https://github.com/criptoreal/criptoreal.git

###  Mantenedores/engenheiros de lançamento do Criptoreal, atualizem a versão nos fontes

	pushd ./criptoreal
	contrib/verifysfbinaries/verify.sh
	configure.ac
	doc/README*
	doc/Doxyfile
	contrib/gitian-descriptors/*.yml
	src/clientversion.h (change CLIENT_VERSION_IS_RELEASE to true)

	# tag version in git

	git tag -s v(new version, e.g. 0.8.0)

	# write release notes. git shortlog helps a lot, for example:

	git shortlog --no-merges v(current version, e.g. 0.7.2)..v(new version, e.g. 0.8.0)
	popd

* * *

### Configurar e executar compilações do Gitian

 Configurar descriptores Gitian:

	pushd ./criptoreal
	export SIGNER=(your Gitian key, ie bluematt, sipa, etc)
	export VERSION=(new version, e.g. 0.8.0)
	git fetch
	git checkout v${VERSION}
	popd

  Certifique-se de que seus gitian.sigs estejam atualizados se você deseja verificar suas compilações contra outras assinaturas Gitian.

	pushd ./gitian.sigs
	git pull
	popd

  Certifique-se de que o gitian-builder esteja atualizado para aproveitar os novos recursos de cache (`e9741525c` ou mais recente é recomendado).

	pushd ./gitian-builder
	git pull

### Obtenha e crie entradas: (primeira vez ou quando as versões de dependência mudam)

	mkdir -p inputs
	wget -P inputs https://bitcoincore.org/cfields/osslsigncode-Backports-to-1.7.1.patch
	wget -P inputs http://downloads.sourceforge.net/project/osslsigncode/osslsigncode/osslsigncode-1.7.1.tar.gz

 Registre e faça o download do Apple SDK: veja [OS X readme](README_osx.txt) para detalhes.

 https://developer.apple.com/devcenter/download.action?path=/Developer_Tools/xcode_6.1.1/xcode_6.1.1.dmg

 Usando um Mac, crie um tarball para o sDK 10.9 e copie para o diretório de entradas:

	tar -C /Volumes/Xcode/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/ -czf MacOSX10.9.sdk.tar.gz MacOSX10.9.sdk

### Opcional: Sinalize o cache das fontes Gitian e os repositórios git offline

Por padrão, o Gitian irá buscar arquivos de origem conforme necessário. Para armazená-los antes do tempo:

	make -C ../criptoreal/depends download SOURCES_PATH=`pwd`/cache/common

Somente os arquivos ausentes serão obtidos, então isto é seguro para re-executar para cada compilação.

NOTA: Compilações offline devem usar o sinalizador --url para garantir que o Gitian seja recuperado apenas de URLs locais. Por exemplo:
```
./bin/gbuild --url criptoreal=/path/to/criptoreal,signature=/path/to/sigs {rest of arguments}
```
As invocações gbuild abaixo <b>NÃO FAZEM ISTO</b> por padrão.

### Compilar e assinar o Criptoreal Core para Linux, Windows, e OS X:

	./bin/gbuild --commit criptoreal=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-linux.yml
	./bin/gsign --signer $SIGNER --release ${VERSION}-linux --destination ../gitian.sigs/ ../criptoreal/contrib/gitian-descriptors/gitian-linux.yml
	mv build/out/criptoreal-*.tar.gz build/out/src/criptoreal-*.tar.gz ../

	./bin/gbuild --commit criptoreal=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-win.yml
	./bin/gsign --signer $SIGNER --release ${VERSION}-win-unsigned --destination ../gitian.sigs/ ../criptoreal/contrib/gitian-descriptors/gitian-win.yml
	mv build/out/criptoreal-*-win-unsigned.tar.gz inputs/criptoreal-win-unsigned.tar.gz
	mv build/out/criptoreal-*.zip build/out/criptoreal-*.exe ../

	./bin/gbuild --commit criptoreal=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-osx.yml
	./bin/gsign --signer $SIGNER --release ${VERSION}-osx-unsigned --destination ../gitian.sigs/ ../criptoreal/contrib/gitian-descriptors/gitian-osx.yml
	mv build/out/criptoreal-*-osx-unsigned.tar.gz inputs/criptoreal-osx-unsigned.tar.gz
	mv build/out/criptoreal-*.tar.gz build/out/criptoreal-*.dmg ../
	popd

 Compilação de saída esperada:

  1. fonte tarball (`criptoreal-${VERSION}.tar.gz`)
  2. linux 32-bit e 64-bit dist tarballs (`criptoreal-${VERSION}-linux[32|64].tar.gz`)
  3. windows 32-bit e 64-bit instaladores não assinados e dist zips (`criptoreal-${VERSION}-win[32|64]-setup-unsigned.exe`, `criptoreal-${VERSION}-win[32|64].zip`)
  4. Instalador não assinado do OS X e dist tarball (`criptoreal-${VERSION}-osx-unsigned.dmg`, `criptoreal-${VERSION}-osx64.tar.gz`)
  5. Assinaturas Gitian (em `gitian.sigs.cripto/${VERSION}-<linux|{win,osx}-unsigned>/(your Gitian key)/`)

### Verifique outras assinaturas dos compiladores gitian para o seu próprio. (Opcional)

  Adicione outras chaves de compiladores gitian ao seu chaveiro gpg keyring

	gpg --import ../criptoreal/contrib/gitian-downloader/*.pgp

  Verifique as assinaturas

	./bin/gverify -v -d ../gitian.sigs/ -r ${VERSION}-linux ../criptoreal/contrib/gitian-descriptors/gitian-linux.yml
	./bin/gverify -v -d ../gitian.sigs/ -r ${VERSION}-win-unsigned ../criptoreal/contrib/gitian-descriptors/gitian-win.yml
	./bin/gverify -v -d ../gitian.sigs/ -r ${VERSION}-osx-unsigned ../criptoreal/contrib/gitian-descriptors/gitian-osx.yml

	popd

### Próximos passos:

Confirme sua assinatura ao gitian.sigs:

	pushd gitian.sigs
	git add ${VERSION}-linux/${SIGNER}
	git add ${VERSION}-win-unsigned/${SIGNER}
	git add ${VERSION}-osx-unsigned/${SIGNER}
	git commit -a
	git push  # Assuming you can push to the gitian.sigs tree
	popd

  Aguarde assinaturas independentes do Windows/OS X:

- Uma vez que o Windows/OS X compila, cada um tem 3 assinaturas correspondentes, eles serão assinados com as respectivas chaves de lançamento.
- As assinaturas separadas serão comprometidas com o repositório [criptoreal-detached-sigs](https://github.com/criptoreal/criptoreal-detached-sigs), que pode ser combinado com os aplicativos não assinados para criar binários assinados.

Criar (e verificar opcionalmente) o binário OS X assinado:

	pushd ./gitian-builder
	./bin/gbuild -i --commit signature=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-osx-signer.yml
	./bin/gsign --signer $SIGNER --release ${VERSION}-osx-signed --destination ../gitian.sigs/ ../criptoreal/contrib/gitian-descriptors/gitian-osx-signer.yml
	./bin/gverify -v -d ../gitian.sigs/ -r ${VERSION}-osx-signed ../criptoreal/contrib/gitian-descriptors/gitian-osx-signer.yml
	mv build/out/criptoreal-osx-signed.dmg ../criptoreal-${VERSION}-osx.dmg
	popd

  Criar (e verificar opcionalmente) os binários do Windows assinados:

	pushd ./gitian-builder
	./bin/gbuild -i --commit signature=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-win-signer.yml
	./bin/gsign --signer $SIGNER --release ${VERSION}-win-signed --destination ../gitian.sigs/ ../criptoreal/contrib/gitian-descriptors/gitian-win-signer.yml
	./bin/gverify -v -d ../gitian.sigs/ -r ${VERSION}-win-signed ../criptoreal/contrib/gitian-descriptors/gitian-win-signer.yml
	mv build/out/criptoreal-*win64-setup.exe ../criptoreal-${VERSION}-win64-setup.exe
	mv build/out/criptoreal-*win32-setup.exe ../criptoreal-${VERSION}-win32-setup.exe
	popd

Confirme sua assinatura para os binários OS X / Windows assinados:

	pushd gitian.sigs
	git add ${VERSION}-osx-signed/${SIGNER}
	git add ${VERSION}-win-signed/${SIGNER}
	git commit -a
	git push  # Assuming you can push to the gitian.sigs tree
	popd

-------------------------------------------------------------------------

### Depois que 3 ou mais pessoas tiverem compilado o gitian e seus resultados coincidem:

- Create `SHA256SUMS.asc` for the builds, and GPG-sign it:
```bash
sha256sum * > SHA256SUMS
gpg --digest-algo sha256 --clearsign SHA256SUMS # outputs SHA256SUMS.asc
rm SHA256SUMS
```
(o algoritmo digest é forçado a sha256 para evitar confusão do cabeçalho `Hash:` que o GPG adiciona com o SHA256 usado para os arquivos)
Nota: verifique que SHA256SUMS por si só não termina em SHA256SUMS, que é uma entrada falsa/absurda.

- Faça o upload dos zips e installers, assim como  o arquivo`SHA256SUMS.asc` do último passo, para o servidor criptoreal.org

- Atualize criptoreal.org

- Anuncie o lançamento:

  - Informe no fórum Criptoreal: https://www.criptoreal.org/forum/topic/official-announcements.54/

  - Lista de email de desenvolvedores Criptoreal

  - Atualize o título no canal #criptoreal no Freenode IRC

  - Opcionalmente no reddit /r/Criptorealpay, ... mas isto se resolve sozinho

- Notifique flare para que comece a compilar o [PPAs](https://launchpad.net/~criptoreal.org/+archive/ubuntu/criptoreal)

- Adicione as notas de lançamento sobre a nova versão no diretório `doc/release-notes` no git master

- Comemore
