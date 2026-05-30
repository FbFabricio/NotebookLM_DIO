# 🔐 Caderno Temático: IA Gerando Código com Vulnerabilidades de Segurança

> **Projeto desenvolvido para o Desafio de Projeto da DIO — Explorando o NotebookLM como Ferramenta de Aprendizagem Ativa**

---

## 📌 Contexto e Objetivos

### Contexto

Com a popularização de assistentes de IA para programação — como GitHub Copilot, ChatGPT e Google Gemini — milhões de desenvolvedores passaram a usar código gerado automaticamente no dia a dia. No entanto, estudos recentes mostram que modelos de linguagem podem produzir código funcionalmente correto, mas **inseguro**: com falhas como injeção de SQL, senhas em texto plano, ausência de validação de entrada, entre outras vulnerabilidades clássicas.

Este caderno temático investiga esse fenômeno: **quando e por que a IA gera código vulnerável, quais são os riscos reais e como o desenvolvedor pode se proteger.**

### Objetivos de Estudo

- Compreender por que modelos de IA tendem a reproduzir padrões inseguros de código
- Identificar as categorias de vulnerabilidade mais comuns em código gerado por IA
- Aprender a revisar criticamente o output de ferramentas de IA com foco em segurança
- Criar um conjunto de prompts que incentivem a IA a produzir código mais seguro
- Construir referência prática para uso em projetos reais

---

## 📚 Curadoria de Fontes

As fontes abaixo foram selecionadas por serem abertas, técnicas e diretamente relevantes ao tema. Todas foram utilizadas no NotebookLM para geração dos resumos e análises deste caderno.

