# Dashboard — Meta Ads (Matheus Godinho)

Dashboard de performance de mídia da conta **CA - FAD - Prime**. Construído pela
**Agência B16**.

🔗 https://suporteb16-collab.github.io/dashboard-matheus-godinho/

---

## Escopo atual: mídia estruturada por ação, vendas pendentes

**Não há fonte de vendas conectada ainda.** Este dashboard mostra investimento,
alcance e o funil até o pixel de compra da Meta — nenhum número aqui é faturamento
real, é o que o pixel reporta.

Os números de "compra" e "faturamento" vêm do **pixel do Meta**, não da plataforma
de pagamento — e o pixel tende a superestimar (conversões mal atribuídas, testes,
checkout abandonado que o pixel ainda assim credita). O dashboard avisa isso no
rodapé de cada KPI relevante.

### As 3 ações (definidas pelo cliente em 10/09/2026)

| Ação | O que é | Como é identificada |
|---|---|---|
| **Direct Response** | tráfego direto para LP com VSL | campanha com `[SITE]` ou `[COMPRA]` |
| **Quiz** | venda direta, orgânica e paga | campanha com `Quiz` ou `[VDS]` |
| **Webinário** | webinário semanal gratuito: captação + venda | campanha com `webin`, `aula` ou `[WEB]` |

A classificação é **por padrão no nome da campanha**, porque a conta não usa
`utm_content` estruturada. Campanha que não casar com nenhum padrão cai em
**"Não classificada"** de propósito — aparecer para ser corrigida é melhor do que
sumir silenciosamente. Se a nomenclatura mudar, o ajuste é na view, não no front.

Em 10/09/2026: Quiz com R$ 3.655 (98% do investimento, encerrada em 11/08),
Direct Response com R$ 69,41 (começou em 03/09) e **Webinário ainda sem campanha** —
o card aparece vazio e apagado, sinalizando que a ação não subiu.

### Os 3 produtos (Kiwify B16)

`Onda de Vendas` R$ 297 · `Lista de Fornecedores` R$ 97 · `Calculadora` R$ 97.

**Nenhum deles existe na `transacoes_vendas` ainda** — a Kiwify do Matheus não entrou
na sincronização da B16 (workflow `[B16] Kiwify to Supabase - Full Sync`). A view
`dash_mgodinho_vendas` já está criada e retorna zero linhas de propósito: no dia em
que a ingestão subir, ROAS, ticket e faturamento real preenchem **sem alteração de
código no front**.

⚠️ **Homônimo perigoso:** existe uma `Calculadora de Prestação de Serviço` na base que
é do núcleo `mundial` (Mundial Cromo, campanha `cnp-2-2026`, vendida por WhatsApp/
YouTube) — **não** é a "Calculadora - ODV". Por isso o filtro da view exclui
explicitamente o núcleo `mundial` em vez de casar só pelo nome do produto.

---

## Fonte de dados

Tudo vem do **Supabase** (projeto `Data&Revenue`), por PostgREST, com a chave
publishable.

| View | Origem | O que traz |
|---|---|---|
| `public.dash_mgodinho_midia_acao` | `"trafego-pago".meta_ads_mgodinho` (Meta Ads via Stract) | o mesmo da anterior **+ a coluna `acao`** — é a que o dashboard usa |
| `public.dash_mgodinho_vendas` | `public.transacoes_vendas` (Kiwify) | vendas dos 3 produtos ODV — **vazia hoje**, ver acima |
| `public.dash_mgodinho_midia` | `"trafego-pago".meta_ads_mgodinho` | versão original sem `acao`; mantida para não quebrar quem já consome |

`dash_mgodinho_vendas` é **`security_definer` de propósito** — é o que deixa o anon ler
os agregados sem alcançar a tabela-base, que tem e-mail/CPF/telefone/IP. O linter do
Supabase marca isso como ERROR; **não "corrigir"** para `security_invoker`, isso exporia
a PII. (A view de mídia não tem dado pessoal e é projeção simples.)

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

**Agência B16** — Henrique Cardoso, Business Intelligence · 05/09/2026,
atualizado em 10/09/2026 (estrutura por ação + produtos ODV).
