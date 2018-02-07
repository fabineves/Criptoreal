### Sobre o que é isso

Mais informações a respeito do KeePass: http://keepass.info/

A integração KeePass usa KeePassHttp (https://github.com/pfn/keepasshttp/) para facilitar as comunicações entre o cliente e o KeePass. KeePassHttp é um plugin para KeePass 2.x e fornece um meio seguro de expor as entradas do KeePass via HTTP para os clientes utilizarem.

A implementação depende do seguinte:
 - crypter.h para funções auxiliares de criptografia AES.
 - rpcprotocol.h para manipulação de comunicações RPC. Só pode ser usado parcialmente devido a alguns valores estáticos no código.
 - OpenSSL para codificação base64. As bibliotecas regulares util.h não foram usadas para codificação/decodificação base64 uma vez que não utilizam alocação segura.
 - JSON Spirit para ler / escrever comunicações RPC

### O que é novo

As seguintes novidades estão disponíveis para criptoreald e criptoreal-qt:
 - _-keepass_ Usa a integração do KeePass 2 usando o plugin KeePassHttp (default: 0)
 - _-keepassport=_ Conecta ao KeePassHttp na porta (padrão: 19455)
 - _-keepasskey=_  Chave KeePassHttp  para comunicação codificada AES com o KeePass
 - _-keepassid=_ Id KeePassHttp id para associação estabelecida
 - _-keepassname=_ Nome para construir url de entrada do KeePass que armazena a senha da carteira

Os seguintes comandos rpc estão disponíveis:

 - _keepass genkey_: gera uma chave AES de 256 bits codificada em base64 que pode ser usada para a comunicação com o KeePassHttp. Somente necessário para a configuração manual. Use init para configuração automática.
 - _keepass init_: configura a associação entre o criptoreald e o keepass, gerando uma chave AES e enviando uma mensagem de associação para KeePassHttp. Isto irá desencadear o KeePass para pediur um ID para a associação. Retorna a associação e a string codificada base64 para a chave AES.
 - _keepass setpassphrase_: atualiza a senha no KeePassHttp para um novo valor. Isto deve corresponder a senha que você deve pretende usar para a carteira. Observe que os comandos RPC padrão _walletpassphrasechange_ e a criptografia da carteira do QT GUI já envia as atualizações para KeePassHttp, então isto só é necessário para manipulação manual da senha.

### Como configurar

Exemplo do fluxo de inicialização do console _criptoreal-qt_ (isto precisa ser feito apenas uma vez para configurar a associação):

 - Ter o KeePass rodando com um banco de dados aberto
 - Iniciar _criptoreal-qt_
 - Abrir console
 - Digitar "_keepass init_" no console criptoreal-qt 
 - Keepass aparece e pede um ID de associação, preencha isso com, por exemplo, "_mydrkwallet_"
 - Você deve ter uma resposta como esta "_Association successful. Id: mydrkwalletcriptoreal - Key: AgQkcs6cI7v9tlSYKjG/+s8wJrGALHl3jLosJpPLzUE=_"
 - Editar _criptoreal.conf_ e preencher com estes valores
```
keepass=1
keepasskey=AgQkcs6cI7v9tlSYKjG/+s8wJrGALHl3jLosJpPLzUE=
keepassid=mydrkwallet
keepassname=testwallet
```
 - Reiniciar _criptoreal-qt_

Neste ponto, a associação é feita. A próxima ação depende da sua situação em particular:

 - a carteira atual não está criptografada. Criptografar a carteira irá desencadear a integração e armazena a senha no KeePass (No grupo  '_KeePassHttp Passwords_', com o nome de _keepassname_.
 - carteira atual já está criptografada: use "_keepass setpassphrase_" para armazenar a senha no KeePass.

Neste ponto, a senha é armazenada em KeePassHttp. Ao desbloquear a carteira, pode-se usar _keepass_ como passphrase para desencadear a recuperação de senha. Isto funciona a partir dos comandos RPC, assim como na GUI.

Guia extenso com screenshots também está disponível: https://criptorealtalk.org/threads/keepass-integration.3620/
