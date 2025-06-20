---
toc: false
sql:
  precios: data/precios.csv
---

<div class="precios">
  <h1>Precios de productos al consumidor en Gran Buenos Aires,  Argentina</h1>
</div>

```sql id=productos 
SELECT DISTINCT producto
FROM precios
```


```js
// Creamos un selector para elegir la variable a graficar.
const selectProducto = view(Inputs.select([...productos].map(d => d.producto), {
  label: "Producto",
  //value: productos[0]  // Por defecto, la primera variable de la lista.
}));
```

```sql id=dataPrecios
SELECT *
FROM precios
WHERE producto = ${selectProducto}
```


```js
const unidad = [...dataPrecios][0].unidad
```


```js
// Creamos un selector para elegir la variable a graficar.
const escalaLineal = view(Inputs.toggle({label: "Usar escala lineal", value: false}));
```

  <div class="card">${ chart }
  <div>
  Fuente de datos: Instituto Nacional de Estadística y Censos. (2025). Índice de precios al consumidor. Precios promedio de un conjunto de elementos de la canasta del IPC, según regiones (Junio de 2017–mayo de 2025) y para el GBA (Abril de 2016–mayo de 2025) [Archivo Excel]. Recuperado el 20 de junio de 2025, de <a href="https://www.indec.gob.ar/ftp/cuadros/economia/sh_ipc_precios_promedio.xls">sh_ipc_precios_promedio.xls</a></div>
  </div>
</div>


```js
const chart = (() => {
  const dataPlot = [...dataPrecios];

  return Plot.plot({
    width,
    title: `Precio "${selectProducto} (${unidad})" en Gran Buenos Aires`,
    x: { grid: true },
    y: { type: escalaLineal ? "linear" : "log", label: `Precio (escala ${escalaLineal ? "lineal" : "logarítmica"})`, grid: true, tickFormat: "$,d" },
    marginLeft: 80,
    marks: [
      Plot.ruleY([0]),
      Plot.lineY(dataPlot, {
        x: (d) => moment.utc(d["fecha"], "YYYY-MM").toDate(),
        y: "precio",
        stroke: (d) => "precio"
      }),
      Plot.dot(dataPlot, {
        x: (d) => moment.utc(d["fecha"], "YYYY-MM").toDate(),
        y: "precio",
        fill: (d) => "precio",
        r: 2,
        title: (d) =>
          `${selectProducto}\n${moment(d.fecha).format(
            "D MMM YYYY"
          )}\n${d3.format("$")(d.precio)}`,
        tip: true
      })
    ]
  });
})()
```


<style>

.precios_ {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: var(--sans-serif);
  margin: 2rem 0 2rem;
  text-wrap: balance;
  text-align: center;
}

.precios_ h1 {
  margin: 1rem 0;
  padding: 1rem 0;
  max-width: none;
  font-size: 2em;
  font-weight: 900;
  line-height: 1;
  background: linear-gradient(30deg, var(--theme-foreground-focus), currentColor);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.hero h2 {
  margin: 0;
  max-width: 34em;
  font-size: 20px;
  font-style: initial;
  font-weight: 500;
  line-height: 1.5;
  color: var(--theme-foreground-muted);
}

@media (min-width: 640px) {
  .hero_ h1 {
    font-size: 90px;
  }
}

</style>


```js
// **14. Importar Moment.js para las fechas**
import moment from "npm:moment";
```
