# didactic-broccoli<!DOCTYPE html>
<html lang="pt-pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Investidor Pro - Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root { --primary: #00d1b2; --dark: #121212; --gray: #1e1e1e; --text: #ffffff; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--dark); color: var(--text); margin: 0; padding: 15px; }
        .container { max-width: 500px; margin: auto; }
        .card { background: var(--gray); padding: 20px; border-radius: 15px; margin-bottom: 20px; border: 1px solid #333; }
        .balance-label { font-size: 0.85rem; color: #888; text-transform: uppercase; }
        .balance-value { font-size: 2.2rem; font-weight: bold; color: var(--primary); margin: 5px 0; }
        
        .chart-container { position: relative; height: 250px; width: 100%; margin-top: 10px; }
        
        input { width: 100%; padding: 12px; margin: 10px 0; border-radius: 8px; border: none; background: #333; color: white; box-sizing: border-box; }
        .btn-deposit { width: 100%; background: var(--primary); color: var(--dark); border: none; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; transition: 0.3s; }
        .btn-deposit:hover { background: #00b89c; }

        /* Estilo do Modal */
        #pixModal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.9); justify-content: center; align-items: center; z-index: 10; }
        .modal-content { background: white; color: black; padding: 25px; border-radius: 20px; text-align: center; width: 85%; max-width: 320px; }
    </style>
</head>
<body>

<div class="container">
    <div class="card">
        <div class="balance-label">Património Estimado (12 meses)</div>
        <div class="balance-value" id="total-final">R$ 0,00</div>
        
        <div class="chart-container">
            <canvas id="meuGrafico"></canvas>
        </div>
    </div>

    <div class="card">
        <h3>Simular Novo Aporte</h3>
        <label class="balance-label">Valor Inicial / Aporte</label>
        <input type="number" id="valor-input" placeholder="Ex: 1000">
        
        <label class="balance-label">Taxa de Rendimento (% ao mês)</label>
        <input type="number" id="taxa-input" value="1.5">
        
        <button class="btn-deposit" onclick="atualizarSimulacao()">Atualizar Gráfico</button>
        <button class="btn-deposit" style="margin-top: 10px; background: #fff;" onclick="abrirPix()">Depositar Agora</button>
    </div>
</div>

<div id="pixModal">
    <div class="modal-content">
        <h3>Pagamento Via PIX</h3>
        <p style="font-size: 0.9rem;">Envie o valor e o comprovante para processarmos o seu aporte.</p>
        <div style="background: #f0f0f0; padding: 10px; font-family: monospace; border-radius: 5px; margin: 15px 0;" id="chave">sua-chave-aqui-123</div>
        <button onclick="copiarPix()" style="width: 100%; padding: 10px; border-radius: 5px; border: 1px solid #ccc; cursor: pointer;">Copiar Código</button>
        <p onclick="fecharPix()" style="color: red; margin-top: 15px; cursor: pointer; font-size: 0.8rem;">CANCELAR</p>
    </div>
</div>

<script>
    let meuGrafico;

    function atualizarSimulacao() {
        const capital = parseFloat(document.getElementById('valor-input').value) || 0;
        const taxa = parseFloat(document.getElementById('taxa-input').value) / 100;
        const meses = 12;
        
        let dadosTotal = [];
        let labels = [];
        let capitalInvestido = [];

        for (let t = 0; t <= meses; t++) {
            // M = C * (1 + i)^t
            let montante = capital * Math.pow((1 + taxa), t);
            dadosTotal.push(montante.toFixed(2));
            capitalInvestido.push(capital.toFixed(2));
            labels.push("Mês " + t);
        }

        document.getElementById('total-final').innerText = "R$ " + parseFloat(dadosTotal[meses]).toLocaleString('pt-BR');

        renderizarGrafico(labels, dadosTotal, capitalInvestido);
    }

    function renderizarGrafico(labels, dados, capital) {
        const ctx = document.getElementById('meuGrafico').getContext('2d');
        
        if (meuGrafico) { meuGrafico.destroy(); } // Remove o gráfico antigo antes de criar um novo

        meuGrafico = new Chart(ctx, {
            type: 'line',
            data: {
                labels: labels,
                datasets: [{
                    label: 'Crescimento Total',
                    data: dados,
                    borderColor: '#00d1b2',
                    backgroundColor: 'rgba(0, 209, 178, 0.2)',
                    fill: true,
                    tension: 0.4
                }, {
                    label: 'Capital Investido',
                    data: capital,
                    borderColor: '#ffffff',
                    borderDash: [5, 5],
                    fill: false
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: { legend: { display: false } },
                scales: {
                    y: { ticks: { color: '#888' }, grid: { color: '#333' } },
                    x: { ticks: { color: '#888' }, grid: { display: false } }
                }
            }
        });
    }

    // Funções do PIX
    function abrirPix() { document.getElementById('pixModal').style.display = 'flex'; }
    function fecharPix() { document.getElementById('pixModal').style.display = 'none'; }
    function copiarPix() {
        navigator.clipboard.writeText(document.getElementById('chave').innerText);
        alert("Chave copiada!");
    }

    // Iniciar com uma simulação padrão
    window.onload = () => {
        document.getElementById('valor-input').value = 5000;
        atualizarSimulacao();
    };
</script>

</body>
</html>
