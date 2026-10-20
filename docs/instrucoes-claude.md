# Instruções para o Claude Code — Site de inclusão (Açores)

> Versão revista (2026-10-20). Base técnica e de organização: repositório `nobody`
> (Next.js, React, TypeScript e as suas boas práticas), adaptada a um site estático
> de conteúdo. As alterações à versão original estão assinaladas com **[revisto]**.

## 0. Como trabalhar comigo

- Trabalhamos com **Spec-Driven Development usando o GitHub Spec Kit**. Seguimos as fases por ordem: constitution → specify → clarify → plan → tasks → analyze → implement.
- **No fim de cada fase, pára e espera pela minha confirmação.** Mostra-me o ficheiro gerado e um resumo curto do que decidiste. Não avances sozinho para a fase seguinte.
- **Qualidade acima de velocidade.** Prefiro uma solução bem pensada a uma rápida. Se uma decisão de desenho não estiver clara, pergunta antes de implementar.
- **Quando propuseres ou alterares código, diz sempre em que ficheiro e em que componente, classe, esquema ou função está.**
- Escreve em português de Portugal: documentação, specs, issues, PRs, mensagens de commit e textos do site. **[revisto]** Nomes do domínio no código também em português (`Apoio`, `Porta`, `Edicao`); termos técnicos sem significado de negócio podem ficar em inglês.
- Se encontrares uma contradição entre este documento e o que eu disser no chat, pergunta-me.

### 0.1 Fluxo de trabalho e regras de código **[revisto]**

O fluxo (fases, paragens, issues, git, entrega) e a tabela de regras herdadas do nobody
estão no [`CLAUDE.md`](../CLAUDE.md), que é o documento canónico. Sem quadro de projeto por agora.

---

## 1. Contexto do projeto

Um **site independente (não institucional)** de informação prática para **pessoas com deficiência ou incapacidade e para quem cuida delas, nos Açores**, a começar pela **ilha Terceira**.

- Mostra **o que existe** (apoios, serviços, direitos), **como se pede e onde**, e também **o que não existe** (lacunas).
- A informação vem de fontes oficiais (sobretudo açorianas) e é **validada por pessoas da área**: profissionais de saúde, educação, segurança social e emprego, e pessoas com experiência vivida.
- O objetivo é servir as pessoas: depois de ler uma página, a pessoa tem de conseguir **fazer** alguma coisa (pedir um apoio, saber a quem ligar).
- Só vai para o ar o que já tem conteúdo verificado. O site cresce aos poucos.
- Informação **regional** (leis, regras, valores) é válida para todos os Açores; informação **local** (contactos, serviços) é, por agora, só da Terceira.

Referências no repositório:
- `docs/design/`: capturas e HTML do mock da página inicial e do percurso "autismo" (referência visual, não é código para copiar).
- `docs/pesquisa/`: levantamento de apoios existentes nos Açores (out. 2026), primeira fonte de conteúdo.

---

## 2. Passo 1: preparar o repositório e o Spec Kit **[revisto]**

1. Confirma que tenho Python + `pipx`, Node LTS, `pnpm`, `git` e `gh` instalados. Se faltar algum, diz-me como instalar e não avances.
2. Instala o Spec Kit e inicia o projeto para o Claude:
   - `py -m pipx install git+https://github.com/github/spec-kit.git@v1.2.2` **[revisto]** (sem `uv`)
   - Confirma as opções atuais com `specify init --help` e inicia o projeto na pasta atual com integração para o Claude.
3. Cria `CLAUDE.md` (regras operacionais da secção 0, com ligação para a constituição) e `AGENTS.md` (aponta para o `CLAUDE.md`, como no nobody).
4. Cria as pastas `docs/design/` e `docs/pesquisa/` com um README curto em cada.
5. **Não** copies ainda código do nobody; isso é tarefa da fase implement, depois de aprovado o plano.
6. **Pára e mostra-me a estrutura criada.**

---

## 3. Passo 2: `/speckit-constitution`

Usa este texto como base para a constituição:

