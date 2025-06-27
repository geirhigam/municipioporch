# municipioporch
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Circunscrições Hidrográficas - MG</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 1000px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }
        
        .header {
            background: linear-gradient(135deg, #2c3e50 0%, #3498db 100%);
            color: white;
            padding: 30px;
            text-align: center;
        }
        
        .header h1 { font-size: 2.5em; margin-bottom: 10px; }
        
        .main-content {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
            padding: 30px;
        }
        
        .section {
            background: #f8f9fa;
            border-radius: 10px;
            padding: 25px;
            border: 1px solid #e9ecef;
        }
        
        .section h2 { color: #2c3e50; margin-bottom: 20px; }
        
        .form-group { margin-bottom: 20px; }
        
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: #495057;
        }
        
        select, input {
            width: 100%;
            padding: 12px;
            border: 2px solid #dee2e6;
            border-radius: 8px;
            font-size: 16px;
        }
        
        select:focus, input:focus {
            outline: none;
            border-color: #3498db;
        }
        
        .stats {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 20px;
        }
        
        .stat-card {
            background: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            border: 1px solid #e9ecef;
        }
        
        .stat-number {
            font-size: 2em;
            font-weight: bold;
            color: #3498db;
        }
        
        .stat-label {
            color: #6c757d;
            font-size: 0.9em;
            margin-top: 5px;
        }
        
        .municipalities-list {
            max-height: 400px;
            overflow-y: auto;
            margin-top: 20px;
            border: 1px solid #dee2e6;
            border-radius: 8px;
            background: white;
        }
        
        .municipality-item {
            padding: 12px 15px;
            border-bottom: 1px solid #f1f3f4;
            transition: background-color 0.2s ease;
        }
        
        .municipality-item:hover { background-color: #e3f2fd; }
        .municipality-item:last-child { border-bottom: none; }
        
        .btn {
            background: #3498db;
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            margin-top: 10px;
        }
        
        .btn:hover { background: #2980b9; }
        
        .search-results {
            margin-top: 15px;
            padding: 15px;
            background: #fff3cd;
            border: 1px solid #ffeaa7;
            border-radius: 8px;
            display: none;
        }
        
        .search-results.show { display: block; }
        
        .no-results {
            text-align: center;
            padding: 40px;
            color: #6c757d;
            font-style: italic;
        }
        
        @media (max-width: 768px) {
            .main-content { grid-template-columns: 1fr; }
            .stats { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🌊 Circunscrições Hidrográficas</h1>
            <p>Sistema de Consulta - Minas Gerais</p>
        </div>

        <div class="main-content">
            <div class="section">
                <h2>🎯 Controles</h2>
                
                <div class="form-group">
                    <label for="chSelect">Selecionar CH:</label>
                    <select id="chSelect">
                        <option value="">Selecione uma CH...</option>
                    </select>
                </div>

                <div class="form-group">
                    <label for="searchInput">Buscar Município:</label>
                    <input type="text" id="searchInput" placeholder="Digite o nome...">
                </div>

                <button class="btn" onclick="clearAll()">🔄 Limpar</button>

                <div class="stats">
                    <div class="stat-card">
                        <div class="stat-number" id="totalCHs">43</div>
                        <div class="stat-label">Total CHs</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-number" id="totalMunicipalities">1104</div>
                        <div class="stat-label">Municípios</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-number" id="selectedCount">0</div>
                        <div class="stat-label">CH Selecionada</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-number" id="searchCount">0</div>
                        <div class="stat-label">Busca</div>
                    </div>
                </div>
            </div>

            <div class="section">
                <h2>📊 Resultados</h2>
                
                <div class="search-results" id="searchResults">
                    <strong>Resultados da busca:</strong>
                    <div id="searchList"></div>
                </div>

                <div class="municipalities-list" id="municipalitiesList">
                    <div class="no-results">
                        Selecione uma CH para ver os municípios
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const chData = {
            "BU1": ["Santo Antônio do Jacinto"],
            "DO1": ["Abre Campo", "Acaiaca", "Alto Rio Doce", "Alvinópolis", "Amparo da Serra", "Araponga", "Barra Longa", "Bom Jesus do Galho", "Brás Pires", "Cajuri", "Canaã", "Capela Nova", "Caputira", "Caranaíba", "Carandaí", "Caratinga", "Catas Altas da Noruega", "Cipotânea", "Coimbra", "Conselheiro Lafaiete", "Córrego Novo", "Cristiano Otoni", "Desterro do Melo", "Diogo de Vasconcelos", "Dionísio", "Divinésia", "Dom Silvério", "Dores do Turvo", "Entre Folhas", "Ervália", "Guaraciaba", "Itaverava", "Jaguaraçu", "Jequeri", "Lamim", "Manhuaçu", "Mariana", "Marliéria", "Matipó", "Mercês", "Oratórios", "Ouro Branco", "Ouro Preto", "Paula Cândido", "Pedra Bonita", "Pedra do Anta", "Piedade de Ponte Nova", "Pingo-d'Água", "Piranga", "Ponte Nova", "Porto Firme", "Presidente Bernardes", "Raul Soares", "Ressaquinha", "Rio Casca", "Rio Doce", "Rio Espera", "Santa Cruz do Escalvado", "Santa Margarida", "Santana dos Montes", "Santo Antônio do Grama", "São Domingos do Prata", "São Geraldo", "São José do Goiabal", "São Miguel do Anta", "São Pedro dos Ferros", "Sem-Peixe", "Senador Firmino", "Senhora de Oliveira", "Senhora dos Remédios", "Sericita", "Teixeiras", "Timóteo", "Ubá", "Urucânia", "Vargem Alegre", "Vermelho Novo", "Viçosa"],
            "DO2": ["Alvinópolis", "Antônio Dias", "Barão de Cocais", "Bela Vista de Minas", "Bom Jesus do Amparo", "Catas Altas", "Coronel Fabriciano", "Ipatinga", "Itabira", "Jaguaraçu", "João Monlevade", "Mariana", "Marliéria", "Nova Era", "Ouro Preto", "Rio Piracicaba", "Santa Bárbara", "São Domingos do Prata", "São Gonçalo do Rio Abaixo", "Timóteo"],
            "DO3": ["Açucena", "Alvorada de Minas", "Antônio Dias", "Belo Oriente", "Braúnas", "Bugre", "Caratinga", "Carmésia", "Conceição do Mato Dentro", "Congonhas do Norte", "Dom Joaquim", "Dores de Guanhães", "Ferros", "Guanhães", "Iapu", "Ipaba", "Ipatinga", "Itabira", "Itambé do Mato Dentro", "Joanésia", "Materlândia", "Mesquita", "Morro do Pilar", "Naque", "Passabém", "Sabinópolis", "Santa Maria de Itabira", "Santana do Paraíso", "Santo Antônio do Itambé", "Santo Antônio do Rio Abaixo", "São Sebastião do Rio Preto", "Senhora do Porto", "Serra Azul de Minas", "Serro"],
            "DO4": ["Açucena", "Água Boa", "Aimorés", "Campanário", "Cantagalo", "Coluna", "Conselheiro Pena", "Coroaci", "Cuparaque", "Divino das Laranjeiras", "Divinolândia de Minas", "Felício dos Santos", "Franciscópolis", "Frei Inocêncio", "Frei Lagonegro", "Galiléia", "Goiabeira", "Gonzaga", "Governador Valadares", "Guanhães", "Itambacuri", "Itueta", "Jampruca", "José Raydan", "Malacacheta", "Marilac", "Materlândia", "Nacip Raydan", "Naque", "Paulistas", "Peçanha", "Periquito", "Resplendor", "Rio Vermelho", "Sabinópolis", "Santa Efigênia de Minas", "Santa Maria do Suaçuí", "São Geraldo da Piedade", "São Geraldo do Baixio", "São João Evangelista", "São José da Safira", "São José do Jacuri", "São Pedro do Suaçuí", "São Sebastião do Maranhão", "Sardoá", "Serra Azul de Minas", "Mathias Lobato", "Virginópolis", "Virgolândia"],
            "DO5": ["Alpercata", "Alvarenga", "Bugre", "Capitão Andrade", "Caratinga", "Conselheiro Pena", "Dom Cavati", "Engenheiro Caldas", "Fernandes Tourinho", "Governador Valadares", "Iapu", "Imbé de Minas", "Inhapim", "Ipaba", "Itanhomi", "Piedade de Caratinga", "Resplendor", "Santa Bárbara do Leste", "Santa Rita de Minas", "São Domingos das Dores", "São João do Oriente", "São Sebastião do Anta", "Sobrália", "Tarumirim", "Tumiritinga", "Ubaporanga"],
            "DO6": ["Aimorés", "Alvarenga", "Caratinga", "Chalé", "Conceição de Ipanema", "Conselheiro Pena", "Durandé", "Inhapim", "Ipanema", "Itueta", "Lajinha", "Luisburgo", "Manhuaçu", "Manhumirim", "Martins Soares", "Mutum", "Pocrane", "Alto Jequitibá", "Reduto", "Resplendor", "Santana do Manhuaçu", "Santa Rita do Itueto", "São João do Manhuaçu", "São José do Mantimento", "Simonésia", "Taparuba"],
            "GD1": ["Aiuruoca", "Alagoa", "Andrelândia", "Arantina", "Baependi", "Bocaina de Minas", "Bom Jardim de Minas", "Carrancas", "Carvalhos", "Cruzília", "Ibertioga", "Ibituruna", "Ijaci", "Ingaí", "Itamonte", "Itumirim", "Itutinga", "Lavras", "Liberdade", "Lima Duarte", "Luminárias", "Madre de Deus de Minas", "Minduri", "Nazareno", "Piedade do Rio Grande", "Santana do Garambéu", "Santa Rita do Ibitipoca", "São João del Rei", "São Tomé das Letras", "São Vicente de Minas", "Seritinga", "Serranos"],
            "GD2": ["Alfredo Vasconcelos", "Antônio Carlos", "Barbacena", "Barroso", "Bom Sucesso", "Camacho", "Campo Belo", "Cana Verde", "Candeias", "Carandaí", "Carmo da Cachoeira", "Carmo da Mata", "Casa Grande", "Conceição da Barra de Minas", "Coronel Xavier Chaves", "Dores de Campos", "Ibertioga", "Ibituruna", "Ijaci", "Ingaí", "Lagoa Dourada", "Lavras", "Luminárias", "Nazareno", "Nepomuceno", "Oliveira", "Perdões", "Prados", "Resende Costa", "Ressaquinha", "Ribeirão Vermelho", "Ritápolis", "Santa Cruz de Minas", "Santana do Jacaré", "Santa Rita do Ibitipoca", "Santo Antônio do Amparo", "São Bento Abade", "São Francisco de Paula", "São João del Rei", "São Tiago", "Tiradentes", "Três Pontas"],
            "GD3": ["Aguanil", "Alfenas", "Alpinópolis", "Alterosa", "Areado", "Boa Esperança", "Botelhos", "Cabo Verde", "Camacho", "Campestre", "Campo Belo", "Campo do Meio", "Campos Gerais", "Cana Verde", "Candeias", "Capitólio", "Carmo do Rio Claro", "Conceição da Aparecida", "Congonhal", "Coqueiral", "Córrego Fundo", "Cristais", "Divisa Nova", "Elói Mendes", "Espírito Santo do Dourado", "Fama", "Formiga", "Guapé", "Guaxupé", "Ilicínea", "Ipuiúna", "Itapecerica", "Juruaia", "Machado", "Monte Belo", "Muzambinho", "Nepomuceno", "Nova Resende", "Paraguaçu", "Perdões", "Pimenta", "Poço Fundo", "Santana da Vargem", "São João Batista do Glória", "São João da Mata", "São José da Barra", "São Pedro da União", "Serrania", "Três Pontas"],
            "GD4": ["Aiuruoca", "Alagoa", "Baependi", "Cambuquira", "Campanha", "Carmo da Cachoeira", "Carmo de Minas", "Caxambu", "Conceição do Rio Verde", "Cristina", "Cruzília", "Dom Viçoso", "Elói Mendes", "Itamonte", "Itanhandu", "Jesuânia", "Lambari", "Monsenhor Paulo", "Olímpio Noronha", "Passa Quatro", "Pedralva", "Pouso Alto", "São Gonçalo do Sapucaí", "São Lourenço", "São Sebastião do Rio Verde", "São Tomé das Letras", "Soledade de Minas", "Três Corações", "Três Pontas", "Varginha", "Virgínia"],
            "GD5": ["Borda da Mata", "Brazópolis", "Cachoeira de Minas", "Camanducaia", "Cambuí", "Careaçu", "Carvalhópolis", "Conceição das Pedras", "Conceição dos Ouros", "Congonhal", "Consolação", "Cordislândia", "Córrego do Bom Jesus", "Delfim Moreira", "Elói Mendes", "Espírito Santo do Dourado", "Estiva", "Gonçalves", "Heliodora", "Itajubá", "Lambari", "Machado", "Maria da Fé", "Marmelópolis", "Monsenhor Paulo", "Munhoz", "Natércia", "Ouro Fino", "Paraguaçu", "Paraisópolis", "Passa Quatro", "Pedralva", "Piranguçu", "Piranguinho", "Poço Fundo", "Pouso Alegre", "Santa Rita do Sapucaí", "São Gonçalo do Sapucaí", "São João da Mata", "São José do Alegre", "São Sebastião da Bela Vista", "Sapucaí-Mirim", "Senador Amaral", "Senador José Bento", "Silvianópolis", "Turvolândia", "Virgínia", "Wenceslau Braz"],
            "GD6": ["Albertina", "Andradas", "Arceburgo", "Bandeira do Sul", "Bom Repouso", "Borda da Mata", "Botelhos", "Bueno Brandão", "Cabo Verde", "Caldas", "Campestre", "Estiva", "Guaranésia", "Guaxupé", "Ibitiúra de Minas", "Inconfidentes", "Ipuiúna", "Jacutinga", "Monte Santo de Minas", "Monte Sião", "Munhoz", "Muzambinho", "Ouro Fino", "Poços de Caldas", "Santa Rita de Caldas", "Senador Amaral", "Tocos do Moji"],
            "GD7": ["Alpinópolis", "Bom Jesus da Penha", "Capetinga", "Cássia", "Claraval", "Delfinópolis", "Fortaleza de Minas", "Ibiraci", "Itamogi", "Itaú de Minas", "Jacuí", "Monte Santo de Minas", "Nova Resende", "Passos", "Pratápolis", "Sacramento", "São João Batista do Glória", "São José da Barra", "São Pedro da União", "São Roque de Minas", "São Sebastião do Paraíso", "São Tomás de Aquino"],
            "GD8": ["Água Comprida", "Campina Verde", "Campo Florido", "Carneirinho", "Comendador Gomes", "Conceição das Alagoas", "Conquista", "Delta", "Fronteira", "Frutal", "Itapagipe", "Iturama", "Pirajuba", "Planura", "Prata", "Sacramento", "São Francisco de Sales", "Uberaba", "Veríssimo"],
            "IB1": ["Alto Caparaó", "Caiana", "Caparaó", "Espera Feliz"],
            "IN1": ["Águas Formosas", "Bertópolis", "Fronteira dos Vales", "Machacalis", "Santa Helena de Minas", "Umburatiba"],
            "IP1": ["Lajinha"],
            "IU1": ["Nanuque"],
            "JQ1": ["Berilo", "Bocaiúva", "Botumirim", "Carbonita", "Couto de Magalhães de Minas", "Cristália", "Datas", "Diamantina", "Fruta de Leite", "Grão Mogol", "Guaraciama", "Itacambira", "José Gonçalves de Minas", "Josenópolis", "Leme do Prado", "Novorizonte", "Olhos-d'Água", "Padre Carvalho", "Riacho dos Machados", "Rio Pardo de Minas", "Rubelita", "Serranópolis de Minas", "Serro", "Turmalina", "Virgem da Lapa"],
            "JQ2": ["Angelândia", "Araçuaí", "Aricanduva", "Berilo", "Capelinha", "Carbonita", "Chapada do Norte", "Felício dos Santos", "São Gonçalo do Rio Preto", "Francisco Badaró", "Itamarandiba", "Jenipapo de Minas", "José Gonçalves de Minas", "Leme do Prado", "Malacacheta", "Minas Novas", "Novo Cruzeiro", "Rio Vermelho", "Setubinha", "Senador Modestino Gonçalves", "Turmalina", "Veredinha", "Virgem da Lapa"],
            "JQ3": ["Almenara", "Cachoeira de Pajeú", "Araçuaí", "Bandeira", "Caraí", "Comercinho", "Coronel Murta", "Divisópolis", "Felisburgo", "Fruta de Leite", "Itaobim", "Itinga", "Jacinto", "Jequitinhonha", "Joaíma", "Jordânia", "Mata Verde", "Medina", "Monte Formoso", "Novo Cruzeiro", "Novorizonte", "Padre Paraíso", "Pedra Azul", "Ponto dos Volantes", "Rio do Prado", "Rubelita", "Rubim", "Salinas", "Salto da Divisa", "Santa Cruz de Salinas", "Santa Maria do Salto", "Santo Antônio do Jacinto", "Taiobeiras", "Virgem da Lapa"],
            "JU1": ["Felisburgo", "Palmópolis", "Rio do Prado"],
            "MU1": ["Águas Formosas", "Caraí", "Carlos Chagas", "Catuji", "Crisólita", "Fronteira dos Vales", "Itaipé", "Ladainha", "Malacacheta", "Nanuque", "Novo Oriente de Minas", "Pavão", "Poté", "Serra dos Aimorés", "Teófilo Otoni", "Umburatiba"],
            "PA1": ["Águas Vermelhas", "Berizal", "Curral de Dentro", "Divisa Alegre", "Indaiabira", "Montezuma", "Ninheira", "Rio Pardo de Minas", "Santa Cruz de Salinas", "Santo Antônio do Retiro", "São João do Paraíso", "Taiobeiras", "Vargem Grande do Rio Pardo"],
            "PE1": ["Serra dos Aimorés"],
            "PJ1": ["Camanducaia", "Extrema", "Itapeva", "Sapucaí-Mirim", "Toledo"],
            "PN1": ["Abadia dos Dourados", "Araguari", "Carmo do Paranaíba", "Cascalho Rico", "Coromandel", "Cruzeiro da Fortaleza", "Douradoquara", "Estrela do Sul", "Grupiara", "Guarda-Mor", "Guimarânia", "Iraí de Minas", "Lagamar", "Lagoa Formosa", "Monte Carmelo", "Nova Ponte", "Paracatu", "Patos de Minas", "Patrocínio", "Presidente Olegário", "Rio Paranaíba", "Romaria", "Serra do Salitre", "Unaí"],
            "PN2": ["Araguari", "Araxá", "Campos Altos", "Ibiá", "Indianópolis", "Iraí de Minas", "Nova Ponte", "Patrocínio", "Pedrinópolis", "Perdizes", "Pratinha", "Rio Paranaíba", "Sacramento", "Santa Juliana", "São Roque de Minas", "Serra do Salitre", "Tapira", "Tupaciguara", "Uberaba", "Uberlândia"],
            "PN3": ["Araporã", "Cachoeira Dourada", "Campina Verde", "Campo Florido", "Canápolis", "Capinópolis", "Carneirinho", "Centralina", "Gurinhatã", "Ipiaçu", "Ituiutaba", "Iturama", "Limeira do Oeste", "Monte Alegre de Minas", "Prata", "Santa Vitória", "Tupaciguara", "Uberaba", "Uberlândia", "União de Minas", "Veríssimo"],
            "PS1": ["Além Paraíba", "Antônio Carlos", "Belmiro Braga", "Bias Fortes", "Bicas", "Bocaina de Minas", "Bom Jardim de Minas", "Chácara", "Chiador", "Coronel Pacheco", "Ewbank da Câmara", "Guarará", "Juiz de Fora", "Lima Duarte", "Mar de Espanha", "Maripá de Minas", "Matias Barbosa", "Olaria", "Passa Vinte", "Pedro Teixeira", "Pequeri", "Rio Preto", "Santa Bárbara do Monte Verde", "Santana do Deserto", "Santa Rita do Jacutinga", "Santa Rita do Ibitipoca", "Santo Antônio do Aventureiro", "Santos Dumont", "Senador Cortes", "Simão Pereira"],
            "PS2": ["Além Paraíba", "Antônio Carlos", "Antônio Prado de Minas", "Aracitaba", "Argirita", "Astolfo Dutra", "Barão do Monte Alto", "Barbacena", "Bicas", "Carangola", "Cataguases", "Coronel Pacheco", "Descoberto", "Desterro do Melo", "Divinésia", "Divino", "Dona Euzébia", "Ervália", "Estrela Dalva", "Eugenópolis", "Faria Lemos", "Fervedouro", "Goianá", "Guarani", "Guidoval", "Guiricema", "Itamarati de Minas", "Laranjal", "Leopoldina", "Mercês", "Miradouro", "Miraí", "Muriaé", "Oliveira Fortes", "Orizânia", "Paiva", "Palma", "Patrocínio do Muriaé", "Pedra Dourada", "Piau", "Pirapetinga", "Piraúba", "Recreio", "Rio Novo", "Rio Pomba", "Rochedo de Minas", "Rodeiro", "Rosário da Limeira", "Santa Bárbara do Tugúrio", "Santana de Cataguases", "Santo Antônio do Aventureiro", "Santos Dumont", "São Francisco do Glória", "São Geraldo", "São João Nepomuceno", "São Sebastião da Vargem Alegre", "Senador Cortes", "Silveirânia", "Tabuleiro", "Tocantins", "Tombos", "Ubá", "Vieiras", "Visconde do Rio Branco", "Volta Grande"],
            "SF1": ["Abaeté", "Arcos", "Bambuí", "Bom Despacho", "Campos Altos", "Capitólio", "Córrego Danta", "Dores do Indaiá", "Doresópolis", "Estrela do Indaiá", "Formiga", "Iguatama", "Japaraíba", "Lagoa da Prata", "Luz", "Martinho Campos", "Medeiros", "Moema", "Pains", "Pimenta", "Piumhi", "Pratinha", "Quartel Geral", "Santo Antônio do Monte", "São Roque de Minas", "Serra da Saudade", "Tapiraí", "Vargem Bonita"],
            "SF10": ["Bocaiúva", "Capitão Enéas", "Catuti", "Espinosa", "Francisco Sá", "Gameleiras", "Glaucilândia", "Guaraciama", "Ibiracatu", "Jaíba", "Janaúba", "Juramento", "Mamonas", "Matias Cardoso", "Mato Verde", "Mirabela", "Monte Azul", "Montes Claros", "Nova Porteirinha", "Pai Pedro", "Patis", "Porteirinha", "Riacho dos Machados", "São João da Ponte", "Serranópolis de Minas", "Varzelândia", "Verdelândia"],
            "SF2": ["Araújos", "Bom Despacho", "Carmo da Mata", "Carmo do Cajuru", "Carmópolis de Minas", "Cláudio", "Conceição do Pará", "Desterro de Entre Rios", "Divinópolis", "Florestal", "Igaratinga", "Itaguara", "Itapecerica", "Itatiaiuçu", "Itaúna", "Leandro Ferreira", "Maravilhas", "Martinho Campos", "Nova Serrana", "Oliveira", "Onça de Pitangui", "Papagaios", "Pará de Minas", "Passa Tempo", "Pedra do Indaiá", "Perdigão", "Piracema", "Pitangui", "Pompéu", "Resende Costa", "Santo Antônio do Monte", "São Francisco de Paula", "São Gonçalo do Pará", "São Sebastião do Oeste"],
            "SF3": ["Belo Vale", "Betim", "Bonfim", "Brumadinho", "Cachoeira da Prata", "Caetanópolis", "Casa Grande", "Congonhas", "Conselheiro Lafaiete", "Contagem", "Cristiano Otoni", "Crucilândia", "Curvelo", "Desterro de Entre Rios", "Entre Rios de Minas", "Esmeraldas", "Felixlândia", "Florestal", "Fortuna de Minas", "Ibirité", "Igarapé", "Inhaúma", "Itatiaiuçu", "Itaúna", "Itaverava", "Jeceaba", "Juatuba", "Lagoa Dourada", "Maravilhas", "Mário Campos", "Mateus Leme", "Moeda", "Ouro Branco", "Ouro Preto", "Papagaios", "Pará de Minas", "Paraopeba", "Pequi", "Piedade dos Gerais", "Pompéu", "Queluzito", "Resende Costa", "Rio Manso", "São Brás do Suaçuí", "São Joaquim de Bicas", "São José da Varginha", "Sarzedo", "Sete Lagoas"],
            "SF4": ["Abaeté", "Arapuá", "Biquinhas", "Campos Altos", "Carmo do Paranaíba", "Cedro do Abaeté", "Córrego Danta", "Estrela do Indaiá", "Felixlândia", "Lagoa Formosa", "Matutina", "Morada Nova de Minas", "Paineiras", "Patos de Minas", "Pompéu", "Quartel Geral", "Rio Paranaíba", "Santa Rosa da Serra", "São Gonçalo do Abaeté", "São Gotardo", "Serra da Saudade", "Tiros", "Três Marias", "Varjão de Minas"],
            "SF5": ["Araçaí", "Augusto de Lima", "Baldim", "Belo Horizonte", "Buenópolis", "Caeté", "Capim Branco", "Conceição do Mato Dentro", "Confins", "Congonhas do Norte", "Contagem", "Cordisburgo", "Corinto", "Curvelo", "Datas", "Diamantina", "Esmeraldas", "Funilândia", "Gouvêa", "Inimutaba", "Itabirito", "Jaboticatubas", "Jequitibá", "Joaquim Felício", "Nova União", "Lagoa Santa", "Lassance", "Matozinhos", "Monjolos", "Morro da Garça", "Nova Lima", "Ouro Preto", "Paraopeba", "Pedro Leopoldo", "Pirapora", "Presidente Juscelino", "Presidente Kubitschek", "Prudente de Morais", "Raposos", "Ribeirão das Neves", "Rio Acima", "Sabará", "Santa Luzia", "Santana de Pirapama", "Santana do Riacho", "Santo Hipólito", "São José da Lapa", "Sete Lagoas", "Taquaraçu de Minas", "Várzea da Palma", "Vespasiano"],
            "SF6": ["Bocaiúva", "Brasília de Minas", "Buenópolis", "Buritizeiro", "Campo Azul", "Claro dos Poções", "Coração de Jesus", "Engenheiro Navarro", "Francisco Dumont", "Ibiaí", "Icaraí de Minas", "Jequitaí", "Joaquim Felício", "Lagoa dos Patos", "Lassance", "Luislândia", "Mirabela", "Montes Claros", "Pirapora", "Ponto Chique", "Santa Fé de Minas", "São Francisco", "São Gonçalo do Abaeté", "São João da Lagoa", "São João do Pacuí", "São Romão", "Três Marias", "Ubaí", "Várzea da Palma"],
            "SF7": ["Bonfinópolis de Minas", "Brasilândia de Minas", "Buritizeiro", "Cabeceira Grande", "Dom Bosco", "Guarda-Mor", "João Pinheiro", "Lagamar", "Lagoa Grande", "Natalândia", "Paracatu", "Patos de Minas", "Presidente Olegário", "Santa Fé de Minas", "Unaí", "Vazante"],
            "SF8": ["Arinos", "Bonfinópolis de Minas", "Buritis", "Chapada Gaúcha", "Formoso", "Pintópolis", "Riachinho", "Santa Fé de Minas", "São Romão", "Unaí", "Uruana de Minas", "Urucuia"],
            "SF9": ["Bonito de Minas", "Brasília de Minas", "Chapada Gaúcha", "Cônego Marinho", "Formoso", "Ibiracatu", "Itacarambi", "Jaíba", "Januária", "Japonvar", "Juvenília", "Lontra", "Luislândia", "Manga", "Matias Cardoso", "Miravânia", "Montalvânia", "Pedras de Maria da Cruz", "Pintópolis", "São Francisco", "São João da Ponte", "São João das Missões", "Urucuia", "Varzelândia"],
            "SM1": ["Ataléia", "Central de Minas", "Frei Gaspar", "Itabirinha", "Itambacuri", "Mantena", "Mendes Pimentel", "Nova Belém", "Nova Módica", "Ouro Verde de Minas", "Pescador", "São Félix de Minas", "São João do Manteninha", "São José do Divino"]
        };

        let currentCH = '';

        document.addEventListener('DOMContentLoaded', function() {
            populateSelect();
            setupEvents();
            updateStats();
        });

        function populateSelect() {
            const select = document.getElementById('chSelect');
            Object.keys(chData).sort().forEach(ch => {
                const option = document.createElement('option');
                option.value = ch;
                option.textContent = `${ch} - ${chData[ch].length} municípios`;
                select.appendChild(option);
            });
        }

        function setupEvents() {
            document.getElementById('chSelect').onchange = function() {
                selectCH(this.value);
            };

            document.getElementById('searchInput').oninput = function() {
                searchMunicipalities(this.value);
            };
        }

        function selectCH(ch) {
            currentCH = ch;
            showMunicipalities(ch);
            updateStats();
            hideSearch();
        }

        function showMunicipalities(ch) {
            const list = document.getElementById('municipalitiesList');
            
            if (!ch || !chData[ch]) {
                list.innerHTML = '<div class="no-results">Selecione uma CH para ver os municípios</div>';
                return;
            }

            const municipalities = chData[ch].sort();
            list.innerHTML = municipalities.map(m => 
                `<div class="municipality-item">📍 ${m}</div>`
            ).join('');
        }

        function searchMunicipalities(term) {
            if (!term.trim()) {
                hideSearch();
                return;
            }

            const results = [];
            Object.keys(chData).forEach(ch => {
                chData[ch].forEach(municipality => {
                    if (municipality.toLowerCase().includes(term.toLowerCase())) {
                        results.push({ municipality, ch });
                    }
                });
            });

            showSearchResults(results);
            document.getElementById('searchCount').textContent = results.length;
        }

        function showSearchResults(results) {
            const searchDiv = document.getElementById('searchResults');
            const searchList = document.getElementById('searchList');

            if (results.length === 0) {
                searchList.innerHTML = '<p>Nenhum resultado encontrado</p>';
            } else {
                searchList.innerHTML = results.map(r => 
                    `<div class="municipality-item" onclick="selectCH('${r.ch}')">
                        📍 ${r.municipality} <small>(${r.ch})</small>
                    </div>`
                ).join('');
            }

            searchDiv.classList.add('show');
        }

        function hideSearch() {
            document.getElementById('searchResults').classList.remove('show');
            document.getElementById('searchCount').textContent = '0';
        }

        function updateStats() {
            document.getElementById('totalCHs').textContent = Object.keys(chData).length;
            
            const total = Object.values(chData).reduce((sum, municipalities) => sum + municipalities.length, 0);
            document.getElementById('totalMunicipalities').textContent = total;
            
            const selected = currentCH && chData[currentCH] ? chData[currentCH].length : 0;
            document.getElementById('selectedCount').textContent = selected;
        }

        function clearAll() {
            currentCH = '';
            document.getElementById('chSelect').value = '';
            document.getElementById('searchInput').value = '';
            showMunicipalities('');
            hideSearch();
            updateStats();
        }
    </script>
</body>
</html>
