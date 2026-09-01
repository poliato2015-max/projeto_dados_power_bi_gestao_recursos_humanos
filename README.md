# 📊 Análise de Dados de RH - Dashboard em Power BI

Dashboard executivo de Recursos Humanos construído no Power BI, com preparação de dados no Power Query (linguagem M) e cálculos em DAX, desenvolvido como parte do Mini-Projeto 3 da formação em Análise/Ciência de Dados.

[![Power BI](https://img.shields.io/badge/Ferramenta-Power%20BI-F2C811?logo=powerbi&logoColor=black)](https://powerbi.microsoft.com)
[![DAX](https://img.shields.io/badge/Linguagem-DAX-blue)]()
[![Power Query](https://img.shields.io/badge/ETL-Power%20Query%20(M)-purple)]()
[![Status](https://img.shields.io/badge/Status-Concluído-brightgreen)]()

---

## 📋 Sobre o projeto

Este projeto tem como objetivo transformar uma base de dados bruta de Recursos Humanos em um **dashboard executivo** capaz de responder, de forma visual e imediata, perguntas como: qual o total de funcionários, qual a distribuição por gênero e estado cívil, qual o salário médio, qual o nível de envolvimento no trabalho, quantos treinamentos foram realizados e quantos funcionários estão disponíveis para hora extra.

Mais do que "montar gráficos", o desafio aqui foi de **análise de dados**: a base bruta (`06 - DatasetRH`) chegou com colunas em formatos que não podiam ser exibidos diretamente ao público de negócio, códigos numéricos (1 a 4) no lugar de categorias, e siglas ("S"/"N") no lugar de "Sim"/"Não". Todo o trabalho de tratamento, criação de colunas condicionais e medidas DAX documentado abaixo existe justamente para transformar esse dado bruto em informação pronta para decisão.

> *"Não é sobre criar gráficos ou fazer cálculos — é sobre analisar dados. Os dados precisam ser transformados e limpos antes de virarem informação visual."*

---

## 🖥️ Resultado final

![Dashboard de Análise de Dados para RH](https://raw.githubusercontent.com/poliato2015-max/imagens/main/projeto_dados_power_bi_gestao_recursos_humanos/projeto_dados_power_bi_gestao_recursos_humanos_dashboard.png)

O dashboard reúne:
- Segmentação de dados (slicer) por Idade
- Cartão: Total de Funcionários (+ Experiência Média em anos)
- Cartão: Gênero, com percentual de Masculino e Feminino
- Gráfico de rosca: distribuição por Estado Civil (Casado / Solteiro / Divorciado)
- Cartão: Salário Médio
- Tabela: Quantidade de Funcionários por Nº de Treinamentos no Ano
- Gráfico de barras horizontais: Total de Funcionários por Função
- Gráfico de barras: Envolvimento no Trabalho (Médio / Baixo / Alto / Ruim)
- Gráfico de pizza: percentual de funcionários disponíveis para hora extra (Sim / Não)

---

## 🎯 Objetivos de aprendizado

| Etapa | Ferramenta/Recurso | O que foi praticado |
|---|---|---|
| 📥&nbsp;**Carga&nbsp;de&nbsp;dados** | Power BI Desktop — Obter Dados (Texto/CSV) | Conectar a um arquivo CSV e validar o reconhecimento automático de colunas |
| 🧹&nbsp;**Preparação&nbsp;de&nbsp;dados** | Power Query (linguagem M) | Colunas condicionais, substituição de valores, engenharia de atributos |
| 🧮&nbsp;**Camada&nbsp;de&nbsp;cálculo** | DAX (AVERAGE, COUNTROWS, CALCULATE, DIVIDE) | Criação de uma tabela de medidas organizada e reutilizável |
| 🎨&nbsp;**Design&nbsp;do&nbsp;dashboard** | Formatação de visuais do Power BI | Cartões, formas com transparência, formatação de rótulos, legendas e percentuais |

---

## 🏗️ Etapas do projeto

### 1. Carregamento dos dados

- Em **Página Inicial → Obter Dados → Texto/CSV**, conectei o arquivo `06 - DatasetRH`.
- O Power BI reconheceu automaticamente os cabeçalhos das colunas (`Id_Funcionario`, `Idade`, `Genero`, `Estado_Civil`, `Departamento`, `Funcao`, `Indice_Envolvimento_trabalho`, `Salario_Mensal`, `Disponivel_Hora_Extra`, `Anos_Experiencia`, `Numero_Treinamentos_Ano_Anterior`).
- Não havia necessidade de transformação neste primeiro momento, os dados foram carregados diretamente para exploração na aba **Dados**.

### 2. Diagnóstico dos dados (antes de criar qualquer gráfico)

Antes de sair criando visual, o passo importante foi **questionar a matéria-prima**. Duas colunas chamaram atenção:

- **Índice Envolvimento no Trabalho**: valores numéricos de 1 a 4, mas o Power BI os interpretou como variável *quantitativa* (mostrou o ícone de somatório ∑). Na prática, é uma variável **categórica** (1 = Ruim, 2 = Baixo, 3 = Médio, 4 = Alto), o tipo de dado é uma decisão do analista com a área de negócio, não algo que a ferramenta decide sozinha.
- **Disponibilidade para Hora Extra**: valores representados apenas como `S` e `N`, quando o ideal para apresentação a um público de negócio é `Sim` e `Não`.

Regra de ouro aplicada aqui: **nunca deixar margem de interpretação para a audiência**, se alguém precisa adivinhar o que "S" ou o índice "3" significam, o gráfico não está pronto.

![Análise Dos Dados Carregados](https://raw.githubusercontent.com/poliato2015-max/imagens/main/projeto_dados_power_bi_gestao_recursos_humanos/projeto_dados_power_bi_gestao_recursos_humanos_analise_colunas_entrada.png)

### 3. Transformações no Power Query (linguagem M)

No Editor do Power Query (**Transformar Dados**), duas técnicas foram usadas, dependendo do caso:

**a) Substituição direta de valores**, usada quando a coluna já é categórica e só precisa de um "de-para" simples (poucos valores possíveis):
- Coluna `Disponível_HoraExtra`: clique direito → **Substituir Valores** → `S` → `Sim`, e depois `N` → `Não`.

**b) Coluna condicional**, usada quando o "de-para" tem várias regras, ou quando a variável está armazenada como número mas representa uma categoria (mudar o tipo diretamente exigiria conversão e traria risco de erro):
- Nova coluna **Envolvimento_Trabalho**, criada em **Adicionar Coluna → Coluna Condicional**, com base na coluna original de envolvimento no trabalho:
  - Se `= 1` → `"Ruim"`
  - Se `= 2` → `"Baixo"`
  - Se `= 3` → `"Médio"`
  - Se `= 4` → `"Alto"`

**c) Sem transformação necessária** — colunas que já chegaram prontas para uso direto nos visuais: `Estado Civil`, `Departamento`, `Funcao`, `Numero_Treinamentos_Ano`.

Depois de cada transformação: **Página Inicial → Fechar e Aplicar**.

> 📌 **Por que criar coluna nova em vez de sobrescrever?** Substituir valores é ideal para variáveis binárias/simples já em formato de texto. Para variáveis numéricas com significado categórico e várias regras, criar uma coluna nova evita erro de conversão de tipo e preserva a coluna original, uma boa prática de engenharia de atributos.

![Adicionar Coluna Condicional](https://raw.githubusercontent.com/poliato2015-max/imagens/main/projeto_dados_power_bi_gestao_recursos_humanos/projeto_dados_power_bi_gestao_recursos_humanos_analise_adicionar_coluna.png)

### 4. Criação da tabela de medidas (DAX)

Antes de criar as medidas, foi criada uma **tabela vazia** só para organizá-las: **Inserir Dados → nomear como "Medidas" → Carregar**. Isso não é obrigatório (as medidas poderiam ficar na própria tabela de dados), mas facilita uma futura manutenção e todas as métricas do dashboard ficam em um único lugar.

Medidas criadas (botão direito em *Medidas* → **Nova Medida**):

```DAX
TotalFunc = COUNTROWS('06 - DatasetRH')

TotalFeminino = CALCULATE([TotalFunc], '06 - DatasetRH'[Genero] = "Feminino")

TotalMasculino = CALCULATE([TotalFunc], '06 - DatasetRH'[Genero] = "Masculino")

% Masculino = DIVIDE([TotalMasculino], [TotalFunc], 0)

% Feminino = DIVIDE([TotalFeminino], [TotalFunc], 0)

SalarioMedio = AVERAGE('06 - DatasetRH'[Salario_Mensal])

```

**Por que usar medidas DAX em vez do cálculo padrão do Power BI (arrastar e soltar com agregação automática)?** O cálculo padrão não é reutilizável em outros cálculos e não tem customização,  é recalculado em tempo de execução sem flexibilidade. Uma medida DAX é armazenada, pode ser reaproveitada dentro de outras medidas (como `% Masculino` reaproveitando `TotalMasculino` e `TotalFunc`) e, em bases maiores, tende a performar melhor.

![Adicionar Coluna Medidas](https://raw.githubusercontent.com/poliato2015-max/imagens/main/projeto_dados_power_bi_gestao_recursos_humanos/projeto_dados_power_bi_gestao_recursos_humanos_analise_adicionar_medidas.png)

### 5. Formatação de valores percentuais

As medidas de percentual (`% Masculino`, `% Feminino`) vinham como número decimal bruto. A formatação foi feita direto no **painel Modelo → selecionar a medida → Propriedades → Formato → Porcentagem** (com duas casas decimais), em vez de aplicar formatação manual em cada visual — assim a formatação fica atrelada à própria medida.

### 6. Construção dos elementos visuais

| Visual | Campos usados |
|---|---|
| Cartão - Total de Funcionários / Experiência Média | `[TotalFunc]` (título em cima) + `Anos_Experiencia` com agregação Média (rótulo embaixo) |
| Cartão - Gênero (combinado, com ícones) | `[% Masculino]` ao lado do ícone masculino + `[% Feminino]` ao lado do ícone feminino |
| Gráfico de rosca - Estado Civil | Legenda: `Estado Civil` · Valores: `[TotalFunc]` |
| Cartão - Salário Médio | Valor: `[SalarioMedio]` |
| Tabela - Treinamentos no ano | Colunas: `Numero_Treinamentos_Ano` e `[TotalFunc]` (com total geral no rodapé) |
| Gráfico de barras - Funcionários por Função | Eixo Y: `Funcao` · Eixo X: `[TotalFunc]` |
| Gráfico de barras - Envolvimento no Trabalho | Eixo X: `Envolvimento_Trabalho` · Eixo Y: `[TotalFunc]` |
| Gráfico de pizza - Hora Extra | Legenda: `Disponivel_Hora_Extra` · Valores: `[TotalFunc]` |

### 7. Formatação final do dashboard

- **Títulos e rótulos**: título habilitado na parte de cima de cada visual (em negrito, centralizado); rótulo de categoria removido dos cartões que mostram só uma informação. Nos cartões com duas métricas (ex. Total de Funcionários + Experiência Média), manteve-se um título em cima e um rótulo customizado embaixo, para diferenciar as duas informações.
- **Eixos e legendas**: título do eixo removido quando redundante com o próprio conteúdo do gráfico (ex. eixo Y do gráfico de barras, já que os nomes das funções falam por si). Legendas posicionadas na parte inferior central nos gráficos de pizza e rosca.
- **Rótulos de detalhe**: no gráfico de pizza (2 categorias), mantido somente o percentual. No gráfico de rosca (4 categorias), mantidos valor absoluto + percentual, por ter mais categorias para interpretar.
- **Efeito de "caixinha"**: em vez de aplicar contorno em cada visual individualmente, foi inserida uma **forma retangular com cantos arredondados** (Inserir → Formas) por cima de cada grupo de valores, com a cor ajustada e a **transparência** reduzida gradualmente até simular um cartão. Uma forma de linha foi usada para separar visualmente duas métricas dentro do mesmo cartão (ex. Total Masculino e seu percentual).

---

## 📂 Estrutura sugerida do repositório

```
📁 projeto_dados_power_bi_gestao_recursos_humanos/
├── 📄 README.md
├── 📊 06 - Dashboard_RH.pbix
└── 📄 06 - DatasetRH.csv

```

## 🧠 Principais aprendizados

**1. Tipo de dado é decisão do analista, não da ferramenta**
O Power BI classificou a coluna de envolvimento no trabalho como numérica só porque continha números, mas semanticamente era uma categoria. Reconhecer isso evitou um cálculo sem sentido (média de uma categoria) e guiou a escolha certa: coluna condicional em vez de agregação numérica.

**2. Substituir valores vs. coluna condicional**
Para dado binário já em texto (S/N), substituir valores é suficiente. Para dado numérico com significado categórico e várias regras (1 a 4), criar uma coluna condicional nova evita conversão de tipo arriscada e preserva a coluna original.

**3. Tabela de medidas como boa prática de organização**
Centralizar todas as medidas DAX em uma tabela dedicada (em vez de deixá-las espalhadas dentro da tabela de dados) facilita manutenção, leitura e reaproveitamento, uma medida pode ser usada dentro de outra medida (ex. `Percentual Masculino` reaproveita `Total Masculino` e `Total Func`).

**4. Reduzir a margem de interpretação da audiência**
Cada escolha de formatação (nomes de categoria em vez de código, percentual com casas decimais, remoção de títulos redundantes) teve como objetivo entregar uma informação que o público de RH entende no primeiro olhar, sem precisar perguntar "o que significa isso?".

---

## 🛠️ Tecnologias utilizadas

| Tecnologia | Uso no projeto |
|---|---|
| **Power BI Desktop** | Ferramenta principal de BI e construção do dashboard |
| **Power Query (M)** | Transformação e limpeza dos dados (colunas condicionais, substituição de valores) |
| **DAX** | Camada de cálculo — tabela de medidas (AVERAGE, COUNTROWS, CALCULATE, DIVIDE) |
| **CSV** | Formato de origem dos dados brutos de RH |

---

## 👨‍💻 Autor

*(inclua aqui seus badges de LinkedIn/GitHub, seguindo o mesmo padrão dos seus outros projetos)*
