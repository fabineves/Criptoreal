Ferramentas de Carteira
---------------------

### [SpendFrom](/contrib/spendfrom) ###

Usa a API de transações raw para enviar as moedas recebidas em um determinado endereço (ou endereços).

Repositório de Ferramentas
---------------------

### [Ferramentas de Desenvolvedor](/contrib/devtools) ###
Ferramentas específicas para desenvolvedores que trabalham neste repositório. Contém o script `github-merge.sh` para mesclar as pull requests do github com segurança e assiná-las usando o GPG.

### [Verificar-Commits](/contrib/verify-commits) ###
Ferramenta para verificar se cada commit mesclado foi assinado por um desenvolvedor usando o script `github-merge.sh` acima.

### [Linearizar](/contrib/linearize) ###
Constrói um linear, não-fork, a melhor versão da blockchain.

### [Qos](/contrib/qos) ###

Um script Linux bash que irá configurar o controle de tráfego (tc) para limitar a largura de banda de saída para conexões a rede Criptoreal. Isto significa que pode-se ter uma instância sempre executada no criptoreald, e outra instância local criptoreald/criptoreal-qt  que se conecta a este nó e recebe blocos dele.

### [Seeds](/contrib/seeds) ###
Utilitário para gerar a matriz pnSeed[] que é compilada no cliente.

Ferramentas de compilação e chaves
---------------------

### [Debian](/contrib/debian) ###
Contém arquivos usados para o pacote criptoreald/criptoreal-qt para usuários Linux baseados em Debian. Se você compilar criptoreald/criptoreal-qt por conta própria, existem alguns arquivos bem úteis aqui.

### [Descriptores Gitian](/contrib/gitian-descriptors) ###
Notas do Gavin sobre como compilar o Gitian usando KVM.

### [Gitian-downloader](/contrib/gitian-downloader)
Vários arquivos PGP de desenvolvedores principais.

### [MacDeploy](/contrib/macdeploy) ###
Scripts e notas para os compiladores Mac. 

Ferramentas de verficação e teste
---------------------

### [TestGen](/contrib/testgen) ###
Utilidades para gerar vetores de teste para os testes de Criptoreal baseados em dados.

### [Test Patches](/contrib/test-patches) ###
Estes patches são aplicados quando o testador automatizado testa cada chamado e quando o master é testado usando jenkins.

### [Verificar SF Binaries](/contrib/verifysfbinaries) ###
Este script tenta baixar e verificar o arquivo de assinatura SHA256SUMS.asc do SourceForge.
