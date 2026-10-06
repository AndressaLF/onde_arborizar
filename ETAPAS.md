# Etapas de execução

Este arquivo mostra o caminho da ferramenta, do arquivo baixado até a página da prefeitura. O [README.md](README.md) explica o problema, quem usa o mapa e de onde vêm os dados.

A primeira cidade é Parnamirim, código IBGE `2403251`. As outras cidades do Rio Grande do Norte entram depois que a fila desta cidade estiver conferida.

Os arquivos baixados ficam em `Data/`. Eles são a fonte: ninguém edita o original. Toda conta grava só em `resultados/`.

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

Python sozinho abre texto. Os dados chegam como tabela, desenho de mapa e imagem de satélite. Cada biblioteca lê um desses formatos ou mostra o resultado.

| Biblioteca | O que ela faz neste projeto |
|---|---|
| `pandas` | Lê e junta as tabelas de moradores, renda, escolas e chuva |
| `geopandas` | Lê o desenho dos setores e grava o mapa |
| `shapely` | Vê se a escola cai dentro do setor e se o setor encosta na lagoa |
| `pyproj` | Coloca os mapas no mesmo lugar do Brasil |
| `rasterio` | Lê a imagem de satélite do calor e do verde |
| `numpy` | Transforma calor e verde em notas e aplica os pesos |
| `requests` | Baixa um arquivo quando o endereço é direto |
| `matplotlib` | Desenha o gráfico e o mapa da conferência |
| `streamlit` | Monta a página no navegador |
| `folium` | Desenha o mapa com clique e zoom |
| `streamlit-folium` | Encaixa esse mapa na página |
| `openpyxl` | Lê a planilha do IBGE quando ela vem em Excel |

<a id="etapa-1"></a>

## 1. Preparar o ambiente

Esta etapa prepara o computador uma vez. As bibliotecas ficam numa caixa separada, chamada `.venv`, para não se misturarem com as de outros programas. A lista do que instalar fica em `requirements.txt`. No fim, a pasta `resultados/` existe e está vazia.

No PowerShell, dentro da pasta do projeto:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Quando o ambiente está ativo, o início da linha mostra `(.venv)`.

<a id="etapa-2"></a>

## 2. Baixar os dados

Cada órgão publica um pedaço da cidade. O arquivo entra na pasta daquele órgão, com a data do download no nome, e fica como veio.

| Pasta | O que entra |
|---|---|
| `Data/IBGE/` | Desenho dos setores, moradores, idade, renda, árvore, calçada e ponto de ônibus |
| `Data/MapBiomas/` | Calor, verde da cidade e o que cobre o chão |
| `Data/Escolas/` | Escolas, com endereço |
| `Data/EMPARN/` | Chuva do posto de Parnamirim |
| `Data/INMET/` | Temperatura da estação de Natal |
| `Data/IDEMA/` | Mata, água e área protegida |

Os sites estão na seção [Dados abertos](README.md#dados-abertos) do README. Postos e hospitais entram depois, no mesmo espírito das escolas.

O arquivo certo de moradores, somado, fica perto da população conhecida de Parnamirim no Censo 2022.

<a id="etapa-3"></a>

## 3. Organizar e cruzar

Os arquivos ainda estão separados. O programa `cruzar.py` junta tudo numa ficha: uma linha por setor. O código do setor continua texto, para a junção não perder gente. Mapas de órgãos diferentes passam para o mesmo sistema de coordenadas antes de se encontrarem.

No fim existem `resultados/setores_prontos.csv`, para abrir no Excel, e `resultados/setores_prontos.gpkg`, com o desenho do mapa. Ainda não há fila.

<a id="etapa-4"></a>

## 4. Calcular a fila

O programa `fila.py` transforma cada coluna numa nota de 0 a 1 e soma. O 1 é o caso mais urgente daquele item. Os pesos de partida são calor 3, falta de árvore 2, pessoas expostas 2, escola 2 e espaço para plantar 3.

O setor quente, com pouca árvore, gente no sol e chão livre sobe. Sem chão livre, ou em mata, lagoa e duna, a recomendação é `outra medida`. A posição 1 é onde a equipe começa.

No fim existem `resultados/fila_plantio.csv`, `resultados/fila_plantio.gpkg` e `resultados/pesos.csv`.

<a id="etapa-5"></a>

## 5. Conferir o resultado

Esta etapa não produz dado novo. Ela decide se a fila pode ir para a página. Um mapa e um gráfico ajudam a olhar a cidade inteira. O resultado de cada olhar fica em `resultados/conferencia.txt`, com a data e `ok` ou `falhou`.

A página só abre depois do `ok`. O primeiro lugar precisa estar quente e com pouco verde. Área protegida não aparece como lugar de muda.

<a id="etapa-6"></a>

## 6. Abrir para o público

O arquivo `app.py` lê a fila pronta e mostra a página. Quem usa a prefeitura não mexe nesse arquivo.

```powershell
streamlit run app.py
```

O navegador abre o mapa de Parnamirim, a lista e os pesos. O clique num setor mostra a ficha daquele pedaço. No rodapé ficam três avisos: a nota diz onde plantar primeiro; ela não mede oxigênio e não escolhe o buraco da muda; a árvore não entra como filtro da avenida mais poluída.

<a id="ferramenta-automatica"></a>

## Da execução manual à ferramenta automática

As etapas acima constroem e conferem o primeiro mapa. Depois disso, a prefeitura abre o navegador e vê a lista.

O programa `atualizar.py` repete o download, o cruzamento e a fila numa data combinada, pelo Agendador de Tarefas do Windows. A fila nova só substitui a que está no ar se a conferência passar. Se um arquivo vier vazio, a página continua com a última lista boa. Cada rodada escreve uma linha em `resultados/atualizacao.log`.

O mesmo endereço serve aos municípios do Rio Grande do Norte. Mudam o recorte, o posto de chuva e a estação de temperatura. Parnamirim continua sendo a cidade em que a conta é conferida primeiro.
