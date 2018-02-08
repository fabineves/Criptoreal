SUPORTE TOR NO CRIPTOREAL
======================

É possível executar Criptoreal como um serviço oculto Tor, e se conectar a estes serviços.

As instruções a seguir indicam que você possui um proxy Tor que roda na porta 9050. Muitas distribuições são padrão para ter um proxy SOCKS que escuta na porta 9050, mas outros talvez não. Em particular, o navegador Tor é padrão para ouvir na porta 9150. Veja [Tor Project FAQ:TBBSocksPort](https://www.torproject.org/docs/faq.html.en#TBBSocksPort) para saber como configurar Tor corretamente.


1. Execute criptoreal atras de um proxy Tor
-------------------------------------------

O primeiro passo é executar Criptoreal atrás de um proxy Tor. Isto já permitirá que todas as conexões de saída sejam anônimas, mas é possível fazer mais.

	-proxy=ip:port  Configura o servidor proxy. Se SOCKS5 for selecionado (padrão), este servidor proxy
	                será usado para tentar alcançar os endereços .onion também.

	-onion=ip:port  Define o servidor proxy para uso em serviços ocultos. Você não precisa
	                definir isso se for o mesmo que -proxy. Você pode usar -noonion
	                para desativar explicitamente o acesso ao serviço oculto.

	-listen         Ao usar -proxy, a escuta é desabilitada por padrão. Se você deseja
	                eecutar um serviço oculto (veja a próxima seção), você precisará ativar
	                explicitamente.

	-connect=X      Quando estiver por trás de um proxy Tor, você pode especificar endereços .onion
	-addnode=X      ao invés de endereços IP addresses ou hostnames nestes parâmetros. Requer
	-seednode=X     SOCKS5. No modo Tor, tais endereços podem ser trocados com outros
	                nós P2P.

	-onlynet=tor    Conecta somente aos nós .onion de solta as conexões IPv4/6.

Um exemplo de como iniciar o cliente se o proxy Tor estiver sendo executado no local host na porta 9050 e permite somente a conexão de nós .onion:

	./criptoreald -onion=127.0.0.1:9050 -onlynet=tor -listen=0 -addnode=ssapp53tmftyjmjb.onion

Em uma situação típica, isto basta para executar atrás de um proxy Tor:

	./criptoreald -proxy=127.0.0.1:9050


2. Executar criptoreal em um servidor oculto
--------------------------------------------

Se você configurar seu sistema Tor em conformidade, é possível tornar seu nó também acessível a partir da rede Tor. Adicione estas linhas ao seu /etc/tor/torrc (ou arquivo de configuração equivalente):

	HiddenServiceDir /var/lib/tor/criptoreal-service/
	HiddenServicePort 5511 127.0.0.1:5511
	HiddenServicePort 15511 127.0.0.1:15511

O diretório pode ser diferente, claro, mas (ambos) números de porta devem ser iguais a porta de escuta P2P do seu criptoreald (5511 por padrão).

	-externalip=X   Você informar ao criptoreal sobre o endereço acessível ao público usando
	                esta opção, e esta pode ser um endereço .onion. Com a configuração 
	                acima, você pode encontrar o seu endereço onion em
	                /var/lib/tor/criptoreal-service/hostname. Endereços Onion recebem preferência
	                para seu nó anunciar-se com, para conexões provenientes de endereços
	                não roteáveis (como 127.0.0.1, onde o proxy Tor geralmente é executado).

	-listen         Você precisa habilitar a audição de conexões recebidas, pois estas
	                estão desativadas por padrão quando estão por trás de um proxy.

	-discover       Quando -externalip é especificado, nenhuma tentativa é feita para descobrir endereços locais
	                IPv4 ou IPv6. Se você deseja executar uma pilha dupla, acessível tanto de
	                Tor e IPv4 (ou IPv6), você precisará passar seus outros endereços 
	                usando -externalip, ou explicitamente habilitar -discover.
	                Note que ambos os endereços de um sistema de pilha dupla podem ser facilmente
	                vinculáveis usando análise de tráfego.

Em uma situação típica, onde você só pode ser alcançado via Tor, isto deve bastar:

	./criptoreald -proxy=127.0.0.1:9050 -externalip=ssapp53tmftyjmjb.onion -listen

(obviamente, substitua o endereço Onion com o seu próprio). Deve notar-se que você ainda escuta em todos os dispositivos e outro nó pode estabelecer uma conexão inteligente, quando conhece seu endereço. Para mitigar isto, vincule o endereço do seu proxy Tor:

	./criptoreald ... -bind=127.0.0.1

Se você não se importa muito em esconder o seu nó, e quer ficar alcançável também no IPv4, use `discover`:

	./criptoreald ... -discover

e abra a porta 5511 no seu firewall (ou use -upnp).

Se você quiser usar Tor apenas para alcançar o endereço onion, mas não use como um proxy para comunicação IPv4/IPv6, use:

	./criptoreald -onion=127.0.0.1:9050 -externalip=ssapp53tmftyjmjb.onion -discover


3. Lista dos relays do Criptoreal Core Tor
------------------------------------

* [darkcoinie7ghp67.onion](http://darkcoinie7ghp67.onion/)
* [drktalkwaybgxnoq.onion](http://drktalkwaybgxnoq.onion/)
* [drkcoinooditvool.onion](http://drkcoinooditvool.onion/)
* [darkcoxbtzggpmcc.onion](http://darkcoxbtzggpmcc.onion/)
* [ssapp53tmftyjmjb.onion](http://ssapp53tmftyjmjb.onion/)
* [j2dfl3cwxyxpbc7s.onion](http://j2dfl3cwxyxpbc7s.onion/)
* [vf6d2mxpuhh2cbxt.onion](http://vf6d2mxpuhh2cbxt.onion/)
* [rj24sicr6i4vsnkv.onion](http://rj24sicr6i4vsnkv.onion/)
* [wrwx2dy7jyh32o53.onion](http://wrwx2dy7jyh32o53.onion/)
* [f5ekot4ajkbe23gt.onion](http://f5ekot4ajkbe23gt.onion/)
* [dshtord4mqvgzqev.onion](http://dshtord4mqvgzqev.onion/)


4. Automatically listen on Tor
--------------------------------

Começando com a versão 0.2.7.1 do Tor é possível, através da API do soquete de controle de Tor, criar e destruir programas escondidos "efêmeros" de forma pragmática. Criptoreal Core foi atualizado para utilizar isso.

Isto significa que se Tor estiver em execução (e a autorização adequada está disponível), o Criptoreal Core automaticamente cria um serviço oculto para ouvir, sem configuração manual. Isto afetará positivamente o número de nós .onion disponíveis.

Esta nova funcionalidade é ativada por padrão se o Criptoreal Core estiver ouvindo e uma conexão Tor possa ser feita. Isto pode ser configurado com  `-listenonion`, `-torcontrol` e `-torpassword`. Para mostrar informações detalhadas de depuração, use `-debug=tor`.
