# Mkplace Docs

Documentação técnica da plataforma Mkplace, hospedada em [Mintlify](https://mintlify.com).

## Estrutura

| Pasta / arquivo | Conteúdo |
|---|---|
| [index.mdx](index.mdx) | Landing — intro Mkplace e cards para os dois manuais |
| [sellers/](sellers/) | Manual de Integração de Vendedores |
| [stores/](stores/) | Manual de Integração de Lojas (em construção) |
| [api-reference/openapi/](api-reference/openapi/) | Specs OpenAPI usados para gerar a referência da API |
| [docs.json](docs.json) | Configuração global (navegação, tema, servidores, auth) |
| [AGENTS.md](AGENTS.md) | Instruções para agentes de IA contribuindo no repo |
| `.github/workflows/docs-check.yml` | CI que valida cada PR |

## Rodando localmente

```bash
npm i -g mint
mint dev
```

Abre em `http://localhost:3000` com hot reload.

## CI

Todo pull request executa três verificações em `.github/workflows/docs-check.yml`:

- `mint validate` — schema do `docs.json`, frontmatter MDX, componentes, OpenAPI.
- `mint broken-links` — links internos quebrados.
- `mint a11y` — contraste de cores e alt text em imagens.

PRs com qualquer falha bloqueiam o merge.

## Contribuindo

Para adicionar uma página:

1. Criar `.mdx` na pasta apropriada (`sellers/` ou `stores/`).
2. Frontmatter com `title` e `description`.
3. Registrar em `docs.json` → `navigation.tabs[].groups[].pages`.
4. Rodar `mint dev` e conferir o render.
5. Abrir PR.

Convenções de conteúdo (glossário, estilo, componentes Mintlify, boundaries) em [AGENTS.md](AGENTS.md).

## Deploy

Push para `main` dispara deploy automático no Mintlify Dashboard (via GitHub App).

## Suporte

- **Suporte geral**: [suporte@mkplace.com.br](mailto:suporte@mkplace.com.br)
- **Integração técnica**: [ti@mkplace.com.br](mailto:ti@mkplace.com.br)
