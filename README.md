# Onde Arborizar?

<a id="o-problema"></a>

**O problema.** No asfalto, na calçada e no ponto de ônibus, a cidade esquenta. A prefeitura tem mudas, mas o plantio costuma seguir o pedido de um bairro, a pressa da campanha ou a mesma quantidade para cada região. A árvore vai para um lugar que já tem sombra, para uma calçada estreita demais, ou para uma duna que deve continuar como está. Quem espera o ônibus na porta da escola segue no sol.

<a id="o-que-a-ferramenta-faz"></a>

**O que a ferramenta faz.** Mostra num mapa a ordem dos lugares para plantar e deixar a cidade mais fresca. O primeiro da lista é onde a equipe começa na segunda-feira. Sobe o pedaço quente, com pouca árvore, com gente parada no sol e com espaço na calçada. Essa lista ordenada é a fila de plantio. A primeira cidade é Parnamirim. O mesmo mapa vale para os 167 municípios do Rio Grande do Norte.

<a id="quem-usa"></a>

**Quem usa.** A prefeitura, os órgãos de meio ambiente e o horto. O IDEMA e a EMPARN. As secretarias de saúde e de educação. A defesa civil e a secretaria de planejamento urbano e ambiental.

<a id="dores"></a>

**A dor que esses dados tratam.** O plantio por pedido, a calçada estreita, a duna e o ponto de ônibus na porta da escola já estão no problema acima. A tabela reúne as outras falhas, e o que o mapa faz com cada uma.

| A dor hoje | Como a ferramenta trata |
|---|---|
| A muda seca porque foi plantada no mês sem chuva, ou a espécie não aguenta o clima do lugar | A chuva de cada posto e a temperatura da estação indicam o mês de regar e o clima da espécie |
| Mata e lagoa recebem árvore, no lugar de permanecer como estão | Essas áreas saem da lista de plantio. A muda fica no chão livre |
| Na entrada e na saída, o caminho até a escola e o posto segue sem sombra | Esses endereços sobem na lista quando o trecho está quente e com pouca árvore |
| O loteamento novo abre num chão que já esquenta e quase não tem verde | Esses trechos aparecem para o plano diretor, antes da aprovação |

A ordem de cada pedaço da cidade sai de quatro perguntas:

1. Onde o asfalto esquenta mais?
2. Onde falta árvore?
3. Onde tem gente parada no sol?
4. Onde a muda cabe?

O lugar sobe quando as quatro se encontram. Calor sozinho não põe a duna na lista.

O passo a passo e os testes estão em [ETAPAS.md](ETAPAS.md).

## Sumário

