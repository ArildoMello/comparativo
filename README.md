<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Dashboard - Guarda Municipal de Londrina</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.4/dist/chart.umd.min.js"></script>
    <link rel="icon" type="image/png" href="./favicon.png">
    <style>
        :root {
            --primary-color: #2c3e50;
            --secondary-color: #3498db;
            --accent-color: #e74c3c;
            --light-color: #ecf0f1;
            --dark-color: #34495e;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--light-color);
            color: var(--dark-color);
            line-height: 1.6;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }

        header {
            background-color: var(--primary-color);
            color: white;
            padding: 1rem;
            text-align: center;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        .container {
            max-width: 1200px;
            margin: 2rem auto;
            padding: 0 1rem;
            flex: 1;
            width: 100%;
        }

        h1, h2, h3 {
            margin-bottom: 1rem;
            color: var(--primary-color);
        }

        .dashboard-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 1rem;
            margin-bottom: 2rem;
        }

        .period-selector {
            font-weight: bold;
            white-space: nowrap;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 2rem;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
            background-color: white;
        }

        th, td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }

        th {
            background-color: var(--primary-color);
            color: white;
            position: sticky;
            top: 0;
            z-index: 1;
        }

        tr:nth-child(even) {
            background-color: #f2f2f2;
        }

        tr:hover {
            background-color: #e3e3e3;
        }

        .positive { color: #27ae60; font-weight: bold; }
        .negative { color: #e74c3c; font-weight: bold; }
        .neutral  { color: #7f8c8d; }

        .performance-indicator {
            display: inline-block;
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 0.8rem;
        }

        .improved { background-color: rgba(46, 204, 113, 0.2); color: #27ae60; }
        .worsened { background-color: rgba(231, 76, 60, 0.2); color: #e74c3c; }
        .stagnant { background-color: rgba(127, 140, 141, 0.2); color: #7f8c8d; }

        .view-chart-btn {
            padding: 6px 12px;
            background-color: var(--secondary-color);
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            transition: background-color 0.3s;
            font-size: 0.9rem;
            white-space: nowrap;
        }

        .view-chart-btn:hover {
            background-color: var(--dark-color);
        }

        /* Modal Styles */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            z-index: 1000;
            align-items: center;
            justify-content: center;
        }

        .modal-content {
            background-color: white;
            border-radius: 8px;
            padding: 2rem;
            max-width: 700px;
            width: 90%;
            max-height: 80vh;
            overflow-y: auto;
            position: relative;
            box-shadow: 0 0 20px rgba(0,0,0,0.3);
        }

        .modal-close {
            position: absolute;
            top: 1rem;
            right: 1rem;
            background-color: var(--secondary-color);
            color: white;
            border: none;
            border-radius: 4px;
            padding: 8px 16px;
            cursor: pointer;
            transition: background-color 0.3s;
        }

        .modal-close:hover {
            background-color: var(--primary-color);
        }

        .chart-wrapper {
            width: 100%;
            max-width: 600px;
            margin: 2rem auto;
            position: relative;
            height: 400px;
        }

        footer {
            background-color: var(--primary-color);
            color: white;
            text-align: center;
            padding: 1rem;
            margin-top: auto;
        }

        @media (max-width: 768px) {
            .container {
                padding: 0 0.5rem;
                margin: 1rem auto;
            }
            
            header {
                padding: 0.8rem;
            }
            
            h1 {
                font-size: 1.5rem;
            }
            
            h2 {
                font-size: 1.3rem;
            }
            
            table {
                font-size: 0.9rem;
            }
            
            th, td {
                padding: 8px 10px;
            }
            
            .chart-wrapper {
                height: 300px;
            }
            
            .period-selector {
                white-space: normal;
                font-size: 0.9rem;
            }

            .modal-content {
                width: 95%;
                padding: 1rem;
            }
        }

        @media (max-width: 480px) {
            .dashboard-header {
                flex-direction: column;
                align-items: flex-start;
            }
            
            table, thead, tbody, th, td, tr {
                display: block;
            }

            thead tr {
                display: none;
            }

            tr {
                margin-bottom: 1rem;
                border: 1px solid #ccc;
                border-radius: 6px;
                padding: 10px;
            }

            td {
                display: flex;
                justify-content: space-between;
                padding: 8px;
                border-bottom: 1px solid #eee;
            }
            
            td::before {
                content: attr(data-label);
                font-weight: bold;
                margin-right: 1rem;
                flex: 0 0 40%;
            }
            
            .chart-wrapper {
                height: 250px;
            }

            .modal-content {
                width: 95%;
                padding: 1rem;
            }
        }
    </style>
</head>
<body>
    <header>
        <h1>Guarda Municipal de Londrina</h1>
        <p>Relatório Comparativo 1º Semestre 2024 - 1º Semestre 2025</p>
    </header>

    <div class="container">
        <div id="main-dashboard">
            <div class="dashboard-header">
                <h2>Principais Categorias de Ocorrências</h2>
                <div class="period-selector">
                    Período: 1º Semestre 2024 vs 1º Semestre 2025
                </div>
            </div>
            <table>
                <thead>
                    <tr>
                        <th>Categoria</th>
                        <th>Total 2024</th>
                        <th>Total 2025</th>
                        <th>Variação</th>
                        <th>Desempenho</th>
                        <th>Ação</th>
                    </tr>
                </thead>
                <tbody id="data-table-body"></tbody>
            </table>
        </div>

        <div id="chart-modal" class="modal">
            <div class="modal-content">
                <button class="modal-close" id="modal-close">Fechar</button>
                <h2 id="chart-title">Gráfico de Pizza</h2>
                <div class="chart-wrapper">
                    <canvas id="pie-chart"></canvas>
                </div>
            </div>
        </div>
    </div>

    <footer>
        <p>Prefeitura do Município de Londrina - Estado do Paraná</p>
        <p>Secretaria Municipal de Defesa Social - Diretoria Operacional</p>
    </footer>

    <script>
        // Dados das ocorrências
        const occurrenceData = [
            { category: "Ocorrências diversas (alarme, outros tipos, etc.)", total2024: 1305, total2025: 827, variation: -36.63, performance: "Melhorou – reduziu" },
            { category: "Chamadas indevidas (engano, trote)", total2024: 45, total2025: 91, variation: 102.22, performance: "Melhorou – reduziu" },
            { category: "Crimes contra a autoridade (desastro, desobediência)", total2024: 15, total2025: 13, variation: -13.33, performance: "Melhorou – reduziu" },
            { category: "Ocupações irregulares / invasões de área pública", total2024: 140, total2025: 121, variation: -13.57, performance: "Melhorou – reduziu" },
            { category: "Animais (coitos, maus-tratos, recolhimento)", total2024: 26, total2025: 42, variation: 61.54, performance: "Melhorou – reduziu" },
            { category: "Crimes contra o patrimônio público e privado (roubo, furto, dano, etc.)", total2024: 45, total2025: 186, variation: 313.33, performance: "Melhorou – aumentou" },
            { category: "Atividades típicas de polícia (eventos, fiscalizações, etc.)", total2024: 3045, total2025: 3654, variation: 20.00, performance: "Melhorou – aumentou" },
            { category: "Apoio e escolas (travessia, atendimento, abertura)", total2024: 13, total2025: 25, variation: 92.31, performance: "Melhorou – aumentou" },
            { category: "Trânsito (acidentes, fiscalizações, pátio, etc.)", total2024: 881, total2025: 1714, variation: 94.55, performance: "Melhorou – aumentou" },
            { category: "Ocorrências ambientais (poluição, queimadas, etc.)", total2024: 149, total2025: 211, variation: 41.61, performance: "Melhorou – aumentou" },
            { category: "Estelionato e fraude (cheque sem fundo)", total2024: 2, total2025: 2, variation: 0.00, performance: "Estagnou – sem alt." },
            { category: "Crimes contra a honra (calúnia, injúria, etc.)", total2024: 1, total2025: 7, variation: 600.00, performance: "Melhorou – aumentou" },
            { category: "Perturbação do sossego / trabalho", total2024: 19, total2025: 265, variation: 1294.74, performance: "Melhorou – aumentou" },
            { category: "Drogas – tráfico e uso (ocorrências)", total2024: 99, total2025: 120, variation: 21.21, performance: "Melhorou – aumentou" },
            { category: "Drogas – quantidade apreendida (kg)", total2024: 29027, total2025: 92438, variation: 218.46, performance: "Melhorou – aumentou" },
            { category: "Drogas – pessoas detidas ou apreendidas (ocorrências ref. entorpecentes)", total2024: 40, total2025: 70, variation: 75.00, performance: "Melhorou – aumentou" },
            { category: "Violência doméstica (casos Maria da Penha)", total2024: 197, total2025: 233, variation: 18.27, performance: "Melhorou – aumentou" },
            { category: "Violência doméstica (prisões de agressores)", total2024: 57, total2025: 67, variation: 17.54, performance: "Melhorou – aumentou" },
            { category: "Cumprimento de mandados de prisão", total2024: 23, total2025: 44, variation: 91.30, performance: "Melhorou – aumentou" },
            { category: "Crimes diversos graves (homicídio, sequestro, etc.)", total2024: 84, total2025: 156, variation: 85.71, performance: "Melhorou – aumentou" },
            { category: "Crimes com armas de fogo (posse, porte e disparo)", total2024: 3, total2025: 6, variation: 100.00, performance: "Melhorou – aumentou" },
            { category: "Ocorrências de emergência (desastres naturais)", total2024: 111, total2025: 316, variation: 184.68, performance: "Melhorou – aumentou" },
            { category: "Abordagens policiais", total2024: 554, total2025: 1009, variation: 82.13, performance: "Melhorou – aumentou" },
            { category: "Crimes (roubos, furtos, receptação, dano)", total2024: 178, total2025: 258, variation: 44.94, performance: "Melhorou – aumentou" }
        ];

        // Preencher a tabela com os dados
        const tableBody = document.getElementById('data-table-body');
        
        try {
            occurrenceData.forEach(item => {
                if (!item.total2024 || !item.total2025) {
                    console.warn(`Dados faltando para ${item.category}`);
                    return;
                }
                
                const row = document.createElement('tr');
                
                const variationClass = item.variation > 0 ? 'positive' : item.variation < 0 ? 'negative' : 'neutral';
                const performanceClass = item.performance.includes('Melhorou') ? 'improved' : 
                                      item.performance.includes('Estagnou') ? 'stagnant' : 'worsened';
                
                row.innerHTML = `
                    <td data-label="Categoria">${item.category}</td>
                    <td data-label="Total 2024">${item.total2024.toLocaleString('pt-BR')}</td>
                    <td data-label="Total 2025">${item.total2025.toLocaleString('pt-BR')}</td>
                    <td data-label="Variação" class="${variationClass}">${item.variation.toFixed(2).replace('.', ',')}%</td>
                    <td data-label="Desempenho"><span class="performance-indicator ${performanceClass}">${item.performance}</span></td>
                    <td data-label="Ação"><button class="view-chart-btn" data-category="${item.category}" data-2024="${item.total2024}" data-2025="${item.total2025}">Ver Gráfico</button></td>
                `;
                
                tableBody.appendChild(row);
            });

            // Variável para armazenar o gráfico atual
            let currentChart = null;
            
            // Referências ao modal
            const modal = document.getElementById('chart-modal');
            const modalClose = document.getElementById('modal-close');
            
            // Adicionar eventos aos botões de visualização de gráfico
            document.querySelectorAll('.view-chart-btn').forEach(button => {
                button.addEventListener('click', function() {
                    const category = this.getAttribute('data-category');
                    const total2024 = parseInt(this.getAttribute('data-2024'));
                    const total2025 = parseInt(this.getAttribute('data-2025'));
                    
                    // Mostrar o modal
                    modal.style.display = 'flex';
                    
                    // Atualizar o título do gráfico
                    document.getElementById('chart-title').textContent = category;
                    
                    // Criar ou atualizar o gráfico de pizza
                    const ctx = document.getElementById('pie-chart').getContext('2d');
                    
                    // Destruir o gráfico anterior se existir
                    if (currentChart) {
                        currentChart.destroy();
                    }
                    
                    currentChart = new Chart(ctx, {
                        type: 'pie',
                        data: {
                            labels: ['2024', '2025'],
                            datasets: [{
                                data: [total2024, total2025],
                                backgroundColor: ['#e74c3c', '#3498db'],
                                borderWidth: 1
                            }]
                        },
                        options: {
                            responsive: true,
                            maintainAspectRatio: false,
                            plugins: {
                                legend: {
                                    position: 'top',
                                },
                                tooltip: {
                                    callbacks: {
                                        label: function(context) {
                                            const label = context.label || '';
                                            const value = context.raw || 0;
                                            const total = context.dataset.data.reduce((a, b) => a + b, 0);
                                            const percentage = Math.round((value / total) * 100);
                                            return `${label}: ${value.toLocaleString('pt-BR')} (${percentage}%)`;
                                        }
                                    }
                                }
                            }
                        }
                    });
                });
            });
            
            // Fechar o modal
            modalClose.addEventListener('click', function() {
                modal.style.display = 'none';
                if (currentChart) {
                    currentChart.destroy();
                    currentChart = null;
                }
            });

            // Fechar o modal clicando fora
            modal.addEventListener('click', function(e) {
                if (e.target === modal) {
                    modal.style.display = 'none';
                    if (currentChart) {
                        currentChart.destroy();
                        currentChart = null;
                    }
                }
            });
        } catch (error) {
            console.error('Erro ao carregar o dashboard:', error);
        }
    </script>
</body>
</html>
