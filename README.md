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
      background: #f3f4f6;
      color: #1f2937;
    }

    .container {
      max-width: 700px;
      margin: 40px auto;
      padding: 20px;
    }

    .card {
      background: white;
      padding: 30px;
      border-radius: 16px;
      box-shadow: 0 8px 30px rgba(0,0,0,.08);
    }

    h1 {
      text-align: center;
      color: #2563eb;
      margin-top: 0;
    }

    .subtitle {
      text-align: center;
      color: #6b7280;
      margin-bottom: 30px;
    }

    .products {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 20px;
    }

    .product {
      border: 1px solid #e5e7eb;
      border-radius: 12px;
      padding: 20px;
    }

    .product h2 {
      margin-top: 0;
    }

    label {
      display: block;
      margin-top: 14px;
      margin-bottom: 6px;
      font-weight: bold;
    }

    input,
    select {
      width: 100%;
      padding: 11px;
      border: 1px solid #d1d5db;
      border-radius: 8px;
      font-size: 16px;
    }

    button {
      width: 100%;
      margin-top: 25px;
      padding: 14px;
      border: 0;
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
      display: none;
      margin-top: 25px;
      padding: 22px;
      border-radius: 12px;
      background: #eff6ff;
      text-align: center;
    }

    .winner {
      font-size: 22px;
      font-weight: bold;
      color: #15803d;
      margin-bottom: 15px;
    }

    .price {
      margin: 8px 0;
      font-size: 17px;
    }

    .explanation {
      margin-top: 15px;
      color: #4b5563;
    }

    .error {
      color: #b91c1c;
      font-weight: bold;
    }

    @media (max-width: 600px) {
      .products {
        grid-template-columns: 1fr;
      }

      .container {
        margin: 10px auto;
      }
    }
  </style>
</head>

<body>

<div class="container">

  <div class="card">
    <div class="subtitle">
      Descubre cuál producto te da más por tu dinero.
    </div>

    <div class="products">

      <!-- PRODUCTO 1 -->
      <div class="product">

        <h2>Producto 1</h2>

        <label>Precio ($)</label>
        <input
          type="number"
          id="precio1"
          step="0.01"
          placeholder="p.ej. 30"
        >

        <label>Cantidad</label>
        <input
          type="number"
          id="cantidad1"
          step="0.01"
          placeholder="p.ej. 50"
        >

        <label>Unidad</label>
        <select id="unidad1">
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
      <div class="product">

        <h2>Producto 2</h2>

        <label>Precio ($)</label>
        <input
          type="number"
          id="precio2"
          step="0.01"
          placeholder="p.ej. 70"
        >

        <label>Cantidad</label>
        <input
          type="number"
          id="cantidad2"
          step="0.01"
          placeholder="p.ej. 100"
        >

        <label>Unidad</label>
        <select id="unidad2">
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

    <button onclick="comparar()">
      Comparar precios
    </button>

    <div id="resultado"></div>

  </div>

</div>


<script>

//
// Factores de conversión.
// Todo se convierte internamente a una unidad base.
//

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


// Determina a qué grupo pertenece una unidad.
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


// Convierte cualquier cantidad a la unidad base.
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


//
// Decide automáticamente qué unidad es más fácil
// para mostrar el resultado.
//

function elegirUnidad(precio, cantidadBase, tipo) {

  if (tipo === "peso") {

    const opciones = [
      { unidad: "g", factor: 1 },
      { unidad: "kg", factor: 1000 }
    ];

    return elegirMejorUnidad(precio, cantidadBase, opciones);
  }


  if (tipo === "volumen") {

    const opciones = [
      { unidad: "ml", factor: 1 },
      { unidad: "L", factor: 1000 }
    ];

    return elegirMejorUnidad(precio, cantidadBase, opciones);
  }


  return {
    unidad: "unidad",
    precio: precio / cantidadBase
  };
}


//
// Escoge una unidad cuyo precio por unidad
// sea fácil de interpretar.
//

