Contribuindo para o Criptoreal Core
============================

O projeto Criptoreal opera um modelo de colaborador aberto, onde qualquer um é convidado a contribuir para o desenvolvimento na forma de revisões, testes e patches. Este documento explica o processo prático e as diretrizes para contribuir.

Primeiramente, em termos de estrutura, não existe um conceito particular de “desenvolvedores principais” no sentido de pessoas privilegiadas. O código aberto geralmente gira em torno da meritocracia, onde os contribuintes de longo prazo ganham mais confiança da comunidade de desenvolvedores. Entretanto, alguma hierarquia é necessária para fins práticos. Como tal, existem "mantenedores" do respositório que são responsáveis pela incorporação de pull requests assim como um “mantenedor líder” que é responsável pelo ciclo de lançamento, fusão geral, moderação e nomeação dos outros mantenedores.


Fluxo de trabalho do Colaborador
--------------------

A base de código é mantida usando o  “fluxo de trabalho do colaborador” onde todos, sem exceção contribuem com propostas de patch usando  “pull requests”. Isto facilita a contribuição social, os testes e as revisões de pares.

Para contribuir com um patch, o fluxo de trabalho deve ser o seguinte::

  - Repositório Fork 
  - Criar um tópico
  - Enviar patches

As convenções de codificação do projeto localizadas em [doc/developer-notes.md](doc/developer-notes.md) devem ser respeitadas.

