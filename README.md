# AI-Search
Descrição da implementação e utilização das ferramentas auxiliares (mandatórias) e da plataforma Azure AI Search.

Neste caso, usando os conceitos de engenharia de prompt aprendidos no curso, pedi à IA que descrevesse o processo, no caso o Copilot. As imagens também foram criadas pela IA.

Naturalmente, tudo foi revisado e algumas alterações (basicamente de estilo) foram feitas. Seguindo-se os passos desse guia, será possível fazer uso da plataforma Azure AI Search de maneira bastante fácil.

Ao final, colocarei os prompts usados para atingir os resultados.

# Guia Completo de Azure AI Search

Este guia mostra, de forma básica, como criar e configurar tudo que você precisa para usar o Azure AI Search com documentos `.docx` e `.pdf`. O títulode cada seção corresponde ao link para a imagem cosrrespondente.

---

## 1. Criar Azure Cognitive Search

[Passo 1: Criar Azure Cognitive Search](https://github.com/msccobra/AI-Search/blob/Images/Criar%20AI%20Services%20no.png)

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

[Passo 2: Criar AI Services](https://github.com/msccobra/AI-Search/blob/Images/Criar%20Azure%20Cognitiv.png)

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

[Passo 3: Criar Storage Account](https://github.com/msccobra/AI-Search/blob/Images/Passo%203_%20Criar%20Stora.png)

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

[Passo 4: Upload de Documentos](https://github.com/msccobra/AI-Search/blob/Images/Passo%204_%20Upload%20de%20D.png)

### 4.1 Pelo Portal do Azure

1. Abra sua **Storage Account**.  
2. No menu lateral, vá em **Containers** ► **+ Container**:
   - Nome: `docs`  
   - Public access level: **Private**  
3. Dentro de `docs`, clique em **Upload**, selecione seus `.docx` e `.pdf`, e confirme.

---

## 5. Configurar Azure Cognitive Search

[Passo 5: Configurar Cognitive Search](https://github.com/msccobra/AI-Search/blob/Images/Passo%205_%20Configurar%20.png)

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

[Passo 6: Buscar no Search Explorer](https://github.com/msccobra/AI-Search/blob/Images/Passo%206_%20Fazer%20busca.png)

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


#Prompts

## Transcrição Rápida dos Prompts

Abaixo, a sequência dos seus comandos ao longo da conversa:

1. **Criação do guia básico**  
   “Crie um guia simples (em linguagem markdown) a respeito da utilização da ferramenta Azure AI search. O guia deve conter em sua estrutura, a criação de um recurso no azure ai search, criar um novo recurso de ai services, criar uma storage account. A descrição da criação desses recursos deve ser bem básica, sem se aprofundar nas opções avançadas, atendendo ao básico, apenas.”

2. **Inserir dados e indexar**  
   “Está muito bom. Poderia citar como inserir dados no armazenamento que criamos e daí como indexar e fazer as buscas por AI em documentos de texto, como docx e pdf?”

3. **Guia unificado para GitHub**  
   “É possível pegar toda a descrição feita, desde o primeiro prompt, e organizá-la de maneira a colocá-la no meu GitHub, em uma só parte?”

4. **Adicionar imagens (placeholders)**  
   “Seria possível, já que você também é uma plataforma da Microsoft, adicionar algumas imagens desses passos? Não mais que uma por etapa e colocar o link no texto.”

5. **Imagem do Passo 1**  
   “Vc poderia criar um imagem relativa ao passo 1?”

6. **Imagem do Passo 2**  
   “Agora do passo 2.”

7. **Imagens dos Passos 3 e 4**  
   “Dos passos 3 e 4.”

8. **Imagens dos Passos 5 e 6**  
   “Por fim os passos 5 e 6.”

9. **Transcrição em Markdown**  
   “Vc poderia fazer, em markdown, uma transcrição rápida da nossa conversa, com foco nos meus prompts?”
