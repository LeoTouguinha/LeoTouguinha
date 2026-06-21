# 🏦 Data Foundation
### Pipeline de Dados para o Mercado Financeiro — Python + PySpark

---

## Sobre este repositório

Este repositório documenta um pipeline completo de dados aplicado ao mercado financeiro: da geração e ingestão de dados brutos até a análise de funil de receita com impacto de negócio quantificado.

> **Os parâmetros deste projeto são baseados em operação real:**  
> 100.000 cotações/mês, funil de 3 estágios, mix de modalidades 10/70/20.  
> Os dados gerados são sintéticos. Os números de negócio são reais.

---

## Stack

![Python](https://img.shields.io/badge/Python-3.10-blue)
![PySpark](https://img.shields.io/badge/PySpark-3.x-orange)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-green)

---

## Estrutura do Repositório

```
data-foundation/
│
├── README.md
├── .gitignore
│
├── data/
│   ├── raw/        ← Camada Bronze (gerada pelo notebook 01)
│   ├── silver/     ← Camada Silver (gerada pelo notebook 02)
│   └── gold/       ← Camada Gold / Star Schema (gerada pelo notebook 03)
│
├── notebooks/
│   ├── 01_geracao_bases.ipynb           ← Python puro
│   ├── 02_qualidade_dados.ipynb         ← PySpark
│   ├── 03_modelagem_dimensional.ipynb   ← PySpark — Star Schema
│   └── 04_funil_originacao.ipynb        ← PySpark — Análise de receita
│
└── src/
    └── utils.py    ← Funções e constantes reutilizáveis
```

---

## Notebooks

### 01 — Geração de Bases Sintéticas `Python`
Geração de 7 bases relacionadas que simulam o ambiente de dados de uma corretora no mercado financeiro.

| Base | Registros | Descrição |
|------|-----------|-----------|
| clientes | 1.000 | Cadastro base — chave mestra |
| transacoes | 10.000 | Movimentações com canal, valor e data |
| status_clientes | 1.000 | Ativo / Inativo (70/30) |
| classificacao_clientes | 1.000 | Segmentação A/B/C por valor |
| faixa_salarial | 1.000 | Renda com distribuição 50/30/20 |
| produtos_contratados | ~2.500 | Cross-sell — relação N:N |
| transacoes_raw | ~10.300 | Versão Bronze com erros de qualidade |

---

### 02 — Qualidade de Dados `PySpark`
Implementação das 4 dimensões de qualidade com quantificação de impacto financeiro.

**O que é analisado:**
- **Completude** — nulos em campos críticos e risco regulatório (BACEN 4.658)
- **Unicidade** — duplicatas e risco de dupla contagem no DRE
- **Integridade** — orphan records e risco de PLD/COAF
- **Validade** — regras de negócio: valores negativos, datas futuras

**Output:** DQ Score ponderado (0–100) + camada Silver tratada

---

### 03 — Modelagem Dimensional `PySpark`
Star Schema do funil de originação com decisões de arquitetura documentadas.

**Decisão documentada:** Star Schema sobre Data Vault — justificado pelo perfil analítico do consumidor e volume de 29k emissões/mês.

**Decisão documentada:** `taxa_comissao` como atributo degenerado na fato — porque varia por seguradora × cliente, não por produto nem canal.

```
dim_tempo ─────────────────────────────┐
dim_produto ──────── FATO_FUNIL ───────┤
dim_canal  ──────── ORIGINACAO ────────┤
dim_cliente ───────────────────────────┘
```

---

### 04 — Análise de Funil de Originação `PySpark`
A análise que gerou ação de negócio real.

**Contexto:** 100.000 cotações/mês. 29.000 viravam receita. Ninguém sabia onde as outras 71.000 morriam.

**Técnica central:** Anti-join para identificar propostas formalizadas sem emissão correspondente — leads quentes parados no funil.

**Output gerado:**
- Mapeamento dos dois gargalos com causas distintas
- Quantificação da comissão em risco nas propostas travadas
- Projeção de ganho incremental com melhoria de +6pp na conversão
- Lista de reengajamento priorizada por valor potencial

**Resultado:** processo de reengajamento ativo implementado pela diretoria.

---

## Arquitetura Medallion

```
┌──────────┐     ┌──────────┐     ┌──────────┐
│  BRONZE  │────▶│  SILVER  │────▶│   GOLD   │
│  (RAW)   │     │ Validado │     │Star Schema│
│ notebook │     │ notebook │     │ notebook │
│    01    │     │    02    │     │  03 + 04  │
└──────────┘     └──────────┘     └──────────┘
```

---

## Como Executar

### Pré-requisitos
```bash
# Anaconda (recomendado) ou pip
conda install -c conda-forge pyspark findspark
pip install pandas jupyter
```

### Sequência de execução
```bash
# 1. Abrir Jupyter no Anaconda Navigator
# 2. Executar os notebooks em ordem:

notebooks/01_geracao_bases.ipynb        # gera data/raw/
notebooks/02_qualidade_dados.ipynb      # gera data/silver/
notebooks/03_modelagem_dimensional.ipynb # gera data/gold/
notebooks/04_funil_originacao.ipynb     # análise final
```

> **Importante:** Execute o Notebook 01 antes de qualquer outro.  
> Os CSVs gerados em `data/raw/` são a entrada dos notebooks seguintes.

---

## Série #DataFoundation

Este repositório é a base técnica da série **#DataFoundation** no LinkedIn — posts que combinam código real com raciocínio de negócio do mercado financeiro.

Cada notebook corresponde a um post da série.

---

## Autor

**Léo** | Data Lead | Mercado Financeiro  
[LinkedIn](https://linkedin.com/in/[seu-usuario]) · [GitHub](https://github.com/[seu-usuario])
