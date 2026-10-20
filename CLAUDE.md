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
- **No fim de cada bloco:** correr `pnpm lint && pnpm typecheck && pnpm test && pnpm verificar && pnpm build` (os que já existirem), rever contra as regras abaixo e parar à espera de confirmação.
- **Entrega:** ficheiros criados/alterados (com linhas, componente/função tocados) e
  `lint ✅ | typecheck ✅ | test ✅ | verificar ✅ | build ✅ | revisão ✅`.
- Ao propor ou alterar código, dizer sempre em que ficheiro e em que componente, esquema ou função.
- Em caso de dúvida de desenho, contradição ou ambiguidade: **parar e perguntar**.

### Tarefas pontuais (fora do Spec Kit)

Manutenção, documentação, configuração e correções que não pertencem a uma funcionalidade:

**issue → plano proporcional à alteração → aprovação → implementação → PR.**

O plano diz que ficheiros cria ou altera e porquê; pode ser curto, mas é sempre aprovado antes de implementar.

---

## Git

- **Nunca fazer push direto para `main`.** Uma branch por issue: `feature/…`, `fix/…`, `chore/…`, `docs/…`.
- PR para `main` com `Closes #X` e as verificações aplicáveis a passar.
- **Verificações aplicáveis:** exigem-se as que já existem no repositório. Cada verificação nova
  (CI, SonarCloud, axe, Lighthouse, lychee) passa a obrigatória a partir do PR que a introduz.
  Enquanto não houver CI, o PR indica as verificações manuais feitas.
- **Merge só com aprovação explícita.**
- Exceção única já usada: o commit inicial de preparação (repositório vazio).

---

## Arquitetura

```
apps/web            (apresentação: Next.js, output: 'export')
   │            └──────────────┐ tipos e funções puras
   ▼ repositórios               ▼
packages/conteudo ──────► packages/dominio ──────► packages/errors
(infraestrutura:          (entidades, regras,       (Result e erros base;
 Markdown/YAML + Zod)      interfaces de             núcleo interno sem
                           repositório)              dependências)
```

Direções permitidas: `web → conteudo`, `web → dominio`, `conteudo → dominio`,
`dominio → errors`; qualquer pacote pode importar `errors`.

- `web` importa do domínio os **tipos** e as **funções puras** (ex.: cálculo de "a rever"); obtém o **conteúdo** sempre através dos repositórios de `conteudo`.
- `errors` não tem dependências; é a única importação permitida ao domínio.

`conteudo/` (na raiz) guarda o conteúdo em Markdown e YAML, separado do código.

| Importação | Onde | Violação |
|---|---|---|
| `next`, `react`, `zod`, `node:fs`, qualquer pacote externo | `packages/dominio`, `packages/errors` | CA-01 |
| `packages/conteudo` ou `apps/web` | `packages/dominio` | CA-01 |
| Qualquer outro pacote | `packages/errors` | CA-01 |
| `apps/web` | `packages/*` | CA-02 |
| Leitura direta de ficheiros de conteúdo (`node:fs`, Markdown, YAML) | `apps/web` | CA-02 |

---

## Regras (violação = rejeitar)

| ID | Regra |
|---|---|
| CA-01 | `packages/dominio` só importa `packages/errors`; `errors` não importa nada |
| CA-02 | Importações só nas direções permitidas (ver Arquitetura) |
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
| A11Y-02 | Além do axe, avaliação **manual** de cada tipo de página novo ou alterado: só teclado, leitor de ecrã (NVDA no Windows, TalkBack no Android), zoom a 200 % e 320 px. Resultado registado no PR |
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
