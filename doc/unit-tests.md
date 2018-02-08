Compilando/executando testes de unidade
------------------------------------

Testes de unidade serão compilados automaticamente se as dependências forem atendidas em `./configure` e se os testes não foram explicitamente desabilitados.

Após a configuração, eles podem ser executados com `make check`.

Para executar os testes do criptoreald tests manualmente, carregue `src/test/test_criptoreal`.

Para adicionar mais testes criptoreald, adicione as funções`BOOST_AUTO_TEST_CASE` para os arquivos .cpp existentes no diretório `test/` directory ou adicione novos arquivos .cpp que implementam novas seções BOOST_AUTO_TEST_SUITE.

Para executar os testes criptoreal-qt manualmente, carregue `src/qt/test/test_criptoreal-qt`

Para adicionar mais testes criptoreal-qt, adicione-os ao diretório `src/qt/test/` e ao arquivo `src/qt/test/test_main.cpp`.
