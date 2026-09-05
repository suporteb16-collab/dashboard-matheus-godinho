# Dashboard — Meta Ads (Matheus Godinho)

Dashboard de performance de mídia da conta **CA - FAD - Prime**. Construído pela
**Agência B16**.

🔗 https://suporteb16-collab.github.io/dashboard-matheus-godinho/

---

## Escopo atual: só mídia

**Não há fonte de vendas conectada ainda.** Este dashboard mostra investimento,
alcance e o funil até o pixel de compra da Meta — nenhum número aqui é faturamento
real, é o que o pixel reporta. Quando os dados de compra (Kiwify) estiverem
disponíveis, o plano é replicar o padrão do dashboard AAEMCWEB: uma view de vendas
separada, cruzada com a de mídia por campanha/conjunto/criativo, para chegar a ROAS,
ticket médio e break-even de verdade.

Até lá, os números de "compra" e "faturamento" no card de resultado vêm do
**pixel do Meta**, não da plataforma de pagamento — e o pixel tende a
superestimar (conversões mal atribuídas, testes, checkout abandonado que o pixel
ainda assim credita). O dashboard avisa isso no rodapé de cada KPI relevante.

---

## Fonte de dados

Tudo vem do **Supabase** (projeto `Data&Revenue`), por PostgREST, com a chave
publishable.

| View | Origem | O que traz |
|---|---|---|
| `public.dash_mgodinho_midia` | `"trafego-pago".meta_ads_mgodinho` (Meta Ads via Stract) | gasto, impressões, cliques, LPV, checkouts e compras/receita do pixel, por dia/campanha/conjunto/anúncio |

A extração roda via Stract (trigger "Meta Ads - CA - FAD - Prime"), com atualização
diária automática, histórico desde 2026-01-01, sem filtro de campanha — puxa a conta
inteira.

### Por que uma view e não a tabela direto

O schema `trafego-pago` não é publicado no PostgREST — a view em `public` é a ponte.
Essa tabela de mídia não tem dado pessoal, então a view aqui é uma projeção simples
(sem `security_definer`), diferente das views de vendas dos outros dashboards B16
que mascaram PII.

---

## Stack e design

HTML/CSS/JS puro, Chart.js 4, sem build. Mesma paleta clássica B16 dos demais
dashboards (Mundial Cromo, AAEMCWEB): `#f4f4f2` / `#d4a800` / `#111`, Bebas Neue +
DM Sans, tema claro e escuro. Funil em trapézio (CSS puro, magnitude na largura, não
na cor) — mesmo padrão dos outros.

### Filtro de período

*Tudo · 7 dias · Hoje · Período* (datas livres), mesmo comportamento dos outros
dashboards B16: datas invertidas são trocadas com aviso, o botão "Máximo" volta ao
intervalo inteiro, e o cabeçalho mostra "sem dado no período" em vez de ficar em
branco quando o recorte não pega nada.

---

## Arquivos

| | |
|---|---|
| `index.html` | o dashboard |

---

## Deploy

Esta pasta é o repositório git, ligado a `suporteb16-collab/dashboard-matheus-godinho`
(branch `main`, GitHub Pages). `git push origin main` e o Pages republica em ~20s.

**Agência B16** — Henrique Cardoso, Business Intelligence · 05/09/2026.
