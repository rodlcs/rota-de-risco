# Rota de Risco

Análise de acidentes em rodovias federais brasileiras (dados abertos da PRF, 2022–2026) para identificar **trechos onde a gravidade dos acidentes é maior que a esperada** e verificar se esses trechos **continuam críticos ao longo do tempo**.

**Ferramentas:** Python, Pandas, SciPy, Power BI e uma aplicação web interativa (HTML, CSS e JavaScript).

## Pergunta

Em quais trechos das rodovias federais os acidentes graves aparecem acima do esperado, e isso se repete em um período posterior ou foi variação ao acaso?

## Dados

- **Fonte:** Polícia Rodoviária Federal, dados abertos de acidentes (`datatran`), 2022 a 2026.
- **Volume:** 326.815 acidentes. O ano de 2026 é parcial, até agosto.
- Os CSVs brutos não estão neste repositório por causa do tamanho. Baixe-os no portal de dados abertos da PRF e coloque-os na pasta `dados/`.
- Registros com `br = 0` e `km = 0` foram descartados.

## Método

1. **Trecho:** rodovia (BR) + estado (UF) + faixa de 10 km.
2. **Acidente grave:** pelo menos um morto ou um ferido grave.
3. **Métrica:** taxa de gravidade (graves ÷ total de acidentes do trecho), e não a contagem bruta.
4. **Períodos:** 2022–2024 define os trechos críticos; 2025–2026 (até agosto) testa se eles persistem.
5. **Trecho crítico:** pelo menos 20 acidentes no período de definição e taxa de gravidade acima da média nacional (28,4%), segundo teste binomial unilateral com correção de Benjamini-Hochberg (q < 5%).
6. **Persistência:** no período de teste, avaliados os trechos com pelo menos 11 acidentes.

## Resultados

- **132 trechos críticos** entre 2.229 elegíveis.
- Dos 118 críticos avaliáveis no teste, **93% continuaram acima da média nacional**.
- **12 trechos continuaram estatisticamente críticos** (critério mais rígido).
- A taxa média dos críticos passou de 49,1% para 41,7%, e a média nacional ficou estável (28,4% → 28,2%). A queda parcial é esperada por regressão à média.

### Teste de robustez

| Cenário | Trechos críticos | Excesso de gravidade mantido | Acima da média no teste | Continuam críticos | Correlação (Spearman) |
|---|---|---|---|---|---|
| Corte menor (n ≥ 10) | 207 | 67% | 93% | 9% | 0,46 |
| **Atual (n ≥ 20)** | **132** | **65%** | **93%** | **10%** | **0,53** |
| Corte maior (n ≥ 30) | 96 | 63% | 93% | 11% | 0,58 |
| Só mortes (n ≥ 20) | 29 | 61% | 96% | 4% | 0,36 |

Em todos os cenários testados, a correlação entre os períodos foi positiva e uma parte relevante do excesso de gravidade se manteve.

## Limitações

- A PRF não divulga volume de tráfego por trecho. A métrica mede **gravidade dos acidentes**, não risco por veículo.
- O ano de 2026 vai só até agosto.
- O "esperado" é a média nacional do período, sem ajuste por tipo de via, relevo ou clima.
- Resultado estatístico não indica causa: os trechos apontados merecem investigação, não são uma conclusão sobre o motivo da gravidade.

## Conteúdo do repositório

| Arquivo | Descrição |
|---|---|
| `analise.ipynb` | Notebook com todo o pipeline (leitura, limpeza, testes e exportações) |
| `dados_monitor.json` | Dados processados que alimentam a aplicação web |
| `trechos.csv` | Tabela por trecho com taxas e situação no teste |
| `sensibilidade.csv` | Resultados dos 4 cenários de robustez |
| `top5_causas_*.csv` | Causas mais graves, geral e por ano |

## Dashboard e aplicação

- **Dashboard Power BI** com três páginas: Visão geral, Trechos críticos e Robustez.
- **Monitor de Acidentes** (aplicação web): mapa dos trechos, filtros e simulador de critérios que recalcula os trechos críticos na hora.
  - Link: _[https://rodlcs.github.io/rota-de-risco/]_

## Como reproduzir

1. Coloque os CSVs da PRF em `dados/`.
2. Instale as dependências: `pip install pandas scipy jupyter`.
3. Execute `analise.ipynb` de cima para baixo.

## Contato

[Perfil no LinkedIn](https://www.linkedin.com/in/rodrigolucastec/)