| # | Título | Tipo | Link |
|---|--------|------|------|
| 1 | **Do Users Write More Insecure Code with AI Assistants?** — Sandoval et al., Stanford | PDF / Artigo Científico | [arxiv.org/abs/2211.03622](https://arxiv.org/abs/2211.03622) |
| 2 | **OWASP Top 10 — 2021** (Guia oficial de vulnerabilidades mais críticas em aplicações web) | Documento técnico | [owasp.org/Top10](https://owasp.org/www-project-top-ten/) |
| 3 | **GitHub Copilot and the Insecure Code Problem** — Purple AI Blog | Artigo | [blog.gitguardian.com](https://blog.gitguardian.com/is-github-copilot-safe/) |
| 4 | **Asleep at the Keyboard? Assessing the Security of GitHub Copilot's Code Contributions** — Pearce et al. | PDF / Artigo Científico | [arxiv.org/abs/2108.09293](https://arxiv.org/abs/2108.09293) |
| 5 | **CWE Top 25 Most Dangerous Software Weaknesses — 2023** (MITRE) | Documento técnico | [cwe.mitre.org/top25](https://cwe.mitre.org/top25/archive/2023/2023_top25_list.html) |

> 💡 **Como usar no NotebookLM:** Acesse [notebooklm.google.com](https://notebooklm.google.com), crie um novo notebook, clique em "Add Source" e cole os links ou faça o upload dos PDFs diretamente.

---

## 🧪 Engenharia de Prompts e Cicatrizes

Esta seção documenta os prompts testados no NotebookLM, as respostas obtidas, variações tentadas e as dificuldades encontradas (troubleshooting).

---

### Prompt 1 — Entendendo o problema geral

**Prompt enviado:**
```
Com base nas fontes carregadas, explique por que modelos de IA tendem a gerar código com vulnerabilidades de segurança. Quais são as causas raiz desse problema?
```

**Resposta obtida (resumida):**
O NotebookLM identificou três causas principais apontadas nos artigos:
1. Os modelos são treinados em código público do GitHub, que historicamente contém muitos exemplos inseguros
2. O objetivo dos modelos é completar código de forma plausível e funcional, sem critério de segurança
3. Desenvolvedores tendem a confiar excessivamente na IA sem revisar o output com olhar crítico

**Referências citadas pelo NotebookLM:** Pearce et al. (2021), Sandoval et al. (2022)

**Dificuldade encontrada:** A primeira versão do prompt retornou uma resposta muito genérica. Precisei reformular para deixar claro que queria causas raiz com base nas fontes, não uma explicação superficial.

---

### Prompt 2 — Identificando as vulnerabilidades mais comuns

**Prompt enviado:**
```
Quais são as categorias de vulnerabilidade mais frequentemente encontradas em código gerado por IA? Liste com exemplos práticos e relacione com o OWASP Top 10 e o CWE Top 25.
```

**Resposta obtida (resumida):**
O NotebookLM listou as seguintes categorias com maior incidência:
- **Injeção de SQL (A03:2021 / CWE-89):** Código gerado sem uso de prepared statements
- **Hardcoded credentials (CWE-798):** Senhas e tokens inseridos diretamente no código
- **Ausência de validação de entrada (A03:2021 / CWE-20):** Aceitação de dados sem sanitização
- **Uso de funções criptográficas fracas (CWE-327):** Uso de MD5 ou SHA1 onde deveria ser bcrypt
- **Path Traversal (CWE-22):** Acesso a arquivos do sistema via entrada do usuário

**Variação de prompt testada:**
```
Foque apenas nas vulnerabilidades relacionadas a autenticação e criptografia presentes no código gerado por IA, com exemplos em Python.
```
Essa variação gerou respostas mais focadas e úteis para estudo técnico.

**Dificuldade encontrada:** Com fontes muito densas, o NotebookLM às vezes misturava informações do OWASP com as do CWE sem deixar claro a origem. Solução: pedir explicitamente "cite a fonte de cada ponto".

---

### Prompt 3 — Exemplo prático de código vulnerável vs. seguro

**Prompt enviado:**
```
Com base nas fontes, mostre um exemplo de código vulnerável que uma IA poderia gerar e a versão corrigida e segura do mesmo código. Use Python como linguagem.
```

**Resposta obtida (resumida):**

❌ **Código vulnerável (IA pode gerar assim):**
```python
import sqlite3

def buscar_usuario(nome):
    conn = sqlite3.connect("banco.db")
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM usuarios WHERE nome = '" + nome + "'")
    return cursor.fetchall()
```

✅ **Código seguro (versão corrigida):**
```python
import sqlite3

def buscar_usuario(nome):
    conn = sqlite3.connect("banco.db")
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM usuarios WHERE nome = ?", (nome,))
    return cursor.fetchall()
```

**Dificuldade encontrada:** O NotebookLM não gera código por conta própria com base nas fontes — ele explicou o conceito e eu adaptei o exemplo com base nas descrições do artigo do OWASP. Isso é esperado, já que a ferramenta é para análise de documentos, não geração de código.

---

### Prompt 4 — Como criar prompts mais seguros para IAs de código

**Prompt enviado:**
```
Com base nas fontes, quais estratégias ou padrões de prompt podem ser usados para induzir ferramentas de IA a gerar código mais seguro? Existe alguma abordagem recomendada?
```

**Resposta obtida (resumida):**
- Especificar explicitamente no prompt que o código deve seguir as boas práticas de segurança do OWASP
- Pedir à IA que explique as decisões de segurança tomadas
- Incluir contexto sobre o ambiente de produção e possíveis vetores de ataque
- Solicitar revisão de segurança do próprio código gerado em um segundo prompt

**Variação testada:**
```
Se eu estou usando GitHub Copilot para gerar uma função de login, qual prompt ou comentário no código eu devo escrever para reduzir o risco de código inseguro?
```
Essa variação foi mais prática e gerou sugestões mais aplicáveis ao dia a dia.

---

### Prompt 5 — Responsabilidade do desenvolvedor

**Prompt enviado:**
```
Segundo as fontes, qual é o papel e a responsabilidade do desenvolvedor ao usar IA para geração de código do ponto de vista de segurança? O que os pesquisadores recomendam?
```

**Resposta obtida (resumida):**
- O desenvolvedor permanece responsável pelo código, independente da origem
- Revisão manual com foco em segurança é indispensável
- Ferramentas de SAST (Static Application Security Testing) devem ser integradas ao pipeline
- Treinamento em segurança de software deve preceder o uso intensivo de IA em ambientes produtivos

---

## 📖 Miniguia de Estudo — Entrega Final

### 1. Resumos Estruturados

#### 🧩 Por que a IA gera código inseguro?

Modelos de linguagem são treinados em repositórios públicos que contêm décadas de código com falhas de segurança. O objetivo do modelo é prever o próximo token mais plausível — não o mais seguro. Isso cria uma tendência de reproduzir padrões inseguros que aparecem com alta frequência no conjunto de treinamento.

O estudo de Pearce et al. (2021) testou o GitHub Copilot com 89 cenários de código e encontrou que **aproximadamente 40% das sugestões continham vulnerabilidades de segurança**, muitas relacionadas ao CWE Top 25.

#### 🎯 As 5 vulnerabilidades mais comuns em código gerado por IA

| Vulnerabilidade | CWE | Descrição |
|----------------|-----|-----------|
| SQL Injection | CWE-89 | Concatenação direta de input do usuário em queries SQL |
| Hardcoded Credentials | CWE-798 | Senhas e chaves de API escritas diretamente no código |
| Improper Input Validation | CWE-20 | Dados do usuário aceitos sem validação ou sanitização |
| Use of Weak Cryptography | CWE-327 | Uso de MD5/SHA1 em contextos que requerem segurança real |
| Path Traversal | CWE-22 | Acesso a arquivos do sistema via input não sanitizado |

#### 🛡️ Como se proteger

1. **Nunca confie cegamente no código gerado** — revise sempre com olhar crítico
2. **Use ferramentas de SAST** como Bandit (Python), SonarQube, Semgrep
3. **Consulte o OWASP Cheat Sheet Series** como referência ao revisar código
4. **Escreva testes de segurança** além dos testes funcionais
5. **Informe o contexto de segurança no prompt** ao pedir código para IA

---

### 2. Glossário de Conceitos

| Termo | Definição |
|-------|-----------|
| **Vulnerabilidade** | Fraqueza em um sistema que pode ser explorada por um atacante |
| **CWE (Common Weakness Enumeration)** | Lista padronizada de tipos de fraquezas de software mantida pela MITRE |
| **OWASP Top 10** | Lista das 10 categorias de risco de segurança mais críticas para aplicações web |
| **SQL Injection** | Técnica de ataque que insere comandos SQL maliciosos via input do usuário |
| **Hardcoded Credentials** | Credenciais (senhas, tokens) escritas diretamente no código-fonte |
| **SAST** | Static Application Security Testing — análise de código sem executá-lo |
| **DAST** | Dynamic Application Security Testing — análise de segurança com o sistema em execução |
| **Sanitização** | Processo de limpar/validar dados de entrada para remover conteúdo malicioso |
| **Prepared Statement** | Técnica de query parametrizada que previne SQL Injection |
| **LLM (Large Language Model)** | Modelo de linguagem de grande escala, como GPT, Gemini, Claude |
| **GitHub Copilot** | Assistente de IA para programação desenvolvido pelo GitHub e OpenAI |
| **Path Traversal** | Ataque que manipula caminhos de arquivo para acessar diretórios não autorizados |
| **Vetor de Ataque** | Caminho ou método pelo qual um atacante pode explorar uma vulnerabilidade |
| **Token (no contexto de LLM)** | Unidade mínima de texto processada pelo modelo (palavra, parte de palavra ou símbolo) |

---

### 3. Prompts Reutilizáveis para Revisões Futuras

Estes prompts podem ser usados em sessões futuras no NotebookLM ou em qualquer ferramenta de IA para revisar e aprofundar os estudos.

#### Para revisão conceitual:
```
Explique o conceito de [VULNERABILIDADE] com base nas fontes, incluindo: 
como ela ocorre, como a IA pode gerá-la inadvertidamente, e como corrigi-la.
```

#### Para análise de código suspeito:
```
Analise o seguinte trecho de código com foco em segurança. 
Identifique possíveis vulnerabilidades, classifique pelo CWE e 
sugira a versão corrigida:
[COLE O CÓDIGO AQUI]
```

#### Para geração de código seguro com IA:
```
Crie uma função em [LINGUAGEM] para [FUNCIONALIDADE]. 
O código deve seguir as melhores práticas de segurança do OWASP, 
evitar as vulnerabilidades do CWE Top 25, e incluir comentários 
explicando as decisões de segurança tomadas.
```

#### Para estudo comparativo:
```
Compare como desenvolvedores que usam IA diferem em termos de 
segurança de código em relação a desenvolvedores que não usam, 
com base nas fontes disponíveis.
```

#### Para revisão de dependências:
```
Quais riscos de segurança estão associados ao uso de bibliotecas 
de terceiros sugeridas por IA? Como mitigá-los?
```

---

## 🛠️ Ferramentas Utilizadas

- **[NotebookLM](https://notebooklm.google.com)** — Organização e análise das fontes com IA
- **[GitHub](https://github.com)** — Versionamento e publicação do projeto
- **[OWASP.org](https://owasp.org)** — Referência de segurança
- **[arxiv.org](https://arxiv.org)** — Artigos científicos open access

---

## 👤 Autor

Desenvolvido como parte do **Bootcamp de Cybersecurity da DIO**.  
Conecte-se comigo no [LinkedIn](#) | Veja outros projetos no [GitHub](#)

---

*Este caderno foi construído com apoio do NotebookLM (Google) e representa uma síntese ativa de aprendizado — não apenas um resumo, mas uma curadoria crítica e aplicada do conhecimento.*
