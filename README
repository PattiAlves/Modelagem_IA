# Reconstrução do nível da Lagoa de Jacarepaguá com aprendizado de máquina

[![Open Notebook 1 in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/PattiAlves/Modelagem_IA/blob/main/notebooks/01_tratamento_dados.ipynb)

Banco de dados de chuva, maré e nível da lagoa como base para um índice de resiliência urbana à inundação na bacia do Rio das Pedras (Rio de Janeiro), desenvolvido na disciplina **EQM2118 – Modelagem Matemática com Aplicação de Inteligência Artificial**.

## Apresentação do Trabalho

A apresentação em vídeo do **Trabalho 1** está disponível no YouTube.

[![Assistir à apresentação no YouTube](https://img.youtube.com/vi/KPCK3wu9cNU/hqdefault.jpg)](https://youtu.be/KPCK3wu9cNU)

▶ **[Assistir à apresentação no YouTube](https://youtu.be/KPCK3wu9cNU)**

## Executar o projeto

Os arquivos `.ipynb` são notebooks executáveis. No GitHub eles são exibidos de forma estática; para executar as células, utilize o botão **Open in Colab** acima.

No Google Colab, os notebooks utilizam o Google Drive como área de trabalho, na pasta:

```text
/content/drive/MyDrive/Modelagem_IA
```

Os arquivos brutos devem ser colocados em `data/raw/` dentro dessa pasta. Como as séries de 10 minutos ao longo de vários anos são volumosas, os dados não são versionados no GitHub.

Para execução local, as dependências estão em [`requirements.txt`](requirements.txt):

```bash
pip install -r requirements.txt
```

## Contexto do projeto

A pesquisa que motiva este trabalho é o **Índice de Resiliência Urbana à Inundação (IRUI)**, que avalia a resiliência de cada célula de uma bacia em três dimensões: absorção, enfrentamento, e recuperação e adaptação. Parte dos indicadores do índice, como áreas inundáveis e redes de drenagem, depende diretamente do nível d’água.

Na bacia do Rio das Pedras, esse nível é condicionado não apenas pela chuva, mas também pela dinâmica da lagoa e da maré: a oscilação da lagoa alaga a bacia mesmo sem chuva. No entanto, o nível da lagoa só foi medido entre julho de 2010 e setembro de 2017, enquanto chuva e maré possuem séries contínuas.

O objetivo é **reconstruir, por imputação com aprendizado de máquina, a série do nível da lagoa nos períodos sem medição**, a partir das séries de chuva e maré, e **correlacioná-la às cotas de inundação simuladas** na bacia do Rio das Pedras.

Nesta fase, correspondente ao **Trabalho 1**, o foco está na análise crítica da base de dados, no tratamento e na consolidação das séries. O treinamento do modelo será realizado na etapa seguinte. A aplicação do IRUI à bacia do Rio das Pedras é um trabalho futuro.

## Área de estudo

O Rio das Pedras, com cerca de 5,5 km de extensão, fica na Baixada de Jacarepaguá e deságua no sistema lagunar de Jacarepaguá. A bacia tem cerca de 67 mil habitantes e ocupa um antigo brejo aterrado, plano e mal drenado.

**Premissa adotada:** as lagoas de Jacarepaguá e da Tijuca são conectadas; considera-se que ambas seguem o mesmo nível d’água.

## Fonte dos dados

| Variável | Fonte | Estação | Disponibilidade |
|---|---|---|---|
| Precipitação | Alerta Rio | Jacarepaguá/Cidade de Deus | Série contínua no período de estudo |
| Nível de maré | BNDO/CHM (Marinha do Brasil) | Recreio dos Bandeirantes | Série contínua no período de estudo |
| Nível da lagoa | Rio-Águas | Rede Sarah | 14/07/2010 a 16/09/2017, a cada 10 min |

## Tratamento dos dados

As três séries foram compatibilizadas na frequência de 10 minutos, que é a da lagoa:

1. **Maré:** interpolação por spline cúbica (`scipy.interpolate.CubicSpline`);
2. **Precipitação:** redistribuição proporcional do acumulado horário nos seis intervalos de 10 minutos;
3. **Consolidação:** união das séries pela coluna `datetime` e auditoria do banco (duplicatas, lacunas e valores faltantes).

O processo está documentado em:

- [`notebooks/01_tratamento_dados.ipynb`](notebooks/01_tratamento_dados.ipynb)
- [`outputs/reports/relatorio_tratamento_dados.md`](outputs/reports/relatorio_tratamento_dados.md) (gerado pelo notebook)

Uma versão preliminar do banco consolidado, com 2.486 registros (14 a 31/07/2010), foi utilizada para validar o procedimento antes de estendê-lo ao período completo.

## Estrutura do banco de dados

| Coluna | Conteúdo | Origem | Papel no modelo |
|---|---|---|---|
| `datetime` | Data e hora, passo de 10 min | — | Índice temporal |
| `precip_10min` | Chuva redistribuída em 10 min | Alerta Rio | Preditor |
| `nivel_mare_10min` | Maré interpolada (spline cúbica) | BNDO/CHM | Preditor |
| `lagoa_10min` | Nível da Lagoa de Jacarepaguá | Rio-Águas | Variável-alvo |
| `id_evento` | Evento de chuva simulado | MODCEL | Correlação (a incluir) |
| `lamina_celula_<id>` | Cota de inundação simulada nas células selecionadas | MODCEL | Correlação (a incluir) |

## Decisões metodológicas

Nem todas as técnicas estudadas na disciplina se aplicam a este problema. A escolha foi condicionada à natureza dos dados.

| Técnica | Decisão | Justificativa |
|---|---|---|
| Compatibilização temporal (data wrangling) | Aplicada | As séries têm resoluções diferentes e precisam de uma grade comum de 10 min |
| Interpolação por spline cúbica | Aplicada à maré | A maré é um sinal contínuo e suave; a spline preserva a forma da curva |
| Redistribuição proporcional | Aplicada à chuva | A chuva é intermitente; uma spline geraria oscilações e valores negativos |
| Imputação de dados faltantes | Objetivo central | O nível da lagoa só tem medição entre 2010 e 2017; a imputação por regressão reconstrói os demais anos |
| Detecção de outliers | A definir na auditoria | Picos de sensor no nível da lagoa devem ser avaliados frente a limites físicos |
| PCA (redução de dimensionalidade) | Não aplicada nesta fase | Há apenas dois preditores; reavaliar caso sejam criadas variáveis defasadas |
| SMOTE | Não aplicado | O problema é de regressão, não de classificação desbalanceada |
| Divisão aleatória treino/teste | Não aplicada | A ordem temporal deve ser preservada |
| Normalização | Não necessária para Random Forest | Modelos baseados em árvores não dependem da escala; reavaliar se forem testados SVM ou redes neurais |
| Correlação de Spearman | Prevista | Relação monotônica entre o nível da lagoa e as cotas de inundação |

## Preparação experimental

O modelo de regressão será um **Random Forest Regressor**, com divisão temporal:

- **Treino:** 14/07/2010 a 13/07/2015;
- **Teste:** 14/07/2015 a 16/09/2017.

As métricas previstas são **R², RMSE, MAE e eficiência de Nash-Sutcliffe (NSE)**.

## Estrutura do repositório

```text
Modelagem_IA/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── 01_tratamento_dados.ipynb
├── data/
│   ├── raw/
│   ├── interim/
│   └── processed/
└── outputs/
    ├── reports/
    ├── figures/
    ├── tables/
    └── presentations/
        └── README.md
```

## Próximas etapas

1. **Treino e validação:** treinamento do Random Forest Regressor e avaliação no período de teste;
2. **Reconstrução e consistência:** imputação do nível da lagoa nos períodos sem medição e verificação frente à batimetria e às cotas de transbordamento;
3. **Correlação:** correlação de Spearman entre a série reconstruída e as cotas de inundação simuladas no modelo quasi-2D (MODCEL), quantificando o efeito de remanso.

## Trabalhos futuros

- Aplicar o IRUI à bacia do Rio das Pedras, com o indicador da lagoa atualizado pela série reconstruída;
- Transpor o método para outras bacias onde lagoa e maré influenciam as inundações, como a bacia da Lagoa Rodrigo de Freitas e o centro histórico de Paraty.

## Autoras

- Carolina Lopes
- Patrícia Alves

## Disciplina

**EQM2118 – Modelagem Matemática com Aplicação de Inteligência Artificial**  
Professor: **Brunno Ferreira dos Santos**  
PUC-Rio
