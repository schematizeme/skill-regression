# Seleção e frequência da suíte de regressão

> Parte da `schematize-regression`. A suíte de regressão só protege se rodar; quando fica lenta, a
> tentação é pular. Aqui está o que se pode otimizar e o que nunca.

## 1. Quando roda o quê

| Gatilho | O que roda | Pode ser reduzido? |
|---|---|---|
| Local, antes do commit | unitários rápidos e o teste do bug em curso | sim (é conveniência) |
| PR | seleção por impacto **ou** completa | só com a completa no merge (item 3) |
| Merge em `main` | **suíte completa** | **não** |
| Nightly | completa + lenta (carga, perf, visual completo, e2e longo, mutação por amostragem) | não |
| Antes do release | completa verde no commit exato do release | não |

## 2. Suíte completa é o piso do merge

Nenhum merge em `main` sem a suíte completa verde naquele commit (ou no resultado do merge queue).
O CI do PR pode ser mais rápido que o do merge; **o do merge não pode ser mais fraco que ele**.

## 3. Seleção por impacto (test impact analysis)

Rodar só os testes afetados pelo diff acelera o PR. Aceite com condições:

- **Mapa arquivo -> teste** derivado do grafo de dependências (build tool) ou da cobertura por teste,
  regenerado no nightly; mapa velho é falso verde.
- **Falha segura:** arquivo sem entrada no mapa, mudança em configuração, build, dependência,
  `Dockerfile`, esquema do banco ou código gerado => **suíte completa**.
- **A completa roda no merge.** Sem isso, seleção por impacto é só pular teste com outro nome.
- Reporte o que foi **selecionado e o que foi pulado** no resumo do PR, para ninguém achar que a
  suíte inteira passou.

## 4. Nunca pular

**VETADO**
- Pular, desligar ou rebaixar a suíte de regressão "para agilizar" um merge, um release ou um hotfix.
  Hotfix é onde a regressão mais ataca; ele roda a regressão completa.
- Marcar o job como `allow-failure` ou "não bloqueante" para destravar. Job que não bloqueia não é gate.
- Retry automático até passar (esconde corrida real; ver `schematize-qa -> references/flaky.md`).
- Suíte lenta resolvida por **apagar teste**. Resolva por paralelismo, fixtures mais baratas, nível mais
  baixo da pirâmide, cache de dependências, sharding.

## 5. Quando a suíte fica lenta

Meça por teste e ataque o topo (curva de Pareto): os 5% mais lentos costumam valer 50% do tempo. Em
ordem: paralelizar e fatiar (shard) -> trocar e2e por teste de nível mais baixo -> fixtures
compartilhadas imutáveis -> banco em memória só onde fiel (preferir o real em contêiner) -> seleção por
impacto no PR. Orçamento de referência: PR em minutos, merge em dezenas de minutos, nightly sem teto
mas com dono.

## 6. Auditoria de saúde da suíte (`/reg-suite`)

Perguntas que a auditoria responde com número, não com impressão: duração p50/p95 do PR e do merge;
testes em quarentena, há quanto tempo e de quem; skips vencidos (`grep regression-skip`); taxa de
flaky por teste; mapa de impacto datado; rotas/módulos críticos sem nenhum teste de regressão.
