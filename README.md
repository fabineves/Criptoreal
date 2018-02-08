Criptoreal Core staging tree 0.12.2
===============================

`master:` [![Build Status](https://travis-ci.org/criptoreal/criptoreal.svg?branch=master)](https://travis-ci.org/criptoreal/criptoreal) `develop:` [![Build Status](https://travis-ci.org/criptoreal/criptoreal.svg?branch=develop)](https://travis-ci.org/criptoreal/criptoreal/branches)

https://www.criptoreal.org


O que é Criptoreal?
----------------

Criptoreal é uma nova moeda digital experimental que permite o anonimato, pagamentos instantâneos para qualquer pessoa, em qualquer lugar do mundo. Criptoreal usa a tecnologia peer-to-peer para operar sem uma autoridade central: gerenciamento de transações e emissão de dinheiro são realizadas coletivamente pela rede. Criptoreal Core é o nome do software de código aberto que permite o uso desta moeda.

Para mais informações, assim como uma versão binária do software Criptoreal Core que você pode usar imediatamente, veja https://www.criptoreal.org/get-criptoreal/.


Licença
-------

Criptoreal Core é disponibilizada nos termos da licença MIT. Veja [COPYING](COPYING) para mais informações ou veja https://opensource.org/licenses/MIT.

Processo de desenvolvimento
-------------------

O `master` deve ser estável. O desenvolvimento normalmente é feito em ramos separados. [Tags](https://github.com/criptoreal/criptoreal/tags) são criadas para indicar novas versões oficiais e estáveis do Criptoreal Core.

O fluxo de trabalho de contribuição é descrito em [CONTRIBUTING.md](CONTRIBUTING.md).

Testando
-------

Testes e revisão de código são o gargalo para o desenvolvimento; obtemos mais pull requests do que podemos revisar e testar em curto prazo. Por favor, seja paciente e ajude testando as pull requests de outras pessoas e lembre-se de que este é um projeto crítico de segurança, onde qualquer erro pode custar muito dinheiro às pessoas.

### Testes automatizados

Desenvolvedores são fortemente encorajados a escrever [testes unitários](/doc/unit-tests.md) para códigos novos, e para enviar novos testes de unidades para um código antigo. Testes de unidade podem ser compilados e executados (assumindo que eles não estavam desabilitados na configuracão) com: `make check`

Também existem testes [de regressão e integração](/qa) na interface RPC, escritos em Python, que são executados automaticamente no servidor de compilação. Estes testes podem ser executados (se as [dependências de teste](/qa) estiverem instaladas) com: `qa/pull-tester/rpc-tests.py`

O sistema Travis CI garante que todas as pull requests sejam compiladas para Windows e Linux, OS X, e que os testes de unidade e sanidade sejam executados automaticamente.

### Teste manual de garantia de qualidade (QA) 

As alterações devem ser testadas por alguém que não o desenvolvedor que escreveu o código. Isto é especialmente importante para mudanças de grande ou alto risco. É útil adicionar um plano de teste na descrição da pull request se o teste das mudanças não for direto.

Traduções
------------

Alterações em traduções e novas traduções podem ser enviadas para [a página do Criptoreal Core no Transifex](https://www.transifex.com/projects/p/criptoreal/).

Traduções são periodicamente retiradas da Transifex e mescladas para o repositório git. Veja o [processo de tradução](doc/translation_process.md) para detalhes sobre como isso funciona.

**Importante**: Nós não aceitamos mudanças de tradução, pois as pull requests no GitHub do Transifex automaticamente substituiria os arquivos.

Os tradutores também devem seguir o [forum](https://www.criptoreal.org/forum/topic/criptoreal-worldwide-collaboration.88/).
