<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerenciamento de Clientes</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; max-width: 600px; }
        .form-group { margin-bottom: 12px; }
        label { display: block; margin-bottom: 4px; font-weight: bold; }
        input, select, button { padding: 8px; width: 100%; box-sizing: border-box; }
        button { margin-top: 5px; cursor: pointer; background-color: #007BFF; color: white; border: none; font-weight: bold; }
        button:hover { background-color: #0056b3; }
        .btn-secondary { background-color: #6c757d; }
        .btn-secondary:hover { background-color: #5a6268; }
        hr { margin: 20px 0; }
    </style>
</head>
<body>

    <h1>Gerenciamento de Clientes</h1>

    <!-- Seleção e Busca -->
    <div class="form-group">
        <label for="select-cliente">Selecione um cliente:</label>
        <select id="select-cliente" onchange="carregarClienteSelecionado()">
            <option value="">-- Selecione --</option>
        </select>
    </div>

    <hr>

    <!-- Formulário de Cadastro / Edição -->
    <div class="form-group">
        <input type="hidden" id="cliente-id">
        <label for="nome">Nome:</label>
        <input type="text" id="nome" placeholder="Digite o nome completo">
    </div>

    <div class="form-group">
        <label for="email">E-mail:</label>
        <input type="email" id="email" placeholder="Digite o e-mail">
    </div>

    <!-- Botões de Ação -->
    <button type="button" onclick="incluirCliente()">Incluir Cliente</button>
    <button type="button" class="btn-secondary" onclick="alterarCliente()">Alterar Cliente</button>
    <button type="button" class="btn-secondary" onclick="limparFormulario()" style="background-color: #dc3545;">Limpar / Novo</button>

    <script>
        // Chave utilizada no localStorage
        const STORAGE_KEY = 'clientes_app';

        // Carrega os clientes ao iniciar a página
        window.onload = function() {
            inicializarDadosExemplo();
            atualizarSelectClientes();
        };

        // Adiciona dados padrão caso o localStorage esteja vazio
        function inicializarDadosExemplo() {
            if (!localStorage.getItem(STORAGE_KEY)) {
                const dadosIniciais = [
                    { id: '1', nome: 'João Silva', email: 'joao@email.com' },
                    { id: '2', nome: 'Maria Souza', email: 'maria@email.com' }
                ];
                localStorage.setItem(STORAGE_KEY, JSON.stringify(dadosIniciais));
            }
        }

        // Retorna a lista de clientes do localStorage
        function obterClientes() {
            const dados = localStorage.getItem(STORAGE_KEY);
            return dados ? JSON.parse(dados) : [];
        }

        // Salva a lista de clientes no localStorage
        function salvarClientes(clientes) {
            localStorage.setItem(STORAGE_KEY, JSON.stringify(clientes));
        }

        // Atualiza as opções do elemento <select>
        function atualizarSelectClientes(idSelecionado = '') {
            const clientes = obterClientes();
            const select = document.getElementById('select-cliente');
            
            select.innerHTML = '<option value="">-- Selecione --</option>';
            
            clientes.forEach(cliente => {
                const option = document.createElement('option');
                option.value = cliente.id;
                option.textContent = cliente.nome;
                if (cliente.id === idSelecionado) {
                    option.selected = true;
                }
                select.appendChild(option);
            });
        }

        // Valida se todos os campos obrigatórios estão preenchidos
        function validarCampos(nome, email) {
            if (!nome.trim() || !email.trim()) {
                alert('Atenção: Todos os campos devem estar preenchidos antes de salvar.');
                return false;
            }
            return true;
        }

        // Carrega os dados do cliente selecionado para os inputs do formulário
        function carregarClienteSelecionado() {
            const idSelecionado = document.getElementById('select-cliente').value;
            if (!idSelecionado) {
                limparFormulario();
                return;
            }

            const clientes = obterClientes();
            const cliente = clientes.find(c => c.id === idSelecionado);

            if (cliente) {
                document.getElementById('cliente-id').value = cliente.id;
                document.getElementById('nome').value = cliente.nome;
                document.getElementById('email').value = cliente.email;
            }
        }

        // Botão "Incluir Cliente"
        function incluirCliente() {
            const nome = document.getElementById('nome').value;
            const email = document.getElementById('email').value;

            if (!validarCampos(nome, email)) return;

            const clientes = obterClientes();
            
            // Gera um ID único baseado no timestamp atual
            const novoId = Date.now().toString();

            const novoCliente = {
                id: novoId,
                nome: nome.trim(),
                email: email.trim()
            };

            clientes.push(novoCliente);
            salvarClientes(clientes);

            // Atualiza a lista e limpa o formulário
            atualizarSelectClientes(novoId);
            alert('Cliente incluído com sucesso!');
        }

        // Botão "Alterar Cliente"
        function alterarCliente() {
            const id = document.getElementById('cliente-id').value;
            const nome = document.getElementById('nome').value;
            const email = document.getElementById('email').value;

            if (!id) {
                alert('Selecione um cliente na lista para poder alterá-lo.');
                return;
            }

            if (!validarCampos(nome, email)) return;

            let clientes = obterClientes();
            const index = clientes.findIndex(c => c.id === id);

            if (index !== -1) {
                clientes[index].nome = nome.trim();
                clientes[index].email = email.trim();
                
                salvarClientes(clientes);
                atualizarSelectClientes(id);
                alert('Cliente alterado com sucesso!');
            }
        }

        // Limpa os campos do formulário
        function limparFormulario() {
            document.getElementById('cliente-id').value = '';
            document.getElementById('nome').value = '';
            document.getElementById('email').value = '';
            document.getElementById('select-cliente').value = '';
        }
    </script>
</body>
</html>
