# Documentation project instructions

Instruções para agentes de IA (Claude Code, Cursor, etc.) e humanos contribuindo neste repositório. Leia antes de editar conteúdo.

## About this project

- Site de documentação técnica da **Mkplace** em [Mintlify](https://mintlify.com).
- Páginas são MDX com YAML frontmatter; configuração global em [docs.json](docs.json).
- Navegação em 3 tabs: **Introdução**, **Integração de Vendedores**, **Integração de Lojas**.
- Referência da API auto-gerada a partir de [api-reference/openapi/plataforma-mkplace.json](api-reference/openapi/plataforma-mkplace.json) (não editar à mão — substitua o spec inteiro).
- Use o MCP server `https://mcp.mintlify.com` para editar via MCP.
- Antes de criar conteúdo novo, instale a skill: `npx skills add https://mintlify.com/docs`.

## Ambientes e canais

- **Site da empresa**: https://www.mkplace.com.br/
- **API**:
  - `https://api.main.mkplace.com`
  - `https://api.main.mkplace.com.br`
- **Auth**: OAuth2 `client_credentials` via Keycloak; JWT no header `Authorization: Bearer <token>`.
- **Suporte**: suporte@mkplace.com.br · ti@mkplace.com.br

## Glossário (terminologia obrigatória)

| Termo | Definição |
|---|---|
| **Seller** (vendedor) | Empresa cadastrada que disponibiliza produtos no marketplace via API. |
| **Store** (loja) | Front-end do marketplace que consome o catálogo e dispara checkout. |
| **Warehouse** | Centro de distribuição do seller; obrigatório, pode ter múltiplos. |
| **Sugestão** (`suggestion`) | Submissão de produto pelo seller; passa por curadoria antes de virar produto/oferta. |
| **Curadoria** | Análise pelo marketplace que decide aceitar a sugestão como produto novo ou fazer `match` com produto existente. |
| **Produto / SKU / variante** | Produto = item conceitual; SKU/variante = combinação específica (cor/tamanho/etc.). |
| **Oferta** | Preço, estoque e disponibilidade de uma SKU; atualização não passa por curadoria. |
| **Multi-seller** | Pedido com itens de mais de um seller; status hierárquico em pedido → pacote → item. |
| **Pacote** (`shipment`) | Agrupamento de itens de um seller dentro de um pedido; tem status próprio. |
| **Pre-order** | Status inicial; itens reservados aguardando pagamento (expira em 5 min). |
| **Status de pedido** | `PRE-ORDER` → `PAID` → `BILLED`/`PARTIALLY-BILLED` → `SENT`/`PARTIALLY-SENT` → `DELIVERED`. Pode terminar em `CANCELED`, `RETURNED`, `REFUNDED`, `EXPIRED`. |
| **Webhook** | Endpoint exposto pelo seller que recebe eventos de pedido (criação, mudança de status). |
| **Homologação** | Processo de validação da integração antes de liberar chaves de produção. |

**Não use**: "user" ou "merchant" → use **seller**. "Cliente" para Store → use **loja** ou **store**.

## Estrutura

```
docs/
├── docs.json                    # configuração global (navegação, tema, servers)
├── index.mdx                    # landing
├── sellers/                     # tab "Integração de Vendedores"
│   ├── index.mdx                # visão geral / homologação
│   ├── autenticacao.mdx
│   ├── configuracoes/           # warehouse, frete, webhooks
│   ├── produtos-ofertas.mdx
│   ├── pedidos/                 # ciclo de vida e schema
│   └── conclusao.mdx
├── stores/                      # tab "Integração de Lojas" (em construção)
├── api-reference/openapi/       # specs OpenAPI (referência auto-gerada)
└── .github/workflows/           # CI (mint validate, broken-links, a11y)
```

## Estilo de escrita

- **Voz**: segunda pessoa, ativa. **Você**, não "o usuário".
- **Idioma**: português brasileiro.
- **Headings em sentence case**: "Cadastros e configurações" (não "Cadastros E Configurações").
- **Code formatting** para nomes de campo, paths, comandos e status: `client_id`, `/sales/order/cancel`, `PARTIALLY-BILLED`.
- **Bold** para elementos de UI: "Clique em **Sugestões**".
- **Componentes Mintlify** preferidos sobre markdown cru:
  - `<Steps>` para procedimentos sequenciais
  - `<Tabs>` para variações (linguagem, ambiente)
  - `<Card>` em `<Columns>` para navegação visual
  - `<Note>` / `<Warning>` / `<Tip>` / `<Check>` para callouts por severidade
  - `<ParamField>` / `<ResponseField>` para contratos de API quando manual (sem OpenAPI)
  - `<AccordionGroup>` para detalhes opcionais
- **Mermaid** suportado nativamente — usar para diagramas de fluxo (status de pedido, sequência de auth).
- **Emoji decorativo**: evitar em conteúdo novo. Conteúdo migrado do Stoplight (sellers/*) preserva os emojis originais por consistência histórica.
- **Sem linguagem de marketing**: nada de "poderoso", "robusto", "seamless", "simplesmente", "basta".

## Boundaries

**Documentar aqui**:
- Integração técnica de Sellers (auth, cadastros, produtos, pedidos, webhooks).
- Integração técnica de Stores (a definir com o time de produto).
- Schemas de payload, códigos de status, eventos de webhook.
- Regras de negócio que afetam a integração (multi-seller, curadoria, pre-order, expiração).

**Não documentar aqui**:
- Painel admin interno / backoffice.
- Infra (Kubernetes, Terraform, CI/CD dos serviços) — fica nos repos `mkplace/gitops`, `mkplace/infrastructure`.
- Código interno dos microsserviços.
- Roadmap comercial / pricing.

## Workflows comuns

### Adicionar página nova

1. Criar `.mdx` na pasta apropriada (`sellers/`, `stores/`).
2. Frontmatter com `title` e `description`.
3. Registrar em `docs.json` → `navigation.tabs[].groups[].pages`.
4. Rodar `mint dev` localmente.
5. Abrir PR — o CI roda `mint validate` + `mint broken-links` + `mint a11y`.

### Atualizar referência da API

- Substituir `api-reference/openapi/plataforma-mkplace.json` pelo spec novo.
- Sem mudança em `docs.json` — endpoints são auto-detectados via `tags` do spec.

### Migrar conteúdo legado do Stoplight

- Fonte: `c:\Users\Arthur\Downloads\Mkplace - Manual para Integração de Vendedores\docs\` (export local).
- Links do Stoplight (`mkplace.stoplight.io/docs/sellers-integration-docs/...`) viram âncoras root-relative (`/sellers/autenticacao`).
- `mermaid` funciona direto no Mintlify, sem syntax change.
- `json json_schema` (sintaxe Stoplight) vira `json` puro no Mintlify; valor de exemplo direto.

## CI

`.github/workflows/docs-check.yml` roda em todo PR:

| Step | O que cobre |
|---|---|
| `mint validate` | schema `docs.json`, frontmatter MDX, sintaxe de componentes, OpenAPI |
| `mint broken-links` | links internos (rotas inexistentes) |
| `mint a11y` | contraste de cores em `docs.json`, alt text em imagens, hierarquia de headings |

Se o CI quebrar, **investigue a causa raiz**. Não silencie a11y ou broken-links em conteúdo novo.
