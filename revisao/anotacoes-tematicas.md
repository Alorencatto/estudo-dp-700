# DP-700 — Revisão Temática

Notas de estudo organizadas por tema do exame **Microsoft Fabric Data Engineer Associate (DP-700)**, para montar revisões, podcasts, quizzes e flashcards.

**Convenção:** trechos marcados com `[ENRIQUECIMENTO]` foram adicionados com base no [outline oficial da DP-700](https://learn.microsoft.com/credentials/certifications/exams/dp-700/) e no papel de Fabric Data Engineer Associate.

\-------------------------------------------------------------------------------- 

## Como usar este arquivo

| Uso                 | Como extrair                                                       |
| ------------------- | ------------------------------------------------------------------ |
| **Quiz**            | Itens em listas + bloco “Perguntas-gatilho” no fim de cada domínio |
| **Flashcards**      | Pares “conceito → regra / quando usar”                             |
| **Revisão rápida**  | Ir direto a “Cheatsheet de decisão” e “Mapa do exame”              |
| **Estudo profundo** | Ler a seção completa + exemplos SQL/KQL/código                     |

### Mapa do exame DP-700 (pesos)

| Domínio                                   | Peso   | Seções deste arquivo |
| ----------------------------------------- | ------ | -------------------- |
| Implementar e gerenciar solução analítica | 30–35% | 1, 2, 3, 4           |
| Ingerir e transformar dados               | 30–35% | 5, 6, 7, 8, 9        |
| Monitorar e otimizar solução analítica    | 30–35% | 10, 11, 12           |

**Skills esperadas:** SQL / T-SQL, PySpark, KQL; padrões de carga; arquiteturas; orquestração.

**Linguagens/ferramentas do papel:** pipelines, notebooks, Dataflows Gen2, Lakehouse, Warehouse, SQL Database, Eventstream, Eventhouse, OneLake, Git/CI-CD, Capacity Metrics.

\-------------------------------------------------------------------------------- 

# 1\. Governança, Segurança e Workspace

## 1.1 Roles de Workspace e permissões em item

* Permissões no Fabric são **aditivas**: Workspace Roles + permissões em nível de item se acumulam.
* Se o usuário já tiver acesso a nível de objeto direto no Warehouse e precisa **visualizar os itens do Workspace**, basta associá-lo à role de **Visualizador**.
* **Compartilhamento de Lakehouse:** apenas acesso de **leitura**. Escrita exige pelo menos **Contributor**.
* Para escrever no Lakehouse, é necessário **Contributor ou superior**.
* Compartilhar um item diretamente permite que um usuário acesse apenas um Lakehouse **sem torná-lo membro do Workspace**: conceder permissões de leitura do **SQL Analytics Endpoint** \+ **ReadAll** para Apache Spark.
* **Não** se deve deixar dados com **PII** sob o mesmo limite de permissão — separar fisicamente essas informações.
* É possível **desabilitar** o acesso de usuários via **OneLake File Explorer** nas configurações do workspace.
* `[ENRIQUECIMENTO]` Roles típicas de workspace (do menos ao mais privilegiado): **Viewer** → **Contributor** → **Member** → **Admin**. Viewer: leitura; Contributor: criar/editar itens; Member: gerenciar membros (exceto Admin); Admin: configurações do workspace.

### ReadAll vs ReadData (crítico para prova)

| Permissão    | Caminho                        | RLS / CLS / OLS                            |
| ------------ | ------------------------------ | ------------------------------------------ |
| **ReadAll**  | OneLake via ADLS API (e Spark) | **Bypassa** RLS, CLS e OLS do SQL Endpoint |
| **ReadData** | SQL Analytics Endpoint         | **Respeita** políticas RLS                 |

* Dentro da segurança do OneLake, a função **DefaultReader** de um Lakehouse inclui automaticamente os usuários que têm permissão **ReadAll**.

## 1.2 RLS, CLS, OLS, DDM e DENY/REVOKE

### Row-Level Security (Warehouse)

Para aplicar RLS no Warehouse, use função com **FILTER PREDICATE** \+ Security Policy:

```
-- Predicado: permite a linha quando o vendedor é o usuário atual
CREATE FUNCTION Security.fn_filtro_vendas
(
    @Vendedor VARCHAR(100)
)
RETURNS TABLE
WITH SCHEMABINDING
AS
    RETURN
        SELECT 1 AS permitido
        WHERE @Vendedor = USER_NAME();
GO

CREATE SECURITY POLICY Security.PoliticaVendas
ADD FILTER PREDICATE Security.fn_filtro_vendas(Vendedor)
ON dbo.Vendas
WITH (STATE = ON);
GO

```

### Dynamic Data Masking (DDM)

* Mascara **apenas o resultado exibido** pelo `SELECT`.
* A cláusula **WHERE é avaliada com o dado real**.
* Exemplo: `partial(1, "****", 2)` → `1234567` vira `1****07`.
* Role **db\_reader:** não dá bypass na role de unmask.
* Role **db\_owner:** sempre dá bypass nas permissões de segurança.

### REVOKE vs DENY

* **REVOKE:** remove permissões concedidas ou negadas explicitamente antes.
* **DENY:** tem **precedência** sobre as outras permissões. Um usuário pode ter `ReadData` (ver tudo) e um **DENY** em tabela/schema específico bloqueia aquele objeto.

### `[ENRIQUECIMENTO]` CLS / OLS / File-level

* **CLS (Column-Level Security):** restringe colunas sensíveis (ex.: CPF, salário) mesmo com acesso à tabela.
* **OLS (Object-Level Security):** restringe objetos (tabelas, views, schemas) — combina com DENY em schemas.
* **File-level / folder-level (OneLake):** controle em pastas/arquivos do Lakehouse; complementar a ReadAll. Princípio: menor privilégio + separação física de PII.

## 1.3 Sensitivity Labels, Purview e Herança

* **Herança de Sensitivity Labels:** herda automaticamente de itens associados (arquivos/tabelas) **apenas no momento da criação**, não em atualizações posteriores.
* **Herança downstream:** recurso habilitado pelo **admin do Fabric**; propaga o rótulo de confidencialidade entre os itens.
* **Purview Information Protection:** políticas de rotulagem obrigatória; **default label policy** — todos os itens herdam um rótulo padrão definido.

## 1.4 Endossos (badges)

Itens do Fabric e do Power BI (exceto dashboards do PBI) podem ser **recomendados (Promoted)** ou **certificados (Certified)**.

| Badge           | Significado                                                                                       |
| --------------- | ------------------------------------------------------------------------------------------------- |
| **Certified**   | Altamente confiável, governado, validação formal; demonstra conformidade com padrões de qualidade |
| **Promoted**    | Recomendado para uso mais amplo / reutilização                                                    |
| **Master Data** | Dados fundacionais da organização (datasets validados)                                            |

* No Fabric, **apenas o Dashboard do Power BI não entra** no fluxo de aprovação e certificação.

## 1.5 Domínios (OneLake Domains)

* Unidades organizacionais para agrupar workspaces relacionados sob uma área de negócio, com administradores de domínio próprios.
* Atribuição de workspace a domínio/subdomínio: configurações do workspace.
* **Apenas Fabric Admin** pode criar novo domínio de **nível superior**; com esse papel também cria subdomínios e atribui áreas de trabalho.
* **Domínio padrão:** atribui automaticamente a um domínio os workspaces de usuários e grupos de segurança especificados. Configurável por admin do Fabric ou admin do workspace. Ex.: usuário do grupo/domínio `TiDigital` cria workspace `Fatura Segura` → atribuído automaticamente ao domínio.

## 1.6 Capacidade e associação de Workspace

* Para atribuir um workspace à capacidade Fabric: permissão **Contributor da capacidade** \+ função de **Admin do workspace**.
* Esses papéis **não** alteram nem excluem a capacidade — apenas associam ao workspace. A role de capacidade é nomeada pelo administrador da capacidade.
* `[ENRIQUECIMENTO]` Capacidades relevantes: **F SKUs** (Fabric), trial, etc. Capacidade compartilha CUs entre itens do workspace.

## 1.7 Spark Environment e configurações de workspace

* **Environment:** item que gerencia runtime Spark, configurações de computação e bibliotecas.
* Ambiente padrão: aplica-se a notebooks e definições de trabalho Spark existentes/novos que usam **“Workspace default”**.
* Bibliotecas: **salvar não basta** — precisa **publicar**.
* **Full Mode** para jobs de trabalho; **Quick Mode** funciona apenas para notebooks.
* Opção **“Customize compute configurations for items”** (config de workspace): só **admin do Fabric**. Com habilitada, **Member** e **Contributor** podem alterar no ambiente as configs Spark a nível de sessão.
* Pode habilitar configs personalizadas que sobrescrevem o ambiente padrão. Ex.: `%%configure` / `spark.native.enable True`.
* Nas configs de job Spark, para reservar o máximo de núcleos: **[reserve maximum cores for active spark jobs]**.
* **On-demand Billing for Fabric Spark** (feature do Fabric).
* `spark.fabric.resourceProfile` / `readHeavyForPBI` → habilita **V-Order** e gravação otimizada; ajuda leituras do Power BI.
* **Apache Airflow:** para escalabilidade horizontal → **Apache Airflow Runtime settings**.
* `[ENRIQUECIMENTO]` Workspace settings cobertos no exame: Spark, Domains, OneLake, Data workflow (Airflow). Revisar cada painel de configuração do workspace.

### Perguntas-gatilho §1

1. ReadAll bypassa RLS? E ReadData?
2. Quem cria domínio de nível superior?
3. Dashboard do PBI pode ser Certified no Fabric?
4. Qual diferença entre REVOKE e DENY?
5. Ambiente Spark: salvar vs publicar; Quick vs Full Mode?

\-------------------------------------------------------------------------------- 

# 2\. Ciclo de Vida: Git, CI/CD e Deployment Pipelines

## 2.1 Integração Git (Azure DevOps / GitHub)

**Itens mínimos Azure DevOps:** Organização, Projeto, Repositório e Branch. Conexão via **OAuth2**.

**GitHub — menor privilégio:** criar usuário no GitHub com permissões de **Leitura e Escrita de Conteúdos** no repositório destino.

**Incompatíveis com repositório Git:**

* Dashboards
* Dataflows Gen1

**Desenvolvimento baseado em tronco (trunk-based) com Git:**

* Credenciais de workspace são **substituídas antes da implantação**.
* Adequado usar APIs de itens / **fabric-cicd**.
* Ambientes de **test e prod não estão conectados ao Git**.

## 2.2 Deployment Pipelines

* O pipeline de implantação **aplica a hierarquia de pastas**, preservando criação de pastas e movimentação de itens entre pastas.
* Sempre dá **overwrite** nos itens pareados.
* **Dados** e **schedules de atualização** de modelos semânticos **não são replicados**.
* **Regras de deployment (Deployment Rules):** sempre configuradas no **destino**; englobam Dataflows Gen2; devem ser **salvas explicitamente** antes do deploy.
* Itens compatíveis com regra padrão: **notebooks**.
* Itens **não** compatíveis com regras (ex.: pipelines): gerenciar valores por ambiente com **biblioteca de variáveis** e parametrizar conexões referenciando o **ID**.
* Para atribuir um workspace a um estágio de implantação, este **não pode estar associado a outro pipeline**.
* **Backward deployment** (estágio anterior): possível quando se implanta **todos os itens** em um estágio **vazio**, sem workspace atribuído. Pode criar PRODUCTION e depois replicar para development e test.

## 2.3 Database Projects

* Fabric Warehouse pode ser extraído como **projeto de banco de dados** para validações antes da implantação.
* Versionamento de projetos SQL/tabelas: Database Projects + Git.
* Scripts de migração schema-to-schema: revisar com cuidado — adicionar colunas `NOT NULL` sem `DEFAULT` gera erros; exclusões definitivas de colunas com dados também.
* Na publicação em produção: habilitar **BlockOnPossibleDataLoss** e **DropObjectsNotInSource**.

### Perguntas-gatilho §2

1. Quais 4 infos são obrigatórias no Azure DevOps?
2. O que não migra no deployment pipeline?
3. Onde se configuram Deployment Rules?
4. Backward deployment: qual pré-requisito?

\-------------------------------------------------------------------------------- 

# 3\. Orquestração: Pipelines, Notebooks, Dataflows e Triggers

## 3.1 Escolher ferramenta de orquestração / transformação

`[ENRIQUECIMENTO]` Heurística DP-700:

| Cenário                                                | Preferir                     |
| ------------------------------------------------------ | ---------------------------- |
| Cópia massiva de arquivos / APIs / staging             | **Pipeline + Copy Activity** |
| Transformação low-code, self-service, incremental Gen2 | **Dataflow Gen2**            |
| Lógica complexa PySpark, ML, DAG de notebooks          | **Notebook**                 |
| SQL set-based no Warehouse / CTAS                      | **T-SQL**                    |
| Tempo real / filtros em Event Hubs                     | **Eventstream**              |
| Agregações analíticas em telemetria                    | **KQL / Eventhouse**         |

## 3.2 Dependências e status de atividades

* Se B depende de A com **Succeeded** e A falha → B fica **Skipped**.
* Se C depende de B com **Completed** → Completed engloba Succeeded, Failed ou Skipped → C executa mesmo se B foi pulada.
* If Condition: se avalia **false** e a branch false está vazia → comportamento de **skipped**.
* Corpo da resposta HTTP (Web Activity): `@activity("WebCall").output.body.