# app-data-wise

# Resumo do que cada request retorna

A lógica geral é: **o que você coloca em `agregado` + `nivelAbaixo`** define **o nível da entidade** que vem no `result`.

---

## 1) `fetch-classes-by-school` (turmas por escola)
**Objetivo:** listar **turmas** de uma **escola** específica.  
**Como vem no request:**  
- `agregado = <CD_INSTITUICAO>` (código da escola)  
- `nivelAbaixo = "1"`  

**O que retorna (por item em `result`):**  
- `DC_TIPO_ENTIDADE`: **"TURMA"**  
- `CD_ENTIDADE`: código da **turma**  
- `CD_ENTIDADE_SUPERIOR`: **CD da escola**  
- `NM_ENTIDADE` / `NM_TURMA`: nome da turma (ex.: “1º ANO A”)  
- `VL_FILTRO_DISCIPLINA`, `VL_FILTRO_ETAPA`, `VL_FILTRO_AVALIACAO`  
- Indicadores agregados da turma (ex.: `TX_PARTICIPACAO`, `DC_ATIVIDADES`, `TX_ACERTOS`, `NU_N01/N02/N03`, `TX_ACERTO_HABILIDADE_i`)

**Checklist de validação:**  
- `DC_TIPO_ENTIDADE` = **"TURMA"** 
- `CD_ENTIDADE_SUPERIOR` = **CD da escola passada**

**Use quando:** quer ver o **painel de turmas** de uma escola, comparar desempenho **entre turmas**.

---

## 2) `fetch-schools` (escolas por município)
**Objetivo:** listar **escolas** de um **município** (com filtros como etapa, disciplina, rede).  
**Como vem no request:**  
- `agregado = <CD_MUNICIPIO>` (ex.: `4306809`)  
- `nivelAbaixo = "1"`  
- `filtros`: `VL_FILTRO_AVALIACAO`, `VL_FILTRO_ETAPA`, `VL_FILTRO_DISCIPLINA`  
- `filtrosAdicionais`: `VL_FILTRO_REDE` (ex.: “PÚBLICA”)

**O que retorna (por item em `result`):**  
- `DC_TIPO_ENTIDADE`: **"ESCOLA"**  
- `CD_ENTIDADE`: código da **escola**  
- `CD_ENTIDADE_SUPERIOR`: **CD do município**  
- `NM_ENTIDADE` / `NM_INSTITUICAO`: nome da escola  
- Métricas no nível **escola** (ex.: `TX_PARTICIPACAO`, `DC_ATIVIDADES`, `TX_ACERTOS`, `TX_N01/N02/N03`, `TX_ACERTO_HABILIDADE_i`)  
- Confirmações de contexto: `CD_MUNICIPIO`, `NM_MUNICIPIO`, `VL_FILTRO_*`

**Checklist de validação:**  
- `DC_TIPO_ENTIDADE` = **"ESCOLA"** 
- `CD_ENTIDADE_SUPERIOR` = **CD do município**  
- Campos `VL_FILTRO_*` batendo com seus filtros 

**Use quando:** precisa de um **ranking/lista de escolas** dentro do município, para uma **disciplina/etapa** específicos.

---

## 3) `fetch-students-by-classes` (alunos por turma)
**Objetivo:** listar **alunos** de uma **turma** específica (com resultados por disciplina).  
**Como vem no request:**  
- `agregado = <CD_TURMA>` (ex.: `z8yg99e75f22`)  
- `nivelAbaixo = "1"`  

**O que retorna (por item em `result`):**  
- `DC_TIPO_ENTIDADE`: **"ALUNO"**  
- `CD_ENTIDADE`: **ID do aluno**  
- `CD_ENTIDADE_SUPERIOR`: **CD da turma**  
- `NM_ENTIDADE`: nome do aluno  
- **Um registro por disciplina** (LP e MT), com:  
  - `DC_ATIVIDADES` (ex.: “22 de 22”)  
  - `NU_ACERTOS`, `TX_ACERTOS`, `DC_ACERTOS` (“x / 22”)  
  - `VL_PONTUACAO` e `DC_PONTUACAO` (“Adequado”, “Intermediário”, “Defasagem”)  
  - `NU_ACERTO_HABILIDADE_i` (“x / y” por habilidade)  
  - `NU_MODELO_CADERNO` (versão do caderno)

**Checklist de validação:**  
- `DC_TIPO_ENTIDADE` = **"ALUNO"** 
- `CD_ENTIDADE_SUPERIOR` = **CD da turma**   
- Mesmo aluno aparece em **duas linhas** (LP e MT) 

**Use quando:** quer ver o **detalhe individual** por aluno (acertos, habilidades, classificação).

---

## Dicas (Caso Academico):

- Cuidado com a estrutura HTTP:
- headers → linha em branco → body JSON sem comentários.
- Campos sensíveis (_SessionToken, _InstallationId) não devem ir para repositórios públicos.


os indicadores não dependem do nível de agregação, mas sim da avaliação aplicada.

Indicadores = identificam disciplinas + cadernos (LP14, MT14, etc).

Agregado = muda o nível que você quer ver os resultados:

Município → CD_MUNICIPIO

Escola → CD_INSTITUICAO

Turma → CD_TURMA

Aluno → CD_ENTIDADE

Ou seja, os indicadores são sempre os mesmos porque a prova aplicada é a mesma.
O que muda é onde você foca:

No município inteiro,

numa escola,

numa turma,

ou em um aluno.

🔎 Exemplo prático

fetch-schools → pega resultados agregados de várias escolas.

fetch-classes-by-school → pega resultados das turmas de uma escola.

fetch-students-by-class → pega resultados dos alunos de uma turma.

Todos pedem os mesmos indicadores, porque o teste aplicado (LP/MT, cadernos 14–18) é o mesmo.
Mas o "agregado" muda para dizer em que nível da hierarquia você quer enxergar esses indicadores.

👉 Resumindo:

Mesmos indicadores = mesma avaliação aplicada.

Agregado diferente = nível da análise (município, escola, turma, aluno).

# Como rodar

## 1) Abrir o notebook
- No VS Code, abra este repositório.
- No Explorer, vá até **get_table.ipynb** (na pasta `python/` ou onde você salvou) e abra.

> Dica: se estiver em terminal, você pode subir um nível com `cd ..` até chegar na pasta do projeto.

## 2) Instalar dependências

- Para rodar a aplicação, aperte na célula abaixo e clique no botão (Execute Cell and Below)

Na **primeira célula** do notebook, vai rodar:

%pip install requests pandas matplotlib openpyxl

# Atributos
- NM_ENTIDADE
- DC_ACERTOS
- DC_PONTUACAO
- TX_ACERTOS
- NU_ACERTO_HABILIDADE_1
- NU_ACERTO_HABILIDADE_2
- NU_ACERTO_HABILIDADE_3
- NU_ACERTO_HABILIDADE_4
- NU_ACERTO_HABILIDADE_5
- NU_ACERTO_HABILIDADE_6
- NU_ACERTO_HABILIDADE_7
- NU_ACERTO_HABILIDADE_8
- VL_FILTRO_DISCIPLINA
