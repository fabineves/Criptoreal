## Informação Técnica InstantSend

InstantSend foi integrado no Daemon Core de duas maneiras:
* notificações "push" (ZMQ e opção `-instantsendnotify` cmd-line/config );
* Comandos RPC.

#### ZMQ

Quando ocorre um "Bloqueio de Transação" o hash da transação relacionada é transmitida através do ZMQ usando os canais `zmqpubrawtxlock` e `zmqpubhashtxlock`.

* `zmqpubrawtxlock`: publica a transação raw quando bloqueada via InstantSend
* `zmqpubhashtxlock`: ppublica a transação hash quando bloqueada via InstantSend

Este mecanismo foi integrado no Bitcore-Node-Criptoreal, que permite que a notificação seja transmitida através da Insight API usando uma destas duas formas:
* WebSocket: [https://github.com/criptoreal/insight-api-criptoreal#web-socket-api](https://github.com/criptoreal/insight-api-criptoreal#web-socket-api)
* API: [https://github.com/criptoreal/insight-api-criptoreal#instantsend-transactions](https://github.com/criptoreal/insight-api-criptoreal#instantsend-transactions)

#### Opção de linha de comando

Quando uma transação da carteira InstantSend transaction é bloqueada com sucesso, um comando shell fornecido nesta opção é executado (`%s` em `<cmd>` é substituído por TxID):

```
-instantsendnotify=<cmd>
```

#### RPC

Os detalhes relativos a um "bloqueio de transação" observado, também podem ser recuperados através do RPC, é importante, no entanto, entender o mecanismo subjacente.

Por padrão, o daemon Criptoreal Core será iniciado usando a seguinte constant:

```
static const int DEFAULT_INSTANTSEND_DEPTH = 5;
```

Este valor pode ser substituído passando o seguinte argumento para o daemon Criptoreal Core:

```
-instantsenddepth=<n>
```

A chave é entender que este valor indica o número de "confirmações" que um sincronismo de transação bem sucedido representa. Quando os comandos RPC da carteira que suportam os parâmetros`minconf` e `addlockconf` (assim como `listreceivedbyaddress`) são executados e  `addlockconf` és `true`, então o atributo `instantsenddepth` é levado em consideração ao retornar as informações sobre a transação. Neste caso, o valor no campo `confirmations` que você vê através do RPC é o número de `"Blockchain Confirmations" + "InstantSend Depth"` (assumindo que os fundos foram enviados via InstantSend).

Também existe um campo de nome `instantlock` (que está presente em comandos como o `listsinceblock`). O valor neste campo indica se uma determinada transação está bloqueada via InstantSend.

**Examplos**

1. `listreceivedbyaddress 0 true`
   * Transação InstantSend acabou de ocorrer:
        * confirmations: 5
   * Transação InstantSend transaction recebeu uma confirmação da blockchain:
        * confirmations: 6
   * Uma transação não-InstantSend transaction acabou de ocorrer:
        * confirmations: 0
   * Uma transação não-InstantSend transaction recebeu uma confirmação da blockchain:
        * confirmations: 1

2. `listreceivedbyaddress 0`
   * Transação InstantSend acabou de ocorrer:
        * confirmations: 0
   * Transação InstantSend transaction recebeu uma confirmação da blockchain:
        * confirmations: 1
   * Uma transação não-InstantSend transaction acabou de ocorrer:
        * confirmations: 0
   * Uma transação não-InstantSend transaction recebeu uma confirmação da blockchain:
        * confirmations: 1

3. `listsinceblock`
    * Transação InstantSend acabou de ocorrer:
        * confirmations: 0
        * instantlock: true
    * Transação InstantSend transaction recebeu uma confirmação da blockchain:
        * confirmations: 1
        * instantlock: true
    * Uma transação não-InstantSend transaction acabou de ocorrer:
        * confirmations: 0
        * instantlock: false
    * Uma transação não-InstantSend transaction recebeu uma confirmação da blockchain:
        * confirmations: 1
        * instantlock: false
