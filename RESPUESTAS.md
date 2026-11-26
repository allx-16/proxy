# Respuestas – Patrón Proxy

## Salida obtenida

```
Application started.
Proxy created, but RealImage not loaded yet.
Loading image (very expensive operation): photo.jpg
Displaying image: photo.jpg
Second call:
Displaying image: photo.jpg
```

## Pruebas

- **Sin Proxy:** con `new RealImage("photo.jpg")`, el mensaje "Loading image..." aparece en cuanto se crea el objeto, aunque nunca se llame a `display()`.
- **Con Proxy:** "Loading image..." aparece solo en la primera llamada a `display()`.
- **Múltiples llamadas:** la segunda llamada a `display()` ya no carga la imagen; solo la muestra.

## Preguntas de análisis

**Carga perezosa.** En el código original, el constructor de `RealImage` se ejecuta al crear el objeto con `new`, así que la imagen se carga aunque no se use. Con el Proxy, `RealImage` se construye hasta la primera llamada a `display()`, y solo una vez.

**Transparencia.** No. El cliente trabaja con la interfaz `Image`, y tanto `ProxyImage` como `RealImage` la implementan, así que puede usar cualquiera de las dos sin cambiar su código. El proxy decide internamente cuándo crear el objeto real.

**Rendimiento.** Se evita una operación costosa (leer de disco) cuando la imagen nunca se muestra. El arranque de la aplicación es más rápido, se usa menos memoria y la carga se hace una sola vez aunque se llame a `display()` muchas veces.

**Control de acceso.** Además de la carga perezosa, el proxy puede verificar permisos antes de mostrar la imagen (proxy de protección), guardar un registro de cada acceso (logging), guardar en caché imágenes ya cargadas para compartirlas, o limitar la cantidad de accesos.

**Principio SOLID.** Principalmente el **Principio de Sustitución de Liskov**: el proxy puede sustituir al objeto real porque ambos cumplen el mismo contrato. También el de **Inversión de Dependencias**, porque el cliente depende de la abstracción `Image` y no de una clase concreta, y el de **Abierto/Cerrado**, porque se agrega comportamiento sin modificar `RealImage`.

## Escenarios de extensión

- **Proxy de protección:** recibir un usuario o rol en el constructor y, en `display()`, comprobar sus permisos antes de crear o delegar a `RealImage`.
- **Proxy de caché:** usar un `Map<String, RealImage>` estático compartido para que dos proxies del mismo archivo reutilicen la misma imagen cargada.
- **Proxy de logging:** imprimir o guardar fecha, archivo y usuario cada vez que se llama a `display()`, y después delegar.
- **Proxy remoto:** el proxy se comunicaría por red (HTTP, RMI) con un servidor que tiene la imagen real; el cliente seguiría usando solo la interfaz `Image`.
