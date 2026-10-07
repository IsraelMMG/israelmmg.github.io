<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Comparador de precios</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #f4f6f8;
      color: #222;
    }

    .contenedor {
      max-width: 700px;
      margin: 40px auto;
      padding: 20px;
    }

    .tarjeta {
      background: white;
      padding: 30px;
      border-radius: 16px;
      box-shadow: 0 5px 20px rgba(0,0,0,0.08);
    }

    h1 {
      text-align: center;
      margin-top: 0;
      color: #2563eb;
    }

    .productos {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 20px;
    }

    .producto {
      border: 1px solid #ddd;
      border-radius: 12px;
      padding: 20px;
    }

    .producto h2 {
      margin-top: 0;
    }

    label {
      display: block;
      margin-top: 15px;
      margin-bottom: 5px;
      font-weight: bold;
    }

    input, select {
      width: 100%;
      padding: 12px;
      border: 1px solid #ccc;
      border-radius: 8px;
      font-size: 16px;
    }

    button {
      width: 100%;
      margin-top: 25px;
      padding: 14px;
      border: none;
      border-radius: 8px;
      background: #2563eb;
      color: white;
      font-size: 17px;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      background: #1d4ed8;
    }

    #resultado {
      margin-top: 25px;
      padding: 20px;
      border-radius: 10px;
      background: #eef6ff;
      display: none;
      text-align: center;
    }

    .ganador {
      color: #15803d;
      font-size: 22px;
      font-weight: bold;
    }

    .precioUnidad {
      margin: 8px 0;
      font-size: 17px;
    }

    @media (max-width: 600px) {
      .productos {
        grid-template-columns: 1fr;
      }

      .contenedor {
        margin: 10px auto;
      }
    }
  </style>
</head>

<body>

<div class="contenedor">
  <div class="tarjeta">

    <h1>🛒 Comparador de precios</h1>

    <p style="text-align:center;">
      Descubre cuál producto ofrece más cantidad por tu dinero.
    </p>

    <div class="productos">

      <!-- PRODUCTO 1 -->
      <div class="producto">
        <h2>Producto 1</h2>

        <label>Precio ($)</label>
        <input type="number" id="precio1" placeholder="Ej. 30" step="0.01">

        <label>Cantidad</label>
        <input type="number" id="cantidad1" placeholder="Ej. 50" step="0.01">

        <label>Unidad</label>
        <select id="unidad1">
          <option value="mg">mg</option>
          <option value="g">g</option>
          <option value="kg">kg</option>
          <option value="ml">ml</option>
          <option value="l">L</option>
          <option value="oz">oz</option>
          <option value="lb">lb</option>
          <option value="unidad">unidad</option>
        </select>
      </div>

      <!-- PRODUCTO 2 -->
      <div class="producto">
        <h2>Producto 2</h2>

        <label>Precio ($)</label>
        <input type="number" id="precio2" placeholder="Ej. 70" step="0.01">

        <label>Cantidad</label>
        <input type="number" id="cantidad2" placeholder="Ej. 100" step="0.01">

        <label>Unidad</label>
        <select id="unidad2">
          <option value="mg">mg</option>
          <option value="g">g</option>
          <option value="kg">kg</option>
          <option value="ml">ml</option>
          <option value="l">L</option>
          <option value="oz">oz</option>
          <option value="lb">lb</option>
          <option value="unidad">unidad</option>
        </select>
      </div>

    </div>

    <button onclick="comparar()">Comparar precios</button>

    <div id="resultado"></div>

  </div>
</div>


<script>

function comparar() {

  const precio1 = parseFloat(document.getElementById("precio1").value);
  const cantidad1 = parseFloat(document.getElementById("cantidad1").value);
  const unidad1 = document.getElementById("unidad1").value;

  const precio2 = parseFloat(document.getElementById("precio2").value);
  const cantidad2 = parseFloat(document.getElementById("cantidad2").value);
  const unidad2 = document.getElementById("unidad2").value;

  const resultado = document.getElementById("resultado");

  if (
    isNaN(precio1) ||
    isNaN(cantidad1) ||
    isNaN(precio2) ||
    isNaN(cantidad2) ||
    cantidad1 <= 0 ||
    cantidad2 <= 0
  ) {
    resultado.style.display = "block";
    resultado.innerHTML = "⚠️ Introduce correctamente los precios y cantidades.";
    return;
  }

  /*
    Convertimos las cantidades a una unidad común.
    Esto permite comparar, por ejemplo:
    1 kg vs 500 g
    1000 mg vs 1 g
    1 L vs 500 ml
  */

  const peso = {
    mg: 0.001,
    g: 1,
    kg: 1000,
    oz: 28.3495,
    lb: 453.592
  };

  const volumen = {
    ml: 1,
    l: 1000
  };

  let costo1;
  let costo2;
  let unidadComparacion;

  // Comparación de peso
  if (peso[unidad1] && peso[unidad2]) {

    const cantidad1g = cantidad1 * peso[unidad1];
    const cantidad2g = cantidad2 * peso[unidad2];

    costo1 = precio1 / cantidad1g;
    costo2 = precio2 / cantidad2g;

    unidadComparacion = "g";

  // Comparación de volumen
  } else if (volumen[unidad1] && volumen[unidad2]) {

    const cantidad1ml = cantidad1 * volumen[unidad1];
    const cantidad2ml = cantidad2 * volumen[unidad2];

    costo1 = precio1 / cantidad1ml;
    costo2 = precio2 / cantidad2ml;

    unidadComparacion = "ml";

  // Unidades individuales
  } else if (unidad1 === "unidad" && unidad2 === "unidad") {

    costo1 = precio1 / cantidad1;
    costo2 = precio2 / cantidad2;

    unidadComparacion = "unidad";

  } else {

    resultado.style.display = "block";
    resultado.innerHTML =
      "⚠️ No se pueden comparar esas unidades entre sí.";
    return;
  }


  let mensaje = "";

  if (costo1 < costo2) {

    const ahorro = ((costo2 - costo1) / costo2) * 100;

    mensaje = `
      <div class="ganador">🏆 Producto 1 es mejor compra</div>

      <div class="precioUnidad">
        Producto 1: $${costo1.toFixed(4)} por ${unidadComparacion}
      </div>

      <div class="precioUnidad">
        Producto 2: $${costo2.toFixed(4)} por ${unidadComparacion}
      </div>

      <p>
        El Producto 1 es aproximadamente
        <strong>${ahorro.toFixed(1)}% más barato</strong>
        por unidad.
      </p>
    `;

  } else if (costo2 < costo1) {

    const ahorro = ((costo1 - costo2) / costo1) * 100;

    mensaje = `
      <div class="ganador">🏆 Producto 2 es mejor compra</div>

      <div class="precioUnidad">
        Producto 1: $${costo1.toFixed(4)} por ${unidadComparacion}
      </div>

      <div class="precioUnidad">
        Producto 2: $${costo2.toFixed(4)} por ${unidadComparacion}
      </div>

      <p>
        El Producto 2 es aproximadamente
        <strong>${ahorro.toFixed(1)}% más barato</strong>
        por unidad.
      </p>
    `;

  } else {

    mensaje = `
      <div class="ganador">🤝 Los dos tienen el mismo precio por unidad</div>

      <div class="precioUnidad">
        Ambos: $${costo1.toFixed(4)} por ${unidadComparacion}
      </div>
    `;
  }

  resultado.style.display = "block";
  resultado.innerHTML = mensaje;
}

</script>

</body>
</html>