Em geral [os commits devem ser atômicos](https://en.wikipedia.org/wiki/Atomic_commit#Atomic_commit_convention) e os diffs devem ser fáceis de ler. Por este motivo, não misture nenhuma correção de formatação ou movimentos de código com mudanças de código reais.

As mensagens Commit devem ser detalhadas por padrão consistindo uma linha de assunto curto (50 caracteres no máximo), uma linha em branco e um texto explicativo detalhado com parágrafo(s) separado(s); a menos que o título seja auto-explicativo (como "Erro corrigido no main.cpp") então uma única linha de título é suficiente. As mensagens commit devem ser úteis para as pessoas que leem seu código no futuro, então explique o raciocínio das suas decisões. Maiores explicações [aqui](http://chris.beams.io/posts/git-commit/).

Se uma commit específica faz referência a outro problema, por favor adicione a referência, por exemplo, "refs #1234", ou "correções #4321". Usar palavras-chave "corrigir ou fechar" fará com que o problema correspondente seja fechado quando a pull request for mesclada.

Por favor veja o [manual Git](https://git-scm.com/doc) para mais informações sobre o Git.

  - Fazer mudanças no seu fork
  - Criar pull request

O título da pull request deve ser prefixado pelo componente ou área em que o pull request afeta. Examplos:

    Consenso: Adciona novo opcode para BIP-XXXX OP_CHECKAWESOMESIG
    Net: Automaticamente cria serviço oculto, escuta no Tor
    Qt: Adiciona botão bump
    Trivial: fix typo

Se uma pull request não for especificamente considerada para fusão (ainda), por favor inicie o título com [WIP] ou use [Listas de Tarefas](https://github.com/blog/1375-task-lists-in-gfm-issues-pulls-comments) no corpo da pull request para indicar tarefas que estão pendentes.

O corpo de uma pull request deve conter uma descrição suficiente sobre o que o patch faz em conjunto com qualquer justificativa/raciocínio. Você deve incluir referências a qualquer discussão (por exemplo, outros tickets ou lista de discussões).

Nesta fase, deve-se esperar comentários e reviews de outros contribuidores. Você pode adicionar mais commits para sua pull request, mantendo-os localmente ou enviando para o seu fork até que esteja satisfeito com todo o feedback. Se a sua pull request for aceita para fusão, um mantenedor pode solicitar que você revise seus commits antes da fusão. O tempo necessário para a revisão de pares é imprevisível e irá variar de acordo com cada patch.


Filosofia Pull Request
-----------------------

Os patches devem sempre ser focados. Por exemplo, uma pull request pode adicionar um recurso, corrigir um bug, ou um código de refator; mas não uma mistura. Por favor, evite também pull requests que tenham que fazer muita coisa, são excessivamente grandes ou execessivamente complexas pois isso dificulta a revisão.


### Recursos

Ao adicionar um novo recurso, deve-se considerar a dívida técnica a longo prazo e a manutenção que este recurso pode requerer após a inclusão. Antes de propor um novo recurso que exigirá manutenção, considere se você está disposto a mantê-lo (incluindo correção de bugs). Se os recursos ficarem órfãos sem manutenção no futuro, eles podem ser removidos pelo mantenedor do repositório.


### Refatoração

Refatoração é uma parte necessária da evolução de qualquer projeto de software. As seguintes diretrizes abrangem pull requests de refatoração para o projeto.

Existem três categorias de refatoração, apenas movimento de código, correções de estilo de código, refatoração de código. Em geral, os pull requests de refatoração não devem misturar estes três tipos de atividade, a fim de facilitar a revisão. Em todos os casos, os PRs de refatoração não devem alterar o comportamento do código dentro do pull request (os bugs devem ser preservados como estão).

Os mantenedores do projeto visam uma rápida reviravolta nos pull requests de refatoração, portanto, sempre que possível, mantê-los curtos e fáceis de verificar. 


Processo de "Tomada de Decisão" 
-------------------------

O seguinte se aplica a mudanças de código no projeto do Criptoreal Core (e projetos relacionados como libsecp256k1), e não deve ser confundido com as mudanças gerais do consenso do protocolo da rede Criptoreal.

Se um pull request é incorporado ao Criptoreal Core ele fica com os mantenedores e os líderes do projeto.

Os mantenedores levarão em consideração se um patch estiver em conformidade com os princípios gerais do projeto; se atende aos padrões minimos de inclusão; e julgará o consenso geral dos contribuintes.

Em geral, todas as pull requests devem:

  - ter um caso de uso claro, corrgir um erro ou servir o bem maior do projeto (por exemplo refatoração ou modularização);
  - ser bem revisado pelos pares;
  - ter testes unitários e funcionais, quando apropriado;
  - seguir as diretrizes de estilo de código;
  - não quebrar o conjunto de testes existentes;
  - onde os erros são corrigidos, sempre que possível, deve haver testes unitários que demonstrem o erro e também provar a correção. Isto ajuda a evitar a regressão.

Patches que mudam as regras de consenso do Criptoreal são consideravelmente mais envolvidas do que o normal pois afetam todo o ecossistema e portanto, devem ser precedidos por extensos debates na lista de discussão e ter um BIP numerado. Embora cada caso seja diferente, um deve estar preparado para gastar mais tempo e esforço do que para outros tipos de patches, devido ao aumento dos requisitos de revisão por pares e compilação.


### Revisão de pares

Qualquer pessoa pode participar da revisão de pares, que é expresa pelos comentários na pull request. Normalmente, os revisores irão rever o código para erros óbvios, além de testar o conjunto de correções e opinar sobre os méritos técnicos do patch. Os mantenedores do projeto levam em consideração a avaliação dos pares ao determinar se há consenso para realizar a fusão de um pull request (lembre-se de que as discussões podem ser feitas no github, lista de email e IRC). A seguinte linguagem deve ser usada nos comentários da  pull request:

  - ACK significa "Eu testei o código e concordo que deva ser mesclado";
  - NACK significa "Eu não concordo que isso deve sofrer fusão", e deve estar acompanhado de uma justificativa técnica sólida. NACKs sem o acompanhamento de algum motivo podem ser desconsiderados;
  - utACK significa "Eu não testei o código, mas o revisei e parece OK, Eu concordo que pode ser mesclado";
  - Conceito ACK significa "Eu concordo no princípio geral desta pull request";
  - Nit refere-se a problemas triviais, muitas vezes não bloqueáveis.

Os revisores devem incluir o hash que eles revisaram em seus comentários.

Os mantenedores do projeto se resevam o direito de pesar as opiniões dos revisores de pares usando o julgamento do senso comum e também podem ter peso com base na meritocracia: Aqueles que demonstraram um compromisso e uma compreensão mais profundos em relação ao projeto (ao longo do tempo) ou tem conhecimentos claros de domínio, podem, naturalmente ter mais peso, como seria de se esperar em todos os setores da vida.

Onde um conjunto de patches afetar o código crítico de consenso, a dificuldade será muito maior em termos de discussão e requisitos de revisão por pares, tendo em mente que os erros podem ser muito onerosos para a comunidade em geral. Isto inclui refatoração de código crítico de consenso.

Quando um conjunto de patches propõe mudar o consenso Criptoreal, isto deve ser discutido extensivamente na lista de emails e IRC, ser acompanhado por um BIP amplamente discutido e ter um consenso técnico amplamente percebido de ser uma mudança que vale a pena, com base no julgamento dos mantenedores.


Política de Lançamento
--------------

O líder do projeto é o gerente de lançamento para cada versão Criptoreal Core.
