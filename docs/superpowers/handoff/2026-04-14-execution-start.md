# Session Handoff — Smart Session Rename v1.5 Execution

**De:** Sessão de brainstorming + 3 gates de review (2026-04-13 → 2026-04-14)
**Para:** Próxima sessão dedicada à execução do plano v1.5
**Data:** 2026-04-14

---

## Cole o prompt abaixo em uma nova sessão do Claude Code no diretório deste projeto:

```
Vou retomar a implementação do plugin smart-session-rename v1.5. O trabalho de
brainstorming, spec e plano já está completo. Toda a arquitetura e decisões
estão documentadas. Por favor, leia os arquivos abaixo nesta ordem antes de
qualquer ação para se contextualizar:

1. Memória do projeto (contexto de escopo e decisões-chave):
   - ~/.claude/projects/-Users-fernandobertholdo-Documents-tech-projects-claude-code-smart-session-rename/memory/MEMORY.md
   - ~/.claude/projects/-Users-fernandobertholdo-Documents-tech-projects-claude-code-smart-session-rename/memory/project_v2_deferred.md

2. Spec da v1.5 (arquitetura completa):
   docs/superpowers/specs/2026-04-14-smart-session-rename-v15-design.md

3. Spec da v2 (progressão futura, não construir agora):
   docs/superpowers/specs/2026-04-07-smart-session-rename-v2-design.md

4. Plano de implementação v1.5 (fonte da verdade para execução):
   docs/superpowers/plans/2026-04-14-smart-session-rename-v15.md

5. Últimos 5 commits para entender a trajetória:
   git log --oneline -5

## Contexto mínimo que você precisa saber sem ler os arquivos

- Plugin de bash para Claude Code que auto-renomeia sessões usando
  `claude -p --json-schema` com Haiku 4.5.
- v1 existe no repo mas nunca foi usada em prática: v1.5 é greenfield,
  substitui v1 completamente (deleção na Phase 8 após integração verde).
- Arquitetura modular: scripts/lib/*.sh um por responsabilidade (config,
  state, logger, transcript, scorer, llm, validate, writer) + orchestrator
  rename-hook.sh + smart-rename-cli.sh (skill).
- Economia via work_score heurístico determinístico (budget 6 chamadas LLM
  por sessão, +2 overflow manual via /smart-rename force). Custo real em
  modo OAuth ~$0.60/sessão (documentado no spec §1).
- Output estruturado via --json-schema (não é parsing frágil em bash).
- Plano passou por 3 gates de review (Gate 1 reviewer, Gate 2 codex
  adversarial, Gate 3 spot-check pós-fix). Todos aprovados.

## Próximo passo

O plano tem 12 phases / ~25 tasks seguindo TDD bite-sized. Invoque a skill
`superpowers:subagent-driven-development` para execução com um subagent
fresh por task + review entre tasks. Comece pela Phase 0 (tratar as
modificações pendentes em scripts/generate-name.sh e scripts/rename-hook.sh
do v1 — o plano explica como).

Alternativa: se preferir rodar tudo nesta sessão interativa acompanhando
cada passo em tempo real, invoque `superpowers:executing-plans`.

Confirme que leu os arquivos acima e me mostre a Phase 0 do plano antes de
tocar em qualquer código.
```

---

## Por que este formato

1. **Auto-suficiente:** a nova sessão não precisa nem ver esta sessão passada
   — lendo os 4 arquivos + git log já entende tudo.
2. **Ordem explícita:** memória primeiro (leve), depois specs, depois plano.
   A LLM carrega do mais abstrato para o mais concreto.
3. **Contexto mínimo inline:** se a LLM for preguiçosa e não ler os arquivos,
   ao menos sabe o suficiente pra não errar feio.
4. **Próximo passo explícito:** não deixa ambiguidade sobre "o que fazer
   agora". Aponta para a skill certa e a Phase certa.
5. **Guard-rail final:** "confirme que leu os arquivos... antes de tocar em
   qualquer código" evita que a LLM pule direto para implementação com
   contexto parcial.

## Se preferir um prompt mais curto

Versão enxuta (120 palavras):

```
Retomando a implementação do plugin smart-session-rename v1.5. Contexto
completo em docs/superpowers/plans/2026-04-14-smart-session-rename-v15.md
(plano; fonte da verdade) + docs/superpowers/specs/2026-04-14-smart-session-rename-v15-design.md
(spec) + ~/.claude/projects/-Users-fernandobertholdo-Documents-tech-projects-claude-code-smart-session-rename/memory/
(memórias do projeto). Plano passou por 3 gates de review e está aprovado.
Leia os 3 e invoque superpowers:subagent-driven-development começando pela
Phase 0. Confirme que leu antes de tocar em código.
```

A versão completa acima é preferível se você quer a LLM inicial mais
contextualizada já no primeiro turno.
