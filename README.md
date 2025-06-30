# AI-Search
Descrição da implementação e utilização das ferramentas auxiliares (mandatórias) e da plataforma Azure AI Search

# Guia Completo de Azure AI Search

Este guia unificado mostra, de forma básica, como criar e configurar tudo que você precisa para usar o Azure AI Search com documentos `.docx` e `.pdf`.

---

## 1. Criar Azure Cognitive Search

![Passo 1: Criar Azure Cognitive Search](https://via.placeholder.com/600x300?text=Passo+1+-+Azure+Cognitive+Search)

1. Acesse o [Azure Portal](https://portal.azure.com).  
2. Clique em **Criar um recurso** (＋ no canto superior).  
3. Busque **Azure Cognitive Search** e selecione.  
4. Clique em **Criar** e informe apenas:
   - **Assinatura**: sua assinatura.  
   - **Grupo de Recursos**: novo ou existente.  
   - **Nome do Serviço**: ex. `meuSearchService`.  
   - **Região**: mais próxima de você.  
5. Clique em **Revisar + criar** e depois **Criar**.

---

## 2. Criar recurso AI Services

![Passo 2: Criar AI Services](https://via.placeholder.com/600x300?text=Passo+2+-+AI+Services)

1. No Portal, clique em **Criar um recurso**.  
2. Pesquise por **AI Services** e selecione.  
3. Clique em **Criar** e preencha:
   - **Assinatura**: mesma do Search.  
   - **Grupo de Recursos**: mesmo grupo.  
   - **Nome do Serviço**: ex. `meusAIServices`.  
   - **Região**: igual ao Search.  
4. **Revisar + criar** ► **Criar**.

---

## 3. Criar Storage Account

![Passo 3: Criar Storage Account](https://via.placeholder.com/600x300?text=Passo+3+-+Storage+Account)

1. No Portal, **Criar um recurso** ► **Storage Account**.  
2. Clique em **Criar** e defina:
   - **Assinatura**: mesma.  
   - **Grupo de Recursos**: mesmo.  
   - **Nome da Conta**: ex. `meuStorageAcc`.  
   - **Região**: igual às anteriores.  
   - **Desempenho**: Padrão.  
   - **Replicação**: LRS.  
3. **Revisar + criar** ► **Criar**.

---

## 4. Enviar documentos para a Storage Account

![Passo 4: Upload de Documentos](https://via.placeholder.com/600x300?text=Passo+4+-+Upload+de+Documentos)

### 4.1 Pelo Portal do Azure

1. Abra sua **Storage Account**.  
2. No menu lateral, vá em **Containers** ► **+ Container**:
   - Nome: `docs`  
   - Public access level: **Private**  
3. Dentro de `docs`, clique em **Upload**, selecione seus `.docx` e `.pdf`, e confirme.

---

## 5. Configurar Azure Cognitive Search

![Passo 5: Configurar Cognitive Search](https://via.placeholder.com/600x300?text=Passo+5+-+Configura%C3%A7%C3%A3o+Search)

### 5.1 Data Source

1. No recurso **Azure Cognitive Search**, abra **Data sources** ► **+ Add**.  
2. Configure:
   - **Name**: `docs-source`  
   - **Type**: **Azure Blob Storage**  
   - **Storage account connection**: selecione sua Storage Account  
   - **Container**: `docs`  
3. **Create**.

### 5.2 Skillset

1. Em **Skillsets**, clique em **+ Add**.  
2. Informe:
   - **Name**: `text-extractor`  
   - **Cognitive services**: selecione seu AI Services  
3. Em **Skills**, adicione **Document Extraction** (OCR + parser Office).  
4. **Inputs**: `metadata_storage_path`  
5. **Outputs**:
   - `content` (Edm.String)  
   - `language` (Edm.String)  
6. **Create**.

### 5.3 Index

1. Vá em **Indexes** ► **+ Add**.  
2. Preencha:
   - **Name**: `docs-index`  
   - **Fields**:
     - `id` (Edm.String) – Key, Retrievable  
     - `content` (Edm.String) – Searchable, Retrievable  
     - `metadata_storage_name` (Edm.String) – Filterable, Retrievable  
3. **Create**.

### 5.4 Indexer

1. Em **Indexers**, clique em **+ Add**.  
2. Defina:
   - **Name**: `docs-indexer`  
   - **Data source**: `docs-source`  
   - **Target index**: `docs-index`  
   - **Skillset**: `text-extractor`  
   - **Schedule**: On demand  
3. **Create** e depois **Run**.

---

## 6. Fazer buscas com IA

![Passo 6: Buscar no Search Explorer](https://via.placeholder.com/600x300?text=Passo+6+-+Search+Explorer)

### 6.1 Usando o Search Explorer

1. No **Azure Cognitive Search**, abra **Search explorer**.  
2. Selecione o index `docs-index`.  
3. Digite sua query, por exemplo:
4. Pressione **Enter** ou clique em **Search** para executar.  
5. Confira os resultados retornados:  
- **Trecho do conteúdo** (snippet) extraído pelo skill de Document Extraction  
- **Score**: relevância calculada pelo motor de busca  
- **metadata_storage_name**: nome do arquivo (ex: `relatorioAI.pdf`)  
6. (Opcional) Ative **Semantic search** no painel lateral e escolha a configuração (ex.: `default`) para obter compreensão de linguagem natural nos resultados. 
