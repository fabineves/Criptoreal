NOTA : 12.1 -- REESCREVER


Budget Masternode API
=======================

Criptoreal agora suporta budgets descentralizados completos que são pagos diretamente na blockchain via superblocks uma vez por mês.

Os budgets passam por uma série de etapas antes de serem pagos:
 * preparar - criar uma transação especial que destrói moedas para fazer uma proposta
 * enviar - propagar a transação para pares na rede
 * votação - lobby para votação na sua proposta
 * Obter votos suficientes - faça isto dentro do budget
 * finalização - no final de cada período de pagamento, as propostas são ordenadas, depois compiladas em um budget finalizado
 * votação finalizada de budget - Os masternodes que concordam com a finalização irão votar neste budget
 * pagamento - o budget final vencedor é pago

1. Preparar a transação colateral
--

Nesta transação, preparamos o colateral para "_cool-project_". Esta proposta pagará _1200_ CRS, _12_ vezes ao longo de um ano, totalizando _24000_ CRS.

**Aviso: Se você modificar qualquer campo dentro deste comando, a transação colateral se torna inválida.**

Formato: ```mngovernance prepare proposal-name url payment-count block-start criptoreal-address monthly-payment-criptoreal```

Examplo: ```mngovernance prepare cool-project http://www.cool-project/one.json 12 100000 y6R9oN12KnB9zydzTLc3LikD9cCjjQzYG7 1200 true```

Saída: ```464a0eb70ea91c94295214df48c47baa72b3876cfb658744aaf863c7b5bf1ff0```

Este é o hash colateral, copie esta saída para a próxima etapa.

2 Enviar proposta para a rede
--

Agora podemos enviar nossa proposta para a rede.

Formato: ```mngovernance submit proposal-name url payment-count block-start criptoreal-address monthly-payment-criptoreal fee-tx```

Examplo: ```mngovernance submit cool-project http://www.cool-project/one.json 12 100000 y6R9oN12KnB9zydzTLc3LikD9cCjjQzYG7 1200 464a0eb70ea91c94295214df48c47baa72b3876cfb658744aaf863c7b5bf1ff0```

Saída : ```a2b29778ae82e45a973a94309ffa6aa2e2388b8f95b39ab3739f0078835f0491```

Esta é sua proposta hash, em que os outros nós irão usar para votação.

3. Lobby para votos
--

Verifique duas vezes suas informações.

Formato: ```mngovernance getproposal proposal-hash```

Examplo: ```mngovernance getproposal a2b29778ae82e45a973a94309ffa6aa2e2388b8f95b39ab3739f0078835f0491```
￼
```
{
    "Name" : "cool-project",
    "Hash" : "a2b29778ae82e45a973a94309ffa6aa2e2388b8f95b39ab3739f0078835f0491",
    "FeeHash" : "464a0eb70ea91c94295214df48c47baa72b3876cfb658744aaf863c7b5bf1ff0",
    "URL" : "http://www.cool-project/one.json",
    "BlockStart" : 100000,
    "BlockEnd" : 100625,
    "TotalPaymentCount" : 12,
    "RemainingPaymentCount" : 12,
    "PaymentAddress" : "y6R9oN12KnB9zydzTLc3LikD9cCjjQzYG7",
    "Ratio" : 0.00000000,
    "Yeas" : 0,
    "Nays" : 0,
    "Abstains" : 0,
    "TotalPayment" : 14400.00000000,
    "MonthlyPayment" : 1200.00000000,
    "IsValid" : true,
    "fValid" : true
}
```

Se tudo estiver correto, você pode pedir votos de outros masternodes. Para votar em uma proposta, carregue uma carteira com o arquivo _masternode.conf_. Você não precisa acessar sua carteira fria para votar em propostas.

Formato: ```mngovernance vote proposal-hash [yes|no]```

Examplo: ```mngovernance vote a2b29778ae82e45a973a94309ffa6aa2e2388b8f95b39ab3739f0078835f0491 yes```

4.  Passar para o budget
--

Após conseguir votos suficientes, execute ```mngovernance projection``` para ver se conseguiu chegar ao budget. Se o seu budget foi finalizado neste momento, as propostas estarão nele. Nota: As propostas devem estar ativas pelo menos um dia na rede e receber 10% da rede masternode em votos sim para poder se qualificar (Ex. se existem 3500 masternodes, precisará de 350 votos sim.)

```mngovernance projection```:￼
```
{
    "cool-project" : {
        "Hash" : "a2b29778ae82e45a973a94309ffa6aa2e2388b8f95b39ab3739f0078835f0491",
        "FeeHash" : "464a0eb70ea91c94295214df48c47baa72b3876cfb658744aaf863c7b5bf1ff0",
        "URL" : "http://www.cool-project/one.json",
        "BlockStart" : 100000,
        "BlockEnd" : 100625,
        "TotalPaymentCount" : 12,
        "RemainingPaymentCount" : 12,
        "PaymentAddress" : "y6R9oN12KnB9zydzTLc3LikD9cCjjQzYG7",
        "Ratio" : 1.00000000,
        "Yeas" : 33,
        "Nays" : 0,
        "Abstains" : 0,
        "TotalPayment" : 14400.00000000,
        "MonthlyPayment" : 1200.00000000,
        "IsValid" : true,
        "fValid" : true
    }
}
```

5. Budget finalizado
--

```
"main" : {
        "FeeTX" : "d6b8de9a4cadfe148f91e8fe8eed407199f96639b482f956ae6f539b8339f87c",
        "Hash" : "6e8bbaba5113de592f6888f200f146448440b7e606fcf62ef84e60e1d5ac7d64",
        "BlockStart" : 100000,
        "BlockEnd" : 100000,
        "Proposals" : "cool-project",
        "VoteCount" : 46,
        "Status" : "OK"
    },
```

6. Seja pago
--

Quando o bloco ```1000000``` é alcançado, você receberá um pagamento de ```1200``` CRS para ```y6R9oN12KnB9zydzTLc3LikD9cCjjQzYG7```.

7. Lista de Comandos
--

Os seguintes comandos RPC são suportados:

 - mngovernance "command"... ( "passphrase" )
  - check              - Analise as propostas e remova os inválidos da lista de propostas
  - prepare            - Prepare a proposta assinando e criado o tx
  - submit             - Enviar a proposta para a rede
  - getproposalhash    - Obter proposta hash por nome da proposta
  - getproposal        - Mostrar proposta
  - getvotes           - Mostrar lista de votos por proposta
  - list               - Listar todas as propostas
  - nextblock          - Obtenha informações sobre o próximo superblock para o sistema de budget
  - nextsuperblocksize - Obtenha o tamanho do superblock para uma altura de bloqueio
  - projection         - Mostra a projeção das propostas que serão pagas no próximo ciclo
  - vote               - Vota em uma proposta por um único masternode (usando a configuração criptoreal.conf)
  - vote-many          - Vota em uma proposta por todos os masternodes (usando a configuração masternode.conf)
  - vote-alias         - Vota em uma proposta por alias
 - mnfinalbudget "command"... ( "passphrase" )
  - vote-many   - Vota em budget finalizado
  - vote        - Vota em budget finalizado
  - show        - Mostra todos os budgets finalizados existentes
  - getvotes    - Obtém informações de voto para cada budget finalizado
  - prepare     - Prepara manualmente um budget finalizado
  - submit      - Envia manualmente um budget finalizado

