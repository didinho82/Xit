<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerador de Keys - DIDOMODZ Admin</title>
    
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap" rel="stylesheet">
    
    <script src="https://www.gstatic.com/firebasejs/8.6.8/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.6.8/firebase-firestore.js"></script>
    <style>
        /* --- RESET/FONTES/FUNDO --- */
        body {
            font-family: 'Orbitron', sans-serif;
            display: flex;
            flex-direction: column; 
            justify-content: flex-start;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding-bottom: 50px;
            background-color: #0d061f; /* Fundo mais escuro */
            color: #d1baff;
            overflow-x: hidden;
        }
        
        /* Efeito de background animado */
        body::before {
            content: '';
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(45deg, #120a24 0%, #0d061f 50%, #120a24 100%);
            z-index: -1;
            opacity: 0.8;
            animation: gradient-shift 15s ease infinite;
        }

        @keyframes gradient-shift {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        /* --- TÍTULO PRINCIPAL --- */
        .main-title {
            color: #a42cff;
            font-size: 2.8em; /* Maior e mais impactante */
            margin-top: 40px;
            margin-bottom: 30px;
            text-shadow: 
                0 0 15px #a42cff,
                0 0 30px #ff33cc;
            letter-spacing: 3px;
        }

        /* --- CONTAINER PRINCIPAL --- */
        .container {
            background-color: #1a0f30;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 
                0 0 5px rgba(255, 255, 255, 0.1), 
                0 0 25px rgba(164, 44, 255, 0.7);
            width: 90%;
            max-width: 500px; /* Um pouco mais largo para a tabela */
            border: 2px solid #a42cff;
            text-align: center;
            margin-bottom: 30px;
            position: relative;
        }
        
        /* Efeito de canto (corner glow) */
        .container::before {
            content: '';
            position: absolute;
            top: -2px;
            left: -2px;
            right: -2px;
            bottom: -2px;
            background: linear-gradient(45deg, #ff33cc, #a42cff, #ff33cc);
            z-index: -1;
            filter: blur(8px);
            opacity: 0.3;
        }
        
        .keys-container {
            max-width: 800px; /* Mais largo para a tabela ficar melhor */
        }

        h2 {
            text-align: center;
            color: #ff33cc;
            font-size: 1.5em;
            text-shadow: 0 0 10px #ff33cc;
            border-bottom: 3px dashed #a42cff;
            padding-bottom: 10px;
            margin-top: 0;
            margin-bottom: 25px;
        }

        /* --- INPUTS GERAIS (Number e Text) --- */
        input[type="number"], .key-input {
            width: 100%;
            padding: 12px 10px;
            margin-top: 5px;
            box-sizing: border-box;
            border: 2px solid #a42cff;
            border-radius: 0;
            background-color: #2e1c50;
            color: #d1baff;
            font-size: 16px;
            box-shadow: inset 0 0 5px rgba(164, 44, 255, 0.5);
            transition: border-color 0.3s, box-shadow 0.3s;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; /* Melhor leitura */
        }
        input[type="number"]:focus, .key-input:focus {
            border-color: #ff33cc;
            outline: none;
            box-shadow: 0 0 10px #ff33cc, inset 0 0 8px #ff33cc;
        }
        
        /* Input de Key Gerada */
        .key-input {
            margin-top: 20px;
            font-weight: bold;
            text-align: center;
            cursor: pointer;
            text-transform: uppercase;
        }

        /* --- GRUPO DE OPÇÕES --- */
        .options-group {
            text-align: left;
            margin-bottom: 20px;
            padding: 15px;
            border: 1px dashed #a42cff50; /* Borda tracejada */
            border-radius: 8px;
            background-color: #1f143a; /* Fundo mais claro para destaque */
        }

        .options-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            color: #ff33cc; /* Títulos em pink */
            text-shadow: 0 0 5px #ff33cc50;
            font-size: 1.1em;
        }
        
        /* Checkbox Personalizado (igual ao menu anterior) */
        .checkbox-item {
            display: flex;
            align-items: center;
            margin-bottom: 8px;
            color: #d1baff;
        }
        
        .checkbox-item input[type="checkbox"] {
            appearance: none;
            width: 20px;
            height: 20px;
            margin-right: 10px;
            border: 2px solid #a42cff;
            border-radius: 4px;
            background-color: #2e1c50;
            cursor: pointer;
            transition: all 0.3s;
            position: relative;
        }

        .checkbox-item input[type="checkbox"]:checked {
            background-color: #ff33cc;
            border-color: #ff33cc;
            box-shadow: 0 0 5px #ff33cc;
        }

        .checkbox-item input[type="checkbox"]:checked::after {
            content: '✓';
            color: #0d061f;
            font-size: 14px;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-weight: bold;
        }

        /* --- BOTÃO PRINCIPAL --- */
        .main-button {
            width: 100%;
            padding: 15px;
            margin-top: 25px;
            background-color: #a42cff;
            color: #0d061f;
            border: 3px solid #ff33cc;
            border-radius: 4px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            text-transform: uppercase;
            letter-spacing: 2px;
            box-shadow: 0 0 15px #a42cff;
            transition: all 0.2s ease-in-out;
        }
        .main-button:hover {
            background-color: #ff33cc;
            color: white;
            border-color: #a42cff;
            box-shadow: 0 0 20px #ff33cc, 0 0 30px #a42cff;
            transform: translateY(-2px);
        }
        
        /* Botão de Limpar Keys */
        #clearKeysButton {
            background-color: #e04444; /* Vermelho mais forte */
            border-color: #ff7777;
            margin-top: 30px;
            box-shadow: 0 0 15px #e04444;
        }
        #clearKeysButton:hover {
            background-color: #ff5555;
            box-shadow: 0 0 20px #ff5555, 0 0 30px #e04444;
        }

        #expirationOutput {
            margin-top: 10px;
            font-weight: bold;
            color: #ff33cc;
            text-shadow: 0 0 3px #ff33cc50;
            font-size: 1.05em;
        }
        
        .message {
            margin-top: 15px;
            font-weight: bold;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            padding: 5px;
            border-radius: 3px;
            border: 1px solid; /* Adicionado para dar mais destaque à mensagem */
        }
        
        /* --- TABELA DE KEYS (ADMIN) --- */
        #keysTable {
            width: 100%;
            border-collapse: separate; /* Permite border-radius */
            border-spacing: 0;
            margin-bottom: 20px;
            font-size: 0.9em;
            border-radius: 8px; /* Cantos arredondados na tabela */
            overflow: hidden; /* Garante que os cantos arredondados funcionem */
        }

        #keysTable th, #keysTable td {
            padding: 12px 8px;
            border: 1px solid #a42cff20;
            text-align: center;
            word-break: break-all;
            vertical-align: middle;
        }

        #keysTable th {
            background-color: #2e1c50;
            color: #ff33cc;
            text-transform: uppercase;
            letter-spacing: 1px;
            font-size: 1em;
            border-bottom: 2px solid #a42cff;
        }
        
        #keysTable tr:nth-child(even) {
            background-color: #1f143a; /* Linhas pares */
        }
        #keysTable tr:hover {
            background-color: #2e1c50; /* Efeito hover na linha */
        }

        .status-active {
            color: #6aff6a;
            text-shadow: 0 0 5px #6aff6a;
            font-weight: 700;
        }

        .status-expired {
            color: #ff6a6a;
            text-shadow: 0 0 5px #ff6a6a;
            font-weight: 700;
        }
        
        /* Estilo dos botões de ação na tabela */
        .copy-btn, .delete-btn { 
            background: #a42cff30;
            border: 1px solid #ff33cc;
            color: #ff33cc;
            cursor: pointer;
            margin-left: 8px;
            font-size: 0.8em;
            transition: all 0.2s;
            padding: 4px 6px;
            border-radius: 4px;
            font-family: 'Orbitron', sans-serif;
        }
        .copy-btn:hover {
            color: #0d061f;
            background-color: #ff33cc;
            box-shadow: 0 0 5px #ff33cc;
        }
        .delete-btn {
            background: #ff555530;
            border-color: #ff5555;
            color: #ff5555;
        }
        .delete-btn:hover {
            color: #0d061f;
            background-color: #ff5555;
            box-shadow: 0 0 5px #ff5555;
        }

        @media (max-width: 600px) {
            #keysTable th, #keysTable td {
                padding: 8px 4px;
                font-size: 0.8em;
            }
            .keys-container {
                max-width: 95%;
            }
            .main-title {
                font-size: 2em;
            }
        }
    </style>
