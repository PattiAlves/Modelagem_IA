<div align="center">

# Reconstrução do nível da Lagoa de Jacarepaguá com aprendizado de máquina

**Chuva, maré e nível lagunar como base para um índice de resiliência urbana à inundação na bacia do Rio das Pedras (RJ)**

![Disciplina](https://img.shields.io/badge/EQM2118-PUC--Rio-0F2831)
![Python](https://img.shields.io/badge/Python-pandas%20%7C%20SciPy-2A6F8F?logo=python&logoColor=white)
![Status](https://img.shields.io/badge/status-Trabalho%201%20em%20andamento-E39A52)

<a href="https://youtu.be/KPCK3wu9cNU">
  <img src="https://img.youtube.com/vi/KPCK3wu9cNU/hqdefault.jpg" alt="Vídeo de apresentação do Trabalho 1" width="480">
</a>

*Clique na imagem para assistir à apresentação do Trabalho 1 no YouTube*

</div>

---

### Sumário

[Sobre o projeto](#sobre-o-projeto) · [Estudo de caso](#estudo-de-caso) · [Dados](#dados) · [Fluxo metodológico](#fluxo-metodológico) · [Scripts](#scripts) · [Banco de dados](#banco-de-dados) · [Escolhas metodológicas](#escolhas-metodológicas) · [Roteiro](#roteiro) · [Equipe](#equipe)

---

## Sobre o projeto

O ponto de partida é o **Índice de Resiliência Urbana à Inundação (IRUI)**, que avalia cada célula de uma bacia em três dimensões:

| Antes da inundação | Durante | Depois |
|:---:|:---:|:---:|
| **Absorção** | **Enfrentamento** | **Recuperação e adaptação** |

Parte dos indicadores do índice, como áreas inundáveis e redes de drenagem, depende diretamente do nível d’água. No Rio das Pedras, esse nível responde também à lagoa e à maré, mas a lagoa só foi medida entre 2010 e 2017.

> **Objetivo:** reconstruir, por imputação com aprendizado de máquina, a série do nível da lagoa nos períodos sem medição, a partir das séries de chuva e maré, e correlacioná-la às cotas de inundação simuladas na bacia.

> [!NOTE]
> Este repositório corresponde ao **Trabalho 1**: análise crítica da base de dados e tratamento das séries. O treinamento do modelo vem na etapa seguinte, e a aplicação do IRUI ao Rio das Pedras é um trabalho futuro.

## Estudo de caso

| Extensão do rio | População da bacia | Exutório |
|:---:|:---:|:---:|
| ~5,5 km | ~67 mil habitantes | Sistema lagunar de Jacarepaguá |

A bacia ocupa um antigo brejo aterrado, plano e mal drenado, na Baixada de Jacarepaguá. Ali se sobrepõem três fontes de inundação: a chuva sobre uma área muito impermeabilizada, o transbordamento do canal e a oscilação da lagoa e da maré, que alaga a bacia mesmo sem chuva.

> [!IMPORTANT]
> **Premissa:** as lagoas de Jacarepaguá e da Tijuca são conectadas; considera-se que ambas seguem o mesmo nível d’água.

## Dados

- **Precipitação** — Alerta Rio, Estação Jacarepaguá/Cidade de Deus · série contínua no período de estudo
- **Nível de maré** — BNDO/CHM (Marinha do Brasil), Estação Recreio dos Bandeirantes · série contínua no período de estudo
- **Nível da lagoa** — Rio-Águas, Estação Rede Sarah · medições a cada 10 min, de 14/07/2010 a 16/09/2017

As planilhas não são versionadas neste repositório.

## Fluxo metodológico

| **3.1** Obtenção e tratamento | | **3.2** Treino e validação | | **3.3** Reconstrução e consistência | | **3.4** Correlação |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| chuva · maré · lagoa em 10 min | ➜ | Random Forest Regressor | ➜ | imputação da lagoa e checagem física | ➜ | Spearman com as cotas de inundação |
| 🟠 em andamento | | ⚪ a fazer | | ⚪ a fazer | | ⚪ a fazer |

## Scripts

Os scripts em Python ficam na pasta [`Interpolação`](Interpola%C3%A7%C3%A3o) e levam as séries de chuva e maré para a grade de 10 minutos da lagoa.

#### 🌊 [`Maré`](Interpola%C3%A7%C3%A3o/Mar%C3%A9)

Interpolação por **spline cúbica** (`scipy.interpolate.CubicSpline`) da série da Estação Recreio dos Bandeirantes.
`Maré_jul10.xlsx` ➜ `maré_10min.xlsx`

#### 🌧️ [`Precipitação`](Interpola%C3%A7%C3%A3o/Precipita%C3%A7%C3%A3o)

Conversão do acumulado horário da Estação Jacarepaguá/Cidade de Deus para 10 minutos, por **interpolação linear entre as horas seguida da divisão por 6**.
`chuva_horaria.xlsx` ➜ `chuva_10min.xlsx`

Os procedimentos estão descritos em [`Interpolação/README`](Interpola%C3%A7%C3%A3o/README). Uma versão preliminar do banco consolidado, com 2.486 registros (14 a 31/07/2010), foi usada para validar o procedimento.

<details>
<summary><b>Como executar</b></summary>

<br>

**No Google Colab** (já possui `pandas`, `numpy`, `scipy` e `openpyxl`):

1. abra um novo notebook no [Google Colab](https://colab.research.google.com/);
2. envie a planilha de entrada pelo painel **Arquivos**;
3. cole o conteúdo do script em uma célula e execute;
4. baixe a planilha de saída pelo mesmo painel.

**Localmente:**

```bash
pip install pandas numpy scipy openpyxl
```

</details>

## Banco de dados

Série temporal em passo de 10 minutos.

| Coluna | Descrição | Origem | Função |
|---|---|---|---|
| `datetime` | Data e hora | — | Índice temporal |
| `precip_10min` | Chuva convertida para 10 min | Alerta Rio | 🔹 Preditor |
| `nivel_mare_10min` | Maré interpolada | BNDO/CHM | 🔹 Preditor |
| `lagoa_10min` | Nível da Lagoa de Jacarepaguá | Rio-Águas | 🎯 Variável-alvo |
| `id_evento` | Evento de chuva simulado | MODCEL | 🔸 Correlação *(a incluir)* |
| `lamina_celula_<id>` | Cota de inundação nas células selecionadas | MODCEL | 🔸 Correlação *(a incluir)* |

## Escolhas metodológicas

Legenda: ✅ aplicada · ⏳ prevista ou a definir · ➖ não aplicada

| | Técnica | Por quê |
|:---:|---|---|
| ✅ | Compatibilização temporal | As séries têm resoluções diferentes e precisam de uma grade comum de 10 min |
| ✅ | Spline cúbica (maré) | A maré é um sinal contínuo e suave; a spline preserva a forma da curva |
| ✅ | Conversão proporcional (chuva) | A chuva é intermitente; uma spline geraria oscilações e valores negativos |
| ⏳ | Imputação de dados faltantes | É o objetivo central: reconstruir o nível da lagoa fora de 2010–2017 |
| ⏳ | Detecção de outliers | Picos de sensor na lagoa devem ser avaliados frente a limites físicos |
| ⏳ | Correlação de Spearman | Relação monotônica entre o nível da lagoa e as cotas de inundação |
| ➖ | PCA | Há apenas dois preditores; reavaliar se forem criadas variáveis defasadas |
| ➖ | SMOTE | O problema é de regressão, não de classificação desbalanceada |
| ➖ | Divisão aleatória treino/teste | A ordem temporal deve ser preservada |
| ➖ | Normalização | Árvores de decisão não dependem da escala; reavaliar se forem testados SVM ou redes neurais |

**Modelagem prevista:** Random Forest Regressor, com treino de 14/07/2010 a 13/07/2015 e teste de 14/07/2015 a 16/09/2017, avaliado por R², RMSE, MAE e eficiência de Nash-Sutcliffe (NSE).

## Organização do repositório

```text
Modelagem_IA/
├── README.md
└── Interpolação/
    ├── README          # descrição dos procedimentos de interpolação
    ├── Maré            # script Python: spline cúbica da maré
    └── Precipitação    # script Python: conversão da chuva para 10 min
```

## Roteiro

- [x] Conversão das séries de chuva e maré para 10 minutos
- [ ] Consolidação do banco e auditoria de duplicatas, lacunas e faltantes
- [ ] Treinamento e validação do Random Forest Regressor
- [ ] Reconstrução da série da lagoa e análise de consistência física
- [ ] Correlação de Spearman com as cotas de inundação do MODCEL

**Desdobramentos futuros:** aplicar o IRUI ao Rio das Pedras com o indicador da lagoa atualizado e transpor o método para outras bacias sob influência de lagoa e maré, como a da Lagoa Rodrigo de Freitas e o centro histórico de Paraty.

## Equipe

| Autoras | Disciplina | Professor |
|---|---|---|
| Carolina Lopes<br>Patrícia Alves | EQM2118 – Modelagem Matemática com Aplicação de Inteligência Artificial · PUC-Rio | Brunno Ferreira dos Santos |
