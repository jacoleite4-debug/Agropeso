# Agropes<!DOCTYPE html>
<html lang="pt-PT">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agro Peso - Gestão Pecuária</title>
    <!-- Firebase SDKs -->
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
    <!-- Biblioteca PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <style>
        :root {
            --primary: #1B5E20;
            --primary-dark: #0A1A0C;
            --accent: #2E7D32;
            --earth: #4E342E;
            --bg: #F4F1EA;
            --white: #FFFFFF;
            --text: #212121;
            --gray: #757575;
            --danger: #C62828;
            --warning: #EF6C00;
            --card-shadow: 0 4px 12px rgba(0,0,0,0.08);
        }
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Arial, sans-serif; }
        body { background-color: var(--bg); color: var(--text); padding-bottom: 90px; }
        
        header { 
            background: linear-gradient(135deg, var(--primary-dark), var(--primary)); 
            color: var(--white); 
            padding: 16px; 
            text-align: center; 
            border-bottom: 4px solid var(--earth);
            box-shadow: 0 2px 8px rgba(0,0,0,0.2);
        }
        header h1 { font-size: 22px; letter-spacing: 1.5px; font-weight: 800; }
        header p { font-size: 11px; opacity: 0.85; margin-top: 2px; text-transform: uppercase; }

        .nav-tabs { display: flex; background: var(--primary-dark); overflow-x: auto; position: sticky; top: 0; z-index: 100; }
        .nav-tab { flex: 1; padding: 12px 10px; color: white; text-align: center; font-size: 11px; font-weight: 700; text-transform: uppercase; border: none; background: none; cursor: pointer; white-space: nowrap; }
        .nav-tab.active { background: var(--accent); border-bottom: 3px solid #FFF; }

        .container { padding: 12px; max-width: 650px; margin: 0 auto; }
        
        .card { 
            background: var(--white); 
            border-radius: 12px; 
            padding: 16px; 
            margin-bottom: 12px; 
            border: 1px solid #D7CCC8; 
            box-shadow: var(--card-shadow); 
        }
        
        .dashboard { 
            background: linear-gradient(145deg, var(--primary), var(--accent)); 
            color: var(--white); 
            border: none;
        }
        .dashboard h2 { font-size: 15px; margin-bottom: 8px; text-transform: uppercase; }
        
        .stats-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 8px; margin-top: 10px; }
        .stat-box { background: rgba(0,0,0,0.2); padding: 10px; border-radius: 8px; text-align: center; }
        .stat-box span { font-size: 10px; display: block; opacity: 0.9; text-transform: uppercase; font-weight: 600; }
        .stat-box strong { font-size: 18px; font-weight: 800; }

        .btn { 
            background-color: var(--accent); 
            color: var(--white); 
            border: none; 
            padding: 12px; 
            border-radius: 8px; 
            font-size: 13px; 
            font-weight: 700; 
            cursor: pointer; 
            width: 100%; 
            text-align: center; 
            margin-top: 8px; 
            text-transform: uppercase; 
            transition: all 0.2s ease;
        }
        .btn:active { transform: scale(0.98); }
        .btn-danger { background-color: var(--danger); }
        .btn-whatsapp { background-color: #2E7D32; }
        .btn-outline { background: transparent; border: 2px solid var(--primary); color: var(--primary); }

        .btn-chip {
            background: #E0E0E0;
            color: #333;
            border: none;
            padding: 6px 10px;
            border-radius: 16px;
            font-size: 10px;
            font-weight: 700;
            cursor: pointer;
            white-space: nowrap;
        }
        .btn-chip.active {
            background: var(--primary);
            color: white;
        }

        .btn-weight-add {
            background: #FFF3E0;
            color: #E65100;
            border: 1px solid #FFE0B2;
            padding: 8px;
            border-radius: 6px;
            font-weight: 800;
            font-size: 12px;
            cursor: pointer;
            flex: 1;
            text-align: center;
        }

        .row { display: flex; gap: 8px; }
        .filter-bar { display: flex; gap: 6px; overflow-x: auto; padding-bottom: 8px; margin-bottom: 8px; }
        
        input, select, textarea { 
            width: 100%; 
            padding: 10px; 
            margin-top: 4px; 
            margin-bottom: 10px; 
            border: 2px solid #D7CCC8; 
            border-radius: 8px; 
            font-size: 13px; 
            font-weight: 600; 
            background: #FAFAFA; 
        }
        
        label { font-size: 11px; font-weight: 700; color: var(--primary-dark); text-transform: uppercase; display: block; }

        .animal-item { 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            padding: 12px; 
            margin-bottom: 8px; 
            background: #FAFAFA;
            border-radius: 8px; 
            border-left: 6px solid var(--gray); 
            border-top: 1px solid #EEE; border-right: 1px solid #EEE; border-bottom: 1px solid #EEE;
        }

        .animal-item.pronto-abate {
            background: #FFFDE7 !important;
            border-left: 8px solid #2E7D32 !important;
            border-top: 2px solid #FBC02D !important;
            border-right: 2px solid #FBC02D !important;
            border-bottom: 2px solid #FBC02D !important;
            box-shadow: 0 4px 10px rgba(46, 125, 50, 0.2);
        }

        .badge { background: var(--earth); color: var(--white); padding: 3px 6px; border-radius: 4px; font-size: 9px; font-weight: 700; text-transform: uppercase; }
        .badge-abate { background: #C62828 !important; color: #FFF !important; animation: pulse 1.5s infinite; }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.6; }
            100% { opacity: 1; }
        }

        .hidden { display: none !important; }
        .user-info { display: flex; justify-content: space-between; align-items: center; background: var(--earth); color: white; padding: 8px 12px; font-size: 11px; font-weight: 600; }
        canvas { width: 100% !important; height: 160px !important; margin-top: 10px; background: rgba(0,0,0,0.2); border-radius: 8px; }
    </style>
</head>
<body>

    <div id="bar-user" class="user-info hidden">
        <div>FAZENDA: <strong id="lbl-nome-fazenda">---</strong> <span class="badge" style="background:#2E7D32; margin-left: 4px;">SINCRONIZADO</span></div>
        <button onclick="fazerLogout()" style="background:none; border:none; color:#FFCDD2; font-weight:bold; cursor:pointer; font-size:11px;">SAIR</button>
    </div>

    <header>
        <h1>AGRO PESO</h1>
        <p>Gestão de Pesagem e Rebanho</p>
    </header>

    <div id="app-nav" class="nav-tabs hidden">
        <button class="nav-tab active" onclick="mudarAba('rebanho', this)">Rebanho</button>
        <button class="nav-tab" onclick="mudarAba('curral', this)">Modo Curral 🎙️</button>
        <button class="nav-tab" onclick="mudarAba('pastos', this)">Pastos</button>
        <button class="nav-tab" onclick="mudarAba('config', this)">Configurações ⚙️</button>
    </div>

    <div class="container">

        <!-- LOGIN -->
        <div id="view-login">
            <div class="card" style="margin-top: 30px;">
                <h2 style="color: var(--primary-dark); text-align: center; margin-bottom: 12px; font-size: 16px;">ACEDER À FAZENDA</h2>
                <p style="font-size: 11px; color: var(--gray); text-align: center; margin-bottom: 12px;">Todos os dados ficam guardados automaticamente no dispositivo.</p>
                <label>Nome da Fazenda:</label>
                <input type="text" id="login-fazenda" placeholder="Ex: Fazenda Santa Maria">
                <label>Palavra-Passe:</label>
                <input type="password" id="login-senha" placeholder="Senha secreta">
                <button class="btn" onclick="entrarFazenda()">ENTRAR / CONECTAR</button>
            </div>
        </div>

        <!-- ABA 1: REBANHO -->
        <div id="tab-rebanho" class="tab-content hidden">
            <div class="card dashboard">
                <h2>Resumo do Rebanho</h2>
                <div class="stats-grid">
                    <div class="stat-box"><span>Total Cabeças</span><strong id="total-animais">0</strong></div>
                    <div class="stat-box"><span>Peso Médio</span><strong id="media-peso">0.0 kg</strong></div>
                    <div class="stat-box"><span>Prontos p/ Abate</span><strong id="total-prontos-abate" style="color: #FFEB3B;">0</strong></div>
                    <div class="stat-box"><span>Meta de Abate</span><strong id="lbl-meta-resumo">540 kg</strong></div>
                </div>
            </div>

            <div class="card">
                <button class="btn" onclick="abrirModalCadastro()">+ REGISTAR NOVA PESAGEM</button>
                <div class="row" style="margin-top: 6px;">
                    <button class="btn btn-outline" onclick="gerarPDFRelatorio()">📄 BAIXAR PDF</button>
                    <button class="btn btn-outline" onclick="exportarCSV()">📊 BAIXAR EXCEL</button>
                </div>
                <button class="btn btn-whatsapp" style="margin-top: 6px;" onclick="enviarRelatorioWhatsApp()">📲 ENVIAR NO WHATSAPP</button>
            </div>

            <div class="card" id="area-relatorio-pdf">
                <h3 style="color: var(--primary-dark); margin-bottom: 8px; font-size: 13px;">LISTAGEM DE ANIMAIS</h3>
                
                <!-- FILTROS RÁPIDOS -->
                <div class="filter-bar">
                    <button class="btn-chip active" onclick="filtrarStatus('TODOS', this)">TODOS</button>
                    <button class="btn-chip" onclick="filtrarStatus('ABATE', this)">🎯 PRONTOS ABATE</button>
                    <button class="btn-chip" onclick="filtrarStatus('Pastagem', this)">PASTAGEM</button>
                    <button class="btn-chip" onclick="filtrarStatus('Semi-confinamento', this)">SEMI-CONF.</button>
                    <button class="btn-chip" onclick="filtrarStatus('Confinamento', this)">CONFINAMENTO</button>
                </div>

                <input type="text" id="search-brinco" placeholder="🔍 Pesquisar brinco..." oninput="filtrarAnimais()">
                <div id="lista-animais"></div>
            </div>
        </div>

        <!-- ABA 2: MODO CURRAL (PESAGEM RÁPIDA COM VOZ E ATALHOS) -->
        <div id="tab-curral" class="tab-content hidden">
            <div class="card" style="background:#FFFDE7; border-color:#FBC02D;">
                <h3 style="color: #F57F17; margin-bottom: 8px;">⚡ MODO CURRAL RÁPIDO</h3>
                <label>Lote Atual:</label>
                <input type="text" id="curral-lote" placeholder="Ex: Curral 01">
                
                <label>Sistema de Criação:</label>
                <select id="curral-sistema">
                    <option value="Pastagem">Pastagem</option>
                    <option value="Semi-confinamento">Semi-confinamento</option>
                    <option value="Confinamento">Confinamento</option>
                </select>

                <label>Brinco do Animal:</label>
                <input type="text" id="curral-brinco" placeholder="Digite o brinco">
                
                <label>Peso (Kg):</label>
                <input type="number" step="0.1" id="curral-peso" placeholder="Digite o peso">
                
                <!-- ATALHOS RÁPIDOS DE PESO -->
                <div class="row" style="margin-bottom: 10px;">
                    <button class="btn-weight-add" onclick="ajustarPesoCurral(-1)">-1 kg</button>
                    <button class="btn-weight-add" onclick="ajustarPesoCurral(1)">+1 kg</button>
                    <button class="btn-weight-add" onclick="ajustarPesoCurral(5)">+5 kg</button>
                    <button class="btn-weight-add" onclick="ajustarPesoCurral(10)">+10 kg</button>
                </div>

                <button class="btn" style="background:#F57F17;" onclick="salvarPesagemCurralComVoz()">SALVAR E PRÓXIMO ANIMAL 🔊</button>
            </div>
        </div>

        <!-- ABA 3: MANEJO DE PASTOS -->
        <div id="tab-pastos" class="tab-content hidden">
            <div class="card">
                <h3 style="color: var(--primary-dark); margin-bottom: 10px;">TRANSFERÊNCIA DE LOTE DE PASTO</h3>
                <label>Lote Origem:</label>
                <input type="text" id="pasto-origem" placeholder="Ex: Pasto 01">
                <label>Novo Pasto Destino:</label>
                <input type="text" id="pasto-destino" placeholder="Ex: Piquete 04 (Descansado)">
                <button class="btn" onclick="transferirLotePasto()">MOVER LOTE INTEIRO</button>
            </div>
        </div>

        <!-- ABA 4: CONFIGURAÇÕES DA META -->
        <div id="tab-config" class="tab-content hidden">
            <div class="card">
                <h3 style="color: var(--primary-dark); margin-bottom: 10px;">META DE ABATE PERSONALIZADA</h3>
                <label>Peso Meta para Abate (Kg):</label>
                <input type="number" id="input-meta-abate" placeholder="Ex: 540 (Para 18 @)">
                <button class="btn" onclick="salvarConfiguracoes()">GUARDAR META</button>
            </div>
        </div>

        <!-- FORMULÁRIO PESAGEM -->
        <div id="view-form" class="hidden">
            <button class="btn btn-outline" style="width: auto; padding: 6px 12px; margin-bottom: 10px;" onclick="voltarDashboard()">← CANCELAR</button>
            <div class="card">
                <h3 style="color: var(--primary-dark); margin-bottom: 10px;">REGISTAR PESAGEM</h3>
                <label>Brinco:</label><input type="text" id="input-brinco">
                <label>Peso (kg):</label><input type="number" step="0.1" id="input-peso">
                <label>Data:</label><input type="date" id="input-data">
                
                <label>Sistema de Criação:</label>
                <select id="input-sistema">
                    <option value="Pastagem">Pastagem</option>
                    <option value="Semi-confinamento">Semi-confinamento</option>
                    <option value="Confinamento">Confinamento</option>
                </select>

                <label>Categoria:</label>
                <select id="input-categoria">
                    <option value="Boi Gordo">Boi Gordo</option>
                    <option value="Garrote">Garrote</option>
                    <option value="Bezerro">Bezerro</option>
                    <option value="Novilha">Novilha</option>
                    <option value="Vaca">Vaca</option>
                </select>
                <label>Lote / Pasto:</label><input type="text" id="input-lote">
                <button class="btn" onclick="salvarPesagem()">GUARDAR E SINCRONIZAR</button>
            </div>
        </div>

        <!-- DETALHES -->
        <div id="view-detail" class="hidden">
            <button class="btn btn-outline" style="width: auto; padding: 6px 12px; margin-bottom: 10px;" onclick="voltarDashboard()">← VOLTAR</button>
            <div class="card dashboard">
                <h2 id="detalhe-titulo">BRINCO: ---</h2>
                <p id="detalhe-ultima-data" style="font-size:12px; opacity:0.9;">Última Pesagem: ---</p>
                <p id="detalhe-ganho-total" style="font-weight:bold; font-size:14px; margin-top:4px;">Ganho Total: 0.0 kg (0.0 @)</p>
                <p id="detalhe-gmd" style="font-size:12px; opacity:0.9; margin-top:2px;">GMD Médio: 0.000 kg/dia</p>
                <p id="detalhe-projecao-abate" style="font-weight:bold; color:#FFEB3B; font-size:13px; margin-top:6px; border-top: 1px solid rgba(255,255,255,0.2); padding-top:6px;">Projeção de Abate: ---</p>
                <canvas id="graficoEvolucao"></canvas>
            </div>
            <div class="card"><button class="btn btn-danger" onclick="excluirAnimalCompleto()">ELIMINAR ANIMAL</button></div>
            <div class="card"><div id="lista-historico"></div></div>
        </div>

    </div>

    <script>
        const firebaseConfig = { databaseURL: "https://agropeso-bovinos-default-rtdb.firebaseio.com/" };
        if (!firebase.apps.length) { firebase.initializeApp(firebaseConfig); }
        const database = firebase.database();

        let db = JSON.parse(localStorage.getItem('agropeso_db_offline')) || [];
        let metaAbateKg = parseFloat(localStorage.getItem('agropeso_meta_abate')) || 540;
        let filtroAtivo = 'TODOS';
        
        let fazendaKey = null;
        let fazendaAtual = null;
        let animalAtual = null;

        function hojeISO() { return new Date().toISOString().split('T')[0]; }

        window.onload = function() {
            document.getElementById('input-meta-abate').value = metaAbateKg;
            document.getElementById('lbl-meta-resumo').innerText = `${metaAbateKg} kg (${(metaAbateKg/30).toFixed(1)}@)`;
            
            const sessao = JSON.parse(localStorage.getItem('agropeso_sessao'));
            if (sessao && sessao.fazenda && sessao.senha) {
                document.getElementById('login-fazenda').value = sessao.fazenda;
                document.getElementById('login-senha').value = sessao.senha;
                entrarFazenda();
            }
            atualizarDashboard();
        };

        function ajustarPesoCurral(valor) {
            const el = document.getElementById('curral-peso');
            let atual = parseFloat(el.value) || 0;
            el.value = Math.max(0, atual + valor).toFixed(1);
        }

        function salvarConfiguracoes() {
            const novaMeta = parseFloat(document.getElementById('input-meta-abate').value);
            if (!isNaN(novaMeta) && novaMeta > 0) {
                metaAbateKg = novaMeta;
                localStorage.setItem('agropeso_meta_abate', metaAbateKg);
                document.getElementById('lbl-meta-resumo').innerText = `${metaAbateKg} kg (${(metaAbateKg/30).toFixed(1)}@)`;
                alert('Meta atualizada com sucesso!');
                atualizarDashboard();
            }
        }

        function entrarFazenda() {
            const fazenda = document.getElementById('login-fazenda').value.trim();
            const senha = document.getElementById('login-senha').value.trim();
            if (!fazenda || !senha) { alert('Preencha os campos!'); return; }

            fazendaKey = btoa(fazenda.toLowerCase() + "_" + senha).replace(/=/g, "");
            fazendaAtual = fazenda;
            localStorage.setItem('agropeso_sessao', JSON.stringify({ fazenda, senha }));

            document.getElementById('lbl-nome-fazenda').innerText = fazenda.toUpperCase();
            document.getElementById('bar-user').classList.remove('hidden');
            document.getElementById('app-nav').classList.remove('hidden');
            document.getElementById('view-login').classList.add('hidden');
            document.getElementById('tab-rebanho').classList.remove('hidden');

            database.ref('fazendas/' + fazendaKey).on('value', (snapshot) => {
                const data = snapshot.val();
                if (data) {
                    db = data.pesagens ? Object.values(data.pesagens) : [];
                    guardarCacheLocal();
                    atualizarDashboard();
                }
            });
        }

        function guardarCacheLocal() {
            localStorage.setItem('agropeso_db_offline', JSON.stringify(db));
        }

        function sincronizarNuvem() {
            guardarCacheLocal();
            if (fazendaKey) {
                database.ref('fazendas/' + fazendaKey).set({
                    pesagens: db
                });
            }
        }

        function falarTexto(texto) {
            if ('speechSynthesis' in window) {
                const fala = new SpeechSynthesisUtterance(texto);
                fala.lang = 'pt-BR';
                fala.rate = 1.0;
                window.speechSynthesis.speak(fala);
            }
        }

        function salvarPesagemCurralComVoz() {
            const lote = document.getElementById('curral-lote').value.trim();
            const sistema = document.getElementById('curral-sistema').value;
            const brincoInput = document.getElementById('curral-brinco');
            const pesoInput = document.getElementById('curral-peso');
            
            const brinco = brincoInput.value.trim().toUpperCase();
            const peso = parseFloat(pesoInput.value);

            if (!brinco || isNaN(peso)) { alert('Preencha o brinco e o peso!'); return; }

            db.push({ id: Date.now(), brinco, peso, data: hojeISO(), categoria: 'Boi Gordo', lote, sistema });
            sincronizarNuvem();

            falarTexto(`Brinco ${brinco}, ${peso} quilos registado.`);

            brincoInput.value = '';
            pesoInput.value = '';
            brincoInput.focus();
        }

        function mudarAba(aba, btn) {
            document.querySelectorAll('.tab-content').forEach(t => t.classList.add('hidden'));
            document.querySelectorAll('.nav-tab').forEach(b => b.classList.remove('active'));
            document.getElementById('view-form').classList.add('hidden');
            document.getElementById('view-detail').classList.add('hidden');
            
            document.getElementById('tab-' + aba).classList.remove('hidden');
            btn.classList.add('active');
        }

        function transferirLotePasto() {
            const origem = document.getElementById('pasto-origem').value.trim().toUpperCase();
            const destino = document.getElementById('pasto-destino').value.trim().toUpperCase();
            if (!origem || !destino) return;
            let cont = 0;
            db.forEach(item => { if (item.lote && item.lote.toUpperCase() === origem) { item.lote = destino; cont++; } });
            sincronizarNuvem();
            alert(`${cont} animais foram transferidos para o pasto ${destino}!`);
        }

        function atualizarDashboard() {
            const unicos = [...new Set(db.map(item => item.brinco))];
            document.getElementById('total-animais').innerText = unicos.length;
            
            let somaPesos = 0;
            let prontosAbate = 0;

            unicos.forEach(brinco => {
                const regs = db.filter(i => i.brinco === brinco).sort((a,b) => new Date(b.data) - new Date(a.data));
                if (regs.length > 0) {
                    const ultPeso = regs[0].peso;
                    somaPesos += ultPeso;
                    if (ultPeso >= metaAbateKg) prontosAbate++;
                }
            });

            const mediaPeso = unicos.length > 0 ? (somaPesos / unicos.length).toFixed(1) : 0;
            document.getElementById('media-peso').innerText = mediaPeso + " kg";
            document.getElementById('media-arrobas').innerText = (mediaPeso / 30).toFixed(1) + " @";
            document.getElementById('total-prontos-abate').innerText = prontosAbate;

            renderizarListaAnimais(unicos);
        }

        function filtrarStatus(status, btn) {
            filtroAtivo = status;
            document.querySelectorAll('.btn-chip').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
            filtrarAnimais();
        }

        function renderizarListaAnimais(unicos) {
            const container = document.getElementById('lista-animais');
            container.innerHTML = '';
            
            let listaFiltrada = unicos.filter(b => {
                const regs = db.filter(i => i.brinco === b).sort((x,y) => new Date(y.data) - new Date(x.data));
                const u = regs[0];
                if (filtroAtivo === 'ABATE') return u.peso >= metaAbateKg;
                if (filtroAtivo !== 'TODOS') return u.sistema === filtroAtivo;
                return true;
            });

            listaFiltrada.sort((a, b) => {
                const uA = db.filter(i => i.brinco === a).sort((x,y) => new Date(y.data) - new Date(x.data))[0].peso;
                const uB = db.filter(i => i.brinco === b).sort((x,y) => new Date(y.data) - new Date(x.data))[0].peso;
                return uB - uA;
            });

            listaFiltrada.forEach(brinco => {
                const registros = db.filter(i => i.brinco === brinco).sort((a,b) => new Date(a.data) - new Date(b.data));
                const ultimo = registros[registros.length - 1];
                const primeiro = registros[0];
                
                const ganhoKg = (ultimo.peso - primeiro.peso).toFixed(1);
                const ganhoArrobas = (ganhoKg / 30).toFixed(1);
                const sinal = ganhoKg >= 0 ? '+' : '';
                const sistemaTxt = ultimo.sistema ? ` • ${ultimo.sistema}` : '';

                const eAbate = ultimo.peso >= metaAbateKg;
                const classeAbate = eAbate ? 'pronto-abate' : '';
                const badgeAbate = eAbate ? `<span class="badge badge-abate">🎯 PRONTO P/ ABATE</span>` : `<span class="badge">${ultimo.categoria || 'Gado'}</span>`;

                const div = document.createElement('div');
                div.className = `animal-item ${classeAbate}`;
                div.innerHTML = `
                    <div>
                        <strong>BRINCO: ${brinco}</strong> [${ultimo.lote || 'Sem Lote'}] ${badgeAbate}<br>
                        <small style="color:var(--gray);">Última pesagem: ${ultimo.data}${sistemaTxt} — <strong>${ultimo.peso} kg</strong> (${(ultimo.peso/30).toFixed(1)} @)</small><br>
                        <small style="color:${ganhoKg >= 0 ? 'green' : 'red'}; font-weight:bold;">Ganho: ${sinal}${ganhoKg} kg (${sinal}${ganhoArrobas} @)</small>
                    </div>
                    <button class="btn btn-outline" style="width:auto; padding:4px 8px; font-size:10px;" onclick="abrirDetalhes('${brinco}')">VER</button>
                `;
                container.appendChild(div);
            });
        }

        function abrirModalCadastro() {
            document.getElementById('tab-rebanho').classList.add('hidden');
            document.getElementById('view-form').classList.remove('hidden');
            document.getElementById('input-brinco').value = '';
            document.getElementById('input-peso').value = '';
            document.getElementById('input-data').value = hojeISO();
        }

        function salvarPesagem() {
            const brincoInput = document.getElementById('input-brinco');
            const pesoInput = document.getElementById('input-peso');
            
            const brinco = brincoInput.value.trim().toUpperCase();
            const peso = parseFloat(pesoInput.value);
            const data = document.getElementById('input-data').value;
            const categoria = document.getElementById('input-categoria').value;
            const sistema = document.getElementById('input-sistema').value;
            const lote = document.getElementById('input-lote').value.trim();

            if (!brinco || isNaN(peso)) return;

            db.push({ id: Date.now(), brinco, peso, data, categoria, sistema, lote });
            sincronizarNuvem();
            
            brincoInput.value = '';
            pesoInput.value = '';
            
            voltarDashboard();
        }

        function voltarDashboard() {
            document.querySelectorAll('.tab-content').forEach(t => t.classList.add('hidden'));
            document.getElementById('view-form').classList.add('hidden');
            document.getElementById('view-detail').classList.add('hidden');
            document.getElementById('tab-rebanho').classList.remove('hidden');
            atualizarDashboard();
        }

        function abrirDetalhes(brinco) {
            animalAtual = brinco;
            document.getElementById('tab-rebanho').classList.add('hidden');
            document.getElementById('view-detail').classList.remove('hidden');
            document.getElementById('detalhe-titulo').innerText = `BRINCO: ${brinco}`;

            const registros = db.filter(i => i.brinco === brinco).sort((a,b) => new Date(a.data) - new Date(b.data));
            const ultimo = registros[registros.length - 1];
            const primeiro = registros[0];

            const ganhoKg = (ultimo.peso - primeiro.peso).toFixed(1);
            const ganhoArrobas = (ganhoKg / 30).toFixed(1);
            const sinal = ganhoKg >= 0 ? '+' : '';

            let gmdText = "GMD: Insuficiente (Requer +1 pesagem)";
            let projecaoText = "Projeção de Abate: ---";

            if (registros.length > 1) {
                const d1 = new Date(primeiro.data);
                const d2 = new Date(ultimo.data);
                const diffDias = Math.max(1, Math.round((d2 - d1) / (1000 * 60 * 60 * 24)));
                const gmd = (ultimo.peso - primeiro.peso) / diffDias;

                gmdText = `GMD Médio: ${gmd.toFixed(3)} kg/dia (${diffDias} dias decorridos)`;

                if (ultimo.peso >= metaAbateKg) {
                    projecaoText = `🎯 ANIMAL PRONTO PARA O ABATE! (${ultimo.peso} kg / ${(ultimo.peso/30).toFixed(1)}@)`;
                } else if (gmd > 0) {
                    const kgFaltando = metaAbateKg - ultimo.peso;
                    const diasRestantes = Math.ceil(kgFaltando / gmd);
                    const dataAbate = new Date(d2);
                    dataAbate.setDate(dataAbate.getDate() + diasRestantes);

                    projecaoText = `🎯 Meta de Abate (${metaAbateKg} kg): Faltam ~${diasRestantes} dias (${dataAbate.toISOString().split('T')[0]})`;
                } else {
                    projecaoText = `⚠️ Sem projeção (Animal sem ganho de peso)`;
                }
            }

            document.getElementById('detalhe-ultima-data').innerText = `Última Pesagem: ${ultimo.data} — ${ultimo.peso} kg (${(ultimo.peso/30).toFixed(1)} @) [${ultimo.sistema || 'N/D'}]`;
            document.getElementById('detalhe-ganho-total').innerText = `Ganho Total: ${sinal}${ganhoKg} kg (${sinal}${ganhoArrobas} @)`;
            document.getElementById('detalhe-gmd').innerText = gmdText;
            document.getElementById('detalhe-projecao-abate').innerText = projecaoText;

            desenharGrafico(registros);

            const container = document.getElementById('lista-historico');
            container.innerHTML = '';
            [...registros].reverse().forEach(r => {
                const div = document.createElement('div');
                div.className = 'animal-item';
                div.innerHTML = `<div>Data: <strong>${r.data}</strong> — <strong>${r.peso} kg</strong> (${(r.peso/30).toFixed(1)} @) [${r.sistema || 'Pastagem'}]</div>`;
                container.appendChild(div);
            });
        }

        function desenharGrafico(registros) {
            const canvas = document.getElementById('graficoEvolucao');
            const ctx = canvas.getContext('2d');
            
            canvas.width = canvas.offsetWidth;
            canvas.height = canvas.offsetHeight;
            
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            if (registros.length === 0) return;

            const pesos = registros.map(r => r.peso);
            const minP = Math.min(...pesos) * 0.95;
            const maxP = Math.max(...pesos, metaAbateKg) * 1.05;
            const rangeP = maxP - minP || 1;

            const w = canvas.width;
            const h = canvas.height;

            ctx.strokeStyle = '#FFFFFF';
            ctx.lineWidth = 3;
            ctx.beginPath();

            registros.forEach((reg, i) => {
                const x = (i / (registros.length - 1 || 1)) * (w - 40) + 20;
                const y = h - ((reg.peso - minP) / rangeP) * (h - 40) - 20;

                if (i === 0) ctx.moveTo(x, y);
                else ctx.lineTo(x, y);

                ctx.fillStyle = '#FFFFFF';
                ctx.beginPath();
                ctx.arc(x, y, 5, 0, Math.PI * 2);
                ctx.fill();

                ctx.font = "bold 10px Arial";
                ctx.fillStyle = "#FFFFFF";
                ctx.fillText(`${reg.peso}kg`, x - 12, y - 10);
            });
            ctx.stroke();
        }

        /* FUNÇÃO PARA EXCLUIR O ANIMAL COMPLETO */
        function excluirAnimalCompleto() {
            if (confirm(`Tem certeza que deseja eliminar o animal ${animalAtual} e todo o seu histórico?`)) {
                db = db.filter(i => i.brinco !== animalAtual);
                sincronizarNuvem();
                voltarDashboard();
            }
        }

        function gerarPDFRelatorio() {
            html2pdf().from(document.getElementById('area-relatorio-pdf')).save(`agropeso_relatorio_${hojeISO()}.pdf`);
        }

        function exportarCSV() {
            let csv = "Brinco,Peso (Kg),Arrobas (@),Pronto Abate,Data,Categoria,Sistema,Lote\n";
            db.forEach(r => { 
                const pronto = r.peso >= metaAbateKg ? 'SIM' : 'NAO';
                csv += `${r.brinco},${r.peso},${(r.peso/30).toFixed(1)},${pronto},${r.data},${r.categoria},${r.sistema || ''},${r.lote || ''}\n`; 
            });
            const link = document.createElement("a");
            link.href = encodeURI("data:text/csv;charset=utf-8," + csv);
            link.download = `agropeso_relatorio_${hojeISO()}.csv`;
            link.click();
        }

        function enviarRelatorioWhatsApp() {
            const unicos = [...new Set(db.map(item => item.brinco))];
            if (!unicos.length) { alert('Nenhum animal cadastrado para enviar!'); return; }

            let somaPesos = 0;
            let prontosCount = 0;

            unicos.forEach(brinco => {
                const regs = db.filter(i => i.brinco === brinco).sort((a,b) => new Date(b.data) - new Date(a.data));
                if (regs.length > 0) {
                    somaPesos += regs[0].peso;
                    if (regs[0].peso >= metaAbateKg) prontosCount++;
                }
            });

            const mediaPeso = (somaPesos / unicos.length).toFixed(1);
            const mediaArrobas = (mediaPeso / 30).toFixed(1);

            let texto = `🐂 *AGRO PESO — RELATÓRIO DO REBANHO* 🐂\n`;
            texto += `• Fazenda: ${fazendaAtual}\n`;
            texto += `• Total de Cabeças: ${unicos.length}\n`;
            texto += `• Peso Médio: ${mediaPeso} kg (${mediaArrobas} @)\n`;
            texto += `• Meta de Abate: ${metaAbateKg} kg\n`;
            texto += `• Prontos para Abate: ${prontosCount} cabeças 🎯\n`;
            texto += `• Data: ${hojeISO()}\n\n`;
            texto += `📋 *LISTA DE ANIMAIS:*\n`;

            unicos.forEach(b => {
                const regs = db.filter(i => i.brinco === b).sort((a,b) => new Date(a.data) - new Date(b.data));
                const u = regs[regs.length - 1];
                const p = regs[0];
                const g = (u.peso - p.peso).toFixed(1);
                const tagAbate = u.peso >= metaAbateKg ? ' 🎯 [PRONTO ABATE]' : '';
                texto += `- Brinco ${b}: ${u.peso} kg (${(u.peso/30).toFixed(1)} @) | Ganho: ${g} kg${tagAbate}\n`;
            });

            texto += `\n*Gerado via App Agro Peso*`;

            const url = `https://api.whatsapp.com/send?text=${encodeURIComponent(texto)}`;
            window.open(url, '_blank');
        }

        function filtrarAnimais() {
            const termo = document.getElementById('search-brinco').value.toUpperCase();
            renderizarListaAnimais([...new Set(db.map(item => item.brinco))].filter(b => b.includes(termo)));
        }

        function fazerLogout() {
            localStorage.removeItem('agropeso_sessao');
            location.reload();
        }
    </script>
</body>
</html>
o
