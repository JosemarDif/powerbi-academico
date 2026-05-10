# 🎓 Dashboard de Desempenho Académico — Power BI

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![Education](https://img.shields.io/badge/Área-Education%20Analytics-F5A623?style=flat)
![Status](https://img.shields.io/badge/Status-Concluído-4ADE80?style=flat)
![Registos](https://img.shields.io/badge/Dados-750%20Registos-F5A623?style=flat)

> Análise do desempenho de 250 alunos de ensino superior com 500 registos de notas. Identifica cursos críticos, disciplinas difíceis, alunos em risco e correlação entre frequência e aprovação.

---

## 🎯 Objetivos do Projeto

- Calcular taxas de aprovação por curso e disciplina
- Identificar **alunos em risco** (nota < 12 e frequência < 75%)
- Comparar o desempenho de bolseiros vs não bolseiros
- Analisar a correlação entre frequência e nota final
- Detectar disciplinas que necessitam de intervenção pedagógica

---

## 📁 Estrutura de Ficheiros

```
PBI4_Academico/
├── README.md
├── PBI4_Dados_Educacao.xlsx     ← Fonte de dados
│   ├── fAlunos                  (250 linhas — dados dos alunos)
│   └── fNotas                   (500 linhas — notas por disciplina)
└── GUIA_POWER_BI.md
```

---

## 🗂️ Modelo de Dados

```
fAlunos (250)
    │  1
    │  *
    └── fNotas (500)
        via ID_Aluno
```

**Relacionamento:**
- `fNotas[ID_Aluno]` → `fAlunos[ID_Aluno]` (N:1)

---

## 📋 Dicionário de Dados

### fAlunos
| Coluna | Tipo | Valores Exemplo |
|--------|------|----------------|
| ID_Aluno | Texto | ALU00001... |
| Curso | Texto | Eng. Informática · Gestão · Direito · Medicina... |
| Ano_Curricular | Inteiro | 1 – 5 |
| Nota_Media | Decimal | 8.0 – 20.0 |
| Frequência_Pct | Decimal | 55% – 100% |
| Situação | Texto | Aprovado · Reprovado · Condicional |
| Género | Texto | M / F |
| Bolseiro | Texto | Sim / Não |
| Modalidade | Texto | Presencial · E-Learning |

### fNotas
| Coluna | Tipo | Valores |
|--------|------|---------|
| ID_Aluno | Texto | Chave para fAlunos |
| Disciplina | Texto | Matemática · Algoritmos · BD · Redes · IA... |
| Tipo_Avaliação | Texto | Frequência · Exame Final · Trabalho Prático |
| Nota | Decimal | 0 – 20 |
| Época | Texto | 1ª Época · 2ª Época · Recurso |

---

## 📐 Medidas DAX

```dax
-- Total de Alunos
Total Alunos = COUNTROWS(fAlunos)

-- Taxa de Aprovação
Taxa Aprovação =
    DIVIDE(
        CALCULATE([Total Alunos], fAlunos[Situação] = "Aprovado"),
        [Total Alunos]
    )

-- Nota Média Geral
Nota Média = AVERAGE(fAlunos[Nota_Media])

-- Frequência Média
Frequência Média = AVERAGE(fAlunos[Frequência_Pct])

-- Alunos em Risco
Alunos em Risco =
    CALCULATE(
        [Total Alunos],
        fAlunos[Nota_Media] < 12 && fAlunos[Frequência_Pct] < 85
    )

-- % Bolseiros
% Bolseiros =
    DIVIDE(
        CALCULATE([Total Alunos], fAlunos[Bolseiro] = "Sim"),
        [Total Alunos]
    )

-- Média de Notas por Disciplina
Nota Média Disciplina = AVERAGE(fNotas[Nota])

-- Taxa de Reprovação por Disciplina
Taxa Reprovação Disciplina =
    DIVIDE(
        CALCULATE(COUNTROWS(fNotas), fNotas[Nota] < 10),
        COUNTROWS(fNotas)
    )

-- Disciplina Mais Difícil (menor média)
-- Usar TOP N visual ou TOPN() em tabela calculada
```

---

## 🖥️ Como Abrir no Power BI Desktop

**1. Importar os dados**
```
Power BI Desktop → Obter Dados → Excel
→ Seleccionar PBI4_Dados_Educacao.xlsx
→ Marcar AMBAS as sheets: fAlunos e fNotas
→ Carregar
```

**2. Verificar tipos no Power Query**
```
fAlunos[Nota_Media]       → Decimal Number
fAlunos[Frequência_Pct]   → Decimal Number
fAlunos[Ano_Curricular]   → Whole Number
fNotas[Nota]              → Decimal Number
```

**3. Criar relacionamento**
```
Vista Modelo:
→ Arrastar fNotas[ID_Aluno] para fAlunos[ID_Aluno]
→ Cardinalidade: Muitos para Um (N:1)
→ Dirección do filtro cruzado: Ambas
```

**4. Construir as 4 páginas**

| Página | Visuais |
|--------|---------|
| 📊 Resumo | KPI Cards · Gráfico Rosca (Situação) · Barras (Frequência por Ano) |
| 📚 Por Curso | Tabela com taxa aprovação · Gráfico de barras comparativo |
| 📝 Disciplinas | Gráfico de barras (nota média) · Tabela de reprovações |
| 👤 Perfil do Aluno | Rosca (género) · Barras (modalidade) · KPIs bolseiros |

---

## 📊 KPIs Principais

| KPI | Valor |
|-----|-------|
| Total Alunos | 250 |
| Taxa de Aprovação | 74.2% |
| Nota Média | 13.6 / 20 |
| Alunos em Risco | 42 |
| % Bolseiros | 18% |
| Nota Bolseiros | 14.8 (vs 13.3 não bolseiros) |

---

## 💡 Insights Obtidos

- 📉 **Estatística** é a disciplina mais difícil (média 11.2)
- 🏥 **Medicina** tem a maior taxa de reprovação (37.5%)
- 🎯 **Bolseiros** têm nota 11% acima da média geral
- 📉 Frequência diminui com os anos (88% no 1.º → 72% no 5.º)
- ⚠️ **42 alunos em risco** identificados para apoio pedagógico urgente
- 🏆 **Projecto** é a disciplina com melhor média (16.1)

---

## 🏷️ Tecnologias

`Power BI Desktop` `DAX` `Education Analytics` `CALCULATE` `DIVIDE` `Relacionamento N:1` `Slicers` `Formatação Condicional` `Gráfico de Rosca` `Tabela` `TOPN`

---

## 👤 Autor

**Josemar Manuel** · [LinkedIn](https://linkedin.com/in/josemarmanuel) · josemardiferencial@gmail.com · Luanda, Angola
