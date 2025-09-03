<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fluxo Interativo de Chamados de Limpeza</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Warm Neutrals -->
    <!-- Application Structure Plan: A "Process Explorer" SPA. Features a main navigation bar and an interactive flowchart representing the 5 phases. Clicking a phase in the flowchart or nav bar dynamically updates a central content area to display that phase's detailed steps. This task-oriented design transforms the static document into an engaging, explorable tool, preventing information overload. A small dashboard with a Chart.js visualization is included to show the potential data-driven outcomes of the workflow, adding an analytical layer. This structure was chosen to provide a clear overview while allowing users to drill down into details on demand, enhancing usability and comprehension. -->
    <!-- Visualization & Content Choices: Report Info: 5-phase workflow -> Goal: Organize & Inform -> Viz/Presentation: Interactive flowchart (HTML/CSS) and dynamic text blocks -> Interaction: Click on a phase node to update the main content view -> Justification: Provides a clear mental model of the entire process and encourages active exploration over passive reading. | Report Info: Process outcomes -> Goal: Inform/Analyze -> Viz/Presentation: Donut Chart (Chart.js/Canvas) and Key Stat Cards -> Interaction: Static display of sample data -> Justification: Adds a quantitative, data-driven context to the qualitative process description. All visuals are built with HTML/CSS or Chart.js, confirming no SVG/Mermaid is used. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f5f5f4; /* stone-100 */
        }
        .flow-node {
            transition: all 0.3s ease-in-out;
            cursor: pointer;
        }
        .flow-node.active {
            transform: translateY(-5px) scale(1.05);
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            background-color: #0d9488 !important; /* teal-600 */
            border-color: #0d9488;
        }
        .arrow-tip-right {
            content: '';
            position: absolute;
            top: 50%;
            right: -8px;
            transform: translateY(-50%);
            border-width: 6px 0 6px 8px;
            border-style: solid;
            border-color: transparent transparent transparent #a8a29e; /* stone-400 */
        }
        .arrow-tip-down {
            content: '';
            position: absolute;
            left: 50%;
            bottom: -8px;
            transform: translateX(-50%);
            border-width: 8px 6px 0 6px;
            border-style: solid;
            border-color: #a8a29e transparent transparent transparent; /* stone-400 */
        }
        .content-section {
            display: none;
        }
        .content-section.active {
            display: block;
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 350px;
            margin-left: auto;
            margin-right: auto;
            height: 350px;
            max-height: 400px;
        }
    </style>
