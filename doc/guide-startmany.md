# Guia de configuração start-many

## Configurando sua carteira

### Criar um novo endereço de carteira

1. Abra a carteira QT.
2. Clique na aba Receber (Receive).
3. Preencha o formulário para requerer um pagamento.
    * Etiqueta: mn01
    * Valor: 1000 (optional)
    * Clique no botão  *Requerer Pagamento* 
5. Clique no botão *Copiar endereço* 

Crie um novo endereço de carteira para cada Masternode.

Feche sua carteira QT.

### Eviar 1000 CRS para novos endereços

Envia exatamente 1000 CRS para cada endereço criado acima.

### Criar novas chaves privadas de Masternode 

Abra sua carteira QT e depois vá para console (no menu selecione `Tools` => `Debug Console`)

Emita o seguinte:

```masternode genkey```

*Note: Uma chave privada masternode precisará ser criada para cada Masternode que você executar. Você não deve usar a mesma chave privada masternode para vários Masternodes.*

Feche sua carteira QT .

## <a name="masternodeconf"></a>Criar arquivo masternode.conf 

Lembre-se... isto é local. Verifique se o seu QT não está sendo executado.

Crie o arquivo `masternode.conf` no mesmo diretório da sua `wallet.dat`.

Copie a chave privada masternode private key e corresponda uma transação de saída colateral que contenha 1000 CRS.

*Nota: A chave privada masternode **não** igual a chave privada da carteira. **Nunca** coloque a chave privada da sua carteira no arquivo masternode.conf. Isto é quase equivalente a colocar seus 1000 CRS no servidor remoto e derrota o propósito de uma configuração quente/fria.*

### Obter a saída colateral

Abra a sua carteira QT Wallet e vá até console (no menu selecione `Tools` => `Debug Console`)

Emita o seguinte:

```masternode outputs```

Anote o hash (que é o seu collateral_output) e o index.

### Informe seus detalhes Masternode no arquivo masternode.conf 
[No repositório do criptoreal no github](https://github.com/criptoreal/criptoreal/blob/master/doc/masternode_conf.md)

`masternode.conf` é um arquivo de texto separado em espaços. Cada linha consiste em um alias, endereço IP seguido por porta, chave privada masternode, id de transação de saída colateral e índice de saída colateral.

```
alias ipaddress:port masternode_private_key collateral_output collateral_output_index
```

Examplo:

```
mn01 127.0.0.1:5511 93HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg 2bcd3c84c84f87eaa86e4e56834c92927a07f9e18718810b92e0d0324456a67c 0
mn02 127.0.0.2:5511 93WaAb3htPJEV8E9aQcN23Jt97bPex7YvWfgMDTUdWJvzmrMqey aa9f1034d973377a5e733272c3d0eced1de22555ad45d6b24abadff8087948d4 0
```

## Atualizar criptoreal.conf no servidor

Se você gerou uma nova chave privada masternode, você precisará atualizar os arquivos remotos `criptoreal.conf`.

Desligue o daemon e depois edite o arquivo.

```nano .criptoreal/criptoreal.conf```

### Editar o masternodeprivkey

Se você gerou uma nova chave privada masternode, você precisará atualizar o valor `masternodeprivkey` no seu arquivo remoto `criptoreal.conf`.

## Iniciar seus Masternodes

### Remoto

Se o seu servidor remoto não estiver sendo executado, incie seu daemon remoto como faria normalmente. 

Você pode confirmar que o servidor remoto está no bloco correto emitindo

```criptoreal-cli getinfo```

e comparando com o explorador oficial em https://explorer.criptoreal.org/chain/Criptoreal

### Local

Finalmente... hora de começar do local.

#### Abra sua carteira QT 

No menu selecione `Tools` => `Debug Console`

Se você deseja rever sua configuração do arquivo `masternode.conf` antes de iniciar os Masternodes, informe o seguinte no Debug Console:

```masternode list-conf```

Faça o teste eye-ball. Se estiver satisfeito, você pode começar seus Masternodes de duas maneiras.

1. `masternode start-alias [alias_from_masternode.conf]`  
Examplo ```masternode start-alias mn01```
2. `masternode start-many`

## Verificar se os Masternodes realmente iniciaram

### Remoto

Emitir comando `masternode status`
Deve retornar para você algo parecido com isto:
```
criptoreal-cli masternode status
{
    "outpoint" : "<collateral_output>-<collateral_output_index>",
    "service" : "<ipaddress>:<port>",
    "pubkey" : "<1000 CRS address>",
    "status" : "Masternode successfully started"
}
```
O resultado do comando deve ter  "_Masternode successfully started_" no campo `status` agora. Se ao invés disso mostra "_not capable_", você deve verifiar suas configurações novamente.

### Local

Busque seus Masternodes em https://criptorealninja.pl/masternodes.html

_Dica: Salve nos favoritos, você definitivamente vai usar muito este site._
