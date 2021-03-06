---
title: Sintaxe de consulta Lucene
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe completa da consulta Lucene, como usado na Pesquisa Cognitiva do Azure para curinga, pesquisa difusa, RegEx e outros construtos avançados de consulta.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: f4c3330b23b8b724cdbf5d7e09eec8a8dd5b8cfa
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258976"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta de Lucene em Azure Cognitive Search

Você pode escrever consultas contra o Azure Cognitive Search com base na rica sintaxe [de Parser lucene query](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) para formulários de consulta especializada: curinga, pesquisa difusa, pesquisa de proximidade, expressões regulares são alguns exemplos. Grande parte da sintaxe de Parser de Consulta de Lucene é [implementada intacta na Busca Cognitiva do Azure,](search-lucene-query-architecture.md)com exceção das pesquisas de *alcance* que são construídas na Busca Cognitiva do Azure através de `$filter` expressões. 

> [!NOTE]
> A sintaxe completa de Lucene é usada para expressões de consulta passadas no parâmetro de **pesquisa** da API documentos de [pesquisa,](https://docs.microsoft.com/rest/api/searchservice/search-documents) para não ser confundida com a [sintaxe OData](query-odata-filter-orderby-syntax.md) usada para o [parâmetro $filter](search-filters.md) dessa API. Essas diferentes sintaxes têm suas próprias regras para construir consultas, escapar de strings, e assim por diante.

## <a name="invoke-full-parsing"></a>Invoque a análise completa

Defina o parâmetro de pesquisa `queryType` para especificar qual análise usar. Os valores válidos incluem `simple|full`, com `simple` como o padrão, e `full` para o Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Exemplo exibindo a sintaxe completa

O exemplo a seguir localiza os documentos no índice usando a sintaxe de consulta do Lucene, que fica evidente no parâmetro `queryType=full`. Essa consulta retorna hotéis onde o campo de categoria contém o termo "orçamento" e todos os campos pesquisáveis com a frase "renovado recentemente". Os documentos com a frase "renovado recentemente" apresentam uma classificação superior como um resultado do valor de aumento de termo (3).  

O parâmetro `searchMode=all` é relevante neste exemplo. Sempre que os operadores estiverem na consulta, geralmente você deve definir `searchMode=all` para garantir que *todos* os critérios sejam correspondidos.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Como alternativa, use POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Para exemplos adicionais, consulte [exemplos de sintaxe de consulta de Lucene para construir consultas na Pesquisa Cognitiva do Azure](search-query-lucene-examples.md). Para obter detalhes sobre como especificar o contingente completo de parâmetros de consulta, consulte Documentos de [pesquisa &#40;Aa6 Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  O Azure Cognitive Search também suporta [a Sintaxe de Consulta Simples](query-simple-syntax.md), uma linguagem de consulta simples e robusta que pode ser usada para pesquisa direta de palavras-chave.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> Conceitos básicos da sintaxe  

os seguintes fundamentos de sintaxe se aplicam a todas as consultas que usam a sintaxe lucene.  

### <a name="operator-evaluation-in-context"></a>Avaliação de operador no contexto

O posicionamento determina se um símbolo é interpretado como um operador ou apenas outro caractere em uma cadeia de caracteres.

Por exemplo, na sintaxe completa do Lucene, o til (~) é usado na busca difusa e na pesquisa de proximidade. Quando colocado depois de uma frase entre aspas, invoca a procura por proximidade. Quando colocado no final de um termo, o ~ invoca a pesquisa difusa.

Dentro de um termo, como "analista~de~negócios", o caractere não é avaliado como um operador. Nesse caso, supondo que a consulta seja um termo ou uma consulta de frase, a [pesquisa de texto completo](search-lucene-query-architecture.md) com [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis) retira o ~ e divide o termo "analista~de~negócios" em dois: analista OU negócios.

O exemplo acima usa o til (~), mas o mesmo princípio se aplica a todos os operadores.

### <a name="escaping-special-characters"></a>Caracteres especiais de escape

Para usar qualquer um dos operadores de pesquisa como parte do texto de pesquisa,`\`fuja do caractere prefixando-o com uma única barra invertida ( ). Por exemplo, para uma `https://`pesquisa `://` curinga em , onde é `search=https\:\/\/*`parte da seqüência de consulta, você especificaria . Da mesma forma, um padrão de `\+1 \(800\) 642\-7676`número de telefone fugitivo pode ser assim.

Os caracteres especiais que requerem fuga incluem o seguinte:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Embora escapar mantenha os tokens juntos, a [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis) durante a indexação pode tirá-los. Por exemplo, o analisador lucene padrão quebrará palavras em hífens, espaço em branco e outros caracteres. Se você precisar de caracteres especiais na seqüência de consultas, talvez precise de um analisador que os preserve no índice. Algumas opções incluem [analisadores de linguagem](index-add-language-analyzers.md)natural da Microsoft, que preserva palavras hifenizadas, ou um analisador personalizado para padrões mais complexos. Para obter mais informações, consulte [Termos parciais, padrões e caracteres especiais](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de caracteres reservados e não seguros em URLs

Verifique se todos os caracteres reservados e não seguros estão codificados em uma URL. Por exemplo, '#' é um caractere inseguro porque é um identificador de fragmento/âncora em uma URL. O caractere deverá ser codificado como `%23` se for usado em uma URL. '&' e '=' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva do Azure. Consulte [RFC1738: Url (Uniform Resource Locators, localizadores de recursos uniformes)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

Os caracteres não seguros são ``" ` < > # % { } | \ ^ ~ [ ]``. Os caracteres reservados são `; / ? : @ = + &`.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limites de tamanho da consulta

 Há um limite para o tamanho das consultas que você pode enviar para a Pesquisa Cognitiva do Azure. Especificamente, você pode ter, no máximo, 1024 cláusulas (expressões separadas por AND, OR e assim por diante). Há também um limite de aproximadamente 32 KB em relação ao tamanho de qualquer termo individual em uma consulta. Se seu aplicativo gerar consultas de pesquisa por meio de programação, é recomendável criá-lo de forma que ele não gere consultas de tamanho ilimitado.  

### <a name="precedence-operators-grouping"></a>Operadores de precedência (agrupamento)

 Você pode usar parênteses para criar subconsultas, incluindo operadores dentro da instrução entre parênteses. Por exemplo, `motel+(wifi||luxury)` irá procurar documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas tem como escopo o agrupamento para um único campo. Por exemplo, `hotelAmenities:(gym+(wifi||pool))` pesquisa o campo "comodidadesDoHotel" para "academia" e "wifi" ou "academia" e "piscina".  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a>Busca booleana

 Sempre especifique operadores boolianos de texto (AND, OR, NOT) com tudo em maiúsculas.  

### <a name="or-operator-or-or-"></a>Operador OR `OR` ou `||`

O operador OR é uma barra vertical ou um caractere de pipe. Por exemplo: `wifi || luxury` irá procurar documentos que contenham "wifi" ou "luxo", ou ambos. Porque OR é o operador de conjunção padrão, que também pode ser deixado de fora, de modo que `wifi luxury` é o equivalente de `wifi || luxury`.

### <a name="and-operator-and--or-"></a>Operador AND `AND`, `&&` ou `+`

O operador AND é um e comercial ou um sinal de adição. Por exemplo: `wifi && luxury` irá procurar documentos que contenham "wifi" e "luxo". O caractere de adição (+) é usado para termos obrigatórios. Por exemplo, `+wifi +luxury` estipula que os dois termos devem aparecer em algum lugar no campo de um único documento.

### <a name="not-operator-not--or--"></a>Operador NOT `NOT`, `!` ou `-`

O operador NOT é um sinal de subtração. Por exemplo, `wifi –luxury` procurará documentos `wifi` que tenham o termo `luxury`e/ou não tenham .

O parâmetro **searchMode** em uma solicitação de consulta controla se um termo com o operador NOT é ANDed `+` `|` ou ORed com outros termos na consulta (assumindo que não há ou operador nos outros termos). Os valores válidos incluem `any` ou `all`.

`searchMode=any`aumenta o recall de consultas, incluindo mais `-` resultados, e por padrão será interpretado como "OU NÃO". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` ou aqueles que não contêm o termo `luxury`.

`searchMode=all`aumenta a precisão das consultas, incluindo menos resultados, e por padrão - será interpretado como "E NÃO". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` e não contêm o termo "luxo". Esse é indiscutivelmente um comportamento mais intuitivo para o operador `-`. Portanto, você deve `searchMode=all` considerar `searchMode=any` usar em vez de se você quiser otimizar pesquisas de `-` precisão em vez de recall, *e* seus usuários usam frequentemente o operador em pesquisas.

Ao decidir uma configuração **searchMode,** considere os padrões de interação do usuário para consultas em vários aplicativos. Os usuários que estão procurando informações são mais propensos a incluir um operador em uma consulta, em oposição a sites de comércio eletrônico que têm estruturas de navegação mais incorporadas.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Pesquisa em campo

Você pode definir uma operação `fieldName:searchExpression` de pesquisa em campo com a sintaxe, onde a expressão de pesquisa pode ser uma única palavra ou uma frase, ou uma expressão mais complexa entre parênteses, opcionalmente com operadores booleanos. Alguns exemplos incluem o seguinte:  

- gênero:jazz NÃO histórico  

- artistas:("Miles Davis" "John Coltrane")

Coloque várias cadeias de caracteres entre aspas se quiser que ambas cadeias de caracteres sejam avaliadas como uma única entidade, como neste caso, pesquisar duas cidades distintas no campo `artists`.  

O campo especificado em `fieldName:searchExpression` deve ser um campo `searchable`.  Confira [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados em definições de campo.  

> [!NOTE]
> Ao usar expressões de pesquisa em campo, `searchFields` você não precisa usar o parâmetro porque cada expressão de pesquisa em campo tem um nome de campo explicitamente especificado. No entanto, você `searchFields` ainda pode usar o parâmetro se quiser executar uma consulta onde algumas partes são escopo para um campo específico, e o resto pode se aplicar a vários campos. Por exemplo, a `search=genre:jazz NOT history&searchFields=description` consulta `jazz` corresponderia `genre` apenas ao campo, enquanto corresponderia `NOT history` ao `description` campo. O nome de `fieldName:searchExpression` campo fornecido sempre `searchFields` tem precedência sobre o parâmetro, razão `genre` pela `searchFields` qual, neste exemplo, não precisamos incluir no parâmetro.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Pesquisa difusa

Uma pesquisa difusa encontra correspondências em termos que têm uma construção semelhante, expandindo um termo até o máximo de 50 termos que atendem aos critérios de distância de dois ou menos. Para obter mais informações, consulte [pesquisa fuzzy](search-query-fuzzy.md).

 Para fazer uma pesquisa difusa, use o símbolo til "~" no final de uma única palavra com um parâmetro opcional, um número entre 0 e 2 (padrão), que especifica a distância de edição. Por exemplo, "mar~" ou "mar~1" retornaria "mar", "amar" e "maré".

 A pesquisa fuzzy só pode ser aplicada a termos, não frases, mas você pode anexar o tilde a cada termo individualmente em um nome ou frase de várias partes. Por exemplo, "Unviersty~ de~ "Wshington~" combinaria com "University of Washington".
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>Pesquisa de proximidade

As pesquisas de proximidade são usadas para localizar termos que estejam próximos um do outro em um documento. Insira um símbolo til "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, `"hotel airport"~5` encontrará os termos "hotel" e "aeroporto" em cinco palavras uma da outra em um documento.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> Aumento de termos

O aumento de termos refere-se ao aumento da classificação de um documento caso ele contenha o termo aumentado, em relação aos documentos que não contêm o termo. Isso é diferente dos perfis de pontuação, já que os perfis de pontuação aumentam determinados campos, em vez de termos específicos.  

O exemplo a seguir ajuda a ilustrar as diferenças. Considere um perfil de pontuação que aumente as correspondências em um determinado campo, como *gênero* no [exemplo de índice de loja de música](index-add-scoring-profiles.md#bkmk_ex). O aumento de termos pode ser usado para melhorar a posição de determinados termos de pesquisa. Por exemplo, `rock^2 electronic` aumentará os documentos que contêm os termos de pesquisa no campo gênero, à frente de outros campos pesquisáveis no índice. Além disso, os documentos com o termo de pesquisa *rock* serão mais bem classificados do que o outro termo de pesquisa *eletrônico* como resultado do valor de aumento de termo (2).  

 Para aumentar um termo, use o sinal de interpolação, "^", com um fator de aumento (um número) no final do termo que você está pesquisando. Você também pode aumentar as frases. Quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor do que 1 (por exemplo, 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> Pesquisa com expressão regular  
 Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre as barras "/", como documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Por exemplo, para localizar documentos que contenham "motel" ou "hotel", especifique `/[mh]otel/`. As pesquisas com expressões regulares são comparadas com palavras individuais.

Algumas ferramentas e idiomas impõem requisitos adicionais de caráter de fuga. Para JSON, as strings que incluem uma barra para a `search=/.*microsoft.com\/azure\/.*/` `search=/.* <string-placeholder>.*/` frente são escapadas `microsoft.com\/azure\/` com uma barra para trás: "microsoft.com/azure/" torna-se onde configura a expressão regular, e é a corda com uma barra de avanço escapou.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> Pesquisa com curinga  

Você pode usar a sintaxe geralmente reconhecida para pesquisas com vários caracteres curinga (*) ou um caractere curinga (?). Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um único termo e não uma frase.

A pesquisa por prefixo`*`também usa o caractere asterisco ( ) Por exemplo, uma expressão `search=note*` de consulta de devoluções "notebook" ou "bloco de notas". A sintaxe completa de Lucene não é necessária para pesquisa por prefixo. A simples sintaxe apoia esse cenário.

A pesquisa sufixo, onde `*` ou `?` precede a seqüência, requer sintaxe lucene completa e uma expressão regular (você não pode usar um * ou ? símbolo como o primeiro personagem de uma pesquisa). Dado o termo "alfanumérico",`search=/.*numeric.*/`uma expressão de consulta de ( ) encontrará a correspondência.

> [!NOTE]  
> Durante a análise da análise, as consultas formuladas como prefixo, sufixo, curinga ou expressões regulares são passadas como está na árvore de consulta, ignorando a [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis). As correspondências só serão encontradas se o índice contiver as strings no formato que sua consulta especifica. Na maioria dos casos, você precisará de um analisador alternativo durante a indexação que preserve a integridade das cordas para que a correspondência parcial de prazo e padrão seja bem sucedida. Para obter mais informações, consulte [Pesquisa parcial de prazo nas consultas de pesquisa cognitiva do Azure](search-query-partial-matching.md).

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a> Classificar consultas de caractere curinga e regex

O Azure Cognitive Search usa pontuação baseada em freqüência[(TF-IDF)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)para consultas de texto. No entanto, para consultas curinga e regex em que o escopo de termos pode ser potencialmente amplo, o fator de frequência é ignorado para impedir que a classificação seja direcionada para correspondências de termos mais raros. Todas as correspondências são tratadas da mesma maneira para as pesquisas de caractere curinga e regex.

## <a name="see-also"></a>Confira também

+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para pesquisa completa de Lucene](search-query-lucene-examples.md)
+ [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Sintaxe de expressão OData para filtros e classificação](query-odata-filter-orderby-syntax.md)   
+ [Sintaxe de consulta simples na Pesquisa Cognitiva do Azure](query-simple-syntax.md)   
