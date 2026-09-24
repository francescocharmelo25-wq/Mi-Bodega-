<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Mi Bodega - Catálogo de Precios</title>
  <style>
    :root {
      --primary: #2563eb;
      --bg: #f8fafc;
      --card-bg: #ffffff;
      --text: #0f172a;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; -webkit-tap-highlight-color: transparent; }
    body { background-color: var(--bg); color: var(--text); padding-bottom: 20px; }
    
    header { background: var(--primary); color: white; padding: 15px; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 8px rgba(0,0,0,0.15); }
    .header-top { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; }
    .rate-box { background: rgba(255,255,255,0.2); padding: 5px 10px; border-radius: 8px; font-size: 14px; font-weight: bold; display: flex; align-items: center; gap: 5px; }
    .rate-box input { width: 75px; padding: 4px 6px; border: none; border-radius: 4px; font-weight: bold; text-align: center; font-size: 14px; }

    .search-box { width: 100%; padding: 10px 15px; border: none; border-radius: 20px; outline: none; font-size: 15px; }

    nav { display: flex; background: white; border-bottom: 1px solid #e2e8f0; position: sticky; top: 92px; z-index: 99; }
    nav button { flex: 1; padding: 12px 5px; border: none; background: none; font-weight: 600; color: #64748b; font-size: 14px; border-bottom: 3px solid transparent; }
    nav button.active { color: var(--primary); border-bottom-color: var(--primary); }

    .container { padding: 12px; }
    .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(150px, 1fr)); gap: 10px; }
    
    .card { background: var(--card-bg); border-radius: 12px; padding: 12px; box-shadow: 0 1px 3px rgba(0,0,0,0.05); border: 1px solid #e2e8f0; display: flex; flex-direction: column; justify-content: space-between; }
    .card h4 { font-size: 14px; margin-bottom: 8px; color: #334155; }
    .price-bs { font-size: 16px; font-weight: bold; color: var(--primary); }
    .price-usd { font-size: 12px; color: #64748b; margin-top: 2px; }
  </style>
</head>
<body>

<header>
  <div class="header-top">
    <h2>Mi Bodega</h2>
    <div class="rate-box">
      <span>Tasa BCV:</span>
      <input type="number" id="tasaInput" value="854.46" step="0.01" oninput="actualizarPrecios()">
    </div>
  </div>
  <input type="text" id="searchInput" class="search-box" placeholder="Buscar producto..." onkeyup="filtrar()">
</header>

<nav>
  <button class="tab-btn active" onclick="filtrarCategoria('todas', this)">Todos</button>
  <button class="tab-btn" onclick="filtrarCategoria('viveres', this)">Víveres</button>
  <button class="tab-btn" onclick="filtrarCategoria('charcuteria', this)">Charcutería</button>
  <button class="tab-btn" onclick="filtrarCategoria('chucheria', this)">Chucherías</button>
</nav>

<div class="container">
  <div id="gridProductos" class="grid"></div>
</div>

<script>
  const productos = [
    // Charcutería
    { nombre: "Mortadela 1kg", bs: 3630, cat: "charcuteria" },
    { nombre: "Mortadela 1/2kg", bs: 2120, cat: "charcuteria" },
    { nombre: "Muslo", bs: 2980, cat: "charcuteria" },
    { nombre: "Pechuga", bs: 4250, cat: "charcuteria" },
    { nombre: "Pollo Entero", bs: 2720, cat: "charcuteria" },
    { nombre: "Pollo Picado", bs: 3210, cat: "charcuteria" },
    { nombre: "Salchicha Puro Lomo", bs: 4150, cat: "charcuteria" },
    { nombre: "Queso Llanero", bs: 6450, cat: "charcuteria" },
    { nombre: "Huevos (Cartón)", bs: 5950, cat: "charcuteria" },
    { nombre: "Huevos (1/2 Cartón)", bs: 2975, cat: "charcuteria" },

    // Chucherías
    { nombre: "Chicles Agogó", bs: 300, cat: "chucheria" },
    { nombre: "Azucaradas", bs: 420, cat: "chucheria" },
    { nombre: "Barrilete", bs: 90, cat: "chucheria" },
    { nombre: "Bianchi Chocolate", bs: 410, cat: "chucheria" },
    { nombre: "Caramelos", bs: 20, cat: "chucheria" },
    { nombre: "Cheese Tris / Chistri", bs: 890, cat: "chucheria" },
    { nombre: "Chicles", bs: 50, cat: "chucheria" },
    { nombre: "Chupetas", bs: 120, cat: "chucheria" },
    { nombre: "Cocosette", bs: 980, cat: "chucheria" },
    { nombre: "Doritos", bs: 1100, cat: "chucheria" },
    { nombre: "Flips", bs: 680, cat: "chucheria" },
    { nombre: "Frustiaros", bs: 420, cat: "chucheria" },
    { nombre: "Galleta Club Social", bs: 320, cat: "chucheria" },
    { nombre: "Galleta Crack-It", bs: 200, cat: "chucheria" },
    { nombre: "Galleta María", bs: 240, cat: "chucheria" },
    { nombre: "Galleta Oreo", bs: 520, cat: "chucheria" },
    { nombre: "Galleta Soda", bs: 220, cat: "chucheria" },
    { nombre: "Galleta Tip-Top", bs: 200, cat: "chucheria" },
    { nombre: "Galletas Minis", bs: 180, cat: "chucheria" },
    { nombre: "Mega Bit", bs: 540, cat: "chucheria" },
    { nombre: "Nucita", bs: 270, cat: "chucheria" },
    { nombre: "Palito", bs: 540, cat: "chucheria" },
    { nombre: "Riko Pepito", bs: 710, cat: "chucheria" },
    { nombre: "Samy", bs: 250, cat: "chucheria" },
    { nombre: "Tostón Pequeño", bs: 490, cat: "chucheria" },

    // Víveres
    { nombre: "Arroz Premium 900g", bs: 1430, cat: "viveres" },
    { nombre: "Cigarro (Unidad)", bs: 90, cat: "viveres" },
    { nombre: "Cigarro Viceroy Caja", bs: 1700, cat: "viveres" },
    { nombre: "Chimó Apureñito", bs: 580, cat: "viveres" },
    { nombre: "Crema Dental", bs: 720, cat: "viveres" },
    { nombre: "Cubito", bs: 380, cat: "viveres" },
    { nombre: "Desodorante", bs: 520, cat: "viveres" },
    { nombre: "Harina PAN", bs: 1180, cat: "viveres" },
    { nombre: "Jugo Justy 1.5L", bs: 1120, cat: "viveres" },
    { nombre: "Jugo Justy 400ml", bs: 410, cat: "viveres" },
    { nombre: "Mantequilla 250g", bs: 1580, cat: "viveres" },
    { nombre: "Papel Alisof", bs: 630, cat: "viveres" },
    { nombre: "Pasta Codito", bs: 1720, cat: "viveres" },
    { nombre: "Pasta Larga", bs: 1470, cat: "viveres" },
    { nombre: "Refresco 1L", bs: 840, cat: "viveres" },
    { nombre: "Refresco 2L", bs: 1050, cat: "viveres" },
    { nombre: "Sal 500g", bs: 610, cat: "viveres" },
    { nombre: "Sardinas", bs: 780, cat: "viveres" },
    { nombre: "Toallas Sanitarias", bs: 1310, cat: "viveres" },
    { nombre: "Vaso de Aceite", bs: 320, cat: "viveres" },
    { nombre: "Salsa de Tomate Grd", bs: 1980, cat: "viveres" },
    { nombre: "Salsa de Tomate Pq", bs: 1510, cat: "viveres" },
    { nombre: "Suavizante", bs: 910, cat: "viveres" },
    { nombre: "Jabón Alive", bs: 1520, cat: "viveres" },
    { nombre: "Presto Barba", bs: 460, cat: "viveres" },
    { nombre: "Shampoo Sobre", bs: 660, cat: "viveres" },
    { nombre: "Mayonesa Pq Mavesa", bs: 1840, cat: "viveres" }
  ];

  let catActual = 'todas';

  function render(lista) {
    const grid = document.getElementById('gridProductos');
    const tasa = parseFloat(document.getElementById('tasaInput').value) || 1;
    grid.innerHTML = '';
    
    lista.forEach(p => {
      const usd = (p.bs / tasa).toFixed(2);
      grid.innerHTML += `
        <div class="card">
          <h4>${p.nombre}</h4>
          <div>
            <div class="price-bs">${p.bs.toLocaleString('es-VE')} Bs.</div>
            <div class="price-usd">$${usd} USD</div>
          </div>
        </div>
      `;
    });
  }

  function actualizarPrecios() {
    filtrar();
  }

  function filtrarCategoria(cat, btn) {
    catActual = cat;
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    filtrar();
  }

  function filtrar() {
    const query = document.getElementById('searchInput').value.toLowerCase();
    const filtrados = productos.filter(p => {
      const coincideCat = catActual === 'todas' || p.cat === catActual;
      const coincideNombre = p.nombre.toLowerCase().includes(query);
      return coincideCat && coincideNombre;
    });
    render(filtrados);
  }

  render(productos);
</script>
</body>
</html>
