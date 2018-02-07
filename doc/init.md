Exemplos de script de inicialização e configuração de serviço para criptoreald
==========================================================

Scripts de exemplo e arquivos de configuração para systemd, Upstart e OpenRC podem ser encontrados na pasta contrib/init

    contrib/init/criptoreald.service:    unidade de configuração de serviço systemd
    contrib/init/criptoreald.openrc:     Script de inicialização OpenRC compatível com SysV
    contrib/init/criptoreald.openrcconf: arquivo OpenRC conf.d 
    contrib/init/criptoreald.conf:       Arquivo de configuração do serviço de inicialização
    contrib/init/criptoreald.init:       Script de inicialização SysV compatível com CentOS

1. Usuário de serviço
---------------------------------

Todas as três configurações de inicialização do Linux assumem a existência de um usuário e um grupo "criptoreal". Eles devem ser criados antes de tentar usar estes scripts. A configuração do OS X assume que o criptoreald será configurado para o usuário atual.

2. Configuration
---------------------------------

Em um mínimo, o criptoreald exige que a configuração rpcpassword seja definida quando executada como um daemon. Se o arquivo de configuração não existir ou esta configuração não estiver definida, o bitcoind será desligado imediatamente após a inicialização.
Esta senha não precisa ser lembrada ou digitada, pois é usada principalmente como um token fixo onde o bitcoind e os programas são lidos a partir do arquivo de configuração, entretanto é recomendado que uma senha forte e segura seja usada, pois ela é de segurança crítica para garantir a carteira caso esteja habilitada.

Se criptoreald for executado com o sinalizador "-server" (definido por padrão) e nenhuma rpcpassword está configurada, ele usará um arquivo de cookie especial para autenticação. O cookie é gerado com conteúdo aleatório quando o daemon é iniciado e excluído quando terminado. O acesso de leitura a este arquivo controla quem pode acessá-lo através do RPC.

Por padrão, o cookie é armazenado no diretório de dados, mas sua localização pode ser substituída pela opção '-rpccookiefile'.

Isto permite a execução do criptoreald sem ter que fazer qualquer configuração manual.

`conf`, `pid`, e `wallet` aceitam caminhos relativos que são interpretados como relativos ao diretório de dados. `wallet` *somente* suporta caminhos relativos.

Para um exemplo de arquivo de configuração que descreve as configurações, consulte `contrib/debian/examples/criptoreal.conf`.

3. Caminhos
---------------------------------

3a) Linux

Todas as três configurações assumem vários caminhos que talvez precisem ser ajustados.

Binários:              `/usr/bin/criptoreald`  
Arqivo de configuração:  `/etc/criptoreal/criptoreal.conf`  
Diretório de dados:      `/var/lib/criptoreald`  
Arquivo PID:            `/var/run/criptoreald/criptoreald.pid` (OpenRC and Upstart) or `/var/lib/criptoreald/criptoreald.pid` (systemd) 
Arquivo Lock:           `/var/lock/subsys/criptoreald` (CentOS)  

O arquivo de configuração, o diretório PID (se aplicável) e o diretório de dados devem ser todos pertencentes ao usuario e ao grupo criptoreal. É aconselhável por motivos de segurança tornar o arquivo de configuração e o diretório de dados legíveis apenas para o usuário e grupo criptoreal.  O acesso ao criptoreal-cli e outros clientes rpc criptoreald podem ser controlados pela associação de grupo.

3b) Mac OS X

Binário:              `/usr/local/bin/criptoreald`  
Arquivo de Configuração:  `~/Library/Application Support/Criptoreal/criptoreal.conf`  
Diretório de dados:      `~/Library/Application Support/Criptoreal`
Arquivo Lock:           `~/Library/Application Support/Criptoreal/.lock`

4. Instalando a configuração do serviço
-----------------------------------

4a) systemd

A instalação deste arquivo .service consiste apenas em copiá-lo para /usr/lib/systemd/diretório do sistema, seguido pelo comando `systemctl daemon-reload` para atualizar a execução da configuração systemd.

Para testar, execute `systemctl start criptoreald` e para habilitar para inicialização execute `systemctl enable criptoreald`

4b) OpenRC

Renomeie criptoreald.openrc para criptoreald e solte-o em /etc/init.d. Verifique a propriedade e as permissões e torne-o executável.  Teste com `/etc/init.d/criptoreald start` e configure para executar na inicialização com `rc-update add criptoreald`

4c) Upstart (para distribuições baseadas em Debian/Ubuntu)

Coloque criptoreald.conf em /etc/init. Teste executando `service criptoreald start`. Ele será executado automaticamente na inicialização.

NOTA: Este script é incompatível com o CentOS 5 e o Amazon Linux 2014, pois usam versões antigas do Upstart e não fornecem o utilitário start-stop-daemon.

4d) CentOS

Copie criptoreald.init para /etc/init.d/criptoreald. Teste executando `service criptoreald start`.

Usando este script, você pode ajustar o caminho e os sinalizadores para o programa criptoreald definindo as variáveis de ambiente CRIPTOREALD e FLAGS no arquivo /etc/sysconfig/criptoreald. Você tambem pode usar a variável de ambiente DAEMONOPTS aqui.


4e) Mac OS X

Copie org.criptoreal.criptoreald.plist para ~/Library/LaunchAgents. Carregue o agente executando `launchctl load ~/Library/LaunchAgents/org.criptoreal.criptoreald.plist`.

Este agente fará com que o criptoreald inicie assim que o usuário efetuar o login.

NOTA: Esta abordagem destina-se a quem desejar executar o criptoreald como usuário atual. Você precisará modificar org.criptoreal.criptoreald.plist  se você pretende usá-lo como Launch Daemon com um usuário bitcoin dedicado.

5. Auto-respawn
-----------------------------------

Auto respawning atualmente só está configurado para Upstart e systemd. Padrões razoáveis foram escolhidos, mas YMMV.
