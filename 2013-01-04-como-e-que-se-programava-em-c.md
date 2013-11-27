title: Como é que se programava em C mesmo?
hidden:

Parece que as sextas feiras são bons dias para questões existenciais :D
Cenas que eu devia saber fazer em C, mas não sei ou não me lembro ou já não funcionam de todo:

## string encoding/locale

- como é que detecto o encoding das strings que o sistema passa para o
  programa? e as paths do FS?
- como é que converto entre formatos? que tipo de dados uso para
  representação interna?
- Se usar utf8 para representar strings internamente que funções é que
  uso para manipular strings? Senão?
- iconv? libicu?

## File encoding

- Se abrir um ficheiro em que encoding é que ele está
- BOM BOM BOM
- E se for binário o que é que faço?

## E parsers?

- Eu sabia fazer parsers, commo é que faço agora

## Asynchronous input/output

- Isto até funciona bem com coisas como libevent2/libev, a parte
  realmente difícil é definir buffers para os canais de comunicação :D
  porque em protocolos async - bem são parsers :D

## Alguém sabe fazer maquinas de estados?

- Maquinas de estados eram a bimby do C
- Mais a sério, o facto do estado não estar implícito nos objectos (:S
  OOP) ajuda a desenhar o programa de forma decente - só precisei de
  uns 10 anos para perceber isto.

## FS e performance de leitura de ficheiros

- Tópico antigo, mmap vs others
- Weird algorithms, sliding windows and cache graphs/trees
- Agora misturem isto com encoding e a minha cabeça explode


## Já agora

* http://www.250bpm.com/blog:4
* http://www.joelonsoftware.com/items/2009/09/23.html


