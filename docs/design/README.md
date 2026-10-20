# Design

Referência visual do site: mocks HTML da página inicial e do percurso "autismo".

| Ficheiro | O que mostra |
|---|---|
| [`pagina-inicial.html`](./pagina-inicial.html) | Página inicial (funcionalidade 001) |
| [`percurso-autismo.html`](./percurso-autismo.html) | Página de Situação "autismo" (funcionalidade 002) |

Para ver: abrir o ficheiro diretamente no browser (duplo clique). As duas páginas ligam
uma à outra.

**Não é código para copiar.** Serve para orientar o aspeto, as cores (tokens em `:root`)
e a hierarquia. No site real:

- o tipo de letra (Atkinson Hyperlegible) é alojado no próprio site; os mocks usam o
  Google Fonts só por comodidade;
- os estilos inline passam a classes e tokens;
- só aparece conteúdo publicado (as entradas "em preparação" do mock não aparecem);
- as ligações "Leitura fácil" e "LGP" ficam para funcionalidades futuras;
- a pesquisa funciona com Pagefind e, sem JavaScript, é substituída por uma ligação
  para a lista de Apoios;
- as páginas de Situação (percursos) são a funcionalidade 002.
