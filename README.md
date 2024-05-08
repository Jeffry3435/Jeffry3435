<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Simulador de Saldo</title>
<style>
  body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .container {
    background-color: #fff;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    max-width: 400px;
    width: 100%;
    text-align: center;
  }

  h1 {
    margin-top: 0;
  }

  input[type="text"],
  input[type="number"],
  button {
    padding: 10px;
    margin: 5px;
    border: 1px solid #ccc;
    border-radius: 5px;
    width: calc(50% - 15px);
  }

  button {
    background-color: #4CAF50;
    color: white;
    cursor: pointer;
    transition: background-color 0.3s;
  }

  button:hover {
    background-color: #45a049;
  }

  #saldo {
    font-size: 24px;
    margin-bottom: 20px;
  }

  #processando,
  #comprovante,
  #contador {
    font-size: 18px;
    margin-bottom: 10px;
    display: none;
  }

  #comprovante {
    background-color: #4caf50;
    color: #fff;
    padding: 10px;
    border-radius: 5px;
  }
</style>
</head>
<body>

<div class="container">
  <h1>Simulador de Saldo</h1>
  <div id="saldo"></div>
  <div id="processando">Processando...</div>
  <div id="comprovante"></div>
  <div id="contador"></div>
  <input type="text" id="numeroInput" placeholder="Número de Telefone">
  <br>
  <input type="number" id="valorEnviarInput" placeholder="Valor a Enviar">
  <button onclick="iniciarContagem('enviar')">Enviar</button>
  <br><br>
  <input type="number" id="valorInput" placeholder="Digite o valor">
  <button onclick="iniciarContagem('receber')">Receber</button>
</div>

<script>
  let saldo = parseFloat(localStorage.getItem('saldo')) || 20;
  atualizarSaldo();

  function atualizarSaldo() {
    document.getElementById('saldo').textContent = `$${saldo}`;
    localStorage.setItem('saldo', saldo);
  }

  function mostrarProcessando() {
    document.getElementById('processando').style.display = 'block';
  }

  function esconderProcessando() {
    document.getElementById('processando').style.display = 'none';
  }

  function mostrarComprovante(comprovante) {
    const comprovanteDiv = document.getElementById('comprovante');
    comprovanteDiv.textContent = comprovante;
    comprovanteDiv.style.display = 'block';
  }

  function esconderComprovante() {
    document.getElementById('comprovante').style.display = 'none';
  }

  function mostrarContador() {
    document.getElementById('contador').style.display = 'block';
  }

  function esconderContador() {
    document.getElementById('contador').style.display = 'none';
  }

  function iniciarContagem(acao) {
    mostrarContador();
    let contadorSegundos = 3;
    let contadorInterval = setInterval(() => {
      document.getElementById('contador').textContent = `${acao.charAt(0).toUpperCase() + acao.slice(1)} em ${contadorSegundos} segundos...`;
      contadorSegundos--;
      if (contadorSegundos < 0) {
        clearInterval(contadorInterval);
        if (acao === 'enviar') {
          enviar();
        } else if (acao === 'receber') {
          receber();
        }
      }
    }, 1000);
  }

  function enviar() {
    const numero = document.getElementById('numeroInput').value;
    const valor = parseFloat(document.getElementById('valorEnviarInput').value);
    if (!isNaN(valor) && numero.trim() !== '') {
      if (valor <= saldo) {
        mostrarProcessando();
        setTimeout(() => {
          saldo -= valor;
          atualizarSaldo();
          esconderProcessando();
          const comprovante = `Você enviou $${valor} para o número ${numero}.`;
          mostrarComprovante(comprovante);
        }, 60000); // 1 minuto em milissegundos
      } else {
        alert('Saldo insuficiente.');
      }
    } else {
      alert('Por favor, insira um número de telefone e um valor válido.');
    }
    esconderContador();
  }

  function receber() {
    const valor = parseFloat(document.getElementById('valorInput').value);
    if (!isNaN(valor)) {
      mostrarProcessando();
      setTimeout(() => {
        saldo += valor;
        atualizarSaldo();
        esconderProcessando();
      }, 60000); // 1 minuto em milissegundos
    } else {
      alert('Por favor, insira um valor válido.');
    }
    esconderContador();
  }

  function copiarComprovante() {
    const comprovanteDiv = document.getElementById('comprovante');
    const comprovanteTexto = comprovanteDiv.textContent;
    navigator.clipboard.writeText(comprovanteTexto);
    alert('Comprovante copiado!');
    esconderComprovante();
  }
</script>

</body>
</html>
