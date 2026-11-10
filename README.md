# Estudo DP-700 — Microsoft Fabric Data Engineer Associate

Material de revisão para a certificação **DP-700**, organizado por tema do exame oficial, com prompts prontos para gerar podcasts e quizzes de estudo.

> Não é material oficial da Microsoft. Use como apoio, sempre cruzando com o [outline oficial da DP-700](https://learn.microsoft.com/credentials/certifications/exams/dp-700/) e o Microsoft Learn.

## Conteúdo

| Pasta / arquivo | Para quê |
| --- | --- |
| [`revisao/anotacoes-tematicas.md`](revisao/anotacoes-tematicas.md) | Revisão temática consolidada (governança, Git/CI-CD, pipelines, etc.) |
| [`prompts/quiz.md`](prompts/quiz.md) | Prompt para gerar quiz no estilo da prova |
| [`prompts/podcast.md`](prompts/podcast.md) | Prompt para Audio Overview / podcast de revisão |
| `recursos/` | Pasta local para PDFs e materiais privados (não versionada) |

## Mapa do exame

| Domínio | Peso |
| --- | --- |
| Implementar e gerenciar uma solução analítica | 30–35% |
| Ingerir e transformar dados | 30–35% |
| Monitorar e otimizar uma solução analítica | 30–35% |

## Como estudar com este repo

1. Leia a [revisão temática](revisao/anotacoes-tematicas.md) por domínio.
2. Foque nas **perguntas-gatilho** e nas tabelas de decisão (são as pegadinhas clássicas).
3. Carregue a revisão + o prompt de [`prompts/quiz.md`](prompts/quiz.md) em um LLM / notebook para praticar.
4. Use [`prompts/podcast.md`](prompts/podcast.md) para gerar um áudio de revisão rápida (20–25 min).
5. Valide dúvidas no Microsoft Learn e em laboratórios hands-on no Fabric.

### Pegadinhas que mais caem

- **ReadAll** vs **ReadData** (RLS)
- **REVOKE** vs **DENY**
- **OPTIMIZE** vs **VACUUM** vs **Z-ORDER** / **V-Order**
- Janelas de streaming: Tumbling / Hopping / Sliding / Session
- Dependências de pipeline: **Succeeded** vs **Completed**
- O que **não** migra em Deployment Pipelines
- SCD1 vs SCD2 vs membro inferido

## Estrutura

```text
estudo-dp-700/
├── README.md
├── LICENSE
├── revisao/
│   └── anotacoes-tematicas.md
├── prompts/
│   ├── quiz.md
│   └── podcast.md
└── recursos/          # local only (gitignore)
```

## Contribuições

Sugestões de correção, exemplos e novos tópicos do outline oficial são bem-vindos via Pull Request ou Issue.

Antes de contribuir:

- Prefira conceitos e regras de decisão (não copie enunciados de simulados pagos).
- Marque enriquecimentos baseados no outline oficial quando fizer sentido.
- Não envie PDFs ou conteúdo proprietário de terceiros.

## Aviso

- Este material é de estudo comunitário e pode conter imprecisões.
- Não redistribua conteúdo de cursos/simulados pagos sem autorização.
- A Microsoft e o Microsoft Fabric são marcas da Microsoft Corporation.

## Licença

Conteúdo original deste repositório sob [CC BY 4.0](LICENSE).
