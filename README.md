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
      padding: 16px;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Arial, sans-serif;
      background: #f5f7fb;
      color: #1f2937;
    }

    .container {
      width: 100%;
      max-width: 650px;
      margin: 0 auto;
    }

    .card {
      background: #ffffff;
      border-radius: 18px;
      padding: 22px 18px;
      box-shadow: 0 4px 20px rgba(0, 0, 0, 0.07);
    }

    h1 {
      margin: 0;
      text-align: center;
      font-size: 26px;
      color: #2563eb;
    }

    .subtitle {
      text-align: center;
      color: #6b7280;
      font-size: 14px;
      margin: 8px 0 24px;
      line-height: 1.4;
    }

    .products {
      display: flex;
      flex-direction: column;
      gap: 14px;
    }

    .product {
      border: 1px solid #e5e7eb;
      border-radius: 14px;
      padding: 16px;
      background: #fafafa;
    }

    .product h2 {
      margin: 0 0 14px;
      font-size: 19px;
    }

    label {
      display: block;
      margin: 12px 0 5px;
      font-size: 14px;
      font-weight: 600;
      color: #374151;
    }

    input,
    select {
      width: 100%;
      height: 48px;
      padding: 0 12px;
      border: 1px solid #d1d5db;
      border-radius: 9px;
      background: white;
      color: #111827;
      font-size: 16px;
      outline: none;
    }

    input:focus,
    select:focus {
      border-color: #2563eb;
      box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.12);
    }

    button {
      width: 100%;
      height: 52px;
      margin-top: 18px;
      border: none;
      border-radius: 10px;
      background: #2563eb;
      color: white;
      font-size: 17px;
      font-weight: 700;
      cursor: pointer;
    }

    button:active {
      transform: scale(0.98);
    }

    #resultado {
      display: none;
      margin-top: 18px;
      padding: 18px;
      border-radius: 14px;
      background: #eff6ff;
      text-align: center;
    }

    .winner {
      font-size: 20px;
      font-weight: 700;
      color: #15803d;
      margin-bottom: 14px;
    }

    .price {
      background: white;
      border-radius: 9px;
      padding: 10px;
      margin: 7px 0;
      font-size: 15px;
    }

    .explanation {
      margin-top: 14px;
      font-size: 14px;
      line-height: 1.4;
      color: #4b5563;
    }

    .error {
      color: #b91c1c;
      font-weight: 600;
      line-height: 1.4;
    }

    @media (min-width: 600px) {

      body {
        padding: 30px 20px;
      }

      .card {
        padding: 30px;
      }

      .products {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 18px;
      }

      h1 {
        font-size: 30px;
      }
    }
  </style>
</head>

<body>

<div class="container">

  <div class="card">
    <div class="subtitle">
      Compara dos productos y descubre cuál te ofrece más por tu dinero.
    </div>

    <div class="products">
      <!-- PRODUCTO 1 -->
      <div class="product">

        <h2>Producto 1</h2>

        <label for="precio1">Precio</label>

        <input
          type="number"
          id="precio1"
          step="0.01"
          min="0"
          inputmode="decimal"
          placeholder="p.ej. 30"
        >


        <label for="cantidad1">Cantidad</label>

        <input
          type="number"
          id="cantidad1"
          step="0.01"
          min="0"
          inputmode="decimal"
          placeholder="p.ej. 50"
        >


        <label for="unidad1">Unidad</label>

        <select id="unidad1">

          <option value="g">gramos (g)</option>

          <option value="kg">kilogramos (kg)</option>

          <option value="ml">mililitros (ml)</option>

          <option value="l">litros (L)</option>

          <option value="oz">onzas (oz)</option>

          <option value="lb">libras (lb)</option>

          <option value="unidad">unidades</option>

        </select>

      </div>


      <!-- PRODUCTO 2 -->

      <div class="product">

        <h2>Producto 2</h2>

        <label for="precio2">Precio</label>

        <input
          type="number"
          id="precio2"
          step="0.01"
          min="0"
          inputmode="decimal"
          placeholder="p.ej. 70"
        >


        <label for="cantidad2">Cantidad</label>

        <input
          type="number"
          id="cantidad2"
          step="0.01"
          min="0"
          inputmode="decimal"
          placeholder="p.ej. 100"
        >


        <label for="unidad2">Unidad</label>

        <select id="unidad2">

          <option value="g">gramos (g)</option>

          <option value="kg">kilogramos (kg)</option>

          <option value="ml">mililitros (ml)</option>

          <option value="l">litros (L)</option>

          <option value="oz">onzas (oz)</option>

          <option value="lb">libras (lb)</option>

          <option value="unidad">unidades</option>

        </select>

      </div>

    </div>


    <button onclick="comparar()">
      Comparar precios
    </button>


    <div id="resultado"></div>

  </div>

</div>


<script>

const conversiones = {

  peso: {
    g: 1,
    kg: 1000,
    oz: 28.3495,
    lb: 453.592
  },

  volumen: {
    ml: 1,
    l: 1000
  }

};


function tipoUnidad(unidad) {

  if (conversiones.peso[unidad] !== undefined) {
    return "peso";
  }

  if (conversiones.volumen[unidad] !== undefined) {
    return "volumen";
  }

  if (unidad === "unidad") {
    return "unidad";
  }

  return null;
}


