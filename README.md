# Controle Financeiro Familiar

Aplicação web (single-file, `index.html`) de controle financeiro familiar. Os dados
(`configuracoes.cfg`, `registros.log` e os backups `.bkp`) são armazenados no **OneDrive** do
usuário e acessados via **Microsoft Graph API**, com autenticação OAuth2 / Microsoft Identity
Platform (MSAL.js). Não há dependência de caminhos locais do dispositivo (`C:\`, `D:\` etc.) —
funciona igualmente no navegador do computador e do celular, sempre sobre os mesmos dados.

## Configuração (obrigatória antes do primeiro uso)

A aplicação precisa de um **App Registration** no Microsoft Entra ID (Azure AD) para poder pedir
permissão de acesso ao OneDrive do usuário. Nenhuma senha ou `client_secret` é armazenado no
código — apenas um "Client ID" público, seguro para ficar em um arquivo estático como este.

1. Acesse o [Portal do Azure](https://portal.azure.com) → **Microsoft Entra ID** → **Registros de
   aplicativo** → **Novo registro**.
   - **Tipos de conta com suporte**: "Contas em qualquer diretório organizacional e contas
     pessoais da Microsoft" (permite tanto conta OneDrive pessoal quanto corporativa/escolar).
   - **Plataforma**: "Aplicativo de página única (SPA)" — obrigatório para o fluxo OAuth2 com PKCE
     usado por este app (sem client secret).
   - **URI de redirecionamento**: a URL pública onde este `index.html` ficará hospedado (ex.:
     `https://seudominio.com/` ou a URL do GitHub Pages).
2. Em **Permissões de API**, adicione (Microsoft Graph → **Delegada**):
   - `User.Read`
   - `Files.ReadWrite`
3. Copie o **"ID do aplicativo (cliente)"** gerado.
4. Abra `index.html` e localize, perto do topo do bloco `<script>`, a constante
   `ONEDRIVE_CONFIG` — substitua o valor de `clientId` pelo ID copiado (ou defina
   `window.ONEDRIVE_CLIENT_ID` antes de carregar o `index.html`, se preferir configurar por
   ambiente/hospedagem sem editar o arquivo).

Por padrão, os arquivos ficam em `OneDrive/Controle Financeiro Familiar/` (e os backups em
`OneDrive/Controle Financeiro Familiar/Backups/`) — para mudar, edite `ONEDRIVE_CONFIG.pastaApp`
e `ONEDRIVE_CONFIG.pastaBackups` (ponto único de configuração, sem caminhos espalhados pelo
código).

## Uso

1. Hospede o `index.html` em qualquer servidor estático (ele é 100% client-side).
2. Abra a URL — a aplicação pede login com a conta Microsoft (OAuth2, via redirecionamento).
3. Após autenticar, ela verifica automaticamente se `configuracoes.cfg` e `registros.log` já
   existem na pasta configurada do OneDrive:
   - Se existirem, carrega os dados automaticamente.
   - Se não existirem, oferece criá-los (vazios) para começar um controle financeiro novo.
4. Toda alteração feita no app é gravada de volta nos mesmos arquivos no OneDrive (botão
   **Salvar**, ou automaticamente ao restaurar um backup).
5. Backups (`.bkp`, com carimbo de data/hora) são criados, listados e restaurados diretamente da
   pasta `Backups` no OneDrive — sem nenhum download/upload manual como mecanismo principal.