function elegirMejorUnidad(precio, cantidadBase, opciones) {

  let mejor = null;

  for (const opcion of opciones) {

    const cantidad = cantidadBase / opcion.factor;
    const precioUnidad = precio / cantidad;

    // Preferimos precios entre $0.01 y $100.
    const esComodo =
      precioUnidad >= 0.01 &&
      precioUnidad <= 100;

    if (esComodo) {

      // Preferimos la unidad más grande posible
      // que siga teniendo un precio razonable.
      mejor = {
        unidad: opcion.unidad,
        precio: precioUnidad
      };
    }
  }

  // Si ninguna unidad queda en un rango cómodo,
  // usamos la unidad más pequeña.
  if (!mejor) {

    const opcion = opciones[0];

    mejor = {
      unidad: opcion.unidad,
      precio: precio / (cantidadBase / opcion.factor)
    };
  }

  return mejor;
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


function comparar() {

  const precio1 = parseFloat(
    document.getElementById("precio1").value
  );

  const cantidad1 = parseFloat(
    document.getElementById("cantidad1").value
  );

  const unidad1 =
    document.getElementById("unidad1").value;


  const precio2 = parseFloat(
    document.getElementById("precio2").value
  );

  const cantidad2 = parseFloat(
    document.getElementById("cantidad2").value
  );

  const unidad2 =
    document.getElementById("unidad2").value;


  const resultado =
    document.getElementById("resultado");


  // Validación

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
        ⚠️ Introduce precios y cantidades válidos.
      </div>
    `;

    return;
  }


  const tipo1 = tipoUnidad(unidad1);
  const tipo2 = tipoUnidad(unidad2);


  // No podemos comparar, por ejemplo,
  // gramos contra mililitros.

  if (tipo1 !== tipo2) {

    resultado.style.display = "block";

    resultado.innerHTML = `
      <div class="error">
        ⚠️ No se pueden comparar esas unidades.
      </div>

      <p>
        Por ejemplo, no es posible comparar directamente
        gramos con mililitros porque miden cosas diferentes.
      </p>
    `;

    return;
  }


  // Convertimos ambas cantidades a la unidad base.

  const cantidadBase1 =
    convertir(cantidad1, unidad1);

  const cantidadBase2 =
    convertir(cantidad2, unidad2);


  //
  // Precio real por unidad base.
  // Esto sirve para determinar cuál es más barato.
  //

  const precioBase1 =
    precio1 / cantidadBase1;

  const precioBase2 =
    precio2 / cantidadBase2;


  //
  // Elegimos automáticamente una unidad
  // fácil de entender.
  //

  const resultado1 =
    elegirUnidad(
      precio1,
      cantidadBase1,
      tipo1
    );

  const resultado2 =
    elegirUnidad(
      precio2,
      cantidadBase2,
      tipo2
    );


  //
  // Para que ambos precios puedan compararse
  // directamente, usamos la misma unidad.
  //

  let unidadMostrar;
  let factorMostrar;


  if (tipo1 === "peso") {

    // Buscamos una unidad cómoda para ambos productos.

    const opciones = [
      { unidad: "kg", factor: 1000 },
      { unidad: "g", factor: 1 },
      { unidad: "mg", factor: 0.001 }
    ];

    const candidatos = opciones.filter(opcion => {

      const p1 =
        precioBase1 * opcion.factor;

      const p2 =
        precioBase2 * opcion.factor;

      return (
        p1 >= 0.01 &&
        p1 <= 100 &&
        p2 >= 0.01 &&
        p2 <= 100
      );

    });

    const elegido =
      candidatos.length > 0
        ? candidatos[0]
        : opciones[opciones.length - 1];

    unidadMostrar = elegido.unidad;
    factorMostrar = elegido.factor;

  } else if (tipo1 === "volumen") {

    const opciones = [
      { unidad: "L", factor: 1000 },
      { unidad: "ml", factor: 1 }
    ];

    const candidatos = opciones.filter(opcion => {

      const p1 =
        precioBase1 * opcion.factor;

      const p2 =
        precioBase2 * opcion.factor;

      return (
        p1 >= 0.01 &&
        p1 <= 100
      ) &&
      (
        p2 >= 0.01 &&
        p2 <= 100
      );

    });

    const elegido =
      candidatos.length > 0
        ? candidatos[0]
        : opciones[opciones.length - 1];

    unidadMostrar = elegido.unidad;
    factorMostrar = elegido.factor;

  } else {

    unidadMostrar = "unidad";
    factorMostrar = 1;
  }


  const costo1 =
    precioBase1 * factorMostrar;

  const costo2 =
    precioBase2 * factorMostrar;


  //
  // Determinar ganador.
  //

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


  //
  // Mostrar resultado.
  //

  resultado.style.display = "block";


  if (ganador === "Ambos productos") {

    resultado.innerHTML = `

      <div class="winner">
        🤝 Los dos tienen el mismo valor
      </div>

      <div class="price">
        Ambos: <strong>
          $${formatoPrecio(costo1)}
        </strong>
        por ${unidadMostrar}
      </div>

    `;

    return;
  }


  resultado.innerHTML = `

    <div class="winner">
      🏆 ${ganador} es la mejor compra
    </div>

    <div class="price">
      Producto 1:
      <strong>
        $${formatoPrecio(costo1)}
      </strong>
      por ${unidadMostrar}
    </div>

    <div class="price">
      Producto 2:
      <strong>
        $${formatoPrecio(costo2)}
      </strong>
      por ${unidadMostrar}
    </div>

    <div class="explanation">
      ${ganador} cuesta aproximadamente
      <strong>${diferencia.toFixed(1)}% menos</strong>
      por ${unidadMostrar}.
    </div>

  `;
}

</script>

</body>
</html>
