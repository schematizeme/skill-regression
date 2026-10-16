# schematize-regression — regressão

> **Bug corrigido nunca volta.** Teste vermelho antes do fix, teste de regressão permanente,
> golden master antes de refatorar legado, suíte completa no merge, desempenho e visual com baseline,
> `git bisect run` e incidente que só fecha com teste. Provado por máquina, não por memória.

Pacote de **skill normativa para [Claude Code](https://claude.com/claude-code)**.
Parte do catálogo **schematize skills**.

## Por que ela existe

A `schematize-qa` define a estratégia e a pirâmide de testes. Faltava a dona do ciclo "nunca volta":
o que obriga o fix a deixar um teste, o que impede o teste de ser apagado ou pulado, e como se
caça e se mede a regressão que escapou.

## Instalar

### Pelo app schematize (recomendado)

```bash
schematize install regression    # requer o CLI schematize instalado
```

### Manual

```bash
git clone https://github.com/schematizeme/skill-regression.git /tmp/skill-regression
bash /tmp/skill-regression/install.sh .        # instala em .claude/ do projeto atual
```

## O que tem dentro

- **SKILL.md** — o contrato: 13 pisos inegociáveis, a fronteira com as skills vizinhas e o mapa de references.
- **references/** — bug primeiro, permanência, caracterização, suíte, desempenho, visual, contrato/dados/IA, bisect, incidente e métricas, e anexo volátil (com data e fonte).
- **scripts/** — `check-regression.sh`, o gate (agnóstico de linguagem, só bash e git; exit 2 = nada verificado), e seu teste com vermelho e verde.
- **assets/** — `CLAUDE.md` sempre-on e comandos `/reg-*`.

## Licença

MIT. Veja `LICENSE`.
