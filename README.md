# **Explorando um Índice do Azure AI Search** ☕🔍  

## **📌 Visão Geral**  
Imagine que você trabalha para a **Fourth Coffee**, uma rede nacional de cafeterias. Sua missão é construir uma **solução de mineração de conhecimento** para facilitar a busca por insights sobre a experiência dos clientes. Para isso, você decide criar um **índice de pesquisa no Azure AI Search**, utilizando dados extraídos de avaliações de clientes.  

Neste repositório, documentamos **todo o processo** para configurar e explorar um **índice de pesquisa** no Azure AI Search.  

---

## **🚀 Etapas do Projeto**  

### **1️⃣ Criar os Recursos no Azure**  
Para construir a solução de pesquisa, são necessários os seguintes recursos:  

- **Azure AI Search**: Gerencia a indexação e consulta de dados.  
- **Azure AI Services**: Fornece habilidades de IA para enriquecer os dados com insights inteligentes.  
- **Storage Account (Blob Storage)**: Armazena documentos brutos e coleções de dados.  

> **⚠️ Importante:** O **Azure AI Search** e o **Azure AI Services** devem estar na mesma região!  

---

### **2️⃣ Criar um Azure AI Search**  
1. No **[Azure Portal](https://portal.azure.com/)**, clique em **+ Criar um recurso**.  
2. Pesquise por **Azure AI Search** e selecione.  
3. Configure com as opções:  
   - **Subscription**: Seu plano de assinatura.  
   - **Resource Group**: Um grupo de recursos novo ou existente.  
   - **Service Name**: Nome exclusivo.  
   - **Location**: Região disponível (exemplo: *East US 2*).  
   - **Pricing tier**: **Basic**.  
4. Clique em **Review + Create** e depois em **Create**.  

Após a implantação, vá até o recurso e explore as opções disponíveis.  

---

### **3️⃣ Criar um Azure AI Services**  
1. No Azure Portal, clique em **+ Criar um recurso**.  
2. Pesquise **Azure AI Services** e selecione.  
3. Configure:  
   - **Subscription**: Seu plano de assinatura.  
   - **Resource Group**: Mesmo grupo do **Azure AI Search**.  
   - **Region**: Mesma região do **Azure AI Search**.  
   - **Name**: Nome exclusivo.  
   - **Pricing tier**: **Standard S0**.  
4. Clique em **Review + Create** e depois em **Create**.  

---

### **4️⃣ Criar um Storage Account**  
1. No **Azure Portal**, clique em **+ Criar um recurso**.  
2. Pesquise **Storage Account** e selecione.  
3. Configure:  
   - **Subscription**: Seu plano de assinatura.  
   - **Resource Group**: Mesmo grupo do **Azure AI Search**.  
   - **Storage Account Name**: Nome exclusivo.  
   - **Location**: Qualquer região disponível.  
   - **Performance**: **Standard**.  
   - **Redundancy**: **Locally Redundant Storage (LRS)**.  
4. Clique em **Review + Create** e depois em **Create**.  

Após a implantação, **ative o acesso anônimo ao Blob**:  
1. No menu esquerdo do Storage Account, vá em **Configuration**.  
2. Defina **Allow Blob anonymous access** como **Enabled** e clique em **Save**.  

---

### **5️⃣ Upload dos Documentos no Blob Storage**  
1. No menu esquerdo do Storage Account, vá até **Containers**.  
2. Clique em **+ Container** e configure:  
   - **Name**: `coffee-reviews`.  
   - **Public access level**: **Container (anonymous read access)**.  
3. Clique em **Create**.  

📥 **Baixe os arquivos das avaliações:**  
[Download dos Reviews](https://aka.ms/mslearn-coffee-reviews)  

4. Extraia os arquivos e **faça o upload** para o container `coffee-reviews`.  

---

### **6️⃣ Indexação dos Documentos**  
Agora, utilizaremos o **Azure AI Search** para extrair insights dos documentos.  

1. No **Azure Portal**, acesse o **Azure AI Search**.  
2. Clique em **Import Data**.  
3. Configure:  
   - **Data Source**: **Azure Blob Storage**.  
   - **Data source name**: `coffee-customer-data`.  
   - **Data to extract**: **Content and metadata**.  
   - **Parsing mode**: **Default**.  
   - **Connection String**: Selecione seu Storage Account e container `coffee-reviews`.  
4. Clique em **Next: Add cognitive skills (Optional)**.  

✳️ **Habilitar IA para enriquecer os dados**:  
1. Escolha seu **Azure AI Services**.  
2. Configure:  
   - **Skillset name**: `coffee-skillset`.  
   - Marque **Enable OCR and merge all text into merged_content field**.  
3. No **Enriched Fields**, selecione:  
   - **Extract location names** → `locations`.  
   - **Extract key phrases** → `keyphrases`.  
   - **Detect sentiment** → `sentiment`.  
   - **Generate tags from images** → `imageTags`.  
   - **Generate captions from images** → `imageCaption`.  

📌 **Criando um Knowledge Store**:  
1. Clique em **Choose an existing connection** e selecione o Storage Account.  
2. Crie um novo **container knowledge-store**.  
3. Marque **Azure blob projections: Document**.  

🚀 **Criando o Índice**:  
1. Nomeie o índice como `coffee-index`.  
2. Configure os campos:  
   - Marque **filterable** para `content, locations, keyphrases, sentiment, merged_content, imageTags, imageCaption`.  

🛠 **Criando o Indexador**:  
1. Nomeie como `coffee-indexer`.  
2. Expanda **Advanced options** e marque **Base-64 Encode Keys**.  
3. Clique em **Submit** para rodar o indexador.  

✅ **Acompanhar a Indexação**:  
1. No **Azure AI Search**, acesse **Indexers**.  
2. Selecione `coffee-indexer` e clique em **Refresh** até o status indicar **Success**.  

---

### **7️⃣ Consultando o Índice com Search Explorer**  
Agora, podemos testar a busca!  

1. No **Azure AI Search**, clique em **Search Explorer**.  
2. No campo de consulta JSON, insira:  

```json
{
    "search": "*",
    "count": true
}

🔎 Filtrar por localização (Chicago):

{
    "search": "locations:'Chicago'",
    "count": true
}

🔎 Filtrar por sentimento negativo:

{
    "search": "sentiment:'negative'",
    "count": true
}
`````
📌 Dica: O resultado exibe a pontuação @search.score, indicando a relevância dos documentos para a pesquisa.

8️⃣ Explorando o Knowledge Store
Os dados enriquecidos foram salvos no Knowledge Store no Storage Account.

No Azure Portal, vá até o Storage Account.
No menu esquerdo, clique em Containers → knowledge-store.
Explore os arquivos JSON para ver os insights gerados!
🎯 Visualizando imagens extraídas:

Vá até coffee-skillset-image-projection.
Abra um arquivo .jpg para visualizar imagens extraídas dos documentos.
📊 Explorando Tabelas:

No menu esquerdo do Storage Account, clique em Storage Browser → Tables.
Selecione coffeeSkillsetKeyPhrases para visualizar palavras-chave extraídas.
📖 Conclusão
Essa solução permite que a Fourth Coffee analise milhares de avaliações, extraindo insights sobre locais, sentimentos e palavras-chave mais mencionadas pelos clientes.

💡 Possibilidades futuras:
✅ Integrar com um chatbot para responder perguntas sobre avaliações.
✅ Criar um dashboard no Power BI para análise de dados.
✅ Aplicar Machine Learning para prever tendências de satisfação.

🚀 Agora é sua vez! Teste e explore as possibilidades do Azure AI Search!


