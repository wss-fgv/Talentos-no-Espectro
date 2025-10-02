# Talentos no Espectro - Versão 4.0 (Manifesto Edition)

Esta é a versão final e mais impactante do projeto "Talentos no Espectro". Além de todos os dados e ferramentas das versões anteriores, esta edição inclui uma seção "Nossa Missão", um manifesto direcionado a empreendedores e gestores para catalisar uma mudança de mentalidade no mercado.

A arquitetura segura com Node.js/Express e a integração com a API do Gemini foram mantidas, garantindo que a aplicação é robusta, segura e pronta para o lançamento.

## Arquitetura

- **Frontend**: `public/index.html` contém toda a interface e lógica do lado do cliente. Nenhuma chave de API está exposta.
- **Backend**: `server.js` (Express) serve a página e atua como um proxy seguro para a API do Gemini, protegendo sua chave.

## 1. Como Executar o Projeto Localmente

Siga estes passos para rodar o projeto no seu próprio computador.

### Pré-requisitos
- **Node.js**: v20 ou superior. Baixe em nodejs.org.
- **Chave de API do Gemini**: Obtenha no Google AI Studio.

### Passos para a Instalação
1.  **Estrutura de Pastas**:
    - Crie uma pasta para o projeto (ex: `talentos-no-espectro`).
    - Salve `server.js`, `package.json` e os outros arquivos na raiz desta pasta.
    - Crie uma subpasta chamada `public` e salve o `index.html` dentro dela.

2.  **Arquivo de Ambiente (`.env`)**:
    - Na pasta raiz, crie um arquivo chamado `.env`.
    - Adicione sua chave de API do tipo **"API Key"** (geralmente começa com `AIzaSy...`):
      ```
      GEMINI_API_KEY=SUA_CHAVE_DE_API_AQUI
      ```
    - Substitua `SUA_CHAVE_DE_API_AQUI` pela sua chave real.

3.  **Instale as Dependências**:
    - Abra um terminal na pasta raiz e rode: `npm install`

4.  **Inicie o Servidor**:
    - No mesmo terminal, rode: `npm start`
    - A mensagem `Servidor rodando na porta 3000` deve aparecer.

5.  **Acesse a Aplicação**:
    - Abra seu navegador e acesse o endereço: http://localhost:3000.
    - **Importante:** Não abra o arquivo `index.html` diretamente. Acesse sempre pelo `localhost`.

---

## 2. Como Fazer o Deploy Seguro no Google Cloud (App Engine)

Para colocar seu projeto no ar de forma segura e escalável, siga estes passos. Usaremos o **Secret Manager** para proteger sua chave de API, evitando expô-la no código.

### Pré-requisitos
- **Conta no Google Cloud**: Com um projeto criado e faturamento ativado.
- **SDK do Google Cloud (`gcloud`)**: Instalado e inicializado.

### Passos para o Deploy
1.  **Configure seu Projeto no gcloud**:
    - Faça login na sua conta:
      ```bash
      gcloud auth login
      ```
    - Defina o projeto que você irá usar (substitua `SEU_ID_DO_PROJETO`):
      ```bash
      gcloud config set project SEU_ID_DO_PROJETO
      ```

2.  **Armazene sua Chave de API no Secret Manager**:
    - Crie um "segredo" para guardar sua chave:
      ```bash
      gcloud secrets create gemini-api-key --replication-policy="automatic"
      ```
    - Adicione sua chave de API a este segredo (substitua `SUA_CHAVE_DE_API_AQUI`):
      ```bash
      printf "SUA_CHAVE_DE_API_AQUI" | gcloud secrets versions add gemini-api-key --data-file=-
      ```

3.  **Dê Permissão ao App Engine para Acessar o Segredo**:
    - Permita que a conta de serviço do App Engine leia o segredo que você criou. Substitua `SEU_ID_DO_PROJETO` nos dois locais:
      ```bash
      gcloud secrets add-iam-policy-binding gemini-api-key \
        --member="serviceAccount:SEU_ID_DO_PROJETO@appspot.gserviceaccount.com" \
        --role="roles/secretmanager.secretAccessor"
      ```

4.  **Crie o arquivo `app.yaml`**:
    - Na raiz do seu projeto, crie o arquivo `app.yaml` com o conteúdo abaixo. Ele já está configurado para buscar a chave do Secret Manager. Lembre-se de substituir `SEU_ID_DO_PROJETO`:
      ```yaml
      runtime: nodejs20
      instance_class: F1
      env_variables:
        GEMINI_API_KEY: 'projects/SEU_ID_DO_PROJETO/secrets/gemini-api-key/versions/latest'
      ```

5.  **Faça o Deploy**:
    - Na pasta raiz do projeto, execute o comando: `gcloud app deploy`

6.  **Acesse sua Aplicação Online**:
    - O `gcloud` fornecerá a URL pública ao final do processo. Use-a para acessar seu portal.
