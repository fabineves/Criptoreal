Configuração Masternode
=======================

Criptoreal Core permite o controle de vários masternodes remotos de uma única carteira. A carteira precisa ter um resultado colateral válido de 1000 moedas para cada masternode e usa um arquivo de configuração chamado `masternode.conf` que pode ser encontrado no seguinte diretório de dados (dependendo do seu sistema operacional):
 * Windows: %APPDATA%\Criptoreal\
 * Mac OS: ~/Library/Application Support/Criptoreal/
 * Unix/Linux: ~/.criptoreal/

`masternode.conf` é um arquivo de texto separado com espaços. Cada linha consiste em um alias, endereço IP, seguido por porta, chave privada masternode, ID de transação colateral de saída e índice de saída colateral.

Example:
```
mn1 127.0.0.2:15511 93HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg 7603c20a05258c208b58b0a0d77603b9fc93d47cfa403035f87f3ce0af814566 0
mn2 127.0.0.4:15511 92Da1aYg6sbenP6uwskJgEY2XWB5LwJ7bXRqc3UPeShtHWJDjDv 5d898e78244f3206e0105f421cdb071d95d111a51cd88eb5511fc0dbf4bfd95f 1
```

No exemplo acima:
* o colateral de 1000 CRS para `mn1` é a saída `0` da transação [7603c20a05258c208b58b0a0d77603b9fc93d47cfa403035f87f3ce0af814566](https://test.explorer.criptoreal.org/tx/7603c20a05258c208b58b0a0d77603b9fc93d47cfa403035f87f3ce0af814566)
* o colateral de 1000 CRS para `mn2` é a saída `1` da transação [5d898e78244f3206e0105f421cdb071d95d111a51cd88eb5511fc0dbf4bfd95f](https://test.explorer.criptoreal.org/tx/5d898e78244f3206e0105f421cdb071d95d111a51cd88eb5511fc0dbf4bfd95f)

_Nota: IPs como 127.0.0.* não são permitidos, estamos usando-os aqui apenas para fins explicativos. Certifique-se de ter IP's remotos alcançáveis no seu arquivo`masternode.conf`._

Os seguintes comandos RPC estão disponíveis (digite `help masternode` no Console para mais informações):
* list-conf
* start-alias \<alias\>
* start-all
* start-missing
* start-disabled
* outputs
