# Script de limpieza — Lenguaje M (Editor Avanzado)

Código M completo y funcional utilizado en Power Query para limpiar los datos de ventas de TechStore, escrito directamente en el Editor Avanzado.

```m
let
    Origen = Table.FromRows(Json.Document(Binary.Decompress(Binary.FromText("XZBBasMwEEWvMmgdB0m2WndpJ4GWNBAaly5MFoqihcCWjGxBr5Mz9Ai+WEcOhai7mQeP/2faljCyIvAuh8kNcPQOmAAkG9cPYZLKzD8WV8YpXVOKE6e8yCjLqCDnVUs4ooMLo4Y3K7v51l+8UQ6hVEqPzhs3Rqn8J5eLnMfoRqtOXh0ctJpv9i4fPz53dYXDi0hFxhexWFKtmZyHYh/7qrRvIZK6PKP5IoqYWAXsGDrp9Qh1g6QKV+PuV6YWo4v1hOh02sLue9Le4ouaGh5bsjzx8r/nPCP60hcle3jdxpzHn5QideJp518=", BinaryEncoding.Base64), Compression.Deflate)), let _t = ((type nullable text) meta [Serialized.Text = true]) in type table [id_venta = _t, nombre_producto = _t, categoria = _t, precio = _t, fecha_venta = _t]),
    // Paso 2: Saco los espacios del inicio y del final de nombre_producto
    // porque " Laptop Pro 15 " y "Laptop Pro 15" tienen que contar como el mismo producto
    LimpiarEspacios = Table.TransformColumns(Origen, {{"nombre_producto", Text.Trim, type text}}),
    // Paso 3: Paso categoria a Title Case para que "computación" y "Computación"
    // queden escritas igual y cuenten como una sola categoría
    EstandarizarCategoria = Table.TransformColumns(LimpiarEspacios, {{"categoria", Text.Proper, type text}}),
    // Paso 4: Me quedo solo con las filas que NO son de prueba.
    // Va después del Paso 3 porque M distingue mayúsculas y "PRUEBA" ya pasó a ser "Prueba"
    EliminarPruebas = Table.SelectRows(EstandarizarCategoria, each [categoria] <> "Prueba"),
    // Paso 5: Pongo el tipo correcto a cada columna (todo venía como texto)
    // "en-US" para que el punto de 1200.00 se lea como decimal
    TiparColumnas = Table.TransformColumnTypes(EliminarPruebas, {
        {"id_venta", Int64.Type},
        {"nombre_producto", type text},
        {"categoria", type text},
        {"precio", type number},
        {"fecha_venta", type date}
    }, "en-US")
in
    TiparColumnas
```
