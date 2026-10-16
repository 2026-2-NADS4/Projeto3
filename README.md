# ShielDepy — TCC

Engenharia de caos dirigida por análise estática. O ShielDepy mapeia o sistema em grafos, encontra
as rotas sensíveis (as que gravam dados ou chamam APIs externas), gera testes de caos só para elas
e bloqueia o PR quando o código quebra sob falha. **O motor prova, a IA propõe:** toda saída da IA
é validada, existe caminho offline, e um teste só conta como falha se o controle passou.

```
Projeto3/
├── PLANO-CHAOS.md                        ← 🧭 plano, progresso e decisões de cada etapa
├── RELATORIO-CUSTO-IA.md                 ← 💰 estimativa de custo da IA no pipeline de caos
├── .github/workflows/shieldepy-chaos.yml ← 🚦 portão de caos no CI (GitHub Actions)
└── shieldepy_p1/                         ← ✅ o sistema — é aqui que se trabalha
```

## ✅ Sistema: `shieldepy_p1/`

Um monorepo em TypeScript:

| Pasta | O que é |
|---|---|
| `packages/core` | grafo estrutural e motor de colisões, topologia (rotas e I/O), diff entre mapas |
| `packages/extractors` | leitura de código com Tree-sitter (TS/JS, Java, Python, C#) e analisador léxico (PL/pgSQL), sem regex |
| `packages/agent` | IA plugável (Anthropic, Gemini ou offline) e o pipeline de caos em LangGraph |
| `packages/viewer` | visualizador do mapa: rotas, I/O, resultados do caos e diff do PR |
| `apps/vscode` | extensão do VS Code: ciclos, colisões, mapa e "Testar Caos" no editor, em modo local grátis |
| `apps/cli` | CLI `shieldepy` (`graph`, `topology`, `cycles`, `chaos`, `diff`, `publish`...) |
| `apps/frontend` | landing e portal em React, que recebe os resultados do caos enviados pelo CI |
| `examples/` | `checkout-express` (vulnerável), `checkout-express-fixed` (corrigido) e outros exemplos |

O grafo estrutural prova ciclos e o grafo de interações prova colisões. A topologia mostra quais
rotas tocam banco e APIs externas, e é sobre elas que os agentes propõem hipóteses de falha. O
[README do `shieldepy_p1`](shieldepy_p1/README.md) explica como o sistema funciona e como rodar.

```bash
cd shieldepy_p1 && npm install && npm run check
```

```bash
npm run cli -- chaos examples/checkout-express --offline --report chaos-report.md
```

O segundo comando gera e roda os testes de caos e aplica o portão: sai com exit 1 no exemplo
vulnerável e com 0 no `-fixed`. Os exemplos precisam do próprio `npm install`.

No VS Code, abra **a pasta `shieldepy_p1`** (não a raiz) para o F5 usar a configuração certa de `.vscode/`.

## Etapas

Concluídas e mergeadas na `main`:

- **E1:** o grafo mapeia o sistema inteiro e mostra a cobertura (`shieldepy graph`).
- **E2:** a topologia de rotas e operações de I/O.
- **E3:** agentes LangGraph que geram testes de caos para os pontos críticos.
- **E4:** portão de CI que bloqueia o PR (`shieldepy-chaos.yml`).
- **E5:** mapa incremental, com caos só nas rotas que o PR tocou e medição de custo.
- **V1–V3:** o visualizador do mapa e os resultados do caos no mapa e no portal.
- **R e Q:** jornada do usuário e `.vsix`; qualidade do mapa em projetos reais.

**Em andamento: C, caos utilizável.** O objetivo é fazer o caos rodar no projeto de quem instala.
Isso inclui validar em projetos Express reais, gerar o contrato quase automático (`--init`), cobrir
outros frameworks JS/TS, rodar o workflow no GitHub Actions de verdade e publicar o release v0.1.0.

Por enquanto o caos cobre **só JS/TS**. O detalhe de cada tarefa, com progresso e decisões, fica
no [`PLANO-CHAOS.md`](PLANO-CHAOS.md).