function convertir(cantidad, unidad) {

  const tipo = tipoUnidad(unidad);

  if (tipo === "peso") {
    return cantidad * conversiones.peso[unidad];
  }

  if (tipo === "volumen") {
    return cantidad * conversiones.volumen[unidad];
  }

  return cantidad;
}


function formatoPrecio(numero) {

  if (numero >= 1) {
    return numero.toFixed(2);
  }

  if (numero >= 0.01) {
    return numero.toFixed(2);
  }

  if (numero >= 0.001) {
    return numero.toFixed(3);
  }

  return numero.toFixed(4);
}


/*
 * Busca una unidad que produzca un precio
 * fácil de interpretar.
 */

function elegirUnidad(precioBase1, precioBase2, tipo) {

  let opciones;

  if (tipo === "peso") {

    opciones = [
      { unidad: "kg", factor: 1000 },
      { unidad: "g", factor: 1 }
    ];

  } else if (tipo === "volumen") {

    opciones = [
      { unidad: "L", factor: 1000 },
      { unidad: "ml", factor: 1 }
    ];

  } else {

    return {
      unidad: "unidad",
      factor: 1
    };
  }


  /*
   * Preferimos que ambos precios estén aproximadamente
   * entre $0.01 y $100 por unidad.
   */

  for (const opcion of opciones) {

    const p1 = precioBase1 * opcion.factor;
    const p2 = precioBase2 * opcion.factor;

    if (
      p1 >= 0.01 &&
      p1 <= 100 &&
      p2 >= 0.01 &&
      p2 <= 100
    ) {

      return opcion;
    }
  }


  /*
   * Si ninguna es cómoda, usamos gramos/ml.
   */

  return opciones[opciones.length - 1];
}


function comparar() {

  const precio1 =
    parseFloat(document.getElementById("precio1").value);

  const cantidad1 =
    parseFloat(document.getElementById("cantidad1").value);

  const unidad1 =
    document.getElementById("unidad1").value;


  const precio2 =
    parseFloat(document.getElementById("precio2").value);

  const cantidad2 =
    parseFloat(document.getElementById("cantidad2").value);

  const unidad2 =
    document.getElementById("unidad2").value;


  const resultado =
    document.getElementById("resultado");


  /*
   * Validación
   */

  if (
    isNaN(precio1) ||
    isNaN(cantidad1) ||
    isNaN(precio2) ||
    isNaN(cantidad2) ||
    precio1 < 0 ||
    precio2 < 0 ||
    cantidad1 <= 0 ||
    cantidad2 <= 0
  ) {

    resultado.style.display = "block";

    resultado.innerHTML = `
      <div class="error">
        ⚠️ Introduce correctamente los precios y cantidades.
      </div>
    `;

    return;
  }


  const tipo1 = tipoUnidad(unidad1);
  const tipo2 = tipoUnidad(unidad2);


  /*
   * No podemos comparar peso con volumen.
   */

  if (tipo1 !== tipo2) {

    resultado.style.display = "block";

    resultado.innerHTML = `
      <div class="error">
        ⚠️ Las unidades no son compatibles.
      </div>

      <p>
        Por ejemplo, no se puede comparar directamente
        gramos con mililitros.
      </p>
    `;

    return;
  }


  /*
   * Convertimos las cantidades a una unidad común.
   */

  const cantidadBase1 =
    convertir(cantidad1, unidad1);

  const cantidadBase2 =
    convertir(cantidad2, unidad2);


  /*
   * Precio por unidad base.
   */

  const precioBase1 =
    precio1 / cantidadBase1;

  const precioBase2 =
    precio2 / cantidadBase2;


  /*
   * Elegimos una unidad fácil de entender.
   */

  const unidadElegida =
    elegirUnidad(
      precioBase1,
      precioBase2,
      tipo1
    );


  const costo1 =
    precioBase1 * unidadElegida.factor;

  const costo2 =
    precioBase2 * unidadElegida.factor;


  /*
   * Determinamos el ganador.
   */

  let ganador;
  let diferencia;


  if (costo1 < costo2) {

    ganador = "Producto 1";

    diferencia =
      ((costo2 - costo1) / costo2) * 100;

  } else if (costo2 < costo1) {

    ganador = "Producto 2";

    diferencia =
      ((costo1 - costo2) / costo1) * 100;

  } else {

    ganador = "Ambos productos";

    diferencia = 0;
  }


  resultado.style.display = "block";


  /*
   * Mostrar empate.
   */

  if (ganador === "Ambos productos") {

    resultado.innerHTML = `

      <div class="winner">
        🤝 Los dos tienen el mismo valor
      </div>

      <div class="price">
        <strong>
          $${formatoPrecio(costo1)}
        </strong>
        por ${unidadElegida.unidad}
      </div>

    `;

    return;
  }


  /*
   * Mostrar ganador.
   */

  resultado.innerHTML = `

    <div class="winner">
      🏆 ${ganador} es la mejor compra
    </div>

    <div class="price">
      Producto 1:
      <strong>
        $${formatoPrecio(costo1)}
      </strong>
      por ${unidadElegida.unidad}
    </div>

    <div class="price">
      Producto 2:
      <strong>
        $${formatoPrecio(costo2)}
      </strong>
      por ${unidadElegida.unidad}
    </div>

    <div class="explanation">
      ${ganador} cuesta aproximadamente
      <strong>${diferencia.toFixed(1)}% menos</strong>
      por ${unidadElegida.unidad}.
    </div>

  `;
}

</script>

</body>
</html>