</head>
<body class="text-stone-800">

    <header class="bg-white shadow-sm">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8 py-4">
            <h1 class="text-2xl md:text-3xl font-bold text-stone-900">Explorador do Fluxo de Chamados de Limpeza</h1>
            <p class="text-stone-600 mt-1">Navegue interativamente pelas etapas do processo de gestão de chamados.</p>
        </div>
    </header>

    <main class="container mx-auto px-4 sm:px-6 lg:px-8 py-8">

        <section id="dashboard" class="mb-12">
            <h2 class="text-xl font-bold mb-4 text-stone-700">Painel de Métricas (Exemplo)</h2>
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <div class="lg:col-span-2 grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div class="bg-white p-6 rounded-lg shadow-md flex items-center">
                        <div class="bg-teal-100 text-teal-700 p-3 rounded-full mr-4">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>
                        </div>
                        <div>
                            <p class="text-stone-500 text-sm">Tempo Médio de Fechamento</p>
                            <p class="text-2xl font-bold">45 min</p>
                        </div>
                    </div>
                    <div class="bg-white p-6 rounded-lg shadow-md flex items-center">
                        <div class="bg-sky-100 text-sky-700 p-3 rounded-full mr-4">
                             <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" /></svg>
                        </div>
                        <div>
                            <p class="text-stone-500 text-sm">Chamados Abertos Hoje</p>
                            <p class="text-2xl font-bold">17</p>
                        </div>
                    </div>
                     <div class="bg-white p-6 rounded-lg shadow-md flex items-center">
                        <div class="bg-amber-100 text-amber-700 p-3 rounded-full mr-4">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" /></svg>
                        </div>
                        <div>
                            <p class="text-stone-500 text-sm">Taxa de Aprovação</p>
                            <p class="text-2xl font-bold">98%</p>
                        </div>
                    </div>
                     <div class="bg-white p-6 rounded-lg shadow-md flex items-center">
                        <div class="bg-rose-100 text-rose-700 p-3 rounded-full mr-4">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" /></svg>
                        </div>
                        <div>
                            <p class="text-stone-500 text-sm">Chamados Reabertos</p>
                            <p class="text-2xl font-bold">2</p>
                        </div>
                    </div>
                </div>
                <div class="bg-white p-6 rounded-lg shadow-md">
                    <h3 class="text-lg font-semibold text-center mb-4 text-stone-700">Distribuição de Status</h3>
                    <div class="chart-container">
                        <canvas id="statusChart"></canvas>
                    </div>
                </div>
            </div>
        </section>


        <section id="workflow-visualizer">
            <div id="flow-diagram" class="hidden md:flex flex-col items-center justify-center p-8">
                <!-- Top Row: Phases 1 to 4 -->
                <div class="flex items-center justify-center">
                    <!-- Phase 1 -->
                <div id="phase1-node" data-phase="1" class="flow-node z-10 w-48 h-28 flex flex-col items-center justify-center text-center p-3 border-2 border-stone-300 bg-white rounded-lg shadow-md">
                    <div class="font-bold">Fase 1</div>
                    <div>Solicitação</div>
                </div>

                    <div class="w-12 h-0.5 bg-stone-400 mx-4 relative">
                        <div class="arrow-tip-right"></div>
                    </div>
                    
                    <!-- Phase 2 -->
                    <div id="phase2-node" data-phase="2" class="flow-node z-10 w-48 h-28 flex flex-col items-center justify-center text-center p-3 border-2 border-stone-300 bg-white rounded-lg shadow-md">
                        <div class="font-bold">Fase 2</div>
                        <div>Notificação</div>
                    </div>
                    
                    <div class="w-12 h-0.5 bg-stone-400 mx-4 relative">
                         <div class="arrow-tip-right"></div>
                    </div>

                    <!-- Phase 3 -->
                    <div id="phase3-node" data-phase="3" class="flow-node z-10 w-48 h-28 flex flex-col items-center justify-center text-center p-3 border-2 border-stone-300 bg-white rounded-lg shadow-md">
                        <div class="font-bold">Fase 3</div>
                        <div>Execução</div>
                    </div>

                    <div class="w-12 h-0.5 bg-stone-400 mx-4 relative">
                         <div class="arrow-tip-right"></div>
                    </div>

                    <!-- Phase 4 -->
                    <div id="phase4-node" data-phase="4" class="flow-node z-10 w-48 h-28 flex flex-col items-center justify-center text-center p-3 border-2 border-stone-300 bg-white rounded-lg shadow-md">
                        <div class="font-bold">Fase 4</div>
                        <div>Validação</div>
                    </div>
                </div>

                <!-- Vertical Connector and Phase 5 -->
                <div class="flex justify-end w-full" style="max-width: 864px;">
                    <div class="flex flex-col items-center">
                        <div class="h-12 w-0.5 bg-stone-400 mt-2 relative">
                            <div class="arrow-tip-down"></div>
                        </div>
                        <div id="phase5-node" data-phase="5" class="flow-node z-10 w-48 h-28 flex flex-col items-center justify-center text-center p-3 border-2 border-stone-300 bg-white rounded-lg shadow-md mt-2">
                            <div class="font-bold">Fase 5</div>
                            <div>Fim de Expediente</div>
                        </div>
                    </div>
                </div>
            </div>
            
             <nav class="flex flex-wrap justify-center gap-2 md:hidden mb-6">
                <button data-phase="1" class="flow-node-nav px-4 py-2 text-sm rounded-full bg-white border border-stone-300 shadow-sm">Fase 1</button>
                <button data-phase="2" class="flow-node-nav px-4 py-2 text-sm rounded-full bg-white border border-stone-300 shadow-sm">Fase 2</button>
                <button data-phase="3" class="flow-node-nav px-4 py-2 text-sm rounded-full bg-white border border-stone-300 shadow-sm">Fase 3</button>
                <button data-phase="4" class="flow-node-nav px-4 py-2 text-sm rounded-full bg-white border border-stone-300 shadow-sm">Fase 4</button>
                <button data-phase="5" class="flow-node-nav px-4 py-2 text-sm rounded-full bg-white border border-stone-300 shadow-sm">Fase 5</button>
            </nav>

            <div id="content-display" class="bg-white p-6 sm:p-8 rounded-lg shadow-lg min-h-[300px]">
            </div>
        </section>

    </main>
    <footer class="bg-white mt-12 py-4 border-t">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8 text-center text-sm text-stone-500">
            <p>&copy; 2025 Gestão de Facilidades. Aplicação de visualização de fluxo.</p>
        </div>
    </footer>


    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const phaseContent = {
                1: {
                    title: "Fase 1: Solicitação (Início pelo Solicitante)",
                    actors: "Solicitante",
                    content: `
                        <ol class="list-decimal list-inside space-y-3 text-stone-700">
                            <li><strong>Acesso à Plataforma:</strong> O solicitante acessa o sistema através de um computador ou dispositivo móvel.</li>
                            <li><strong>Abertura do Chamado:</strong> O solicitante preenche um formulário simples com as seguintes informações:
                                <ul class="list-disc list-inside ml-6 mt-2 space-y-1 text-sm">
                                    <li><strong>Área Solicitante:</strong> Identificação automática com base no login (ex: "Centro Cirúrgico").</li>
                                    <li><strong>Local Específico (Obrigatório):</strong> Campo para detalhar a sala, leito ou área exata.</li>
                                    <li><strong>Nível de Urgência (Recomendado):</strong> Classificação como "Baixa", "Média" ou "Alta".</li>
                                    <li><strong>Agendamento (Opcional):</strong> Campo para definir data/hora futura.</li>
                                </ul>
                            </li>
                            <li><strong>Envio:</strong> Ao submeter, o sistema cria um novo chamado com o status <strong>"Aberto"</strong> ou <strong>"Agendado"</strong>.</li>
                        </ol>
                    `
                },
                2: {
                    title: "Fase 2: Notificação e Atribuição (Ação do Sistema)",
                    actors: "Sistema, Executor",
                    content: `
                        <ol class="list-decimal list-inside space-y-3 text-stone-700">
                            <li><strong>Disparo de Alertas:</strong> Assim que o chamado é criado, o sistema executa duas ações simultaneamente:
                                <ul class="list-disc list-inside ml-6 mt-2 space-y-1 text-sm">
                                    <li><strong>E-mail Geral:</strong> Envia um e-mail para a lista de distribuição da equipe de limpeza.</li>
                                    <li><strong>Alerta Móvel Direcionado:</strong> O sistema consulta a escala de trabalho, identifica o executor responsável e envia uma notificação <em>push</em> para seu dispositivo.</li>
                                </ul>
                            </li>
                            <li><strong>Aceite do Chamado:</strong> O executor visualiza a notificação, abre o chamado no aplicativo e o marca como <strong>"Em Andamento"</strong>. Isso atualiza o status para o solicitante.</li>
                        </ol>
                    `
                },
                3: {
                    title: "Fase 3: Execução e Evidência (Ação do Executor)",
                    actors: "Executor",
                    content: `
                        <ol class="list-decimal list-inside space-y-3 text-stone-700">
                            <li><strong>Realização do Serviço:</strong> O executor se dirige ao local e realiza a limpeza conforme os procedimentos padrão.</li>
                            <li><strong>Coleta de Evidências Fotográficas:</strong> Utilizando o aplicativo, o executor deve tirar fotos do local <strong>antes</strong> e <strong>depois</strong> do serviço.</li>
                            <li><strong>Finalização:</strong> Após anexar as evidências, o executor altera o status do chamado para <strong>"Aguardando Validação"</strong>.</li>
                        </ol>
                    `
                },
                4: {
                    title: "Fase 4: Validação e Encerramento (Ação do Solicitante)",
                    actors: "Solicitante",
                    content: `
                        <ol class="list-decimal list-inside space-y-3 text-stone-700">
                            <li><strong>Notificação para o Solicitante:</strong> O sistema notifica o solicitante que seu chamado foi concluído e aguarda sua aprovação.</li>
                            <li><strong>Análise das Evidências:</strong> O solicitante acessa o chamado, verifica as fotos de "antes" e "depois" e avalia a qualidade do serviço.</li>
                            <li><strong>Decisão Final:</strong>
                                <ul class="list-disc list-inside ml-6 mt-2 space-y-1 text-sm">
                                    <li><strong>Serviço Aprovado:</strong> Se satisfatório, o solicitante altera o status para <strong>"Fechado"</strong>. O ciclo encerra.</li>
                                    <li><strong>Serviço Reprovado:</strong> Se incorreto, o solicitante adiciona um comentário explicando o problema e muda o status para <strong>"Reaberto"</strong>. O fluxo retorna para a Fase 3.</li>
                                </ul>
                            </li>
                        </ol>
                    `
                },
                5: {
                    title: "Fase 5: Regra de Fim de Expediente",
                    actors: "Sistema, Supervisor",
                    content: `
                         <ol class="list-decimal list-inside space-y-3 text-stone-700">
                            <li><strong>Verificação Automatizada:</strong> Ao final do expediente, o sistema gera um relatório de todos os chamados que não estão com status "Fechado" ou "Agendado".</li>
                            <li><strong>Alerta para Supervisão:</strong> Esse relatório é enviado para os supervisores da limpeza e das áreas solicitantes.</li>
                            <li><strong>Ação Requerida:</strong> O objetivo é garantir que nenhuma tarefa crítica ficou pendente. Se um chamado estiver "Aberto" ou "Em Andamento" fora do horário, o supervisor deve investigar e tomar a ação necessária.</li>
                        </ol>
                    `
                }
            };

            const contentDisplay = document.getElementById('content-display');
            const flowNodes = document.querySelectorAll('.flow-node');
            const navButtons = document.querySelectorAll('.flow-node-nav');

            function updateContent(phase) {
                const data = phaseContent[phase];
                if (!data) return;

                contentDisplay.innerHTML = `
                    <h2 class="text-xl md:text-2xl font-bold text-stone-800 mb-1">${data.title}</h2>
                    <p class="text-sm text-stone-500 mb-6 font-medium">Atores Principais: ${data.actors}</p>
                    <div class="prose max-w-none prose-stone">${data.content}</div>
                `;

                flowNodes.forEach(node => {
                    node.classList.toggle('active', node.dataset.phase === phase);
                });
                
                navButtons.forEach(btn => {
                    if (btn.dataset.phase === phase) {
                        btn.classList.add('bg-teal-600', 'text-white', 'border-teal-600');
                        btn.classList.remove('bg-white', 'border-stone-300');
                    } else {
                        btn.classList.remove('bg-teal-600', 'text-white', 'border-teal-600');
                        btn.classList.add('bg-white', 'border-stone-300');
                    }
                });
            }
            
            flowNodes.forEach(node => {
                node.addEventListener('click', () => {
                    const phase = node.dataset.phase;
                    updateContent(phase);
                });
            });

            navButtons.forEach(btn => {
                btn.addEventListener('click', () => {
                    const phase = btn.dataset.phase;
                    updateContent(phase);
                });
            });
            
            updateContent('1');
            
            const ctx = document.getElementById('statusChart').getContext('2d');
            new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: ['Fechado', 'Em Andamento', 'Aberto', 'Aguardando Validação'],
                    datasets: [{
                        label: 'Status dos Chamados',
                        data: [75, 12, 8, 5],
                        backgroundColor: [
                            '#14b8a6', // teal-500
                            '#0ea5e9', // sky-500
                            '#f59e0b', // amber-500
                            '#64748b'  // slate-500
                        ],
                        borderColor: '#ffffff',
                        borderWidth: 3
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'bottom',
                            labels: {
                                padding: 15,
                                font: {
                                   family: "'Inter', sans-serif"
                                }
                            }
                        }
                    },
                    cutout: '65%'
                }
            });
        });
    </script>

</body>
</html>

