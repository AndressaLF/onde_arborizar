# Etapas de execução

Este arquivo é o manual. O [README.md](README.md) explica por que cada dado entra e quem usa o mapa. Aqui está a receita: o que fazer, em que ordem, e como abrir o arquivo no fim de cada passo para ver se a conta bate.

Leia uma etapa por vez. Cada uma termina com um arquivo que dá para abrir no Excel ou no mapa. Se a conferência à mão falhar, pare ali e refaça o passo. O mapa só vai para a prefeitura depois que a conferência de Parnamirim passar.

O primeiro município é Parnamirim, código IBGE `2403251`. Quando a fila fechar nessa cidade, o mesmo roteiro vale para os outros municípios do Rio Grande do Norte. Mudam o recorte, o posto de chuva e a estação de temperatura.

Os arquivos baixados ficam em `Data/`. Esses arquivos são a fonte: ninguém edita, apaga ou corrige célula neles. Toda conta grava só em `resultados/`.

## Sumário

- [Bibliotecas](#bibliotecas)
- [1. Preparar o ambiente](#etapa-1)
- [2. Baixar os dados](#etapa-2)
- [3. Organizar e cruzar](#etapa-3)
- [4. Calcular a fila](#etapa-4)
- [5. Conferir o resultado](#etapa-5)
- [6. Abrir para o público](#etapa-6)
- [Da execução manual à ferramenta automática](#ferramenta-automatica)

<a id="bibliotecas"></a>

## Bibliotecas

Python sozinho abre texto. Os dados deste projeto chegam como tabela, desenho de mapa e foto de satélite. Cada biblioteca abaixo lê um desses formatos ou mostra o resultado.

Na primeira vez, instale todas (etapa 1). Quando uma etapa citar o nome, volte a esta tabela para lembrar o papel dela.

| Biblioteca | O que ela faz | Exemplo neste projeto |
|---|---|---|
| `pandas` | Lê e junta tabelas | Junta moradores, renda e escolas pelo código do setor. Grava o CSV que abre no Excel |
| `geopandas` | Faz o mesmo, e guarda o desenho de cada lugar | Recorta os setores de Parnamirim e grava o mapa que a página lê |
| `shapely` | Mede formas: o que está dentro, o que cruza, a área | Diz se a escola cai dentro do setor e se o setor encosta na lagoa |
| `pyproj` | Converte o sistema de coordenadas | Coloca malha, escolas e mapas do IDEMA no SIRGAS 2000 (EPSG:4674), o sistema oficial do Brasil, para tudo cair no mesmo lugar do mapa |
| `rasterio` | Abre a imagem de satélite com a coordenada (GeoTIFF) | Lê calor, vegetação e cobertura do solo e tira a média dos quadrados que caem dentro do setor |
| `numpy` | Calcula média, mínimo, máximo e escala em bloco | Transforma calor e verde em notas de 0 a 1 e aplica os pesos |
| `requests` | Baixa um arquivo cujo endereço é direto | Salva malha, censo e pacote do INMET em `Data/` |
| `matplotlib` | Desenha gráfico e mapa estático | Na conferência, faz o histograma da nota e o mapa colorido. Essa figura é para quem confere, não a tela da prefeitura |
| `streamlit` | Monta uma página no navegador a partir de um script | É a ferramenta: escolha do município, lista, ficha e pesos |
| `folium` | Desenha o mapa com clique e zoom | Colore os setores e abre a ficha do setor clicado |
| `streamlit-folium` | Encaixa o mapa do `folium` na página do `streamlit` | Liga o clique aos números da fila |
| `openpyxl` | Lê planilha Excel | Entra por baixo do `pandas` quando o IBGE vier em XLSX |

<a id="etapa-1"></a>

## 1. Preparar o ambiente

Esta etapa monta a caixa de ferramentas na máquina. Faz uma vez. Nas próximas execuções, só ative o ambiente e siga a partir da etapa 2.

**No fim desta etapa** existem a pasta `.venv`, o arquivo `requirements.txt` e a pasta vazia `resultados/`.

### O que fazer

1. Abra o PowerShell na pasta do projeto.
2. Crie um ambiente isolado. As bibliotecas ficam dentro dele e não se misturam com as de outros programas:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

3. Crie `requirements.txt` com os nomes da tabela de bibliotecas, um por linha: `pandas`, `geopandas`, `shapely`, `pyproj`, `rasterio`, `numpy`, `requests`, `matplotlib`, `streamlit`, `folium`, `streamlit-folium`, `openpyxl`.
4. Instale:

```powershell
pip install -r requirements.txt
```

5. Crie a pasta `resultados/`. Ela vai receber `setores_prontos.gpkg`, `fila_plantio.gpkg` e `fila_plantio.csv`.

### Como testar à mão

1. O início da linha do PowerShell mostra `(.venv)`. Se o prefixo não aparecer, rode de novo `.venv\Scripts\Activate.ps1`.
2. Rode `python --version`. A resposta é um número de versão, sem mensagem de erro.
3. Rode este comando. Ele tenta abrir cada biblioteca:

```powershell
python -c "import pandas, geopandas, shapely, pyproj, rasterio, numpy, requests, matplotlib, streamlit, folium, streamlit_folium, openpyxl"
```

4. Se o comando voltar ao prompt sem texto, todas abriram. Se aparecer `ModuleNotFoundError`, leia o nome que faltou e rode o `pip install` de novo.
5. No explorador de arquivos, a pasta `resultados` existe e está vazia.

<a id="etapa-2"></a>

## 2. Baixar os dados

Aqui você junta a matéria-prima. Cada site publica um pedaço da cidade: o desenho dos setores, quem mora ali, o calor visto do satélite, a chuva, a escola.

**No fim desta etapa** cada pasta de `Data/` tem pelo menos um arquivo, com a data do download no nome. Exemplo: `malha_setores_rn_2022_2026-10-05.gpkg`. Depois de salvo, o arquivo fica como veio. Uma versão nova ganha outro arquivo, com outra data.

O **setor** é um pedaço menor que o bairro, desenhado pelo IBGE para o censo. Em Parnamirim, o código do município é `2403251`. O código do setor é mais longo e começa com esses dígitos. Toda junção das próximas etapas usa esse código.

### O que fazer

| Pasta | O que baixar | Site | Formato |
|---|---|---|---|
| `Data/IBGE/` | Malha de setores de 2022 do Rio Grande do Norte | [Malha de setores](https://www.ibge.gov.br/geociencias/organizacao-do-territorio/malhas-territoriais/26565-malhas-de-setores-censitarios-divisoes-intramunicipais.html) | GeoPackage, Shapefile ou KML |
| `Data/IBGE/` | Moradores, idade e domicílio por setor | [Censo 2022](https://www.ibge.gov.br/estatisticas/sociais/populacao/22827-censo-demografico-2022.html) | CSV ou XLSX |
| `Data/IBGE/` | Renda do responsável por setor | [Rendimento por setor](https://ftp.ibge.gov.br/Censos/Censo_Demografico_2022/Agregados_por_Setores_Censitarios_Rendimento_do_Responsavel/) | CSV ou XLSX |
| `Data/IBGE/` | Árvore, calçada e ponto de ônibus por bairro | [Entorno das casas](https://sidra.ibge.gov.br/pesquisa/censo-demografico/demografico-2022/universo-caracteristicas-urbanisticas-do-entorno-dos-domicilios) | CSV ou XLSX |
| `Data/MapBiomas/` | Ilha de calor, vegetação urbana e cobertura do solo | [MapBiomas](https://brasil.mapbiomas.org/) | GeoTIFF |
| `Data/Escolas/` | Escolas com endereço | [INEP](https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos) | CSV |
| `Data/EMPARN/` | Chuva do posto 3819844, de Parnamirim | [EMPARN](https://meteorologia.emparn.rn.gov.br/relatorios/relatorios-pluviometricos) | Tabela exportada do site |
| `Data/INMET/` | Temperatura da estação de Natal mais próxima | [INMET](https://portal.inmet.gov.br/dadoshistoricos) | CSV dentro de ZIP |
| `Data/IDEMA/` | Mata, água e unidade de conservação | [SEIA](https://seia.idema.rn.gov.br/) | Arquivo de mapa exportado do portal |

Postos e hospitais ainda não têm pasta. Quando forem entrar, baixe o [CNES](https://cnes.datasus.gov.br/) em CSV e trate como as escolas: endereço primeiro, ponto no mapa depois.

### Como testar à mão

Abra os arquivos e confira quatro fatos que você já conhece da cidade. Se um deles falhar, o download é de outro ano, de outro município, ou veio vazio.

1. No explorador, cada pasta de `Data/` tem pelo menos um arquivo com tamanho maior que zero. Arquivo de 0 KB está vazio: baixe de novo.
2. Na tabela de moradores, filtre o município `2403251`. A tabela filtrada tem linhas. Some a população. O total fica perto de **252.716**, a população de Parnamirim no Censo 2022. Uma diferença pequena pode ser setor rural ou arredondamento. Dezenas de milhares a menos indicam filtro errado ou arquivo de outro ano.
3. Na tabela de entorno, procure o **Jardim Planalto**. A arborização do bairro fica em torno de **46,4%** dos trechos, e os trechos sem árvore em torno de **51,4%**. Se esses percentuais não aparecerem, o arquivo não é o entorno de 2022.
4. Na chuva da EMPARN, procure o posto `3819844` ou o nome Parnamirim. Sem esse posto, a ficha de rega fica sem dado local.
5. Descompacte o ZIP do INMET e abra o CSV. A coluna de temperatura tem números e datas. Anote o nome da estação e o período. A série antiga do aeroporto de Parnamirim encerrou em 2006, então a temperatura desta versão vem de Natal.
6. O GeoTIFF do MapBiomas é uma imagem com coordenada. Se um programa de foto abrir e mostrar uma figura sem localização, o arquivo perdeu o lugar no mapa. Baixe de novo a partir do site, não uma captura de tela.

<a id="etapa-3"></a>

## 3. Organizar e cruzar

Os arquivos da etapa 2 ainda estão separados: um tem gente, outro tem calor, outro tem o desenho. Esta etapa coloca tudo na mesma linha, um setor por linha.

Pense numa ficha de papel. O cabeçalho é o código do setor. Nas colunas entram calor, verde, moradores, escolas, chuva e o aviso de área protegida. O desenho do setor vai junto, para o mapa saber onde pintar.

**No fim desta etapa** existem `resultados/setores_prontos.gpkg` e `resultados/setores_prontos.csv`. Ainda não há fila. Há a ficha completa.

Quem executa é um script Python, na ordem abaixo.

### O que fazer

1. Com `geopandas`, abra a malha e fique só com os setores cujo código começa com `2403251`. Marque urbano e rural na coluna `situacao`. O setor rural continua na tabela e fica fora da fila de calçada: a muda de rua é decisão da área urbanizada.
2. Com `pandas`, leia moradores, idade e renda. Antes de juntar com a malha, converta o código do setor para texto nos dois lados. Assim o zero à esquerda permanece e um código de 15 dígitos não vira número quebrado. Se o código virar número, a junção perde setores e a população cai.
3. Com `pyproj`, passe malha, escolas e camadas do IDEMA para SIRGAS 2000 (EPSG:4674). Para medir área em metros, use em seguida um sistema métrico válido no Rio Grande do Norte e volte a guardar o mapa em SIRGAS 2000. Sem esse passo, a escola pode cair no mar.
4. Com `rasterio` e `numpy`, calcule dentro de cada setor a média da ilha de calor, a parcela de vegetação, a parcela de grama e a parcela de asfalto mais telhado. A grama é o chão livre para plantar agora. Asfalto e telhado entram na ficha como chão ocupado.
5. Localize as escolas pelo endereço. Com `shapely`, conte quantas caem dentro de cada setor. Uma escola na porta de um setor quente é o motivo para esse setor subir depois.
6. Marque o setor que cruza mata, duna, lagoa ou unidade de conservação. Em Parnamirim, a lista do plano diretor é Barreira do Inferno, Lagoa do Jiqui, Emaús, Cajueiro de Pirangi e Cotovelo. A parcela que cruza essas áreas sai do chão plantável.
7. Copie para todos os setores a chuva do posto EMPARN `3819844` e a temperatura da estação do INMET em Natal. Esses dois números descrevem o município inteiro. Servem para a ficha de rega. Eles não mudam a ordem entre um setor e outro.
8. Junte o entorno pelo nome do bairro: percentual de trechos com árvore, com calçada e com ponto de ônibus. Esse número descreve o bairro na ficha. O Jardim Planalto, por exemplo, mostra cerca de metade dos trechos sem árvore.
9. Se um setor ficar sem calor ou sem verde, deixe a célula vazia e preencha `situacao_dado` com `sem dado`. Preencher com a média do vizinho só vale se a ficha registrar que o valor foi estimado.
10. Grave `resultados/setores_prontos.gpkg`. Exporte também `resultados/setores_prontos.csv` para conferir no Excel.

### Como testar à mão

1. Abra `setores_prontos.csv`. A primeira linha é o cabeçalho. Cada linha seguinte é um setor. Não há dois códigos iguais.
2. Filtre `2403251` e some a população de novo. O total continua perto de 252.716. Se a soma caiu muito em relação à etapa 2, a junção pelo código perdeu setores. Volte ao passo 2 desta etapa.
3. Escolha um setor urbano. Calor, verde e grama são números. Calor vazio num setor urbano grande indica que o recorte do satélite não cobriu a cidade.
4. Pegue uma escola que você conhece em Parnamirim, pelo nome, na tabela já localizada. O setor onde ela cai tem contagem de escolas maior que zero.
5. No Jardim Planalto, o percentual de arborização da ficha continua em torno de 46,4%, igual ao arquivo original.
6. Filtre os setores marcados como conservação. A lista inclui área da Barreira do Inferno ou da Lagoa do Jiqui. Se nenhuma unidade aparecer, o arquivo do IDEMA não entrou no cruzamento.
7. Abra o GeoPackage num visualizador de mapa. O desenho cai sobre Parnamirim, ao sul de Natal. Desenho no oceano ou deslocado significa sistema de coordenadas trocado. Volte ao passo 3 desta etapa.

<a id="etapa-4"></a>

## 4. Calcular a fila

A ficha da etapa 3 ainda não diz por onde começar. Esta etapa transforma cada coluna numa nota e soma. O setor que junta calor, falta de árvore, gente exposta e chão livre sobe. O setor que já tem copa, ou que é lagoa, desce.

**No fim desta etapa** existem `resultados/fila_plantio.gpkg`, `resultados/fila_plantio.csv` e `resultados/pesos.csv`. A coluna `posicao` vale 1 no primeiro da fila.

A conta usa `pandas` e `numpy`. O desenho do setor continua no arquivo porque o `geopandas` grava tabela e mapa juntos.

### O que fazer

Dentro dos setores urbanos de Parnamirim, transforme cada indicador em nota de 0 a 1. O 1 é o caso mais urgente daquele item. O 0 é o menos urgente.

- calor: o mais quente fica com 1 e o mais frio com 0;
- falta de árvore: o de menos verde fica com 1;
- pessoas: sobe com mais crianças e idosos e com renda mais baixa;
- escola: sobe com o número de escolas no setor;
- espaço: é a parcela de grama. Setor sem grama fica com 0.

A nota final, coluna `prioridade`, é a soma de cada nota vezes o peso:

| Item | Peso | Por que esse peso |
|---|---|---|
| Calor | 3 | É o motivo do plantio: refrescar |
| Falta de árvore | 2 | Onde já há copa, a muda nova rende menos sombra |
| Pessoas expostas | 2 | Criança, idoso e renda mais baixa sentem mais o calor |
| Escola | 2 | A porta da escola concentra gente parada no sol |
| Espaço livre para plantar | 3 | Sem chão livre, a recomendação deixa de ser muda |

Um exemplo com números redondos, para conferir a fórmula. O setor A fica na porta de uma escola, com asfalto quente, quase sem árvore e com uma faixa de grama na calçada:

| Item | Nota | Peso | Parcela |
|---|---|---|---|
| Calor | 0,9 | 3 | 2,7 |
| Falta de árvore | 0,8 | 2 | 1,6 |
| Pessoas | 0,7 | 2 | 1,4 |
| Escola | 1,0 | 2 | 2,0 |
| Espaço | 0,6 | 3 | 1,8 |
| **Prioridade** | | | **9,5** |

O setor B, na mesma cidade, já tem copa, está mais fresco e não tem grama. As notas dele ficam baixas e a prioridade fica menor que 9,5. Se o espaço dele for 0, a recomendação é `outra medida`: toldo, piso mais claro ou copa espaçada, conforme o caso, e não uma cova de muda.

Ordene da maior prioridade para a menor. Grave `posicao` em 1, 2, 3, sem número repetido e sem salto. Se duas notas forem iguais, desempate pelo maior calor e registre a regra na coluna `criterio_desempate`.

No setor com espaço 0, ou que cruza unidade de conservação ou água, grave `recomendacao` como `outra medida`. Nos demais, grave `plantar`.

Grave os pesos usados em `resultados/pesos.csv`, para a ficha explicar a conta daquela execução.

### Como testar à mão

1. Abra `fila_plantio.csv`. Ordene por `posicao`. A sequência é 1, 2, 3, até o último setor urbano, sem repetição e sem salto.
2. Na posição 1, o calor está entre os mais altos da tabela e o verde entre os mais baixos. Um primeiro lugar frio e já arborizado indica peso ou escala de 0 a 1 invertidos.
3. Filtre `recomendacao` igual a `outra medida`. Essas linhas têm espaço 0 ou marca de conservação ou de água. Uma linha `plantar` dentro da Barreira do Inferno é erro: volte à marca de conservação da etapa 3.
4. Duplique a tabela, mude só o peso do calor de 3 para 1 e rode a conta de novo, gravando um segundo CSV com outro nome. As dez primeiras posições mudam. Se a lista continuar idêntica, a coluna de calor não entrou na soma.
5. Escolha uma linha e refaça a conta no papel, como no exemplo do setor A: calor × 3 + falta de árvore × 2 + pessoas × 2 + escola × 2 + espaço × 3. O resultado é a coluna `prioridade` dessa linha, com diferença só de arredondamento.

<a id="etapa-5"></a>

## 5. Conferir o resultado

Esta etapa não produz dado novo. Ela é a cancela: a fila só segue para a tela se os cinco testes passarem. Se um falhar, volte à etapa 3 ou à 4.

**No fim desta etapa** existem um mapa estático, um histograma e o arquivo `resultados/conferencia.txt` com data, nome de quem conferiu e `ok` ou `falhou` em cada item.

### O que fazer

1. Com `matplotlib`, gere em `resultados/` o histograma da coluna `prioridade` e um mapa dos setores colorido por `posicao`.
2. Percorra a lista abaixo e anote cada item no `conferencia.txt`.

### Como testar à mão

1. No mapa estático, o setor da posição 1 está em área quente e com pouco verde. A cor do mapa e a posição da tabela são da mesma execução. Se discordarem, mapa e CSV foram gerados em momentos diferentes.
2. Barreira do Inferno, Lagoa do Jiqui, Emaús, Cajueiro de Pirangi e Cotovelo aparecem como `outra medida`.
3. No CSV, o Jardim Planalto continua com arborização em torno de 46,4% e trechos sem árvore em torno de 51,4%. Quando houver o arquivo da UFRN, o Centro pode ser comparado às 661 árvores e palmeiras do levantamento de cerca de 2023.
4. A população somada continua perto de 252.716. Um setor urbano que existia na malha original e sumiu da fila precisa estar justificado, por exemplo com `situacao_dado` igual a `sem dado`.
5. O histograma mostra notas espalhadas. Uma nota única repetida em todos os setores significa que a escala de 0 a 1 não rodou.
6. Marque a conferência como `ok` quando os cinco itens passarem. Grave a data no `conferencia.txt`. A tela da etapa 6 só abre depois desse `ok`.

<a id="etapa-6"></a>

## 6. Abrir para o público

A página está em `app.py`. Ela lê `resultados/fila_plantio.gpkg`. Quem abre o navegador vê o mapa e a lista. Não mexe no script e não precisa de Python.

**No fim desta etapa** a prefeitura acessa um endereço no navegador, escolhe Parnamirim e baixa a fila do ano.

### O que fazer

1. Ative o ambiente, como na etapa 1.
2. Na pasta do projeto, rode:

```powershell
streamlit run app.py
```

3. O navegador abre uma página com quatro partes:
   - município, com Parnamirim já selecionado;
   - mapa dos setores colorido pela prioridade; o clique abre a ficha com calor, verde, pessoas, escolas, aviso de rega e a recomendação `plantar` ou `outra medida`;
   - lista da maior nota para a menor, com botão para baixar a tabela;
   - controles dos cinco pesos. Ao mover um peso, a fila é recalculada na memória e a ficha mostra os pesos daquela consulta. Exemplo: no ano de uma onda de calor, o peso do calor sobe e a lista muda na hora.
4. Deixe no rodapé três frases fixas: a nota diz onde ir primeiro para plantar e refrescar; a nota não mede oxigênio e não escolhe o canteiro; a árvore não entra como filtro da avenida mais poluída.

### Como testar à mão

1. A página abre sem mensagem de erro no navegador e sem rastreio vermelho no PowerShell.
2. Clique num setor. Os números da ficha são os mesmos da linha desse código em `fila_plantio.csv`.
3. Mude o peso do calor. A posição de pelo menos um setor no topo da lista muda, como no teste da etapa 4.
4. Baixe a tabela pelo botão. O arquivo abre no Excel e tem as mesmas colunas do CSV de `resultados/`.
5. Procure um setor marcado como `outra medida`. A ficha mostra esse texto, e não uma instrução de plantar.
6. Leia o rodapé. As três frases estão visíveis sem precisar rolar até o fim de uma ficha.

Para a prefeitura usar, deixe este comando rodando num computador de acesso interno. O endereço que aparece no PowerShell é o endereço da ferramenta no navegador.

<a id="ferramenta-automatica"></a>

## Da execução manual à ferramenta automática

As etapas 1 a 6 constroem e conferem o primeiro mapa, com uma pessoa olhando cada teste. Depois disso, a prefeitura abre o navegador e vê a lista. Quem mantém a ferramenta não refaz o roteiro à mão todo mês.

Um script, `atualizar.py`, repete as etapas 2, 3 e 4 sozinho. A página da etapa 6 continua no ar e passa a ler o arquivo novo.

### O que passa a acontecer

1. O Agendador de Tarefas do Windows chama `atualizar.py` numa data combinada. Chuva da EMPARN e temperatura do INMET podem entrar todo mês, porque a ficha de rega muda com a estação. O mapa do MapBiomas entra quando sai uma coleção nova. Censo e camadas do IDEMA só trocam quando um arquivo novo é colocado na pasta do órgão.
2. O script grava o download em `Data/`, com a data no nome, e mantém o arquivo anterior. Dá para comparar o mês passado com este.
3. Ele gera uma fila nova em `resultados/fila_plantio_nova.gpkg`.
4. Ele roda os testes da etapa 5. Se todos passarem, substitui `fila_plantio.gpkg` pela fila nova e move a fila antiga para `resultados/historico/`, com a data no nome.
5. Se um teste falhar, ou se um download vier vazio, o script mantém o mapa que já estava no ar. A prefeitura continua vendo a última fila boa. Um arquivo vazio de chuva, por exemplo, não apaga a lista de plantio da semana.
6. Cada execução escreve uma linha em `resultados/atualizacao.log`: data, hora, município, fontes lidas e o resultado `ok` ou `falhou`, com o motivo.

A página do `streamlit` lê `fila_plantio.gpkg` quando alguém abre o município. Não é preciso reiniciar a ferramenta para a fila nova aparecer. A pessoa da prefeitura abre o navegador na segunda-feira, escolhe o município e vê a lista. Quem cuida da atualização olha o `atualizacao.log` para saber se a rodada daquele mês passou.

O mesmo endereço serve aos 167 municípios. O seletor da página troca a cidade. A regra da fila é a mesma: calor, falta de árvore, pessoas, escola e espaço. Mudam o recorte, a chuva do posto local e a estação do INMET mais próxima. Parnamirim permanece o município de teste. Uma atualização só vai para as outras cidades depois de passar na conferência de Parnamirim.
