# Relatório de uso de modelos e parâmetros — Sprint 03

## 1. Modelos comparados

| | Modelo A | Modelo B |
|---|---|---|
| Repo ID | `deepseek-ai/DeepSeek-V4.1-Flash` | `meta-llama/Llama-3.1-8B-Instruct` |
| Provedor | HuggingFace Inference Providers | HuggingFace Inference Providers |
| Motivo da escolha | Selecionado automaticamente pelo notebook entre os modelos "leves" (menor custo de crédito) disponíveis na conta/token usados no momento do teste — variante "Flash" do DeepSeek V4.1, mais barata que modelos maiores | Modelo consolidado e amplamente documentado da Meta, também selecionado entre os candidatos leves disponíveis; serve de comparação direta a um modelo "flash" mais recente |

> O notebook testa a lista de modelos disponíveis para o token usado (via `GET /v1/models` do router da HuggingFace) e escolhe os 2 primeiros classificados como leves — por isso o Repo ID exato pode variar conforme a conta/token e os Inference Providers habilitados no momento em que rodarem.

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

*Notas de qualidade acima são uma leitura inicial de conteúdo (aderência ao contexto, correção técnica, completude) feita sobre as respostas reais geradas — o grupo pode ajustar se, ao reler, avaliar diferente.*

## 4. Médias

| Métrica | Modelo A (DeepSeek-V4.1-Flash) | Modelo B (Llama-3.1-8B-Instruct) |
|---|---|---|
| Latência média (s) | 13.38 | 12.40 |
| Palavras médias por resposta | 143.0 | 127.2 |
| Nota de qualidade média | 4.4 | 4.6 |

## 5. Modelo e parametrização escolhidos

O Modelo B (`meta-llama/Llama-3.1-8B-Instruct`) teve o melhor equilíbrio geral: latência média ligeiramente menor, nota de qualidade média mais alta e nenhuma resposta comprometida por corte de texto no meio de uma explicação de segurança (diferente do Modelo A, que teve 3 das 5 respostas truncadas pelo limite de tokens). O Modelo A foi mais verboso em média (mais palavras por resposta), mas isso não se traduziu em respostas mais completas dado o teto de `max_new_tokens`.

Quanto à parametrização: `temperature=0.1` se mostrou adequada — nenhuma das respostas fugiu do escopo do documento ou "alucinou" especificações fora do contexto recuperado. O ponto de ajuste real é o `max_new_tokens=300`: ele foi necessário para conter o consumo de créditos gratuitos da HuggingFace, mas custou completude em respostas mais longas (Desmontar, Funcionalidades, Conexão elétrica). Para uma versão de produção (fora do teste com cota limitada), o grupo recomendaria voltar a `max_new_tokens=600–800` como meio-termo entre custo e completude.
