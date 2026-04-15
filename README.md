<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Brawl Ranked Draft Assistant</title>
  <style>
    * { box-sizing: border-box; }
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #0d1020;
      color: #fff;
    }
    .container {
      max-width: 1200px;
      margin: 0 auto;
      padding: 20px;
    }
    h1 {
      text-align: center;
      color: #ffd54a;
      margin-bottom: 8px;
    }
    .subtitle {
      text-align: center;
      color: #b9c0ff;
      margin-bottom: 24px;
    }
    .tabs {
      display: flex;
      gap: 10px;
      justify-content: center;
      margin-bottom: 20px;
      flex-wrap: wrap;
    }
    .tab-btn {
      background: #1b2140;
      color: white;
      border: 0;
      padding: 12px 18px;
      border-radius: 10px;
      cursor: pointer;
      font-weight: bold;
    }
    .tab-btn.active {
      background: #ffd54a;
      color: #111;
    }
    .panel {
      display: none;
      background: #151a33;
      border: 1px solid #2a315f;
      border-radius: 16px;
      padding: 18px;
      margin-bottom: 20px;
    }
    .panel.active {
      display: block;
    }
    .grid {
      display: grid;
      grid-template-columns: repeat(2, minmax(0, 1fr));
      gap: 14px;
    }
    .grid-3 {
      display: grid;
      grid-template-columns: repeat(3, minmax(0, 1fr));
      gap: 14px;
    }
    .field {
      display: flex;
      flex-direction: column;
      gap: 6px;
    }
    label {
      font-size: 14px;
      color: #cfd6ff;
    }
    select, input, button.action-btn {
      width: 100%;
      padding: 12px;
      border-radius: 10px;
      border: 1px solid #3b4378;
      background: #0d1330;
      color: white;
      font-size: 15px;
    }
    button.action-btn {
      background: #ffd54a;
      color: #111;
      font-weight: bold;
      border: none;
      cursor: pointer;
      margin-top: 8px;
    }
    .map-card, .card, .results, .suggestions {
      background: #0d1330;
      border: 1px solid #31396d;
      border-radius: 12px;
      padding: 14px;
      margin-top: 16px;
    }
    .map-title {
      font-size: 20px;
      font-weight: bold;
      color: #ffd54a;
      margin-bottom: 6px;
    }
    .map-meta {
      color: #b7bfff;
      margin-bottom: 8px;
    }
    .map-preview {
      margin-top: 12px;
      background: #091024;
      border: 1px solid #2f3a75;
      border-radius: 12px;
      overflow: hidden;
      max-width: 420px;
    }
    .map-preview img {
      width: 100%;
      display: block;
      aspect-ratio: 16 / 6;
      object-fit: cover;
      background: #0b0f1e;
    }
    .img-fallback {
      padding: 18px;
      color: #9ea7e8;
      font-size: 14px;
    }
    .chips {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      margin-top: 8px;
    }
    .chip {
      background: #27305c;
      color: #fff;
      padding: 8px 12px;
      border-radius: 999px;
      font-size: 14px;
      cursor: pointer;
      border: 1px solid rgba(255,255,255,0.08);
    }
    .chip.ban { background: #7c2230; }
    .chip.pick { background: #1f6b52; }
    .chip.suggest { background: #24356f; cursor: pointer; }
    .small {
      color: #9ea7e8;
      font-size: 13px;
    }
    .ranking-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 12px;
      padding: 10px 12px;
      border-radius: 10px;
      background: #17204a;
      margin-top: 8px;
    }
    .ranking-left {
      font-weight: bold;
    }
    .ranking-right {
      color: #66f0b2;
      font-weight: bold;
    }
    .top-pick {
      font-size: 28px;
      font-weight: bold;
      color: #66f0b2;
      margin: 8px 0;
    }
    .reason {
      color: #cfd6ff;
      line-height: 1.5;
    }
    .search-wrap {
      position: relative;
    }
    .search-results {
      position: absolute;
      top: calc(100% + 6px);
      left: 0;
      right: 0;
      background: #0b1129;
      border: 1px solid #34407b;
      border-radius: 10px;
      overflow: hidden;
      z-index: 10;
      max-height: 220px;
      overflow-y: auto;
      display: none;
    }
    .search-item {
      padding: 10px 12px;
      cursor: pointer;
      border-bottom: 1px solid rgba(255,255,255,0.06);
    }
    .search-item:hover {
      background: #18214a;
    }
    @media (max-width: 900px) {
      .grid, .grid-3 {
        grid-template-columns: 1fr;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>🎮 Brawl Ranked Draft Assistant</h1>
    <div class="subtitle">Map → meilleurs bans % → bans réels → picks conseillés avec %</div>

    <div class="tabs">
      <button class="tab-btn active" id="tabMapBtn" onclick="showTab('map')">1. Map</button>
      <button class="tab-btn" id="tabBanBtn" onclick="showTab('bans')">2. Meilleurs bans %</button>
      <button class="tab-btn" id="tabPickBtn" onclick="showTab('picks')">3. Picks conseillés %</button>
    </div>

    <div class="panel active" id="tabMap">
      <div class="grid">
        <div class="field">
          <label for="mapSelect">Map classée actuelle</label>
          <select id="mapSelect"></select>
        </div>
        <div class="field">
          <label>&nbsp;</label>
          <button class="action-btn" onclick="confirmMap()">Valider cette map</button>
        </div>
      </div>
      <div id="mapInfo" class="map-card" style="display:none;"></div>
    </div>

    <div class="panel" id="tabBans">
      <div class="card">
        <div style="font-weight:bold; color:#ffd54a;">📍 Map sélectionnée</div>
        <div id="selectedMapText" class="small" style="margin-top:8px;">Aucune</div>
      </div>

      <div id="banPercentages" class="suggestions" style="display:none;"></div>

      <div class="card">
        <div style="font-weight:bold; color:#ffd54a;">🚫 Ajouter les bans réellement faits</div>
        <div class="small" style="margin-top:6px;">Clique sur un ban pour le supprimer. Maximum 6 bans.</div>

        <div class="grid" style="margin-top:12px;">
          <div class="field">
            <label for="banSearch">Rechercher un brawler à ban</label>
            <div class="search-wrap">
              <input id="banSearch" type="text" placeholder="Tape un nom..." autocomplete="off">
              <div id="banSearchResults" class="search-results"></div>
            </div>
          </div>
          
        </div>

        <div id="selectedBans" class="chips"></div>
      </div>

      <button class="action-btn" onclick="goToPicks()">Continuer vers les picks</button>
    </div>

    <div class="panel" id="tabPicks">
      <div class="grid">
        <div class="field">
          <label for="pickOrder">Ton ordre de pick</label>
          <select id="pickOrder">
            <option value="first">First pick</option>
            <option value="second">Deuxième pick</option>
            <option value="third">Troisième pick</option>
            <option value="fourth">Quatrième pick</option>
            <option value="fifth">Cinquième pick</option>
            <option value="last">Last pick</option>
          </select>
        </div>
        <div class="field">
          <label>&nbsp;</label>
          <button class="action-btn" onclick="analyzeDraft()">Calculer les meilleurs picks</button>
        </div>
      </div>

      <div class="card">
        <div style="font-weight:bold; color:#ffd54a;">👥 Ajouter les picks ennemis</div>
        <div class="small" style="margin-top:6px;">Clique sur un pick ennemi pour le supprimer. Maximum 3 picks ennemis.</div>

        <div class="grid" style="margin-top:12px;">
          <div class="field">
            <label for="enemySearch">Rechercher un brawler ennemi</label>
            <div class="search-wrap">
              <input id="enemySearch" type="text" placeholder="Tape un nom..." autocomplete="off">
              <div id="enemySearchResults" class="search-results"></div>
            </div>
          </div>
          <div class="field">
            <label>&nbsp;</label>
            <button class="action-btn" onclick="clearEnemies()">Vider les picks ennemis</button>
          </div>
        </div>

        <div id="selectedEnemies" class="chips"></div>
      </div>

      <div id="draftResults" class="results" style="display:none;"></div>
    </div>
  </div>

  <script>
    const MAPS = [
      { id: "dueling-beetles", mode: "Zone réservée", name: "Dueling Beetles", image: "maps/dueling-beetles.png", tags: ["zone", "closed", "control"], strong: ["Lou","Ambre","Squeak","Spike","Gus","Pam"], banTable: [["Lou",95],["Ambre",91],["Spike",88],["Squeak",84],["Pam",77],["Gus",73],["Meg",69],["R-T",65]] },
      { id: "open-business", mode: "Zone réservée", name: "Open Business", image: "maps/open-business.png", tags: ["zone", "mid", "control"], strong: ["Lou","Ambre","Belle","Spike","Gene","Squeak"], banTable: [["Lou",94],["Ambre",90],["Belle",86],["Spike",83],["Gene",78],["Squeak",74],["Meg",69],["Angelo",61]] },
      { id: "parallel-plays", mode: "Zone réservée", name: "Parallel Plays", image: "maps/parallel-plays.png", tags: ["zone", "control", "lane"], strong: ["Lou","Ambre","Spike","R-T","Squeak","Gene"], banTable: [["Lou",96],["Ambre",92],["R-T",87],["Spike",85],["Squeak",79],["Gene",74],["Meg",71],["Belle",66]] },

      { id: "dry-season", mode: "Prime", name: "Dry Season", image: "maps/dry-season.png", tags: ["knockout", "open", "sniper"], strong: ["Piper","Angelo","Belle","Nani","Mandy","Gray"], banTable: [["Angelo",96],["Piper",92],["Belle",88],["Nani",83],["Mandy",78],["Gray",72],["Brock",67],["Gene",61]] },
      { id: "hideout", mode: "Prime", name: "Hideout", image: "maps/hideout.png", tags: ["knockout", "open", "sniper"], strong: ["Piper","Angelo","Belle","Nani","Brock","Mandy"], banTable: [["Angelo",95],["Piper",91],["Belle",87],["Nani",82],["Mandy",77],["Brock",71],["Gray",69],["Gene",60]] },
      { id: "layer-cake", mode: "Prime", name: "Layer Cake", image: "maps/layer-cake.png", tags: ["knockout", "open", "sniper"], strong: ["Belle","Piper","Angelo","Nani","Gray","Mandy"], banTable: [["Belle",94],["Angelo",92],["Piper",89],["Nani",81],["Gray",76],["Mandy",73],["Brock",67],["Gene",59]] },
      { id: "shooting-star", mode: "Prime", name: "Shooting Star", image: "maps/shooting-star.png", tags: ["knockout", "very-open", "sniper"], strong: ["Piper","Angelo","Belle","Nani","Mandy","Gray"], banTable: [["Angelo",97],["Piper",94],["Belle",90],["Nani",86],["Mandy",79],["Gray",75],["Brock",68],["Gene",58]] },

      { id: "beach-ball", mode: "Brawl Ball", name: "Beach Ball", image: "maps/beach-ball.png", tags: ["brawlball", "mid", "aggro"], strong: ["Max","Buster","Fang","Janet","Meg","Gray"], banTable: [["Buster",93],["Max",89],["Fang",85],["Meg",81],["Janet",76],["Gray",71],["Surge",69],["Gene",62]] },
      { id: "center-stage", mode: "Brawl Ball", name: "Center Stage", image: "maps/center-stage.png", tags: ["brawlball", "closed", "aggro"], strong: ["Buster","Max","Fang","Surge","Sam","Gale"], banTable: [["Buster",95],["Fang",91],["Max",87],["Surge",82],["Gale",76],["Sam",72],["Janet",68],["Meg",64]] },
      { id: "pinball-dreams", mode: "Brawl Ball", name: "Pinball Dreams", image: "maps/pinball-dreams.png", tags: ["brawlball", "closed", "aggro"], strong: ["Buster","Max","Surge","Fang","Gale","Sam"], banTable: [["Buster",94],["Fang",90],["Max",86],["Surge",82],["Gale",78],["Sam",72],["Janet",65],["Meg",61]] },
      { id: "sneaky-fields", mode: "Brawl Ball", name: "Sneaky Fields", image: "maps/sneaky-fields.png", tags: ["brawlball", "bushy", "closed"], strong: ["Shelly","Bull","Buster","Rosa","Gale","Max"], banTable: [["Shelly",93],["Buster",89],["Gale",84],["Bull",79],["Max",73],["Rosa",70],["Fang",65],["Surge",61]] },
      { id: "spiraling-out", mode: "Brawl Ball", name: "Spiraling Out", image: "maps/spiraling-out.png", tags: ["brawlball", "lane", "mid"], strong: ["Buster","Max","Meg","Janet","Gray","Surge"], banTable: [["Buster",92],["Meg",88],["Max",84],["Janet",79],["Surge",75],["Gray",70],["Fang",64],["Gene",59]] },
      { id: "triple-dribble", mode: "Brawl Ball", name: "Triple Dribble", image: "maps/triple-dribble.png", tags: ["brawlball", "closed", "aggro"], strong: ["Buster","Max","Fang","Sam","Surge","Gale"], banTable: [["Buster",95],["Fang",90],["Max",85],["Surge",81],["Gale",78],["Sam",71],["Janet",65],["Meg",60]] },

    { id: "bridge-too-far", mode: "Braquage", name: "Bridge Too Far", image: "maps/bridge-too-far.png", tags: ["heist", "open", "damage"], strong: ["Colt","Brock","Belle","Nani","Chuck","A.R.K.A.D."], banTable: [["Chuck",96],["Colt",92],["Belle",87],["Nani",82],["Brock",78],["Colette",73],["A.R.K.A.D.",68],["Penny",61]] },
{ id: "hot-potato", mode: "Braquage", name: "Hot Potato", image: "maps/hot-potato.png", tags: ["heist", "mid", "damage"], strong: ["Colt","Brock","Chuck","Jessie","Colette","A.R.K.A.D."], banTable: [["Chuck",95],["Colt",90],["Jessie",86],["Colette",81],["Brock",76],["A.R.K.A.D.",70],["Penny",66],["Belle",58]] },
{ id: "kaboom-canyon", mode: "Braquage", name: "Kaboom Canyon", image: "maps/kaboom-canyon.png", tags: ["heist", "open", "damage"], strong: ["Colt","Brock","Belle","Chuck","Colette","A.R.K.A.D."], banTable: [["Chuck",96],["Colt",91],["Belle",86],["Colette",82],["Brock",77],["A.R.K.A.D.",71],["Nani",67],["Penny",60]] },
{ id: "safe-zone", mode: "Braquage", name: "Safe Zone", image: "maps/safe-zone.png", tags: ["heist", "open", "damage"], strong: ["Colt","Brock","Chuck","Colette","Belle","A.R.K.A.D."], banTable: [["Chuck",97],["Colt",92],["Colette",88],["Belle",83],["Brock",78],["A.R.K.A.D.",72],["Nani",66],["Penny",60]] },

      { id: "double-swoosh", mode: "Razzia de gemmes", name: "Double Swoosh", image: "maps/double-swoosh.png", tags: ["gem", "bushy", "control"], strong: ["Gene","Spike","Tara","Lou","Pam","Janet"], banTable: [["Gene",94],["Spike",90],["Tara",85],["Lou",80],["Pam",75],["Janet",71],["Sandy",69],["R-T",60]] },
      { id: "gem-fort", mode: "Razzia de gemmes", name: "Gem Fort", image: "maps/gem-fort.png", tags: ["gem", "mid", "control"], strong: ["Gene","Spike","Belle","R-T","Pam","Janet"], banTable: [["Gene",93],["Belle",88],["Spike",84],["R-T",81],["Pam",75],["Janet",70],["Lou",66],["Sandy",61]] },

      { id: "belles-rock", mode: "Knockout", name: "Belle's Rock", image: "maps/belles-rock.png", tags: ["knockout", "mid-open", "sniper"], strong: ["Belle","Piper","Angelo","Gray","Gene","Nani"], banTable: [["Belle",95],["Piper",91],["Angelo",87],["Gray",81],["Nani",77],["Gene",71],["Mandy",66],["Brock",59]] }
    ];

    const BRAWLERS = [
      { name: "A.R.K.A.D.", roles: ["damage","safe","ranged"] },
      { name: "Alli", roles: ["assassin","aggro"] },
      { name: "Ambre", roles: ["control","damage","safe"] },
      { name: "Angelo", roles: ["sniper","safe","ranged"] },
      { name: "Ash", roles: ["tank","aggro"] },
      { name: "Bartaba", roles: ["control","ranged"] },
      { name: "Béa", roles: ["sniper","antiTank","ranged"] },
      { name: "Belle", roles: ["sniper","safe","ranged"] },
      { name: "Berry", roles: ["support","control","safe"] },
      { name: "Billie", roles: ["damage","ranged"] },
      { name: "Bo", roles: ["control","ranged","safe"] },
      { name: "Bonnie", roles: ["aggro","fighter","ranged"] },
      { name: "Brock", roles: ["sniper","damage","ranged"] },
      { name: "Bull", roles: ["tank","aggro"] },
      { name: "Buster", roles: ["tank","safe","aggro"] },
      { name: "Buzz", roles: ["assassin","aggro"] },
      { name: "Byron", roles: ["support","ranged","safe"] },
      { name: "Carl", roles: ["fighter","safe"] },
      { name: "Charlie", roles: ["control","safe"] },
      { name: "Chester", roles: ["fighter","safe"] },
      { name: "Chuck", roles: ["heist","damage"] },
      { name: "Clancy", roles: ["damage","safe","ranged"] },
      { name: "Colette", roles: ["antiTank","damage","ranged"] },
      { name: "Colt", roles: ["damage","heist","ranged"] },
      { name: "Corbac", roles: ["assassin","control"] },
      { name: "Cordelius", roles: ["assassin","antiCarry"] },
      { name: "D'jinn", roles: ["control","ranged"] },
      { name: "Darryl", roles: ["tank","aggro"] },
      { name: "Doug", roles: ["support","tank"] },
      { name: "Draco", roles: ["tank","aggro"] },
      { name: "Dynamike", roles: ["thrower","damage"] },
      { name: "Edgar", roles: ["assassin","aggro"] },
      { name: "El Costo", roles: ["tank","aggro"] },
      { name: "Eliz@", roles: ["support","control"] },
      { name: "Émeri", roles: ["control","safe"] },
      { name: "Eve", roles: ["control","ranged","safe"] },
      { name: "Fang", roles: ["assassin","aggro"] },
      { name: "Finx", roles: ["fighter","damage"] },
      { name: "Frank", roles: ["tank","control"] },
      { name: "Gaël", roles: ["support","safe"] },
      { name: "Gigi", roles: ["fighter","safe"] },
      { name: "Glowy", roles: ["support","control"] },
      { name: "Gray", roles: ["sniper","utility","safe"] },
      { name: "Griff", roles: ["antiTank","damage","ranged"] },
      { name: "Grom", roles: ["thrower","control"] },
      { name: "Gus", roles: ["support","safe","ranged"] },
      { name: "Hank", roles: ["tank","aggro"] },
      { name: "Jacky", roles: ["tank","aggro"] },
      { name: "Jae-Yong", roles: ["fighter","ranged"] },
      { name: "Janet", roles: ["safe","ranged","brawlball"] },
      { name: "Jessie", roles: ["control","heist","safe"] },
      { name: "Juju", roles: ["control","safe"] },
      { name: "Kaze", roles: ["assassin","aggro"] },
      { name: "Kenji", roles: ["assassin","aggro"] },
      { name: "Kit", roles: ["support","assassin"] },
      { name: "Larry & Lawrie", roles: ["control","ranged","safe"] },
      { name: "Léon", roles: ["assassin","aggro"] },
      { name: "Lily", roles: ["assassin","aggro"] },
      { name: "Lola", roles: ["damage","ranged","safe"] },
      { name: "Lou", roles: ["control","zone","safe"] },
      { name: "Lumi", roles: ["support","safe"] },
      { name: "Maisie", roles: ["damage","antiAggro","ranged"] },
      { name: "Mandy", roles: ["sniper","ranged"] },
      { name: "Max", roles: ["support","safe","brawlball"] },
      { name: "Médor", roles: ["fighter","aggro"] },
      { name: "Meeple", roles: ["support","control"] },
      { name: "Meg", roles: ["tank","safe","damage"] },
      { name: "Melody", roles: ["aggro","brawlball"] },
      { name: "Mico", roles: ["assassin","antiThrower"] },
      { name: "Mina", roles: ["fighter","safe"] },
      { name: "Moe", roles: ["control","damage"] },
      { name: "Monsieur M.", roles: ["control","safe"] },
      { name: "Mortis", roles: ["assassin","antiSniper","aggro"] },
      { name: "Najia", roles: ["support","ranged"] },
      { name: "Nani", roles: ["sniper","damage","ranged"] },
      { name: "Nita", roles: ["fighter","control"] },
      { name: "Ollie", roles: ["fighter","aggro"] },
      { name: "Otis", roles: ["control","safe"] },
      { name: "Pam", roles: ["support","safe","zone"] },
      { name: "Pearl", roles: ["damage","tank","safe"] },
      { name: "Penny", roles: ["control","heist","ranged"] },
      { name: "Pierce", roles: ["damage","ranged"] },
      { name: "Poco", roles: ["support","safe"] },
      { name: "Polly", roles: ["support","control"] },
      { name: "R-T", roles: ["control","ranged","safe"] },
      { name: "Ricochet", roles: ["damage","ranged"] },
      { name: "Rosa", roles: ["tank","aggro"] },
      { name: "Sam", roles: ["aggro","tank"] },
      { name: "Shade", roles: ["assassin","aggro"] },
      { name: "Shelly", roles: ["antiTank","aggro","bushy"] },
      { name: "Sirius", roles: ["support","control"] },
      { name: "Spike", roles: ["control","damage","safe"] },
      { name: "Squeak", roles: ["control","zone","safe"] },
      { name: "Stu", roles: ["aggro","safe"] },
      { name: "Surge", roles: ["antiAggro","safe","brawlball"] },
      { name: "Tara", roles: ["control","bushy"] },
      { name: "Tick", roles: ["thrower","control"] },
      { name: "Trunk", roles: ["tank","aggro"] },
      { name: "Wally", roles: ["fighter","safe"] },
      { name: "Willow", roles: ["thrower","control"] },
      { name: "Ziggy", roles: ["damage","ranged"] }
    ];

    let selectedBans = [];
    let selectedEnemies = [];

    function showTab(tab) {
      document.getElementById("tabMap").classList.toggle("active", tab === "map");
      document.getElementById("tabBans").classList.toggle("active", tab === "bans");
      document.getElementById("tabPicks").classList.toggle("active", tab === "picks");
      document.getElementById("tabMapBtn").classList.toggle("active", tab === "map");
      document.getElementById("tabBanBtn").classList.toggle("active", tab === "bans");
      document.getElementById("tabPickBtn").classList.toggle("active", tab === "picks");
    }

    function formatMapLabel(map) {
      return `${map.mode} - ${map.name}`;
    }

    function getMapById(id) {
      return MAPS.find(m => m.id === id);
    }

    function getBrawler(name) {
      return BRAWLERS.find(b => b.name === name);
    }

    function hasRole(brawler, role) {
      return brawler && brawler.roles.includes(role);
    }

    function populateMaps() {
      const mapSelect = document.getElementById("mapSelect");
      MAPS.forEach(map => {
        const option = document.createElement("option");
        option.value = map.id;
        option.textContent = formatMapLabel(map);
        mapSelect.appendChild(option);
      });
    }

    function renderMapCard(map) {
      const mapInfo = document.getElementById("mapInfo");
      mapInfo.style.display = "block";
      mapInfo.innerHTML = `
        <div class="map-title">${map.mode} - ${map.name}</div>
        <div class="map-meta">Tags : ${map.tags.join(", ")}</div>
        <div class="small">Image : place le fichier <strong>${map.image}</strong> dans le dossier du site.</div>
        <div class="map-preview">
          <img src="${map.image}" alt="${map.name}" onerror="this.style.display='none'; this.nextElementSibling.style.display='block';">
          <div class="img-fallback" style="display:none;">Image non trouvée : ${map.image}</div>
        </div>
      `;
    }

    function renderBanPercentages(map) {
      const box = document.getElementById("banPercentages");
      box.style.display = "block";
      box.innerHTML = `
        <div style="font-weight:bold; color:#ffd54a;">🚫 Meilleurs bans sur ${map.name}</div>
        <div class="small" style="margin-top:8px;">Clique aussi sur un nom ci-dessous pour l'ajouter directement aux bans.</div>
        <div style="margin-top:12px;">
          ${map.banTable.map(([name, pct]) => `
            <div class="ranking-item">
              <div class="ranking-left">${name}</div>
              <div class="ranking-right">
                ${pct}% 
                <button class="tab-btn" style="margin-left:8px; padding:6px 10px;" onclick="addBan('${escapeQuote(name)}')">Ajouter</button>
              </div>
            </div>
          `).join("")}
        </div>
      `;
    }

    function confirmMap() {
      const map = getMapById(document.getElementById("mapSelect").value);
      renderMapCard(map);
      document.getElementById("selectedMapText").textContent = formatMapLabel(map);
      renderBanPercentages(map);
      showTab("bans");
    }

    function goToPicks() {
      showTab("picks");
    }

    function escapeQuote(str) {
      return str.replace(/'/g, "\\'");
    }

    function setupSearch(inputId, resultsId, onSelect, currentList, maxItems) {
      const input = document.getElementById(inputId);
      const results = document.getElementById(resultsId);

      function renderResults() {
        const q = input.value.trim().toLowerCase();

        if (!q) {
          results.style.display = "none";
          results.innerHTML = "";
          return;
        }

        const filtered = BRAWLERS
          .filter(b => b.name.toLowerCase().includes(q))
          .filter(b => !currentList().includes(b.name))
          .slice(0, 12);

        if (!filtered.length) {
          results.innerHTML = `<div class="search-item">Aucun résultat</div>`;
          results.style.display = "block";
          return;
        }

        results.innerHTML = filtered.map(b => `
          <div class="search-item" data-name="${b.name}">${b.name}</div>
        `).join("");

        results.style.display = "block";

        Array.from(results.querySelectorAll(".search-item")).forEach(item => {
          const name = item.getAttribute("data-name");
          if (!name) return;
          item.addEventListener("click", () => {
            if (currentList().length >= maxItems) return;
            onSelect(name);
            input.value = "";
            results.innerHTML = "";
            results.style.display = "none";
          });
        });
      }

      input.addEventListener("input", renderResults);
      input.addEventListener("focus", renderResults);

      document.addEventListener("click", (e) => {
        if (!results.contains(e.target) && e.target !== input) {
          results.style.display = "none";
        }
      });
    }

    function addBan(name) {
      if (!name) return;
      if (selectedBans.includes(name)) return;
      if (selectedBans.length >= 6) return;
      if (selectedEnemies.includes(name)) return;
      selectedBans.push(name);
      renderSelectedBans();
    }

    function removeBan(name) {
      selectedBans = selectedBans.filter(b => b !== name);
      renderSelectedBans();
    }

    function clearBans() {
      selectedBans = [];
      renderSelectedBans();
    }

    function renderSelectedBans() {
      const box = document.getElementById("selectedBans");
      if (!selectedBans.length) {
        box.innerHTML = `<span class="small">Aucun ban sélectionné</span>`;
        return;
      }
      box.innerHTML = selectedBans.map(name => `
        <span class="chip ban" onclick="removeBan('${escapeQuote(name)}')" title="Cliquer pour supprimer">
          ${name} ✕
        </span>
      `).join("");
    }

    function addEnemy(name) {
      if (!name) return;
      if (selectedEnemies.includes(name)) return;
      if (selectedEnemies.length >= 3) return;
      if (selectedBans.includes(name)) return;
      selectedEnemies.push(name);
      renderSelectedEnemies();
    }

    function removeEnemy(name) {
      selectedEnemies = selectedEnemies.filter(b => b !== name);
      renderSelectedEnemies();
    }

    function clearEnemies() {
      selectedEnemies = [];
      renderSelectedEnemies();
    }

    function renderSelectedEnemies() {
      const box = document.getElementById("selectedEnemies");
      if (!selectedEnemies.length) {
        box.innerHTML = `<span class="small">Aucun pick ennemi sélectionné</span>`;
        return;
      }
      box.innerHTML = selectedEnemies.map(name => `
        <span class="chip pick" onclick="removeEnemy('${escapeQuote(name)}')" title="Cliquer pour supprimer">
          ${name} ✕
        </span>
      `).join("");
    }

    function scoreMapFit(brawler, map) {
      let score = 0;

      if (map.strong.includes(brawler.name)) score += 28;

      if (map.tags.includes("open")) {
        if (hasRole(brawler, "sniper")) score += 12;
        if (hasRole(brawler, "ranged")) score += 6;
        if (hasRole(brawler, "tank")) score -= 7;
      }

      if (map.tags.includes("very-open")) {
        if (hasRole(brawler, "sniper")) score += 14;
        if (hasRole(brawler, "tank")) score -= 8;
        if (hasRole(brawler, "assassin")) score += 3;
      }

      if (map.tags.includes("closed")) {
        if (hasRole(brawler, "tank")) score += 10;
        if (hasRole(brawler, "aggro")) score += 8;
        if (hasRole(brawler, "antiTank")) score += 5;
        if (hasRole(brawler, "sniper")) score -= 4;
      }

      if (map.tags.includes("bushy")) {
        if (hasRole(brawler, "bushy")) score += 10;
        if (hasRole(brawler, "aggro")) score += 5;
      }

      if (map.tags.includes("zone")) {
        if (hasRole(brawler, "zone")) score += 12;
        if (hasRole(brawler, "control")) score += 9;
        if (hasRole(brawler, "support")) score += 4;
      }

      if (map.tags.includes("gem")) {
        if (hasRole(brawler, "control")) score += 8;
        if (hasRole(brawler, "support")) score += 5;
        if (hasRole(brawler, "safe")) score += 4;
      }

      if (map.tags.includes("knockout")) {
        if (hasRole(brawler, "sniper")) score += 11;
        if (hasRole(brawler, "safe")) score += 7;
      }

      if (map.tags.includes("heist")) {
        if (hasRole(brawler, "heist")) score += 16;
        if (hasRole(brawler, "damage")) score += 8;
      }

      if (map.tags.includes("brawlball")) {
        if (hasRole(brawler, "brawlball")) score += 14;
        if (hasRole(brawler, "aggro")) score += 8;
        if (hasRole(brawler, "tank")) score += 5;
      }

      return score;
    }

    function scoreVsEnemy(candidate, enemy) {
      let score = 0;
      if (!enemy) return score;

      if (hasRole(enemy, "sniper")) {
        if (hasRole(candidate, "assassin")) score += 14;
        if (hasRole(candidate, "aggro")) score += 5;
      }

      if (hasRole(enemy, "tank")) {
        if (hasRole(candidate, "antiTank")) score += 14;
        if (hasRole(candidate, "control")) score += 5;
      }

      if (hasRole(enemy, "thrower")) {
        if (hasRole(candidate, "antiThrower")) score += 15;
        if (hasRole(candidate, "assassin")) score += 9;
      }

      if (hasRole(enemy, "assassin")) {
        if (hasRole(candidate, "control")) score += 12;
        if (hasRole(candidate, "tank")) score += 5;
      }

      if (enemy.name === "Piper" && hasRole(candidate, "antiSniper")) score += 12;
      if (enemy.name === "Tick" && hasRole(candidate, "antiThrower")) score += 12;
      if (enemy.name === "Chuck" && hasRole(candidate, "heist")) score += 3;

      return score;
    }

    function scoreByPickOrder(candidate, order, enemyCount) {
      let score = 0;

      if (order === "first") {
        if (hasRole(candidate, "safe")) score += 18;
        if (hasRole(candidate, "support")) score += 5;
        if (hasRole(candidate, "assassin")) score -= 8;
      }

      if (order === "second") {
        if (hasRole(candidate, "safe")) score += 12;
        if (hasRole(candidate, "control")) score += 5;
      }

      if (order === "third") {
        if (hasRole(candidate, "safe")) score += 10;
        if (hasRole(candidate, "control")) score += 6;
      }

      if (order === "fourth" || order === "fifth") {
        if (hasRole(candidate, "control")) score += 6;
        if (hasRole(candidate, "antiTank")) score += 6;
        if (hasRole(candidate, "assassin")) score += 4;
      }

      if (order === "last") {
        if (enemyCount > 0) {
          if (hasRole(candidate, "assassin")) score += 12;
          if (hasRole(candidate, "antiTank")) score += 12;
          if (hasRole(candidate, "antiThrower")) score += 10;
          if (hasRole(candidate, "safe")) score += 4;
        }
      }

      return score;
    }

    function normalizeToPercent(scored) {
      if (!scored.length) return scored;
      const topScore = scored[0].score || 1;
      return scored.map(item => ({
        ...item,
        percent: Math.max(1, Math.min(99, Math.round((item.score / topScore) * 100)))
      }));
    }

    function analyzeDraft() {
      const map = getMapById(document.getElementById("mapSelect").value);
      const enemies = selectedEnemies.map(getBrawler).filter(Boolean);
      const bannedSet = new Set(selectedBans);
      const enemySet = new Set(selectedEnemies);
      const pickOrder = document.getElementById("pickOrder").value;

      let scored = [];

      BRAWLERS.forEach(candidate => {
        if (bannedSet.has(candidate.name)) return;
        if (enemySet.has(candidate.name)) return;

        let score = 0;
        let reasons = [];

        const mapScore = scoreMapFit(candidate, map);
        score += mapScore;
        if (mapScore >= 24) reasons.push("très fort sur cette map");
        else if (mapScore >= 12) reasons.push("bon fit map");

        let enemyScore = 0;
        enemies.forEach(enemy => {
          enemyScore += scoreVsEnemy(candidate, enemy);
        });
        score += enemyScore;
        if (enemyScore >= 20) reasons.push("counter très bien la compo ennemie");
        else if (enemyScore >= 10) reasons.push("bon contre les picks ennemis");

        const orderScore = scoreByPickOrder(candidate, pickOrder, enemies.length);
        score += orderScore;
        if (orderScore >= 12) reasons.push("très bon pour cet ordre de pick");
        else if (orderScore >= 6) reasons.push("correct pour cet ordre de pick");

        scored.push({ name: candidate.name, score, reasons });
      });

      scored.sort((a, b) => b.score - a.score);
      scored = normalizeToPercent(scored);

      const top = scored.slice(0, 5);
      const best = top[0];

      const box = document.getElementById("draftResults");
      box.style.display = "block";
      box.innerHTML = `
        <div style="font-weight:bold; color:#ffd54a;">📍 ${map.mode} - ${map.name}</div>
        <div class="small" style="margin-top:6px;">Ordre de pick : ${document.getElementById("pickOrder").selectedOptions[0].textContent}</div>

        <div style="margin-top:18px; font-weight:bold;">🎯 Meilleur pick</div>
        <div class="top-pick">${best ? best.name : "Aucun résultat"}</div>
        <div class="reason">${best ? best.reasons.join(" • ") : ""}</div>
        <div class="small" style="margin-top:8px;">Confiance : ${best ? best.percent : 0}%</div>

        <div style="margin-top:18px; font-weight:bold;">🟢 Meilleurs picks avec pourcentage</div>
        <div style="margin-top:12px;">
          ${top.map(item => `
            <div class="ranking-item">
              <div class="ranking-left">${item.name}</div>
              <div class="ranking-right">${item.percent}%</div>
            </div>
          `).join("")}
        </div>

        <div class="reason" style="margin-top:16px;">
          <strong>Bans pris en compte :</strong> ${selectedBans.length ? selectedBans.join(", ") : "aucun"}<br>
          <strong>Picks ennemis pris en compte :</strong> ${selectedEnemies.length ? selectedEnemies.join(", ") : "aucun"}
        </div>
      `;
    }

    populateMaps();
    renderSelectedBans();
    renderSelectedEnemies();

    setupSearch("banSearch", "banSearchResults", addBan, () => selectedBans, 6);
    setupSearch("enemySearch", "enemySearchResults", addEnemy, () => selectedEnemies, 3);
  </script>
</body>
</html>
