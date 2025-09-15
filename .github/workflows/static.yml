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
      display: flex;
      align-items: center;
      gap: 8px;
      position: relative;
    }

    .input-group label {
      display: block;
      min-width: 60px;
    }

    .input-group input {
      flex-grow: 1;
      padding: 10px;
      border: none;
      border-radius: 5px;
      background-color: var(--bg-color);
      color: var(--text-color);
      box-sizing: border-box;
    }

    .copy-btn-inline {
      background-color: transparent;
      border: 1px solid #007BFF;
      color: #007BFF;
      padding: 5px 8px;
      border-radius: 5px;
      cursor: pointer;
      font-size: 12px;
      transition: all 0.2s ease;
      user-select: none;
      flex-shrink: 0;
    }

    .copy-btn-inline:hover {
      background-color: #007BFF;
      color: white;
    }

    .copy-msg {
      position: absolute;
      bottom: -18px;
      right: 0;
      font-size: 12px;
      color: green;
      opacity: 0;
      transition: opacity 0.3s ease;
      user-select: none;
    }

    .copy-msg.show {
      opacity: 1;
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
      padding-left: 0;
      list-style: none;
    }

    #resultadosCep li {
      margin-bottom: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
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
      <button class="copy-btn-inline" onclick="copiarTexto('rua', this)">Copiar</button>
      <span class="copy-msg">Copiado!</span>
    </div>

    <div class="input-group">
      <label for="bairro">Bairro</label>
      <input type="text" id="bairro" readonly />
      <button class="copy-btn-inline" onclick="copiarTexto('bairro', this)">Copiar</button>
      <span class="copy-msg">Copiado!</span>
    </div>

    <div class="input-group">
      <label for="cidade">Cidade</label>
      <input type="text" id="cidade" readonly />
      <button class="copy-btn-inline" onclick="copiarTexto('cidade', this)">Copiar</button>
      <span class="copy-msg">Copiado!</span>
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
      <input type="text" id="uf" placeholder="Ex: RS" maxlength="2" />
    </div>

    <div class="input-group">
      <label for="cidadeNome">Cidade</label>
      <input type="text" id="cidadeNome" placeholder="Ex: Caxias do Sul" />
    </div>

    <div class="input-group">
      <label for="logradouroBusca">Logradouro (Rua)</label>
      <input type="text" id="logradouroBusca" placeholder="Ex: Av. Júlio de Castilhos" />
    </div>

    <button class="btn" onclick="buscarCepsPorCidade()">Buscar CEPs</button>

    <div id="resultadosCep"></div>
  </div>

  <script>
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

    function copiarTexto(id, btn) {
      const texto = document.getElementById(id).value;
      if (!texto) return;
      navigator.clipboard.writeText(texto).then(() => {
        // Mostra a mensagem "Copiado!" próximo ao botão
        const copyMsg = btn.nextElementSibling;
        copyMsg.classList.add('show');
        setTimeout(() => {
          copyMsg.classList.remove('show');
        }, 1500);
      }).catch(() => {
        alert('Erro ao copiar texto');
      });
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
        .then(res => res.json())
        .then(data => {
          resultadosDiv.innerHTML = '';

          if (!Array.isArray(data) || data.length === 0 || data.erro) {
            resultadosDiv.innerHTML = '<p>Nenhum CEP encontrado.</p>';
            return;
          }

          const ul = document.createElement('ul');

          data.forEach(item => {
            const li = document.createElement('li');

            const texto = `${item.logradouro || '(sem rua)'}, Bairro: ${item.bairro}, CEP: ${item.cep}`;
            const span = document.createElement('span');
            span.textContent = texto;

            const btn = document.createElement('button');
            btn.className = 'copy-btn-inline';
            btn.textContent = 'Copiar';
            btn.onclick = () => {
              navigator.clipboard.writeText(texto).then(() => {
                btn.textContent = 'Copiado!';
                setTimeout(() => btn.textContent = 'Copiar', 1500);
              });
            };

            li.appendChild(span);
            li.appendChild(btn);
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