> **Princípios do projeto**
>
> **Conteúdo e domínio**
> 1. **O esquema manda.** O modelo de domínio é definido em esquemas validados; o build falha se algum conteúdo violar o esquema ou as regras do domínio.
> 2. **Nada se publica sem prova.** Um Apoio publicado tem pelo menos uma Fonte oficial e uma Verificação. Uma notícia, sozinha, não chega.
> 3. **Conteúdo separado do código.** O conteúdo é escrito em Markdown simples e dados estruturados; nada de componentes dentro dos textos. O conteúdo tem de sobreviver a uma migração de tecnologia.
> 4. **URLs estáveis e em português** (ex.: `/apoios/prestacao-social-inclusao`). Se mudarem, há redirecionamento.
> 5. **Datas visíveis.** Cada página de conteúdo mostra "verificado em"; o que passa do prazo de verificação aparece como "a rever".
> 6. **Independência.** O site diz claramente que é um projeto independente, não um serviço oficial, e que as regras podem mudar.
>
> **Acessibilidade e experiência**
> 7. **WCAG 2.2 AA é obrigatório**, verificado automaticamente em cada alteração.
> 8. **[revisto] HTML semântico primeiro.** Todo o conteúdo e a navegação funcionam sem JavaScript. Componentes interativos no browser só com justificação escrita; a pesquisa é a única exceção prevista.
> 9. **Pensado primeiro para o telemóvel**, testado a 320 px de largura.
> 10. **Linguagem simples**: frases curtas, sem jargão jurídico por explicar.
> 11. **Orçamento de desempenho**: Lighthouse com 95 ou mais em todas as categorias, medido no CI desde a primeira página.
>
> **Privacidade e soberania**
> 12. **Sem cookies, sem rastreio de terceiros, sem recolha de dados pessoais dos visitantes.**
> 13. **Serviços europeus.** Alojamento e quaisquer serviços que toquem em dados de visitantes são de empresas europeias. Tipos de letra e pesquisa ficam alojados no próprio site.
>
> **Engenharia**
> 14. **Dependências mínimas.** Cada nova dependência é justificada no plano. **[revisto]** A base técnica herdada do nobody é justificada no plano 001.
> 15. **As regras do domínio têm testes unitários**, escritos antes da implementação.
> 16. **O `main` está sempre pronto a publicar.** Commits pequenos e descritivos; **[revisto]** alterações entram por PR ligado a uma issue, com CI verde.
> 17. **[novo] Domínio independente.** O domínio não depende de framework, formato de ficheiro nem bibliotecas externas. As regras são funções puras testadas.

**Pára e mostra-me a constituição.**

---

## 4. Passo 3: `/speckit-specify` (funcionalidade 001: fundação)

Usa este texto. **Não incluas aqui decisões de tecnologia**; essas ficam para o plano.

