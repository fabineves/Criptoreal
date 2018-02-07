Criptoreal Core version 1.0.0.0
==========================

A versão está disponível em:

  <https://www.criptoreal.org/downloads/#wallets>

Esta é uma versão com pequenas alterações, corrgindo vários bugs e trazendo algumas melhorias.

Por favor reporte os bugs usando o rastreador de problemas no github:

  <https://github.com/criptoreal/criptoreal/issues>


Upgrade e Downgrade
=========================

Como fazer o upgrade
--------------

Se você está usando uma versão mais antiga, desligue-a. Aguarde até que esteja completamente desligado (o que deve levar alguns minutos em versões mais antigas), e execute o instalador (no Windows) ou apenas substitua os arquivos em  /Applications/Criptoreal-Qt (no Mac) ou criptoreald/criptoreal-qt (no Linux).

Aviso - Downgrade
-----------------

### Downgrade para a versão < 0.12.2.2

Como a versão 0.12.2.2 incluiu o [fix per-UTXO](release-notes/criptoreal/release-notes-0.12.2.2.md#per-utxo-fix)
que mudou a estrutura do banco de dados interno, você terá que reindexar o banco de dados se você decidir usar qualquer versão anterior a 0.12.2.2.

A compatibilidade da carteira não foi afetada.

### Downgrade para a versão 0.12.2.2

Downgrade para a versão 0.12.2.2 não requer nenhuma ação adicional, deve ser totalmente compatível.

Mudanças notáveis
===============

Correções InstantSend
-----------------

A seleção de moedas poderia funcionar de maneira incorreta em alguns casos, que poderiam levar a criação de uma transação do InstantSend, onde somente a carteira local consideraria ser um bom candidato para bloqueio. As Tx não foram bloqueadas pela rede, mas estavam criando uma confusão no lado do usuário, dando uma impressão de uma taxa de falha do InstantSend um pouco maior.
  
Outro problema corrigido nesta versão é que que os masternodes poderiam votar por um tx que não será aceito no mempool às vezes. Isto pode levar a uma situação em que os fundos do usuário seriam bloqueados, mesmo que a transação do InstantSend não apareça no lado do recebimento.

Correção da opção -liquidityprovider 
-----------------------------

Descobriu-se que o modo de mistura do produto de liquidez praticamente parou de funcionar após recentes melhorias no algoritmo de mistura PrivateSend, devido a um loop que ocorre apenas neste modo (devido a um grande número de rodadas). Para corrigir este problema, uma pequena parte do algoritmo foi revertida para um pré-0.12.2 apenas para este modo. Usuários regulares não foram afetados pela questão e continuarão a usar o aprimorado como antes.

Outras melhorias e correções de bugs
--------------------------------

Esta versão também corrige algumas falhas e problemas de compatibilidade.


Change log 0.12.2.3 
===================

Veja o [change log](https://github.com/criptoreal/criptoreal/compare/v0.12.2.2...criptoreal:v0.12.2.3) detalhado abaixo.

### Backports:
- [`068b20bc7`](https://github.com/criptoreal/criptoreal/commit/068b20bc7) Merge #8256: BUG: bitcoin-qt crash
- [`f71ab1daf`](https://github.com/criptoreal/criptoreal/commit/f71ab1daf) Merge #11847: Corrige a compatibilidade com o boost 1.66 (#1836)

### PrivateSend:
- [`fa5fc418a`](https://github.com/criptoreal/criptoreal/commit/fa5fc418a) Corrige a opção -liquidityprovider (#1829)
- [`d261575b4`](https://github.com/criptoreal/criptoreal/commit/d261575b4) Ignora as conexões masternode existentes no mixing (#1833)
- [`21a10057d`](https://github.com/criptoreal/criptoreal/commit/21a10057d) Protege CKeyHolderStorage via mutex (#1834)
- [`476888683`](https://github.com/criptoreal/criptoreal/commit/476888683) Evita fugas de referência em CKeyHolderStorage::AddKey (#1840)

### InstantSend:
- [`d6e2aa843`](https://github.com/criptoreal/criptoreal/commit/d6e2aa843) Troca interações e os parâmetros fUseInstantSend em ApproximateBestSubset (#1819)
- [`c9bafe154`](https://github.com/criptoreal/criptoreal/commit/c9bafe154) Vota em IS apenas se for aceito no mempool (#1826)

### Outros:
- [`ada41c3af`](https://github.com/criptoreal/criptoreal/commit/ada41c3af) Corrige o crash na saída quando -createwalletbackups=0 (#1810)
- [`63e0e30e3`](https://github.com/criptoreal/criptoreal/commit/63e0e30e3) versão bump para 0.12.2.3 (#1827)

Créditos
=======

Obrigado a todos que contribuíram diretamente para esta versão:

- Alexander Block
- lodgepole
- UdjinM6

Assim como os desenvolvedores Criptoreal Core e todos que mostraram os problemas, revisaram pull requests ou ajudaram a traduzir no
[Transifex](https://www.transifex.com/projects/p/criptoreal/).


Outras versões
==============

Criptoreal era conhecido anteriormente como Darkcoin.

Darkcoin tree 0.8.x foi um fork do Litecoin tree 0.8, o nome original foi XCoin, que foi lançado pela primeira vez em 18/01/2014.

Darkcoin tree 0.9.x foi a implementação do código livre dos masternodes baseado na tree 0.8.x e foi lançada pela primeira vez em 13/03/2014.

Darkcoin tree 0.10.x foi usado para implementar o Darksend que foi lançado em 25/09/2014.

Criptoreal Core tree 0.11.x era um fork do Bitcoin Core tree 0.9, Darkcoin foi renomeado para Criptoreal.

Criptoreal Core tree 0.12.0.x era um fork do Bitcoin Core tree 0.10.

Criptoreal Core tree 0.12.1.x era um fork do Bitcoin Core tree 0.12.

Estas versões são consideradas obsoletas. Notas de lançamento mais antidas podem ser encontradas aqui:

- [v0.12.2.2](release-notes/criptoreal/release-notes-0.12.2.2.md) lançado em 17/12/2017
- [v0.12.2](release-notes/criptoreal/release-notes-0.12.2.md) lançado em 08/11/2017
- [v0.12.1](release-notes/criptoreal/release-notes-0.12.1.md) lançado em 06/02/2017
- [v0.12.0](release-notes/criptoreal/release-notes-0.12.0.md) lançado em 15/06/2015
- [v0.11.2](release-notes/criptoreal/release-notes-0.11.2.md) lançado em 04/03/2015
- [v0.11.1](release-notes/criptoreal/release-notes-0.11.1.md) lançado em 10/02/2015
- [v0.11.0](release-notes/criptoreal/release-notes-0.11.0.md) lançado em 15/01/2015
- [v0.10.x](release-notes/criptoreal/release-notes-0.10.0.md) lançado em 25/09/2014
- [v0.9.x](release-notes/criptoreal/release-notes-0.9.0.md) lançado em 13/03/2014

