# CLAUDE.md

> Regras operacionais para o Claude neste repositório. Os princípios do projeto estão na
> constituição (`.specify/memory/constitution.md`); o arranque do projeto está em
> `docs/instrucoes-claude.md`. Base técnica e de organização herdada do repositório `nobody`.

---

## Língua

Português de Portugal em tudo: documentação, specs, issues, PRs, mensagens de commit,
textos do site e nomes do domínio no código (`Apoio`, `Porta`, `Edicao`). Termos técnicos
sem significado de negócio podem ficar em inglês.

---

## Fluxo de trabalho (OBRIGATÓRIO)

**⛔ Não escrever código sem "sim" explícito ao plano.**

Spec-Driven Development com o GitHub Spec Kit:
constitution → specify → clarify → plan → tasks → analyze → implement.

- **No fim de cada fase, parar** e mostrar o ficheiro gerado com um resumo curto. Não avançar sem confirmação.
- O `/speckit-plan` é o plano; não há plano separado.
- Depois do `/speckit-tasks`, cada tarefa (ou bloco) passa a uma issue no GitHub (`/speckit-taskstoissues` ou `gh issue create`).
- **Implementação, um ficheiro de cada vez:** escrever → `pnpm lint --fix` → `pnpm typecheck` → testes → ficheiro seguinte.
- **No fim de cada bloco:** correr `pnpm lint && pnpm typecheck && pnpm test && pnpm verificar && pnpm build`, rever contra as regras abaixo e parar à espera de confirmação.
- **Entrega:** ficheiros criados/alterados (com linhas, componente/função tocados) e
  `lint ✅ | typecheck ✅ | test ✅ | verificar ✅ | build ✅ | revisão ✅`.
- Ao propor ou alterar código, dizer sempre em que ficheiro e em que componente, esquema ou função.
- Em caso de dúvida de desenho, contradição ou ambiguidade: **parar e perguntar**.

---

## Git

- **Nunca fazer push direto para `main`.** Uma branch por issue: `feature/…`, `fix/…`, `chore/…`, `docs/…`.
- PR para `main` com `Closes #X`, CI verde e avisos do SonarCloud resolvidos.
- **Merge só com aprovação explícita.**
- Exceção única já usada: o commit inicial de preparação (repositório vazio).

---

## Arquitetura

```
apps/web            (apresentação: Next.js, output: 'export')
   │ importa
packages/conteudo   (infraestrutura: lê Markdown/YAML, valida com Zod)
   │ implementa interfaces de
packages/dominio    (entidades, regras e interfaces de repositório; sem dependências)
```

`conteudo/` (na raiz) guarda o conteúdo em Markdown e YAML, separado do código.

| Importação | Onde | Violação |
|---|---|---|
| `next`, `react`, `zod`, `node:fs`, qualquer pacote externo | `packages/dominio` | CA-01 |
| `apps/web` | `packages/*` | CA-02 |
| `packages/conteudo` diretamente nas regras | `packages/dominio` | CA-01 |

---

## Regras (violação = rejeitar)

| ID | Regra |
|---|---|
| CA-01 | `packages/dominio` não importa nada externo |
| CA-02 | Importações só na direção `web` → `conteudo` → `dominio` |
| DIP | O domínio define interfaces de repositório (ex.: `ApoioRepository`); `conteudo` implementa-as |
| SRP | Uma razão para mudar por módulo |
| TS-01 | Sem `any` |
| TS-05 | TypeScript `strict`; nunca ignorar erros |
| LIM-01 | Ficheiro ≤ 200 linhas |
| LIM-02 | Função ≤ 30 linhas (componentes React ≤ 50) |
| LIM-03 | ≤ 4 parâmetros (usar objeto de configuração) |
| LIM-04 | Indentação ≤ 3 níveis |
| LIM-05 | `switch` ≤ 3 casos (usar Strategy) |
| EH-01 | Regras do domínio devolvem `Result`, não lançam exceções |
| EH-02 | Erros de domínio específicos, não `Error` genérico |
| LOG-01 | Sem `console.log`; usar o `logger` nos scripts |
| CLEAN-01 | Sem código comentado |
| CLEAN-02 | `TODO(#123)` ou nada |
| CLEAN-03 | Sem números mágicos |
| TEST-01 | Lógica nova tem `.test.ts`; regras do domínio com testes escritos antes |
| DRY-01 | Sem código duplicado |
| TXT-01 | Textos da interface e nome do site num único módulo tipado |
| JS-01 | Server Components por defeito; `"use client"` só com justificação escrita no PR |
| A11Y-01 | HTML semântico, foco visível, alvos ≥ 44 px, testado a 320 px e sem JavaScript |
| DEP-01 | Cada nova dependência justificada no plano |
| SONAR-01 | Resolver avisos do SonarCloud no PR antes do merge |

---

## Sinais de alerta (PARAR e PERGUNTAR)

- Requisito ambíguo ou várias abordagens válidas
- Alteração afeta mais de 5 ficheiros
- Vontade de usar `any`, `"use client"` ou uma nova dependência
- Testes existentes a falhar
- Dúvida sobre a camada certa
- Conteúdo publicado sem Fonte oficial ou sem Verificação

---

## Ferramentas

| O quê | Como |
|---|---|
| Spec Kit | `specify` (instalado com `pipx`); comandos `/speckit-*` em `.claude/skills/` |
| Node / pacotes | Node LTS, `pnpm`, `turbo` |
| Comandos (depois da base do monorepo) | `pnpm dev`, `pnpm lint`, `pnpm typecheck`, `pnpm test`, `pnpm verificar`, `pnpm build` |