> **Funcionalidade 001: Fundação do site e modelo de domínio**
>
> **Porquê:** a informação sobre apoios à deficiência nos Açores está espalhada por portais, diplomas e notícias, em linguagem jurídica, e muitos apoios abrem e fecham sem aviso. As famílias perdem apoios por não saberem que existem ou que as candidaturas estavam abertas.
>
> **O quê (âmbito da 001):**
> - O modelo de domínio completo, com regras de validação.
> - A página inicial.
> - Uma lista de Apoios e a página (ficha) de cada Apoio.
> - A página "Sobre", com o aviso de projeto independente, quem colabora e como reportar um erro.
> - Um rodapé com o aviso curto em todas as páginas.
>
> **Leitores:** pessoas com deficiência, familiares e cuidadores, muitas vezes cansados e a consultar no telemóvel; também técnicos e associações que encaminham pessoas.
>
> **Entidades do domínio (linguagem de negócio):**
> - **Apoio**: algo a que uma pessoa pode ter acesso. Tem nome, resumo em linguagem simples, âmbito (regional, nacional ou municipal), tipo (prestação, serviço, programa, direito, benefício fiscal), estado (ativo, periódico, aprovado mas não operacional, encerrado, desconhecido), requisitos, como pedir (passos), onde pedir (uma ou mais Portas), fontes, verificação e tema.
> - **Edição**: uma ocorrência de um Apoio periódico (por exemplo, o apoio a computadores e tablets teve edições em 2022 e 2026). Tem ano, data de abertura e de fecho, montantes, ligação ao edital e regras próprias.
> - **Requisito**: condição de acesso (grau de incapacidade, idade, rendimento, residência, outro).
> - **Relação entre Apoios**: "depende de" (quase todos dependem do atestado multiuso) e "não acumula com" (por exemplo, a Prestação Social para a Inclusão e a bonificação por deficiência).
> - **Porta**: onde se pede ou se recebe ajuda (entidade, serviço, morada, contacto, horário, **ilha**).
> - **Tema**: categoria do menu (Saúde, Terapias, Educação, Apoios financeiros, Direitos e benefícios, Emprego, Contactos).
> - **Situação**: um percurso do leitor (ex.: "O meu filho tem (ou pode ter) autismo"), com Etapas ordenadas que apontam para Apoios e Portas. *Na 001 só existe no modelo; as páginas de Situação são a funcionalidade 002.*
> - **Lacuna**: algo que faz falta e não existe, ligado a um Tema ou a uma Situação, com Fonte.
> - **Fonte**: ligação, tipo (diploma oficial, edital, página oficial, notícia, conversa) e data de consulta.
> - **Verificação**: data e Colaborador que validou.
> - **Colaborador**: pessoa que valida conteúdo (nome ou anónimo, função, área).
> - Todas as entidades de conteúdo têm **estado de publicação**: rascunho ou publicado.
>
> **Regras do domínio (invariantes):**
> 1. Só é mostrado no site o que tem estado "publicado".
> 2. Um Apoio publicado tem pelo menos uma Fonte do tipo diploma oficial, edital ou página oficial, e pelo menos uma Verificação.
> 3. Um Apoio com estado "periódico" tem pelo menos uma Edição.
> 4. Uma referência a outra entidade (Porta, Tema, Apoio, Fonte) tem de existir; referências partidas impedem o build.
> 5. Um Apoio cuja última Verificação seja mais antiga do que o prazo definido aparece como "a rever".
> 6. Uma relação "não acumula com" é simétrica: se A não acumula com B, B não acumula com A.
>
> **Página inicial (por ordem):**
> 1. Nome do site e uma frase sobre o que é.
> 2. Pesquisa.
> 3. Nota de âmbito: "Informação regional válida em todos os Açores · Contactos e serviços, por agora, da ilha Terceira".
> 4. "Por onde começo?": entradas por situação. Só aparecem as que estão publicadas.
> 5. "O primeiro passo, quase sempre": atestado médico de incapacidade multiuso.
> 6. Contactos essenciais da Terceira.
> 7. "O que não existe".
> 8. Rodapé com o aviso de projeto independente.
>
> **Ficha de Apoio:** o que é, para quem (requisitos), quanto, como pedir (passos), onde pedir (Portas), o que depende dele e com que não acumula, edições e prazos (se for periódico), fontes, "verificado em / por", ligação "Encontrou um erro?".
>
> **Conteúdo inicial:** criar alguns Apoios reais a partir de `docs/pesquisa/` **em estado rascunho** (não publicados), para testar o modelo. Criar também conteúdo de teste separado para os testes automáticos.
>
> **Fora de âmbito da 001:** páginas de Situação (percursos), podcast, versão em LGP, leitura fácil, editor visual para colaboradores, outras ilhas. **[revisto]** As ligações "Leitura fácil" e "LGP" do mock não aparecem na 001.
>
> **Critérios de aceitação:**
> - O build falha com uma mensagem clara quando qualquer invariante é violada (há um teste para cada uma).
> - Conteúdo em rascunho nunca aparece no site gerado.
> - A página inicial e a ficha de Apoio passam nos testes automáticos de acessibilidade (WCAG 2.2 AA).
> - O site funciona sem JavaScript, exceto a pesquisa. **[revisto]** Sem JavaScript, o campo de pesquisa não aparece e é substituído por uma ligação para a lista de Apoios.

**Pára e mostra-me a spec.**

---

## 5. Passo 4: `/speckit-clarify`

Faz-me perguntas sobre o que estiver ambíguo. Estas decisões estão de certeza em aberto:

