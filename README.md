# Estudo das ferramentas em nuvens comerciais que fornecem soluções de PLN

&emsp;O objetivo desta atividade é realizar um levantamento das ferramentas de NLP presentes em uma nuvem comercial. Listar os serviços e um exemplo de aplicação de cada um deles. Atenção: cada aluno deve listar os serviços de apenas uma nuvem comercial. Apresentar os resultados como um arquivo Markdown no Github. Colar o link do repositório onde a implementação foi realizada. Importante: Não utilizar ferramentas de geração de IA. O processo de aprendizagem e familiarização com ferramentas é importante durante o processo de formação. Sempre que possível, dar preferencia para a implementação por requisições HTTP em detrimento da utilização de uma biblioteca específica do fabricante.

## Introdução

&emsp;O Google Cloud Platform (GCP) oferece uma ampla gama de serviços de computação em nuvem, incluindo soluções avançadas de Inteligência Artificial (IA). Entre os principais serviços de IA disponíveis na GCP estão: visão computacional (Vision AI), tradução automática (Translation AI), speech-to-text, text-to-speech e processamento de linguagem natural (Natural Language AI).

&emsp;Este levantamento foca especificamente nos serviços de **Processamento de Linguagem Natural (PLN / NLP)** da GCP, explorando suas funcionalidades e exemplos de aplicação prática. As informações foram extraídas da documentação oficial da Google Cloud: [https://cloud.google.com/natural-language/docs](https://cloud.google.com/natural-language/docs)

&emsp;Os principais serviços de NLP oferecidos pela GCP são:

- Como analisar sentimentos (Sentiment Analysis)
- Como analisar entidades (Entity Analysis)
- Análise sintática (Syntax Analysis)
- Como analisar o sentimento da entidade (Entity Sentiment Analysis)
- Como classificar conteúdo (Content Classification)

&emsp;Nos tópicos a seguir, cada serviço será descrito individualmente, com um exemplo de aplicação prático.

## 1. Sentiment Analysis

**Referência:** [https://cloud.google.com/natural-language/docs/analyzing-sentiment](https://cloud.google.com/natural-language/docs/analyzing-sentiment)


&emsp;A Análise de Sentimento inspeciona o texto fornecido e identifica a opinião emocional dominante (positiva, negativa ou neutra). A análise retorna dois valores principais:

- **score**: indica se o sentimento geral é positivo (> 0), neutro (= 0) ou negativo (< 0).
- **magnitude**: indica a intensidade emocional.

&emsp;A análise é realizada através do método `analyzeSentiment` da API REST do Google Cloud Natural Language.

**Exemplo de aplicação:**  
&emsp;Monitorar automaticamente o sentimento de comentários de clientes em um e-commerce para gerar relatórios de satisfação.

**Exemplo de requisição HTTP:**

```bash
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data '{
    "encodingType": "UTF8",
    "document": {
      "type": "PLAIN_TEXT",
      "content": "O produto chegou rápido e em ótimo estado. Estou muito satisfeito!"
    }
  }' \
  "https://language.googleapis.com/v2/documents:analyzeSentiment"
```

**Exemplo de resposta JSON:**

```json
{
  "documentSentiment": {
    "magnitude": 1.5,
    "score": 0.9
  },
  "language": "pt",
  "sentences": [
    {
      "text": {
        "content": "O produto chegou rápido e em ótimo estado.",
        "beginOffset": 0
      },
      "sentiment": {
        "magnitude": 0.8,
        "score": 0.7
      }
    },
    {
      "text": {
        "content": "Estou muito satisfeito!",
        "beginOffset": 52
      },
      "sentiment": {
        "magnitude": 0.7,
        "score": 0.9
      }
    }
  ]
}
```

**Interpretação da saída:**

- `"documentSentiment.score": 0.9` → O sentimento geral do comentário é fortemente positivo.
- `"documentSentiment.magnitude": 1.5` → Indica uma intensidade emocional alta.
- A análise por frases mostra que ambas expressam sentimentos positivos, com destaque para a segunda frase ("Estou muito satisfeito!"), com score 0.9.

## 2. Entity Analysis

**Referência:** [https://cloud.google.com/natural-language/docs/analyzing-entities](https://cloud.google.com/natural-language/docs/analyzing-entities?hl=pt-br)

&emsp;A Análise de Entidades inspeciona o texto fornecido para identificar entidades conhecidas, como nomes de pessoas, organizações, locais, datas, números ou endereços, e retorna informações sobre elas.

&emsp;A análise é realizada através do método analyzeEntities da API REST do Google Cloud Natural Language.

**Exemplo de aplicação:**

&emsp;Automatizar a extração de nomes de pessoas, empresas e locais em artigos de notícias para alimentar uma base de dados de inteligência competitiva.

**Exemplo de requisição HTTP:**

```bash
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data '{
    "encodingType": "UTF8",
    "document": {
      "type": "PLAIN_TEXT",
      "content": "O presidente Lula discursou no Palácio do Planalto, em Brasília, no dia 7 de setembro de 2023."
    }
  }' \
  "https://language.googleapis.com/v2/documents:analyzeEntities"
```

**Exemplo de resposta JSON:**

```json
{
  "entities": [
    {
      "name": "Lula",
      "type": "PERSON",
      "mentions": [
        {
          "text": {
            "content": "Lula"
          }
        }
      ]
    },
    {
      "name": "Palácio do Planalto",
      "type": "LOCATION",
      "mentions": [
        {
          "text": {
            "content": "Palácio do Planalto"
          }
        }
      ]
    },
    {
      "name": "Brasília",
      "type": "LOCATION",
      "mentions": [
        {
          "text": {
            "content": "Brasília"
          }
        }
      ]
    },
    {
      "name": "7 de setembro de 2023",
      "type": "DATE",
      "mentions": [
        {
          "text": {
            "content": "7 de setembro de 2023"
          }
        }
      ]
    }
  ],
  "languageCode": "pt",
  "languageSupported": true
}
```
**Interpretação da saída:**

`"entities"` → A resposta traz uma lista das entidades detectadas, com seus respectivos tipos:

* `"Lula"` → PERSON

* `"Palácio do Planalto"` → LOCATION

* `"Brasília"` → LOCATION

* `"7 de setembro de 2023"` → DATE

* `"languageCode"`: `"pt"` → O idioma do texto foi corretamente identificado como português.

&emsp;O sistema poderia utilizar essas informações para indexar automaticamente artigos jornalísticos por entidades citadas, permitindo consultas como "notícias envolvendo Lula", "eventos no Palácio do Planalto" ou "eventos em 7 de setembro".

## 3. Syntax Analysis

**Referência:** [https://cloud.google.com/natural-language/docs/analyzing-syntax?hl=pt-br](https://cloud.google.com/natural-language/docs/analyzing-syntax?hl=pt-br)

&emsp;A Análise Sintática inspeciona a estrutura gramatical do texto. O método analyzeSyntax divide o texto em frases e tokens (geralmente palavras) e fornece informações linguísticas sobre cada token, como parte do discurso (part-of-speech), dependências sintáticas e lematização.

**Exemplo de aplicação:**

&emsp;Aprimorar a capacidade de compreensão de um chatbot, permitindo que ele identifique com mais precisão a função de cada palavra em uma pergunta ou comando do usuário.

**Exemplo de requisição HTTP:**
```bash
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data '{
    "encodingType": "UTF8",
    "document": {
      "type": "PLAIN_TEXT",
      "content": "O Google, sediado em Mountain View, lançou o novo Android durante o evento anual de tecnologia."
    }
  }' \
  "https://language.googleapis.com/v1/documents:analyzeSyntax"
```

**Exemplo de resposta JSON:**

```json
{
  "sentences": [
    {
      "text": {
        "content": "O Google, sediado em Mountain View, lançou o novo Android durante o evento anual de tecnologia.",
        "beginOffset": 0
      }
    }
  ],
  "tokens": [
    {
      "text": {
        "content": "Google",
        "beginOffset": 2
      },
      "partOfSpeech": {
        "tag": "NOUN",
        "number": "SINGULAR",
        "proper": "PROPER"
      },
      "dependencyEdge": {
        "headTokenIndex": 5,
        "label": "NSUBJ"
      },
      "lemma": "Google"
    },
    {
      "text": {
        "content": "lançou",
        "beginOffset": 35
      },
      "partOfSpeech": {
        "tag": "VERB",
        "tense": "PAST"
      },
      "dependencyEdge": {
        "headTokenIndex": 5,
        "label": "ROOT"
      },
      "lemma": "lançar"
    }
    ...
  ],
  "language": "pt"
}
```

**Interpretação da saída:**

`"sentences"` → O texto foi segmentado em frases (aqui, apenas uma).

`"tokens"` → Cada palavra (ou token) foi analisada com:

* `"partOfSpeech"` → Parte do discurso: substantivo (NOUN), verbo (VERB), etc.

* `"lemma"` → Forma base da palavra (ex: "lançar" em vez de "lançou").

* "`dependencyEdge.label"` → Relação sintática (ex: sujeito da frase → NSUBJ; raiz da frase → ROOT).

## 4. Entity Sentiment Analysis

**Referência:** [https://cloud.google.com/natural-language/docs/analyzing-entity-sentiment?hl=pt-br](https://cloud.google.com/natural-language/docs/analyzing-entity-sentiment?hl=pt-br)


&emsp;A Análise de Sentimento da Entidade combina as análises de entidade e de sentimento, detectando como cada entidade é mencionada no texto, com um sentimento positivo, negativo ou neutro.

&emsp;Para cada entidade, a API retorna um score (positivo ou negativo) e uma magnitude (intensidade do sentimento).

&emsp;A análise é realizada através do método analyzeEntitySentiment da API REST do Google Cloud Natural Language.

**Exemplo de aplicação:**

&emsp;Analisar automaticamente menções a produtos específicos em avaliações de clientes, por exemplo, descobrir quais modelos de smartphone são mais elogiados ou criticados em comentários.

**Exemplo de requisição HTTP:**

```bash
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data '{
    "document": {
      "type": "PLAIN_TEXT",
      "content": "Comprei o Galaxy S24 Ultra e a câmera é simplesmente incrível. Já o carregador é muito lento."
    },
    "encodingType": "UTF8"
  }' \
  "https://language.googleapis.com/v1/documents:analyzeEntitySentiment"
```
**Exemplo de resposta JSON (simulada com base no que a API costuma retornar):**

```json
{
  "entities": [
    {
      "name": "Galaxy S24 Ultra",
      "type": "CONSUMER_GOOD",
      "sentiment": {
        "magnitude": 0.9,
        "score": 0.8
      },
      "mentions": [
        {
          "text": {
            "content": "Galaxy S24 Ultra"
          }
        }
      ]
    },
    {
      "name": "câmera",
      "type": "OTHER",
      "sentiment": {
        "magnitude": 0.8,
        "score": 0.9
      },
      "mentions": [
        {
          "text": {
            "content": "câmera"
          }
        }
      ]
    },
    {
      "name": "carregador",
      "type": "OTHER",
      "sentiment": {
        "magnitude": 0.7,
        "score": -0.6
      },
      "mentions": [
        {
          "text": {
            "content": "carregador"
          }
        }
      ]
    }
  ],
  "language": "pt"
}
```
**Interpretação da saída:**

* `"Galaxy S24 Ultra":` score 0.8 → menção positiva ao produto.

* `"câmera":` score 0.9 → menção muito positiva ao componente.

* `"carregador":` score -0.6 → menção negativa.

&emsp;O sistema de análise poderia detectar que o produto principal e a câmera foram elogiados, identificar que o carregador é um ponto de insatisfação e alimentar dashboards de produto com essas informações, ajudando a priorizar melhorias.

## 5. Content Classification

**Referência:** [https://cloud.google.com/natural-language/docs/classifying-text?hl=pt-br](https://cloud.google.com/natural-language/docs/classifying-text?hl=pt-br)

&emsp;A Classificação de Conteúdo analisa um texto e retorna uma lista de categorias temáticas que se aplicam ao conteúdo.

&emsp;A API oferece um conjunto de categorias predefinidas, como Tecnologia, Saúde, Negócios, Esportes, Arte e Entretenimento, entre outras.

**Exemplo de aplicação:**

&emsp;Automatizar a categorização de artigos publicados em um portal de notícias ou blog, permitindo agrupar conteúdos por temas e melhorar a navegação do site.

**Exemplo de requisição HTTP:**

```bash
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
  -H "Content-Type: application/json; charset=utf-8" \
  --data '{
    "document": {
      "type": "PLAIN_TEXT",
      "content": "A startup brasileira HealthTech anunciou hoje uma nova plataforma de telemedicina que conecta pacientes a médicos especialistas em tempo real."
    },
    "classificationModelOptions": {
      "v2Model": {
        "contentCategoriesVersion": "V2"
      }
    }
  }' \
  "https://language.googleapis.com/v1/documents:classifyText"
```

```json
{
  "categories": [
    {
      "name": "/Health/Health Conditions/Medical Technology",
      "confidence": 0.95
    },
    {
      "name": "/Health/Health Services",
      "confidence": 0.88
    }
  ]
}
```

**Interpretação da saída:**

&emsp;A API classificou o texto nas seguintes categorias:

* `/Health/Health Conditions/Medical Technology` → com confiança de 95%.

* `/Health/Health Services` → com confiança de 88%.

&emsp;O sistema poderia automaticamente indexar o artigo sob temas de tecnologia médica e serviços de saúde, organizar melhor os conteúdos do portal, melhorar a experiência de busca e de recomendação personalizada para os leitores.
