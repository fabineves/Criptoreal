Estratégia de desenvolvimento e integração Qt Multiwallet 
===================================================

Para suportar o carregamento de múltiplas carteiras em bitcoin-qt, serão necessárias algumas mudanças na arquitetura UI.
Felizmente, apenas quatro dos arquivos do projeto existente são afetados por esta mudança.

Duas novas classes foram implantadas em dois novos pares de arquivos .h/.cpp, com grande parte da funcionalidade que era implementada anteriormente na classe BitcoinGUI sendo movida para novas classes.

Os dois arquivos existentes mais afetados, de longe, são bitcoingui.h e bitcoingui.cpp, assim como a classe BitcoinGUI exigirá algumas adaptações importantes.

O único que exige algumas pequenas alterações é o criptoreal.cpp.

Finalmente, dois novos cabeçalhos e arquivos fonte terão que ser adicionados ao bitcoin-qt.pro.

Mudanças na classe BitcoinGUI
---------------------------
A principal mudança na classe BitcoinGUI diz respeito à instância QStackedWidget chamada centralWidget. Este widget possui cinco páginas: overviewPage, transactionsPage, addressBookPage, receiveCoinsPage, e sendCoinsPage.

Uma nova classe chamada *WalletView* herdada de QStackedWidget foi escrita para lidar com todas as renderizações e atualizações destas visualizações de página. Além de possuir estas cinco visualizações de página, WalletView também possui um ponteiro para a instância de a WalletModel. Isto permite a construção de múltiplos objetos WalletView, cada um renderizando um carteira distinta.

Uma segunda classe chamada *WalletFrame* herdada de QFrame foi escrita como um recipiente para incorporar todos os controles relacionados a carteira no BitcoinGUI. Atualmente, contém as instâncias do WalletView para as carteiras e faz pouco mais do que transmitir mensagens do BitcoinGUI para o WalletView selecionado atualmente. É uma instância WalletFrame que toma o lugar do que costumava ser centralWidget no BitcoinGUI. O objetivo desta classe é permitir aprimoramentos futuros dos controles da carteira com uma mínima necessidade de modificações no BitcoinGUI, simplificando assim as fusões, reduzindo o risco de quebras em coisas de alto nível.

Mudanças no criptoreal.cpp
----------------------
criptoreal.cpp is é o ponto de entrada em para bitcoin-qt e como tal, irá exigir algumas modificações menores para fornecer ganchos para suporte de carteira múltipla. O mais importante será a forma como a instância WalletModels os passa para BitcoinGUI . Anteriormente, o BitcoinGUI manteve um ponteiro para uma única instância de um WalletModel.
A mudança inicial necessária é muito simples: ao invés de chamar `window.setWalletModel(&walletModel);` realizamos o seguinte em dois passos:

	window.addWallet("~Default", &walletModel);
	window.setCurrentWallet("~Default");

O parâmetro da string é apenas um nome arbitrário dado para a carteira padrão. Foi feito um tilde para evitar colisões de nome no futuro com carteiras adicionais.

A chamada para desligamento `window.setWalletModel(0)` também foi removida. No seu lugar agora usamos: 
window.removeAllWallets();