- [O problema](#o-problema)
- [O que a ferramenta faz](#o-que-a-ferramenta-faz)
- [Quem usa](#quem-usa)
- [A dor que esses dados tratam](#dores)
- [Estudos anteriores](#estudos-anteriores)
- [Dados abertos](#dados-abertos)
- [O que ainda falta](#o-que-ainda-falta)
- [Ferramenta para o Rio Grande do Norte](#ferramenta-rn)
- [Plano de atuação](#plano-de-atuacao)
- [Etapas para criar a ferramenta em Python](#etapas-python)
- [Referências](#referencias)

<a id="estudos-anteriores"></a>

## Estudos anteriores

Uma revisão de 39 estudos mostra o que mais entra na escolha do lugar: quem mora ali (10), o que cobre o chão (8), o calor (6) e a copa (6). A poluição entrou em 3. O carbono entrou em 1 (Zhang e Ludwig, 2026). Três cidades transformaram essa lista numa decisão. A tabela diz o que foi medido. O texto de cada cidade diz o que a prefeitura fez com isso. O dado aberto brasileiro correspondente está na seção seguinte.

| O que mediram | O que isso mostrou | Onde entrou |
|---|---|---|
| Calor do chão | Onde o asfalto fica mais quente que o campo ao redor | Boston conferiu o satélite em 25 termômetros. Joliette usou quadrados de 20 m |
| Copa | Onde já existe sombra | Boston e Lausanne |
| Verde do satélite | Praça, rua e quintal no mesmo número, quando não há lista de árvores | Joliette, imagem de 2017 |
| O que cobre o chão | Grama recebe muda. Asfalto e telhado pedem outra sombra | Boston usou só grama e arbusto. Lausanne dividiu o chão em quadrados de 10 m |
| Sombra, reflexo e água da folha | Quantos graus o ar pode cair se o plano tiver mais árvores | Lausanne, no programa aberto InVEST. O peso da sombra foi 0,6 |
| Termômetro | O ar que a pessoa sente, para conferir o mapa | Boston, 25 pontos. Lausanne, 11 estações, por volta das 21 h |
| Lista de árvores da rua | Árvore por quilômetro, espécie e tamanho. O quintal fica de fora | Joliette: 22.527 árvores, cerca de 91 espécies |
| Quem mora ali | O mesmo calor pesa mais onde há criança, idoso e renda mais baixa | Boston e Joliette. Foi o dado mais frequente na revisão |
| Gente a pé | Onde a pessoa passa | Joliette pôs a rua de casas em primeiro |
| O que impede a muda | Calçada estreita, fio e dono do terreno | Boston: onde não cabe árvore, a saída é toldo ou piso mais claro |
| Qualidade do ar | Espécie de muito pólen onde a asma já é alta | Boston tirou essa espécie da lista |

<a id="boston"></a>

### Boston, Estados Unidos — Werbin et al. (2020)

Boston plantava todo ano, e muita muda morria. O site *Right Place, Right Tree* separa duas perguntas: onde está quente e quem adoece mais com o calor. Área quente com gramado recebe árvore. Área quente só de asfalto e telhado recebe outra sombra. No bairro com muita asma, sai a espécie de pólen alto.

<a id="joliette"></a>

### Joliette, Canadá — Sousa-Silva et al. (2021)

Joliette tem cerca de 20 mil habitantes. A conta foi feita em cada trecho de rua, de uma esquina à outra, numa faixa de 50 m. A prefeitura pediu para reduzir o calor, então a temperatura pesou 3 e o resto pesou menos. O centro subiu: mais quente, com menos árvore e com mais carência. O mapa aponta o trecho. A espécie fica para a visita de campo. Se no ano seguinte a prioridade for a porta das escolas, o peso da escola sobe e a ordem muda.

<a id="lausanne"></a>

### Lausanne, Suíça — Bosch et al. (2021)

Lausanne ainda não faz a fila de canteiros. O estudo calcula quantos graus o ar cai se o plano colocar mais árvores, antes de comprar a muda. A conta usa sombra, a água que a folha solta e o quanto o chão reflete o sol. Foi ajustada aos termômetros da cidade, por volta das 21 h, quando a área urbana segue quente e o campo já esfriou. A cidade sobe cerca de 500 m do lago até o outro extremo.

<a id="dados-abertos"></a>

## Dados abertos

Dado aberto é mapa ou tabela que um órgão público deixa na internet, de graça. Com ele a cidade monta o primeiro mapa antes de ter a lista de cada árvore da calçada.

Cada linha responde uma pergunta. O link é o site do arquivo. O formato é o tipo de arquivo: GeoTIFF é uma imagem de mapa; CSV e XLSX abrem no Excel; GeoPackage e Shapefile são o desenho dos lugares.

| Pergunta | Dado | Onde baixar | O que muda na lista | Formato |
|---|---|---|---|---|
| Onde o asfalto esquenta? | O quanto aquele pedaço fica mais quente que o campo ao redor | [MapBiomas](https://brasil.mapbiomas.org/) | O pedaço mais quente sobe | GeoTIFF |
| Onde o asfalto esquenta? | Temperatura do ar da cidade inteira | [MapBiomas Atmosfera](https://brasil.mapbiomas.org/iniciativas-e-produtos/atmosfera/temperatura/temperatura-do-ar/) | Diz se o município, no conjunto, é quente. A rua entra no mapa acima | GeoTIFF |
| Onde falta árvore? | Verde dentro da área urbana | [MapBiomas, vegetação urbana](https://brasil.mapbiomas.org/iniciativas-e-produtos/cobertura-e-uso-da-terra/areas-urbanizadas/vegetacao-urbana/) | Lugar quente que já tem árvore fica atrás de lugar quente sem árvore | GeoTIFF |
| Onde a muda cabe? | Árvore, grama, asfalto, telhado ou água | [MapBiomas, no Google Earth Engine](https://brasil.mapbiomas.org/downloads/assets-no-google-earth-engine/) | Grama recebe muda. Asfalto e telhado pedem toldo ou piso mais claro | GeoTIFF |
| Onde a muda cabe? | Verde de praça, rua e quintal vistos juntos | [Brazil Data Cube, INPE](https://data.inpe.br/bdc/) | Entra quando a cidade não tem lista de árvores | GeoTIFF |
| Onde a muda cabe? | Parques e praças que a prefeitura declarou | [Cadastro do Ministério do Meio Ambiente](https://dados.mma.gov.br/dataset/cadastro_ambiental_urbano) | Confere a praça já registrada. A praça de fora do formulário continua no satélite | CSV |
| Onde tem gente no sol? | Moradores, idade e casas | [IBGE, Censo 2022](https://www.ibge.gov.br/estatisticas/sociais/populacao/22827-censo-demografico-2022.html) | O mesmo calor pesa mais onde há criança e idoso | CSV e XLSX |
| Onde tem gente no sol? | Renda de quem responde pelo domicílio | [IBGE, renda por setor](https://ftp.ibge.gov.br/Censos/Censo_Demografico_2022/Agregados_por_Setores_Censitarios_Rendimento_do_Responsavel/) | Onde a renda é mais baixa, a casa tem menos como se proteger do calor | CSV e XLSX |
| Onde tem gente no sol? | O desenho do pedaço no mapa | [IBGE, malha 2022](https://www.ibge.gov.br/geociencias/organizacao-do-territorio/malhas-territoriais/26565-malhas-de-setores-censitarios-divisoes-intramunicipais.html) | É a área em que calor, árvore e gente são somados | GeoPackage, Shapefile ou KML |
| Onde tem gente no sol? | Árvore, calçada e ponto de ônibus do bairro | [IBGE, entorno das casas](https://sidra.ibge.gov.br/pesquisa/censo-demografico/demografico-2022/universo-caracteristicas-urbanisticas-do-entorno-dos-domicilios) | Compara bairros. O buraco da muda pede um mapa mais fino | CSV e XLSX |
| Onde tem gente no sol? | Escolas | [INEP, Censo Escolar](https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos) | A porta da escola sobe | CSV |
| Onde tem gente no sol? | Postos e hospitais | [CNES](https://cnes.datasus.gov.br/) | O posto sobe, porque gente espera no sol | CSV |
| Em que mês regar? | Chuva, temperatura, umidade e vento | [INMET](https://portal.inmet.gov.br/dadoshistoricos) | O mês seco pede rega. A temperatura ajuda a escolher a espécie | ZIP com CSV |
| Em que mês regar? | Chuva do Rio Grande do Norte. Em Parnamirim, o posto é o 3819844 | [EMPARN](https://meteorologia.emparn.rn.gov.br/relatorios/relatorios-pluviometricos) | Diz os meses de regar. Litoral e sertão não usam o mesmo calendário | Tabela no site |
| Em que mês regar? | Chuva medida no bairro, onde existe o aparelho | [CEMADEN](http://www.cemaden.gov.br/) | Mostra a época seca daquele ponto | Tabela no site |
| Qual espécie aguenta o lugar? | Ladeira, baixada e relevo | [MapBiomas Urbano](https://brasil.mapbiomas.org/modulo-urbano/) e [TOPODATA](http://www.dsr.inpe.br/topodata/) | Morro e beira de rio pedem espécies diferentes | GeoTIFF |
| Como está o ar da cidade? | Poeira e ozônio medidos na estação | [MonitorAr](https://monitorar.mma.gov.br/), planilhas em [dados.mma.gov.br](https://dados.mma.gov.br/dataset/ar-puro-monitorar) | Vale na cidade que tem estação | ZIP com CSV |
| Como está o ar da cidade? | Se o ar ruim volta todo ano | [Plataforma da Qualidade do Ar, IEMA](https://energiaeambiente.org.br/produto/plataforma-da-qualidade-do-ar) | Compara os anos | Tabela no site |
| Como está o ar da cidade? | Estimativa do município inteiro, mesmo sem estação | [SISAM, INPE](https://data.inpe.br/queimadas/sisam/downloads) | Aviso da cidade. A rua segue pelo calor, pela árvore, pelas pessoas e pela calçada | ZIP por ano |
| O que fica de pé? | Mata, água e área protegida | [SEIA, IDEMA](https://seia.idema.rn.gov.br/) | Essas áreas saem da lista de mudas | Mapa no portal |
| Dá para chegar na calçada? | Cada árvore da rua, com espécie e tamanho | Site da prefeitura. Em São Paulo, o [GeoSampa](https://geosampa.prefeitura.sp.gov.br/) | Entra quando a cidade publica | Arquivo de mapa |
| Dá para chegar na calçada? | Nome da rua, da praça e do ponto de ônibus | [OpenStreetMap](https://www.openstreetmap.org/) | Leva o mapa do bairro até a rua. Na maior parte das cidades a calçada ainda está incompleta | Arquivo de mapa |

O ar entra como aviso. Perto de escola e posto, sai da lista a espécie que solta muito pólen. Na avenida estreita, uma fileira fechada de copa pode segurar a fumaça na altura de quem espera o ônibus. Aí a indicação é copa espaçada.

Para o primeiro mapa bastam cinco arquivos: calor e verde do MapBiomas, moradores do IBGE, chuva da EMPARN, temperatura do INMET e o ar do SISAM ou do MonitorAr. Escola e posto marcam onde a pessoa passa o dia. A lista de árvores e o desenho da calçada entram quando a prefeitura publica. Outros mapas já publicados estão na [INDE](https://inde.gov.br/) e no [dados.gov.br](https://dados.gov.br/).

<a id="o-que-ainda-falta"></a>

## O que ainda falta

A lista aponta o bairro. O buraco da muda, na calçada, fica para uma visita. Também falta saber quantos anos a copa leva para cobrir o ponto de ônibus (Zhang e Ludwig, 2026).

O mesmo tanto de árvores refresca de um jeito numa rua larga e de outro numa rua estreita, entre prédios. Na rua estreita, copa muito fechada pode segurar o calor à noite (Li et al., 2024; Yin et al., 2024).

A ordem para uma cidade brasileira segue as três cidades já descritas. Primeiro, a lista: calor, falta de árvore, pessoas e espaço para a muda (Werbin et al., 2020; Sousa-Silva et al., 2021). Depois, a conta de quantos graus o ar pode cair, como em Lausanne (Bosch et al., 2021). Por último, a espécie, conforme o clima e a largura da rua (Li et al., 2024).

<a id="ferramenta-rn"></a>

## Ferramenta para o Rio Grande do Norte

A prefeitura abre a própria cidade e vê por onde a equipe começa. O IDEMA e a EMPARN abrem os 167 municípios e veem onde faltam muda, rega e gente no sol.

Os números de gente vêm do **Censo Demográfico 2022**, feito pelo [IBGE](https://www.ibge.gov.br/estatisticas/sociais/populacao/22827-censo-demografico-2022.html) (Instituto Brasileiro de Geografia e Estatística). O IBGE visitou os domicílios, contou moradores, idade e renda, e desenhou o setor: um pedaço menor que o bairro. No mesmo censo, o questionário do entorno registrou árvore, calçada e ponto de ônibus. O calor e o verde vêm do MapBiomas. A chuva vem da EMPARN. A temperatura do ar vem do INMET. As escolas vêm do INEP. Os postos vêm do CNES. Mata, lagoa e área protegida vêm do IDEMA. O aviso de ar vem do SISAM, do INPE, ou do MonitorAr quando existe estação na cidade.

<a id="ferramenta-rn-mostra"></a>

### O que o mapa mostra

O mapa pinta esses setores do IBGE. A cor sobe no pedaço mais quente que o entorno, com pouco verde, com mais criança, idoso e renda mais baixa, e perto de escola ou posto. Grama é lugar de plantar. Asfalto, telhado, duna, mata e lagoa ficam de fora da lista de mudas.

O clique abre a ficha: a nota de cada item, a calçada, o ponto de ônibus e os meses de rega. No litoral e no sertão a rega não cai no mesmo mês. O aviso de ar vale para o município inteiro.

<a id="ferramenta-rn-parnamirim"></a>

### Exemplo em Parnamirim

Parnamirim é a primeira cidade. Em 2022 havia 252.716 habitantes, em cerca de 124 km². A área com ruas e casas, em 2019, era de 49,5 km². A sede da EMPARN fica no Parque das Nações.

No Jardim Planalto, o Censo 2022 mostra 51,4% dos trechos sem árvore e 46,4% com alguma. Se o bairro também estiver quente e tiver calçada, ele sobe e recebe muda. O número de 38,4% que ainda aparece no perfil do município é de 2010. O Centro pode ser conferido com o levantamento da UFRN, de cerca de 2023: 661 árvores e palmeiras.

Barreira do Inferno, Lagoa do Jiqui, Emaús, Cajueiro de Pirangi e Cotovelo, mesmo quentes, ficam de fora.

A rega usa a chuva do posto EMPARN 3819844. A temperatura vem do INMET em Natal, porque o posto antigo do aeroporto encerrou a série civil em 2006. O horto sai sabendo o bairro. A largura da calçada e a rede elétrica entram quando a prefeitura publicar o cadastro.

<a id="plano-de-atuacao"></a>

## Plano de atuação

A meta é a prefeitura de Parnamirim abrir uma página no navegador e ver por onde a equipe de plantio começa. As outras cidades do Rio Grande do Norte só entram depois que essa página de Parnamirim estiver conferida.

O trabalho anda em fila. Cada passo produz um arquivo. O passo seguinte abre esse arquivo. Se a conferência falhar, pare e refaça o passo. A página não abre em cima de uma conta errada.

| Passo | O que você termina tendo | Como você sabe que deu certo |
|---|---|---|
| 1 | As pastas `Data/` preenchidas com os arquivos do IBGE, do MapBiomas, das escolas, da EMPARN, do INMET e do IDEMA | Na tabela de moradores, Parnamirim aparece com o código `2403251` e a soma fica perto de 252.716 pessoas. O que essa etapa entrega está em [ETAPAS, etapa 2](ETAPAS.md#etapa-2) |
| 2 | Uma ficha única, `resultados/setores_prontos.gpkg`: um pedaço da cidade por linha, já com calor, verde, gente, escola e área a conservar | A população somada continua perto de 252.716. O que essa etapa entrega está em [ETAPAS, etapa 3](ETAPAS.md#etapa-3) |
| 3 | A lista de plantio, em `resultados/fila_plantio.csv`, que abre no Excel, e o mapa `fila_plantio.gpkg` | O primeiro lugar está quente e com pouco verde. Barreira do Inferno e lagoa saem como `outra medida`, não como muda. O que essa etapa entrega está em [ETAPAS, etapa 4](ETAPAS.md#etapa-4) |
| 4 | Um relatório curto, `resultados/conferencia.txt`, com a data e o resultado de cada teste | Todos os itens estão marcados como `ok`. O que essa etapa entrega está em [ETAPAS, etapa 5](ETAPAS.md#etapa-5) |
| 5 | A página no navegador, aberta pelo comando `streamlit run app.py` | O clique num pedaço mostra os mesmos números da linha correspondente no Excel. O que essa etapa entrega está em [ETAPAS, etapa 6](ETAPAS.md#etapa-6) |
| 6 | A atualização mensal, feita pelo arquivo `atualizar.py` e pelo Agendador de Tarefas do Windows | A lista nova só substitui a que está no ar se a conferência passar. O que essa etapa entrega está em [ETAPAS, atualização automática](ETAPAS.md#ferramenta-automatica) |

<a id="etapas-python"></a>

## Etapas para criar a ferramenta em Python

Python é a linguagem que lê a tabela do IBGE, o mapa de calor e faz a conta da lista. A página no navegador também é um programa Python. O nome desse programa de página é Streamlit.

Faça uma etapa por vez. Cada uma termina com um arquivo que você abre e confere. O que cada etapa entrega está no [ETAPAS.md](ETAPAS.md).

<a id="etapa-ambiente"></a>

### 1. Preparar o ambiente

Você prepara o computador uma vez. Cria uma caixa separada, chamada `.venv`, para estas ferramentas não se misturarem com as de outros programas. A lista do que instalar fica no arquivo `requirements.txt`. No fim, a pasta `resultados/` existe e está vazia.

| Ferramenta | Para que você usa |
|---|---|
| `pandas` | Abrir e juntar tabelas de moradores, renda, escolas e chuva |
| `geopandas` | Abrir o desenho dos setores e gravar o mapa |
| `shapely` | Ver se a escola cai dentro do setor e se o setor encosta na lagoa |
| `pyproj` | Colocar todos os mapas no mesmo lugar do Brasil |
| `rasterio` | Ler a imagem de satélite do calor e do verde |
| `numpy` | Transformar calor e verde em notas de 0 a 1 e aplicar os pesos |
| `openpyxl` | Ler a planilha do IBGE quando ela vier em Excel |
| `matplotlib` | Desenhar o gráfico da conferência |
| `streamlit` | Montar a página no navegador |
| `folium` | Desenhar o mapa com clique, dentro dessa página |

O que essa preparação entrega está em [ETAPAS, etapa 1](ETAPAS.md#etapa-1).

<a id="etapa-baixar"></a>

### 2. Baixar os dados

Você baixa cada arquivo no site do órgão e salva na pasta correspondente, com a data no nome. Exemplo: `Data/IBGE/moradores_censo_2022_2026-10-05.xlsx`. O arquivo baixado não se edita. Se precisar de outra versão, grave outro arquivo.

| Pasta | O que colocar lá |
|---|---|
| `Data/IBGE/` | Desenho dos setores, moradores, idade, renda, árvore, calçada e ponto de ônibus do Censo 2022 |
| `Data/MapBiomas/` | Calor, verde da cidade e o que cobre o chão |
| `Data/Escolas/` | Escolas do INEP, com endereço |
| `Data/EMPARN/` | Chuva. Em Parnamirim, o posto é o 3819844 |
| `Data/INMET/` | Temperatura da estação de Natal |
| `Data/IDEMA/` | Mata, água e área protegida |

A conferência é a população de Parnamirim perto de 252.716, o Jardim Planalto perto de 46,4% com árvore, e o posto 3819844 na tabela de chuva. O que essa etapa entrega está em [ETAPAS, etapa 2](ETAPAS.md#etapa-2).

<a id="etapa-cruzar"></a>

### 3. Organizar e cruzar

Os arquivos ainda estão separados. Esta etapa junta tudo numa ficha: uma linha por setor. O programa que faz isso será o arquivo `cruzar.py`. Ele usa `pandas` para as tabelas, `geopandas` para o desenho, `rasterio` para o satélite e `shapely` para ver o que cai dentro de cada setor.

O código do setor precisa continuar texto. Se virar número, o zero do começo some e a soma de moradores cai. No fim você tem `resultados/setores_prontos.csv`, para abrir no Excel, e `resultados/setores_prontos.gpkg`, que é o mesmo conteúdo com o desenho do mapa.

O que essa etapa entrega está em [ETAPAS, etapa 3](ETAPAS.md#etapa-3).

<a id="etapa-fila"></a>

### 4. Calcular a fila

A ficha ainda não diz por onde começar. O arquivo `fila.py` transforma cada coluna numa nota de 0 a 1. O 1 é o caso mais urgente. O setor mais quente fica com 1 no calor. O de menos verde fica com 1 na falta de árvore.

A nota final soma cada nota vezes um peso: calor 3, falta de árvore 2, pessoas no sol 2, escola ou posto 2, espaço para plantar 3. Um setor na porta da escola, quente e com faixa de grama, sobe. Sem grama, ou em mata e lagoa, a recomendação é `outra medida`.

Você termina com `resultados/fila_plantio.csv`. A posição 1 é onde a equipe começa. O que essa etapa entrega está em [ETAPAS, etapa 4](ETAPAS.md#etapa-4).

<a id="etapa-conferir"></a>

### 5. Conferir o resultado

Aqui você não calcula de novo. Você decide se a lista pode ir para a página. Olhe quatro coisas: o primeiro lugar está quente e com pouco verde; Barreira do Inferno e as lagoas aparecem como `outra medida`; o Jardim Planalto continua perto de 46,4% com árvore; mudar o peso do calor muda a ordem.

Anote data e resultado em `resultados/conferencia.txt`. Só marque `ok` se os testes passarem. O que essa etapa entrega está em [ETAPAS, etapa 5](ETAPAS.md#etapa-5).

<a id="etapa-publico"></a>

### 6. Abrir para o público

O arquivo `app.py` lê a lista pronta e mostra a página. Quem usa a prefeitura não mexe nesse arquivo. No PowerShell, dentro da pasta do projeto e com o ambiente ativo, o comando é `streamlit run app.py`. O navegador abre o mapa de Parnamirim, a lista e os pesos.

O clique num setor mostra os mesmos números daquela linha no Excel. No rodapé ficam três frases: a nota diz onde plantar primeiro; ela não mede oxigênio e não escolhe o buraco da muda; a árvore não entra como filtro da avenida mais poluída.

O que a página mostra está em [ETAPAS, etapa 6](ETAPAS.md#etapa-6). A atualização mensal, depois que a página estiver no ar, está em [ETAPAS, atualização automática](ETAPAS.md#ferramenta-automatica).

<a id="referencias"></a>

## Referências

A lista abaixo é para quem quiser abrir o estudo. O título fica no idioma do artigo. O que cada cidade fez está em Estudos anteriores.

Bosch, M., Locatelli, M., Hamel, P., Remme, R. P., Chenal, J., & Joost, S. (2021). A spatially explicit approach to simulate urban heat mitigation with InVEST (v3.8.0). *Geoscientific Model Development, 14*(6), 3521–3537. https://doi.org/10.5194/gmd-14-3521-2021

Li, H., Zhao, Y., Wang, C., Ürge-Vorsatz, D., Carmeliet, J., & Bardhan, R. (2024). Cooling efficacy of trees across cities is determined by background climate, urban morphology, and tree trait. *Communications Earth & Environment, 5*, 495. https://doi.org/10.1038/s43247-024-01908-4

Sousa-Silva, R., Cameron, E., & Paquette, A. (2021). Prioritizing street tree planting locations to increase benefits for all citizens: Experience from Joliette, Canada. *Frontiers in Ecology and Evolution, 9*, 716611. https://doi.org/10.3389/fevo.2021.716611

USDA Forest Service. (2021). *i-Tree Landscape: Methods, limitations and uncertainties*. https://www.itreetools.org/documents/115/Landscape_Methods.pdf

Werbin, Z. R., Heidari, L., Buckley, S., Brochu, P., Butler, L. J., Connolly, C., Houttuijn Bloemendaal, L., McCabe, T. D., Miller, T. K., & Hutyra, L. R. (2020). A tree-planting decision support tool for urban heat mitigation. *PLOS ONE, 15*(10), e0224959. https://doi.org/10.1371/journal.pone.0224959

Yin, Y., Li, S., Xing, X., Zhou, X., Kang, Y., Hu, Q., & Li, Y. (2024). Cooling benefits of urban tree canopy: A systematic review. *Sustainability, 16*(12), 4955. https://doi.org/10.3390/su16124955

Zhang, X., & Ludwig, F. (2026). Towards “Right Tree, Right Place” in urban environments: A systematic review of decision-support methods and tools for urban tree planting. *Urban Forestry & Urban Greening, 118*, 129335. https://doi.org/10.1016/j.ufug.2026.129335

<a id="fontes-dos-casos"></a>

### Fontes citadas pelos estudos

Estas fontes são as que Joliette usou no Canadá. O INSPQ é o instituto de saúde pública de Quebec. O primeiro link é o mapa de calor. O segundo é o índice de carência do bairro. Os dados do Brasil estão em Dados abertos.

INSPQ. (2012). *Îlots de chaleur/fraîcheur urbains et température de surface*. Données Québec. https://www.donneesquebec.ca/recherche/dataset/ilots-de-chaleur-fraicheur-urbains-et-temperature-de-surface

INSPQ. (2016). *Deprivation index, Canada, 2016*. https://www.inspq.qc.ca/en/expertise/information-management-and-analysis/deprivation-index
