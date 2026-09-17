# EV Challenge — GoodWe · Sprint 03

Chatbot com IA para mobilidade elétrica — Refactory conversacional com framework de agentes (LangChain).

Disciplina: Prompt and Artificial Intelligence — FIAP × GoodWe Brasil — 2026.2

## O que tem nessa entrega

| Arquivo | O que é |
|---|---|
| `sprint-3.ipynb` | Notebook principal — pipeline conversacional em LangChain, memória de sessão, testes de segurança e comparação entre 2 modelos |
| `GW_HCA-G2_User-Manual-PT.pdf` | Manual da GoodWe usado como base de conhecimento (RAG) — necessário pra rodar o notebook |
| `resultados_seguranca.csv` | 6 casos de teste de segurança (prompt injection, escopo, conselho jurídico/elétrico), com a resposta obtida e a avaliação manual |
| `comparativo_modelos.csv` | Resultados do eval set (5 perguntas) rodado nos 2 modelos comparados |
| `relatorio_modelos.md` | Parâmetros usados, resultados e seleção justificada do modelo/parametrização |
| `relatorio_evolucao_sprint03.pdf` | Relatório de evolução do projeto (resumo, refatoração, comparativo antes/depois, problemas e soluções, equipe) |

## O que mudou em relação à Sprint 02

Na Sprint 02, o chatbot era um RAG manual: ChromaDB chamado diretamente e respostas geradas via HuggingFace Inference Client, sem memória de conversa real (o parâmetro de histórico existia mas nunca era usado). Na Sprint 03, o núcleo foi reconstruído com **LangChain**: retrieval via `langchain-chroma`, prompt estruturado (`ChatPromptTemplate`) e memória de sessão nativa via `RunnableWithMessageHistory`. Detalhes completos em `relatorio_evolucao_sprint03.pdf`.

## ⚠️ Importante: o notebook NÃO roda "do zero" com um clique

Diferente de um script comum, este notebook depende de 3 coisas externas que **não vêm junto do arquivo** e precisam ser configuradas por quem for rodar:

1. Uma API key da HuggingFace (para os modelos de chat)
2. O PDF do manual GoodWe anexado como input do notebook (não está embutido no `.ipynb`)
3. Crédito disponível na conta da HuggingFace usada (o plano gratuito tem cota mensal — ver "Limitações" abaixo)

Sem esses três, o notebook trava logo nas primeiras células. Siga o passo a passo abaixo antes de rodar.

## Como rodar

1. **Suba o notebook no Kaggle** (Settings → Accelerator: **None**, esse notebook não precisa de GPU).
2. **Anexe o PDF do manual GoodWe como input**: o arquivo `GW_HCA-G2_User-Manual-PT.pdf` está neste repositório — baixe ele e, no Kaggle, clique em "+ Add Input" → "Upload" → selecione o PDF baixado. O notebook procura automaticamente qualquer `.pdf` dentro de `/kaggle/input`, então não importa o nome do dataset que você criar ao subir.
3. **Configure sua API key da HuggingFace como Secret**:
   - Gere um token em [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)
   - No Kaggle, vá em **Add-ons → Secrets** e crie um secret com esse token
   - O notebook tenta os nomes `giovani-secret`, `HUGGING_FACE_API_KEY` e a variável de ambiente `HF_TOKEN`, nessa ordem — dê um desses nomes ao seu secret (ou adicione o nome que você escolheu na lista `NOMES_SECRET_CANDIDATOS`, na seção 3 do notebook)
4. **Rode as células em ordem**, da seção 1 até a seção 11. A seção 7 escolhe automaticamente 2 modelos "leves" com base no que sua conta/token consegue acessar — os nomes exatos podem variar dos usados na nossa execução.
5. A seção 12 é **opcional** (interface Gradio de demonstração) — pode ser pulada sem prejuízo.

## Limitações conhecidas

- **Cota de créditos gratuitos da HuggingFace**: a conta usada nos testes já esgotou a cota mensal mais de uma vez durante o desenvolvimento (erro HTTP 402). Se isso acontecer ao tentar reproduzir, é necessário usar outro token (de outra conta) ou aguardar o reset mensal — não é um bug do código. Documentado como Problema 2 em `relatorio_evolucao_sprint03.pdf`.
- **max_new_tokens reduzido para 300** (em vez de 1000) para conter esse consumo de crédito — algumas respostas mais longas no eval set saíram truncadas (ver `relatorio_modelos.md`, seção 3).
- Os nomes exatos dos 2 modelos comparados (seção 7) são escolhidos dinamicamente e podem variar dependendo de qual conta/token for usado para rodar.

## Equipe

Ver `identificacao_equipe.txt` — **esse arquivo não faz parte do repositório**, é anexado separadamente apenas na entrega da plataforma da FIAP, junto com o link deste repositório.