</head>
<body>
    <h1 class="main-title">🔑 GERADOR DE KEYS</h1>

    <div class="container">
        <h2>Painel de Geração</h2>

        <div class="options-group">
            <label for="lengthInput">Tamanho da Key:</label>
            <input type="number" id="lengthInput" value="12" min="8" max="64">
        </div>

        <div class="options-group">
            <label for="expirationDaysInput">Validade da Key (Dias):</label>
            <input type="number" id="expirationDaysInput" value="30" min="1" max="365">
        </div>

        <div class="options-group">
            <label>Configuração de Caracteres:</label>
            <div class="checkbox-item">
                <input type="checkbox" id="includeUppercase" checked>
                <label for="includeUppercase">Letras Maiúsculas (A-Z)</label>
            </div>
            <div class="checkbox-item">
                <input type="checkbox" id="includeLowercase" checked>
                <label for="includeLowercase">Letras Minúsculas (a-z)</label>
            </div>
            <div class="checkbox-item">
                <input type="checkbox" id="includeNumbers" checked>
                <label for="includeNumbers">Números (0-9)</label>
            </div>
            <div class="checkbox-item">
                <input type="checkbox" id="includeSymbols">
                <label for="includeSymbols">Símbolos (!@#$)</label>
            </div>
        </div>

        <button id="generateButton" class="main-button">Gerar & Salvar Key no Banco</button>

        <input type="text" id="keyOutput" class="key-input" readonly placeholder="Última Key Gerada (Clique para Copiar)">
        
        <p id="expirationOutput"></p>
        <p class="message" id="message"></p>
    </div>

    <div class="container keys-container">
        <h2>📝 Gerenciamento de Keys (Firebase)</h2>
        <table id="keysTable">
            <thead>
                <tr>
                    <th>Key</th>
                    <th>Expira Em</th>
                    <th>Status</th>
                    <th>Ação</th> </tr>
            </thead>
            <tbody>
                </tbody>
        </table>
        <button id="clearKeysButton" class="main-button">Limpar TODAS as Keys</button>
    </div>
    <script>
        // DOM Elements
        const lengthInput = document.getElementById('lengthInput');
        const includeUppercase = document.getElementById('includeUppercase');
        const includeLowercase = document.getElementById('includeLowercase');
        const includeNumbers = document.getElementById('includeNumbers');
        const includeSymbols = document.getElementById('includeSymbols');
        const expirationDaysInput = document.getElementById('expirationDaysInput');
        const generateButton = document.getElementById('generateButton');
        const keyOutput = document.getElementById('keyOutput');
        const message = document.getElementById('message');
        const expirationOutput = document.getElementById('expirationOutput');
        
        // Elementos da Tabela
        const keysTableBody = document.querySelector('#keysTable tbody');
        const clearKeysButton = document.getElementById('clearKeysButton');

        const uppercaseChars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
        const lowercaseChars = 'abcdefghijklmnopqrstuvwxyz';
        const numberChars = '0123456789';
        const symbolChars = '!@#$%^&*()_+[]{}|;:,.<>?';
        
        // === INÍCIO: INTEGRAÇÃO FIREBASE (MANTIDO) ===
        // Suas Credenciais do Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyBDM1q2Ieq0UaVFRF8dGrtjJ6Gr1IMit9E",
            authDomain: "keys-65d8a.firebaseapp.com",
            projectId: "keys-65d8a",
            storageBucket: "keys-65d8a.firebasestorage.app",
            messagingSenderId: "844882128108",
            appId: "1:844882128108:web:bda5e24e5ef2bb7d8809c9",
            measurementId: "G-J0GLXVP3YN"
        };
        
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(); 
        const keysCollection = db.collection("keys_admin"); // Coleção onde as keys serão salvas
        const TODAY = new Date().setHours(0, 0, 0, 0); 
        // === FIM: INTEGRAÇÃO FIREBASE ===

        // Carrega a lista de keys do Firebase
        async function loadKeys() {
            try {
                const snapshot = await keysCollection.get();
                const keysList = [];
                snapshot.forEach(doc => {
                    // Passa o ID do Firebase para usar na exclusão
                    keysList.push({ ...doc.data(), id: doc.id }); 
                });
                // Ordena pela data de criação para mostrar a mais recente por último
                keysList.sort((a, b) => (a.generatedAt?.seconds || 0) - (b.generatedAt?.seconds || 0));
                return keysList;
            } catch (error) {
                console.error("Erro ao carregar keys do Firebase:", error);
                showMessage('Erro de conexão ao carregar Keys do Firebase!', '#ff5555');
                return [];
            }
        }
        
        // Função para formatar a data
        function formatDate(dateString) {
            if (!dateString) return 'N/A';
            const date = new Date(dateString + 'T00:00:00'); 
            return date.toLocaleDateString('pt-BR');
        }
        
        // ** INÍCIO: FUNÇÕES DE CÓPIA ATUALIZADAS PARA CORRIGIR O ERRO **
        
        // Função de fallback: Seleciona e tenta usar document.execCommand('copy')
        function fallbackCopyText(text, successMsg) {
            try {
                const textarea = document.createElement('textarea');
                textarea.value = text;
                textarea.style.position = 'fixed'; // Para ser invisível mas selecionável
                textarea.style.opacity = 0;
                document.body.appendChild(textarea);
                textarea.focus();
                textarea.select(); // Seleciona o texto
                
                // Tenta copiar
                const successful = document.execCommand('copy');
                document.body.removeChild(textarea); // Remove o elemento temporário

                if (successful) {
                    showMessage(successMsg, '#6aff6a');
                } else {
                    throw new Error('execCommand falhou.');
                }
            } catch (err) {
                console.error('Erro ao copiar com fallback:', err);
                showMessage('Erro ao copiar Key. Copie manualmente.', '#ff5555'); // Cor vermelha
            }
        }
        
        // Função utilitária para copiar texto (AGORA COM FALLBACK)
        function copyText(text, successMsg) {
             // 1. Tenta usar a Clipboard API (moderna e preferencial)
             if (navigator.clipboard && window.isSecureContext) {
                 navigator.clipboard.writeText(text)
                    .then(() => {
                        showMessage(successMsg, '#6aff6a'); // Cor verde
                    })
                    .catch(err => {
                        console.error('Erro ao copiar com Clipboard API:', err);
                        // Se falhar, tenta o fallback
                        fallbackCopyText(text, successMsg);
                    });
             } else {
                 // 2. Se não suportado (ou não seguro), usa o método de fallback
                 fallbackCopyText(text, successMsg);
             }
        }
        
        // ** FIM: FUNÇÕES DE CÓPIA ATUALIZADAS **

        
        // Exclui uma key pelo ID do Firebase
        async function deleteKey(keyId) {
            if (!confirm(`Tem certeza que deseja remover esta Key do Firebase?`)) {
                return;
            }
            
            try {
                const keyObj = (await keysCollection.doc(keyId).get()).data();
                await keysCollection.doc(keyId).delete();
                showMessage(`Key ${keyObj.key.substring(0, 8)}... REMOVIDA do Firebase!`, '#ff5555');
                renderKeysTable(); // Atualiza a tabela
            } catch (error) {
                console.error('Erro ao deletar key:', error);
                showMessage('Erro ao remover Key!', '#ff5555');
            }
        }

        // Renderiza a tabela de keys (agora usa dados do Firebase)
        async function renderKeysTable() {
            const allKeys = await loadKeys();
            keysTableBody.innerHTML = ''; 
            const now = new Date();

            if (allKeys.length === 0) {
                 keysTableBody.innerHTML = '<tr><td colspan="4" style="font-style: italic; color: #a42cff;">Nenhuma key ativa ou expirada no Firebase.</td></tr>';
                 keyOutput.value = '';
                 expirationOutput.textContent = '';
                 return;
            }

            allKeys.forEach(keyObj => {
                const row = keysTableBody.insertRow();
                
                // Formata a data de validade
                const expDate = new Date(keyObj.expiration + 'T23:59:59');
                const isExpired = expDate < now;
                const statusText = isExpired ? 'EXPIRADA' : 'ATIVA';
                const statusClass = isExpired ? 'status-expired' : 'status-active';
                
                // Célula da Key com botão de cópia
                const keyCell = row.insertCell(0);
                keyCell.innerHTML = `
                    ${keyObj.key}
                    <button class="copy-btn" onclick="copyText('${keyObj.key}', 'Key copiada: ${keyObj.key.substring(0, 5)}...')">COPIAR</button>
                `;
                
                // Célula da Validade
                const expCell = row.insertCell(1);
                expCell.textContent = formatDate(keyObj.expiration);

                // Célula do Status
                const statusCell = row.insertCell(2);
                statusCell.innerHTML = `<span class="${statusClass}">${statusText}</span>`;
                
                // Célula de Ação (Excluir) - IMPORTANTE: Usa keyObj.id do Firebase
                const actionCell = row.insertCell(3);
                actionCell.innerHTML = `
                    <button class="delete-btn" onclick="deleteKey('${keyObj.id}')">❌ EXCLUIR</button>
                `;
            });
            
            // Atualiza a visualização da última key no painel principal
            if (allKeys.length > 0) {
                const lastKeyObj = allKeys[allKeys.length - 1]; // Pega a última chave gerada
                keyOutput.value = lastKeyObj.key;
                expirationOutput.textContent = `ÚLTIMA KEY: Expira em ${formatDate(lastKeyObj.expiration)}`;
            }
        }
        
        // Geração e salvamento da Key no Firebase
        async function generateKey() {
            let allowedChars = '';
            let keyLength = parseInt(lengthInput.value, 10);
            
            if (includeUppercase.checked) allowedChars += uppercaseChars;
            if (includeLowercase.checked) allowedChars += lowercaseChars;
            if (includeNumbers.checked) allowedChars += numberChars;
            if (includeSymbols.checked) allowedChars += symbolChars;

            if (allowedChars.length === 0) {
                keyOutput.value = '';
                expirationOutput.textContent = '';
                showMessage('Selecione pelo menos um tipo de caractere!', '#ff5555');
                return;
            }

            keyLength = Math.max(8, Math.min(64, keyLength)); 
            lengthInput.value = keyLength;
            
            // Geração da Key
            let generatedKey = '';
            for (let i = 0; i < keyLength; i++) {
                const randomIndex = Math.floor(Math.random() * allowedChars.length);
                generatedKey += allowedChars[randomIndex];
            }

            // CÁLCULO DA EXPIRAÇÃO
            const days = parseInt(expirationDaysInput.value, 10) || 0; 
            const expirationDate = new Date();
            expirationDate.setDate(expirationDate.getDate() + days);
            
            // Formata a data para SALVAR (formato AAAA-MM-DD)
            const year = expirationDate.getFullYear();
            const month = String(expirationDate.getMonth() + 1).padStart(2, '0');
            const day = String(expirationDate.getDate()).padStart(2, '0');
            const expirationString = `${year}-${month}-${day}`;

            // SALVANDO NO FIREBASE
            const keyObject = {
                key: generatedKey,
                expiration: expirationString,
                generatedAt: firebase.firestore.FieldValue.serverTimestamp() // Timestamp do servidor
            };

            try {
                await keysCollection.add(keyObject); // Adiciona a Key ao Firebase
                
                // Exibe o resultado no painel principal
                const formattedDate = formatDate(expirationString);
                keyOutput.value = generatedKey;
                expirationOutput.textContent = `Validade: ${formattedDate} (${days} dias). CHAVE SALVA ONLINE!`;
                showMessage('Key Gerada e Adicionada ao BANCO DE DADOS ONLINE!', '#6aff6a');
                
                renderKeysTable();
            } catch (error) {
                console.error("Erro ao salvar key no Firebase:", error);
                showMessage('ERRO: Não foi possível salvar a Key online. Verifique as credenciais.', '#ff5555');
            }
        }

        // Funçao para copiar a key do input principal (clicando no input)
        function copyOutputKey() {
            const key = keyOutput.value;
            if (key) {
                // Chama a função robusta para copiar a key do input
                copyText(key, 'Key copiada para a área de transferência!');
            } else {
                showMessage('Nenhuma key para copiar.', '#ff5555');
            }
        }
        
        // Função para limpar TODAS as keys (agora usa um batch delete no Firebase)
        async function clearAllKeys() {
            if (confirm("🚨 ATENÇÃO: Tem certeza que deseja limpar TODAS as chaves salvas? Esta ação é irreversível e será feita no BANCO DE DADOS ONLINE.")) {
                try {
                     const batch = db.batch();
                     const snapshot = await keysCollection.get();

                     snapshot.docs.forEach((doc) => {
                         batch.delete(doc.ref);
                     });

                     await batch.commit(); // Executa a exclusão em massa
                     
                     keyOutput.value = '';
                     expirationOutput.textContent = '';
                     showMessage('Todas as keys foram removidas do Firebase!', '#ff5555');
                     renderKeysTable();

                } catch (error) {
                     console.error("Erro ao limpar keys:", error);
                     showMessage('Erro ao limpar keys!', '#ff5555');
                }
            }
        }

        function showMessage(msg, color = '#6aff6a') {
            message.textContent = msg;
            message.style.color = color;
            message.style.borderColor = color;
            setTimeout(() => {
                message.textContent = '';
            }, 3000);
        }

        generateButton.addEventListener('click', generateKey);
        // Evento de clique para a cópia do input principal
        keyOutput.addEventListener('click', copyOutputKey); 
        clearKeysButton.addEventListener('click', clearAllKeys);

        // EXPOSIÇÃO GLOBAL: Estas linhas são CRUCIAIS para que os botões da tabela funcionem.
        window.copyText = copyText; 
        window.deleteKey = deleteKey; 
        window.fallbackCopyText = fallbackCopyText; // Exposto para ser acessível se necessário

        // Tenta carregar as keys salvas e renderiza a tabela ao abrir o gerador
        renderKeysTable(); 
    </script>
</body>
</html>