1. **Requisitos estruturados ou texto livre?** Estruturados permitem um dia filtrar ("tenho 65% de incapacidade, o que posso pedir?"); texto livre é mais simples de escrever.
2. **Colaboradores com nome público ou só função?**
3. **Prazo da regra "a rever"**: 6 meses? 12?
4. **Nome do site**: ainda por decidir; usar um nome provisório configurável num só sítio.

**[revisto]** Já decidido: nomes do domínio em português; código no GitHub (repositório público `K2Cr2O7/inclusao`).

**Atualiza a spec com as respostas e pára.**

---

## 6. Passo 5: `/speckit-plan` **[revisto]**

Stack escolhido (base do nobody):

- **Monorepo pnpm + turbo**, Node LTS, TypeScript `strict`, ESLint (com os limites LIM e `eslint-plugin-sonarjs` do nobody), Prettier, Husky + lint-staged.
- **`apps/web`: Next.js (App Router) + React**, com `output: 'export'` e `trailingSlash: true`. Só Server Components; sem `next/image` (ou com `unoptimized`), sem `next/script`, sem middleware nem rotas de API. Tipo de letra **Atkinson Hyperlegible com `next/font/local`**.
- **`packages/dominio`**: tipos das entidades, interfaces de repositório e regras do domínio em funções puras que devolvem `Result`. Sem dependências externas.
- **`packages/conteudo`**: lê o Markdown (Apoios, Situações, Lacunas) e o YAML (Portas, Fontes, Colaboradores, Temas) da pasta `conteudo/` na raiz, valida com **esquemas Zod** e entrega entidades do domínio. Implementa as interfaces de repositório.
- **`packages/errors`** (Result e erros de domínio) e **`packages/config`** (ESLint e tsconfig partilhados), adaptados do nobody. Logger mínimo para os scripts.
- **Comando `pnpm verificar`**: corre todas as invariantes sobre o conteúdo e falha com uma lista clara de erros. Corre antes do `build` e no CI.
- **CSS Modules com variáveis** (tokens de cor, tipo e espaçamento em `globals.css`), sem framework de CSS (sem Tailwind).
- **Pesquisa com Pagefind**, a correr sobre a pasta `out/` depois do `next build`; o campo só aparece com JavaScript.
- **Redirecionamentos**: os `redirects` do `next.config` não funcionam com exportação estática; resolver no alojamento (ficheiro de redirects) ou com páginas geradas. Decidir no plano depois de confirmar o que o alojamento suporta.
- Testes: **Vitest** (regras do domínio e leitura do conteúdo), **Playwright + axe-core** (acessibilidade e funcionamento sem JS), **Lighthouse CI**, **lychee** (links partidos).
- **CI no GitHub Actions**: lint, typecheck, testes, verificar, build, axe, Lighthouse. **Rebuild agendado semanal** (para a regra "a rever") e lychee no mesmo agendamento. **SonarCloud** (grátis para repositório público).
- Alojamento europeu: **statichost.eu** (Suécia); alternativa Bunny.net (Eslovénia). Confirmar redirecionamentos e pré-visualizações por PR. Sem estatísticas na 001.
- Do nobody **não** vêm: base de dados/Prisma, autenticação, i18n/next-intl, Tailwind, Vercel, pacotes da Bug Byte.

Gera também o `data-model.md` com cada entidade, os campos e tipos, as relações e o mapeamento **entidade → tipo em `dominio` → esquema Zod em `conteudo` → pasta em `conteudo/`**.

**Pára e mostra-me o plano e o modelo de dados.**

---

## 7. Passo 6: tasks, analyze e implement

1. `/speckit-tasks`: tarefas pequenas, por ordem: primeiro a base do monorepo, depois os tipos e as regras do domínio com os seus testes, depois os esquemas e a leitura do conteúdo, depois as páginas. **[revisto]** Cada tarefa (ou bloco) passa a uma issue no GitHub.
2. `/speckit-analyze`: verifica a consistência entre constituição, spec, plano e tarefas. **Mostra-me os problemas encontrados e pára.**
3. `/speckit-implement`: implementa por blocos. Depois de cada bloco, corre os testes, diz-me em que ficheiros e componentes mexeste, e espera pela minha confirmação antes do bloco seguinte. **[revisto]** Cada bloco entra por PR (secção 0.1).
