Crie um Audio Overview no estilo podcast de estudo para a certificação Microsoft DP-700 (Fabric Data Engineer Associate), usando APENAS as fontes fornecidas (priorize `revisao/anotacoes-tematicas.md`).

FORMATO
- Dois apresentadores: um explica com clareza; o outro faz perguntas de “pegadinha de prova” e resume a regra de ouro.
- Tom: conversa ágil, didática, sem enrolação; português do Brasil.
- Duração alvo: 20–25 minutos (versão completa). Se precisar cortar, priorize pegadinhas de prova.
- Evite ler listas longas. Transforme tudo em diálogo + analogias curtas + “se cair na prova, responda X”.

ROTEIRO OBRIGATÓRIO (nessa ordem)
2. Segurança e governança (4–5 min):
   - ReadAll vs ReadData (RLS bypass)
   - REVOKE vs DENY
   - Roles de workspace + compartilhar Lakehouse sem ser membro
   - Endossos (Certified/Promoted) e exceção do Dashboard do Power BI
   - Domínios e quem cria domínio de nível superior
3. Ciclo de vida e orquestração (4–5 min):
   - Git Azure DevOps: Org + Project + Repo + Branch
   - Deployment pipeline: overwrite, pastas, o que NÃO migra (dados/schedules)
   - Deployment rules no destino; notebooks vs variable library
   - Succeeded vs Completed; Lookup First row only; Append sem idempotência
   - Copy + Staging (quando a fonte não é ADLS/Blob)
4. Lakehouse/Warehouse/Delta (4 min):
   - OPTIMIZE vs VACUUM vs Z-ORDER vs V-Order (e irreversibilidade no Warehouse)
   - Estatísticas, CTAS, PK NONCLUSTERED NOT ENFORCED
   - SCD1/SCD2/hash/membro inferido
5. Streaming e KQL (4 min):
   - Eventstream para filtrar Event Hubs
   - Tumbling / Hopping / Sliding / Session (quando usar cada uma)
   - Ordem dos operadores KQL; has vs contains
   - OneLake availability (5 min–3h)
6. Monitoramento e capacidade (3 min):
   - Monitor Hub / logs de notebook
   - queryinsights.exec_requests_history vs dm_exec_requests
   - Suavização de CU + burst protection (não mata job em andamento)

Pode colocar mais temas condizentes com a DP-700, se achar válido
7. Fechamento (1–2 min): top 10 frases “na prova, escolha isto” + encorajamento objetivo.

REGRAS DE CONTEÚDO
- Não invente features fora das fontes.
- Em cada bloco, incluir pelo menos 1 mini-cenário oral (“Imagine que…”).
- Sempre fechar comparações com uma linha memorável (ex.: “ReadAll passa por cima do RLS; ReadData respeita”).
- Não gastar tempo em setup de conta/Azure genérico; foque em decisão de prova.

VERSÕES EXTRA (se o sistema permitir notas/descrição)
- Título do áudio: “DP-700 Rapid Fire — Pegadinhas que mais caem”
- Descrição curta com os temas cobertos.
- Ao final do roteiro escrito (se gerar texto): lista das 10 regras de ouro mencionadas.