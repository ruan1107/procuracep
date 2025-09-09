<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Consulta de CEP</title>
  <style>
    :root {
      --bg-color: #ffffff;
      --text-color: #000000;
      --input-bg: #f0f0f0;
      --button-bg: #007BFF;
      --button-text: #ffffff;
    }

    body.dark {
      --bg-color: #121212;
      --text-color: #ffffff;
      --input-bg: #1e1e1e;
      --button-bg: #4a90e2;
      --button-text: #ffffff;
    }

    body {
      background-color: var(--bg-color);
      color: var(--text-color);
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      display: flex;
      justify-content: center;
      align-items: flex-start;
      gap: 20px;
      padding: 20px;
      min-height: 100vh;
      margin: 0;
      flex-wrap: wrap;
    }

    .container {
      background-color: var(--input-bg);
      padding: 30px;
      border-radius: 10px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.2);
      width: 320px;
    }

    .container h2 {
      margin-bottom: 20px;
      text-align: center;
    }

    .input-group {
      margin-bottom: 15px;
    }

    .input-group label {
      display: block;
      margin-bottom: 5px;
    }

    .input-group input {
      width: 100%;
      padding: 10px;
      border: none;
      border-radius: 5px;
      background-color: var(--bg-color);
      color: var(--text-color);
      box-sizing: border-box;
    }

    .btn {
      width: 100%;
      padding: 10px;
      border: none;
      border-radius: 5px;
      background-color: var(--button-bg);
      color: var(--button-text);
      cursor: pointer;
      font-weight: bold;
      margin-top: 10px;
    }

    .toggle-mode {
      margin-top: 20px;
      text-align: center;
    }

    .toggle-mode button {
      background: none;
      border: none;
      color: var(--text-color);
      cursor: pointer;
      font-size: 14px;
      text-decoration: underline;
    }

    #resultadosCep {
      max-height: 300px;
      overflow-y: auto;
      margin-top: 10px;
      background-color: var(--bg-color);
      padding: 10px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }

    #resultadosCep ul {
      padding-left: 20px;
    }

    #resultadosCep li {
      margin-bottom: 5px;
    }
  </style>
</head>
<body>
  <!-- Consulta por CEP -->
  <div class="container">
    <h2>Consulta de CEP</h2>

    <div class="input-group">
      <label for="cep">CEP</label>
      <input
        type="text"
        id="cep"
        placeholder="00000-000"
        maxlength="9"
        oninput="mascaraCep(this)"
        onkeypress="verificaEnter(event)"
      />
    </div>

    <div class="input-group">
      <label for="rua">Rua</label>
      <input type="text" id="rua" readonly />
    </div>

    <div class="input-group">
      <label for="bairro">Bairro</label>
      <input type="text" id="bairro" readonly />
    </div>

    <div class="input-group">
      <label for="cidade">Cidade</label>
      <input type="text" id="cidade" readonly />
    </div>

    <div class="input-group">
      <label for="numero">Número</label>
      <input type="text" id="numero" placeholder="Digite o número" />
    </div>

    <button class="btn" onclick="buscarCep()">Buscar Endereço</button>

    <div class="toggle-mode">
      <button onclick="toggleMode()">Alternar Modo Claro/Escuro</button>
    </div>
  </div>

  <!-- Consulta por Cidade + UF + Logradouro -->
  <div class="container">
    <h2>Buscar CEP por Endereço</h2>

    <div class="input-group">
      <label for="uf">UF (Estado)</label>
      <input type="text" id="uf" placeholder="Ex: SP" maxlength="2" />
    </div>

    <div class="input-group">
      <label for="cidadeNome">Cidade</label>
      <input type="text" id="cidadeNome" placeholder="Ex: Campinas" />
    </div>

    <div class="input-group">
      <label for="logradouroBusca">Logradouro (Rua)</label>
      <input type="text" id="logradouroBusca" placeholder="Ex: Av. Brasil" />
    </div>

    <button class="btn" onclick="buscarCepsPorCidade()">Buscar CEPs</button>

    <div id="resultadosCep"></div>
  </div>

  <script>
    // Máscara para o campo de CEP (formato 00000-000)
    function mascaraCep(input) {
      let cep = input.value.replace(/\D/g, '');
      if (cep.length > 5) {
        cep = cep.replace(/(\d{5})(\d)/, '$1-$2');
      }
      input.value = cep;
    }

    function toggleMode() {
      document.body.classList.toggle('dark');
    }

    function verificaEnter(event) {
      if (event.key === 'Enter') {
        event.preventDefault();
        buscarCep();
      }
    }

    function buscarCep() {
      const cep = document.getElementById('cep').value.replace(/\D/g, '');

      if (cep.length !== 8) {
        alert('Digite um CEP válido com 8 dígitos!');
        return;
      }

      fetch(`https://viacep.com.br/ws/${cep}/json/`)
        .then(response => response.json())
        .then(data => {
          if (data.erro) {
            alert('CEP não encontrado.');
            return;
          }

          document.getElementById('rua').value = data.logradouro || '';
          document.getElementById('bairro').value = data.bairro || '';
          document.getElementById('cidade').value = data.localidade || '';
        })
        .catch(error => {
          console.error('Erro ao buscar o CEP:', error);
          alert('Erro ao buscar o CEP.');
        });
    }

    function buscarCepsPorCidade() {
      const uf = document.getElementById('uf').value.trim().toUpperCase();
      const cidade = document.getElementById('cidadeNome').value.trim();
      const logradouro = document.getElementById('logradouroBusca').value.trim();
      const resultadosDiv = document.getElementById('resultadosCep');

      if (!uf || !cidade || logradouro.length < 3) {
        alert('Preencha UF, cidade e logradouro (mínimo 3 letras).');
        return;
      }

      const url = `https://viacep.com.br/ws/${uf}/${encodeURIComponent(cidade)}/${encodeURIComponent(logradouro)}/json/`;

      fetch(url)
        .then(res => {
          if (!res.ok) throw new Error(`Erro HTTP: ${res.status}`);
          return res.json();
        })
        .then(data => {
          resultadosDiv.innerHTML = '';

          if (!Array.isArray(data) || data.length === 0 || data.erro) {
            resultadosDiv.innerHTML = '<p>Nenhum CEP encontrado.</p>';
            return;
          }

          const ul = document.createElement('ul');
          data.forEach(item => {
            const li = document.createElement('li');
            li.textContent = `${item.logradouro || '(sem rua)'}, Bairro: ${item.bairro}, CEP: ${item.cep}`;
            ul.appendChild(li);
          });
          resultadosDiv.appendChild(ul);
        })
        .catch(err => {
          console.error('Erro ao buscar CEPs por endereço:', err);
          resultadosDiv.innerHTML = '<p>Erro ao buscar os CEPs.</p>';
        });
    }
  </script>
</body>
</html>
