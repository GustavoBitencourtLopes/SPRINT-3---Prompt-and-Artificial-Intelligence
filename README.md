# EV Challenge — GoodWe · Sprint 03

Chatbot com IA para mobilidade elétrica — Refactory conversacional com framework de agentes (LangChain).

Disciplina: Prompt and Artificial Intelligence — FIAP × GoodWe Brasil — 2026.2

## O que tem nessa entrega

| Arquivo | O que é |
|---|---|
| `sprint3.ipynb` | Notebook principal — pipeline conversacional em LangChain, memória de sessão, testes de segurança e comparação entre 2 modelos |
| `GW_HCA-G2_User-Manual-PT.pdf` | Manual da GoodWe usado como base de conhecimento (RAG) — necessário pra rodar o notebook |
| `resultados_seguranca.csv` | 6 casos de teste de segurança (prompt injection, escopo, conselho jurídico/elétrico), com a resposta obtida e a avaliação manual |
| `comparativo_modelos.csv` | Resultados do eval set (5 perguntas) rodado nos 2 modelos comparados — também funciona como cache: se a cota da HuggingFace estiver esgotada, o notebook reaproveita este arquivo em vez de travar |
| `relatorio_modelos.md` | Parâmetros usados, resultados e seleção justificada do modelo/parametrização |
| `relatorio_evolucao_sprint03.pdf` | Relatório de evolução do projeto (resumo, refatoração, comparativo antes/depois, problemas e soluções, equipe) |

## O que mudou em relação à Sprint 02

Na Sprint 02, o chatbot era um RAG manual: ChromaDB chamado diretamente e respostas geradas via HuggingFace Inference Client, sem memória de conversa real (o parâmetro de histórico existia mas nunca era usado). Na Sprint 03, o núcleo foi reconstruído com LangChain: retrieval via `langchain-chroma`, prompt estruturado (`ChatPromptTemplate`) e memória de sessão nativa via `RunnableWithMessageHistory`. Detalhes completos em `relatorio_evolucao_sprint03.pdf`.

O pipeline principal (memória, Bloco A) e os testes de segurança (Bloco C) rodam num **modelo local e gratuito** (`Qwen/Qwen2.5-1.5B-Instruct`, com fallback automático para `TinyLlama/TinyLlama-1.1B-Chat-v1.0`) — não dependem de nenhuma cota externa. Só a comparação entre modelos (Bloco B) usa a API da HuggingFace (`DeepSeek-V4.1-Flash` e `Llama-3.1-8B-Instruct`).

## Como rodar

1. Suba o notebook no Kaggle (**Settings → Accelerator: None** é suficiente; com GPU ele roda mais rápido, mas não é obrigatório).
2. Anexe **dois arquivos** como input do notebook, via **"+ Add Input" → "Upload"**:
   - `GW_HCA-G2_User-Manual-PT.pdf` (obrigatório — o notebook procura automaticamente qualquer `.pdf` dentro de `/kaggle/input`, não importa o nome do dataset).
   - `comparativo_modelos.csv` (opcional, mas recomendado — sem ele, se a cota da HuggingFace estiver esgotada, o Bloco B não tem um arquivo pra reaproveitar e a célula fica incompleta).
3. Token da HuggingFace **(opcional)** — só necessário para o Bloco B (comparação entre modelos). Os Blocos A e C funcionam sem ele.
   - Gere um token em huggingface.co/settings/tokens.
   - No Kaggle, vá em **Add-ons → Secrets** e crie um secret com esse token.
   - O notebook tenta os nomes `giovani-secret`, `HUGGING_FACE_API_KEY` e a variável de ambiente `HF_TOKEN`, nessa ordem — use um desses nomes (ou adicione o seu na lista `NOMES_SECRET_CANDIDATOS`, na seção 3 do notebook).
4. Rode as células em ordem, da seção 1 até a seção 12 (a seção 12 é só um checklist de conferência, não tem código).
5. No final:
   - Confira o Turno 3 da seção 9 — ele precisa citar as perguntas dos Turnos 1 e 2 (prova de memória).
   - Confira `resultados_seguranca.csv` gerado e revise a coluna `avaliacao_manual` se rodar de novo.

## Limitações conhecidas

- **Cota de créditos gratuitos da HuggingFace (só afeta o Bloco B):** a conta usada nos testes esgotou a cota mensal mais de uma vez durante o desenvolvimento (erro HTTP 402). Se isso acontecer ao reproduzir, o notebook reaproveita automaticamente o `comparativo_modelos.csv` já entregue neste repositório (com resultados reais de uma execução anterior) em vez de travar — mas só se esse arquivo estiver anexado como input (passo 2 acima). Documentado como Problema 2 em `relatorio_evolucao_sprint03.pdf`.
- `max_new_tokens` reduzido para 300 (em vez de 1000) para conter esse consumo de crédito na comparação de modelos — algumas respostas mais longas do eval set saíram truncadas (ver `relatorio_modelos.md`, seção 3).
- O modelo local (Qwen2.5-1.5B) é pequeno; em alguns casos de teste de segurança a resposta pode sair um pouco desorganizada mesmo sem quebrar o guardrail — isso está registrado com honestidade nas avaliações de `resultados_seguranca.csv` (2 dos 6 casos foram marcados como "PARCIAL", não "OK").

## Equipe

Gustavo Bitencourt — RM: 568885, Daniel Vieira — RM: 573326, Leonardo Takachi — RM: 569066, Giovane Salazar — RM: 570396
