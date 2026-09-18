# Relatório de uso de modelos e parâmetros — Sprint 03

## 1. Modelos comparados

| | Modelo A | Modelo B |
|---|---|---|
| Repo ID | `deepseek-ai/DeepSeek-V4.1-Flash` | `meta-llama/Llama-3.1-8B-Instruct` |
| Provedor | HuggingFace Inference Providers | HuggingFace Inference Providers |
| Motivo da escolha | Variante “Flash” do DeepSeek V4.1 — menor custo de crédito entre os modelos de qualidade disponível na conta usada nos testes | Modelo consolidado da Meta, amplamente documentado; serve de baseline estável para comparar com a variante “flash” mais recente |

> Os dois modelos foram fixados no notebook após testes práticos de cota e disponibilidade. Quando o token/cota da HuggingFace não está disponível (erro 402 ou ausência de secret), o notebook **reaproveita automaticamente** o `comparativo_modelos.csv` já existente com resultados reais de execução anterior, em vez de travar.

## 2. Parametrização usada em ambos os modelos

| Parâmetro | Valor | Justificativa |
|---|---|---|
| `temperature` | 0.1 | Baixa, para respostas mais determinísticas e técnicas (assistente factual sobre o manual GoodWe) |
| `top_p` | 0.95 | Mantido próximo do padrão, sem necessidade de mais diversidade nas respostas |
| `max_new_tokens` | 300 | Reduzido de 1000 para 300 durante os testes para conter o consumo de créditos gratuitos da HuggingFace (ver Problema 2 do relatório de evolução) — trade-off: algumas respostas mais longas saíram truncadas (ver seção 3) |

## 3. Resultados por pergunta (eval set)

| Pergunta | Modelo | Latência (s) | Palavras na resposta | Nota de qualidade (1-5) |
|---|---|---|---|---|
| Como fazer Download e Instalação do Aplicativo? | A | 7.19 | 87 | 5 |
| Como fazer Download e Instalação do Aplicativo? | B | 8.06 | 144 | 5 |
| Como Desmontar o carregador? | A | 11.96 | 162 | 4 (resposta truncada pelo limite de tokens) |
| Como Desmontar o carregador? | B | 5.24 | 92 | 5 |
| Quais são as Funcionalidades? | A | 6.78 | 173 | 4 (resposta truncada pelo limite de tokens) |
| Quais são as Funcionalidades? | B | 26.00 | 166 | 4 (resposta truncada pelo limite de tokens) |
| Como Desligar o carregador? | A | 28.56 | 132 | 5 |
| Como Desligar o carregador? | B | 5.09 | 63 | 4 (correta, porém mais resumida que o documento) |
| Sobre a Conexão elétrica, quais são as precauções de segurança? | A | 12.39 | 161 | 4 (resposta truncada pelo limite de tokens) |
| Sobre a Conexão elétrica, quais são as precauções de segurança? | B | 17.60 | 171 | 5 |

*Notas de qualidade: aderência ao contexto recuperado, correção técnica e completude, avaliadas sobre as respostas reais do `comparativo_modelos.csv`.*

## 4. Médias

| Métrica | Modelo A (DeepSeek-V4.1-Flash) | Modelo B (Llama-3.1-8B-Instruct) |
|---|---|---|
| Latência média (s) | 13.38 | 12.40 |
| Palavras médias por resposta | 143.0 | 127.2 |
| Nota de qualidade média | 4.4 | 4.6 |

## 5. Modelo e parametrização escolhidos

O **Modelo B** (`meta-llama/Llama-3.1-8B-Instruct`) teve o melhor equilíbrio geral: latência média ligeiramente menor, nota de qualidade média mais alta e nenhuma resposta comprometida por corte de texto no meio de uma explicação de segurança (diferente do Modelo A, que teve 3 das 5 respostas truncadas pelo limite de tokens). O Modelo A foi mais verboso em média (mais palavras por resposta), mas isso não se traduziu em respostas mais completas dado o teto de `max_new_tokens`.

Quanto à parametrização: `temperature=0.1` se mostrou adequada — nenhuma das respostas fugiu do escopo do documento ou inventou especificações fora do contexto recuperado. O ponto de ajuste real é o `max_new_tokens=300`: ele foi necessário para conter o consumo de créditos gratuitos da HuggingFace, mas custou completude em respostas mais longas (Desmontar, Funcionalidades, Conexão elétrica). Para uma versão de produção (fora do teste com cota limitada), o grupo recomenda voltar a `max_new_tokens=600–800` como meio-termo entre custo e completude.

## 6. Pipeline principal e testes de segurança (modelo local)

O pipeline conversacional com memória (Bloco A) e os casos de teste de segurança (Bloco C) rodam em modelo **100% local e gratuito** (`Qwen/Qwen2.5-1.5B-Instruct`, com fallback automático para `TinyLlama/TinyLlama-1.1B-Chat-v1.0`), para não depender de cota de Inference Providers e permitir demonstração ao vivo sem risco de erro 402. Os resultados documentados de segurança estão em `resultados_seguranca.csv`.
