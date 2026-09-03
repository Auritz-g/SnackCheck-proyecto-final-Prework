# SnackCheck-proyecto-final-Prework
Proyecto final del Prework 4Geeks: SnackCheck. Incluye el diagrama inicial, el flujo de automatización en n8n, un README explicativo, registro de pruebas y CHANGELOG por etapas.




## 1. Resumen del Proyecto

### 🥑 SnackCheck

### 📌Resumen del proyecto

SnackCheck es una aplicación que se centra en la lectura de códigos de barra de productos de alimentación procesados que podemos encontrar en cualquier supermercado y devuelve un veredicto sobre si es saludable o no y lo acompaña con un consejo.

### 🎯Problema que resuelve

Hoy en día, cuando compramos en un supermercado, cada vez vemos más productos. Cada vez se parecen los unos a los otros, pero probablemente no tengan el mismo nivel en los nutrientes. Además de esto, los clientes y usuarios cada vez sienten una mayor necesidad de información y lo que antes era una tabla pequeña se ha vuelto en una lista de nutrientes e ingredientes muy larga y complicada de entender.


SnackCheck resuelve esta saturación resumiendo y simplificando esos datos:
 * Ofrece un resumen de la información de los nutrientes en tiempo real.
 * Compara los resultados de dos lecturas
   * El semáforo nutricional ofrece una lectura de los nutrientes clasificándolos en saludable, moderado y no saludable
   * El Nutri-Score, ofrece una lectura objetiva y los clasifica del 1 al 5 de saludable a no saludable.

### 👥 Público Objetivo

 * 🛒Compradores habituales de supermercado que buscan tomar decisiones informadas.
 * 🥗Personas interesadas en adoptar hábitos de alimentación más saludables. 
 * 🔎Consumidores que desean comprender mejor el impacto real de los ingredientes y nutrientes de los alimentos procesados que consumen. 

## 2. Cómo Usar

### 📩Formato de entrada

La entrada requerida de esta aplicación es el código numérico de 13 dígitos del código de barras del producto.

Cuando la entrada no es correcta y no se facilita una, se utiliza el nombre del producto o falta algún número, el flujo responde con errores con código (400, 404) respectivamente.

Estos mensajes de error facilitan la comprensión de la operación de la aplicación y facilita un ejemplo para que el usuario sepa cómo responder.

### 🧐Ejemplo de solicitud

```json
{
  "barcode": 3013048610348
}
```

### 📤Ejemplo de respuesta

Una vez introducido el código de barras correctamente, la aplicación puede ofrecer una respuesta como la siguiente.

🍜 *Veredicto de Snackcheck para Hacendado*

📊 *Datos nutricionales*
   *  *Niveles de Azúcar por 100 gr --> 26 gr*
   *  *Niveles de Sal por 100 gr --> 0.11 gr*
   *  *Niveles de Grasa por 100 gr --> 9.3 gr*
   *  *Niveles de energía- kcal por 100 gr --> 367 kcal*

📝 *Recomendación: veredicto final Moderado ✅. Evaluación: La evaluación del producto se basó en el Nutri-Score al ser la lectura más estricta. Este alimento ofrece un equilibrio aceptable entre nutrientes, con un contenido moderado de azúcares y grasas, y aporta fibra y vitaminas que lo hacen una opción razonable dentro de una dieta variada. ¡Aprovéchalo con moderación y disfruta sin culpa!*

*Generado el 2 de septiembre de 2026*

## 3. Funcionalidades

Las principales funcionalidades de la aplicación son 5:

🕖**1. Consulta de datos en tiempo real vía API;**
 * La aplicación lee el código de barras
   * Si existe busca los datos
   * Si no coincide o no se encuentra en la Base de datos, ofrece un mensaje de error.
   * En caso de que los datos nutricionales del producto no estén disponibles, dará otro mensaje de error, explicando qué ha fallado.

📖 **2. Doble lectura de los valores nutricionales del producto;**
 * La API ofrece datos de los nutrientes de los productos y una etiqueta Nutri-Score.
   * La aplicación, toma los valores que queremos analizar y mediante nodos de verificación y rangos de valores, la aplicación determina si es saludable, moderado o no saludable.
   * La etiqueta Nutri-Score, es una lectura de por sí, pero implantando unos rangos, determinados si los productos son saludables, moderados o no saludables.

🛡️ **3. Lógica estricta del veredicto final;**
 * Cuando las dos lecturas existen, el veredicto final, se determinará por la peor lectura de las dos.
   * Si una lectura dice saludable y la otra dice no saludable, el veredicto final dirá que el producto es no saludable.
   * En caso de que la API no pueda devolver un valor {a,b,c,d,e} de Nutri-Score y venga vacío o “null” o “unknown”, el veredicto será automáticamente lo que la lectura del semáforo nutricional diga.
     
💬 **4. Textos dinámicos según el veredicto final;**
 * El tono texto de salida de la aplicación, generado por IA que lee los veredictos una vez pasan por el procesamiento de datos y lógica, depende principalmente de cómo de saludable o no saludable sea el producto.
   * Si es *"saludable"* o *"moderado"*, el tono será más cercano, animado y energético, animando al consumidor.
   * Si es *"no saludable"*, el tono será cauteloso y con delicadeza para ser honesto, pero con tacto con el usuario.

❌ **5. Manejo de errores;**
 * El flujo de trabajo, cuenta con salidas tempranas del proceso cuando unos datos o condiciones no se cumplen permitiendo así un ahorro de tokens y facilitando al usuario ejemplos de como solucionar el error y dónde ha fallado.

## 4. Detalles Técnicos

### 🌐 1. APIs usadas
La API que hemos usado es open food facts.
 * Fuente de información que cuenta con una base de datos muy amplia y cuyos resultados acerca del valor nutricional son muy extendidos y precisos.
   * Facilita cada uno de los nutrientes del producto
     * En valores singulares y por 100gr,
   * Facilita también la etiqueta Nutri-Score del mismo

### 🔀 2. Flujo lógico

#### Puntos de decisión del flujo de trabajo

 * Validar que el código de barras existente y no se ha entregado vacío
 * Validar que el producto existe en la Base de datos de la API
 * Validar que la API haya devuelto los datos de los valores nutricionales del producto
 * Lectura de semaforo
   * Valorar los niveles de azúcar presentes
   * Valorar los niveles de sal presentes
   * Valorar los niveles de grasa presentes
 * Lectura de Nutri-Score
 * Verificar cuál de las dos lecturas se utiliza
   * Se utilizará la más negativa o la peor de las dos

#### El flujo de trabajo
* **Recepción del código del Webhook, inicio del flujo y validación de la entrada**
  * El Webhook inicia el flujo de trabajo mediante una petición HTTP post mediante el código de barras.
  * Al validar el código de barras, aseguramos que el flujo funcionará o que debería pasar al siguiente punto. En caso negativo, el flujo presenta al usuario con un mensaje de error con código 400.
 * **Consultamos la Integridad en la API**
   * Realizar una petición GET a la API
   * Validamos que el producto existe y verificamos que la API nos ha devuelto suficientes datos para generar un veredicto sobre el producto.
 * **Evaluación nutricional**
   * Determinamos los niveles de azúcar, sal y grasa presentes en el producto, pasando cada uno por verificaciones individuales con rangos de valores distintos, pero clasificándolos siempre en “bajo, medio o alto”.
   * Determinamos si el producto es saludable, moderado o no saludable, dependiendo de cuántos niveles de nutrientes son altos.
 * **Evaluación Nutri-Score**
   * Verificamos la existencia de un valor Nutri-Score
     * En caso negativo, su valor será “unknown” y el veredicto se realizará sobre la lectura de semáforo.
   * Se valida el nivel de Nutri-Score entre no saludable, moderado o alto.
 * **Lógica estricta de criterio**
   * Se selecciona la peor de las dos lecturas, significando esto que si una de las dos dice que es saludable y la otra no saludable, el veredicto final será no saludable.
 * **Respuesta final**
   * Mandamos un prompt a la IA, para que reúna la información necesaria y ofrezca un consejo y veredicto final.
   * Lo juntamos con un mensaje personalizado para darle estructura con el nombre y los valores devueltos por la API para ofrecer una respuesta más completa.

### 🧠 3. Integración de IA
La IA mejora la automatización, de forma que al facilitarle los valores de los nutrientes críticos, ofrecerle un prompt con qué es lo que queremos hacer, nos permite que el mensaje final sea más dinámico y se adapte a las lecturas ofrecidas.

## 5. Manejo de Errores

El flujo de trabajo está diseñado de forma que si un punto del mismo falla, sea al inicio y no al final, para evitar frustraciones al usuario.

Asimismo, facilitamos ejemplos de cómo solucionarlos, qué es lo que ha ido mal y un mensaje de respuesta de error breve en el que se detalla todo lo necesario para poder entenderlo;
 * *Status*
 * *Código*
 * *Mensaje del error*
 * *Campo donde ha fallado*
 * *La pista* 
 * *El ejemplo*

De este modo y para este flujo, los errores más frecuentes o posibles que se podrían encontrar serían los siguientes

### Los errores y como solucionarlos
 * **Error 400 (Petición mala)**
   * *Mensaje de error*
     * Código de barras no proporcionado o vacío
   * *Causa del problema*
     * La solicitud enviada por el usuario no incluye el código de barras de forma numérica o no la facilitó directamente.
   * *Solución*
     * Verificar que el cuerpo de la solicitud contenga un código numérico de 13 dígitos que responda a un campo con el nombre de “barcode”.

 * **Error 404 (No encontrado)**
   * *Mensaje de error*
     * Producto no encontrado en la Base de datos de la API
   * *Causa del problema*
     * El código de barras no entra dentro de los códigos de barra que tiene registrados la API Open food facts
   * *Solución*
     * Verifica que los 13 dígitos del código de barras se encuentren en orden y en caso positivo, probar con un código de un producto distinto.

 * **Error 422 (Entidad no procesable)**
   * *Mensaje de error*
     * Información nutricional insuficiente o no disponible
   * *Causa del problema*
     * El producto se registró en la base de datos correctamente, pero la información sobre los valores nutricionales no se encuentra disponible o es insuficiente
   * *Solución*
     * La aplicación no puede ofrecer un veredicto fiable sin estos datos, de modo que la ficha técnica del producto debe ser registrada por Open Food Facts
     * El usuario, mientras tanto, podría probar con un código de barras nuevo o distinto

## 6. Limitaciones y Mejoras Futuras
### ❔Qué no cubre aún tu proyecto

El proyecto, si bien es completo para los nutrientes que la aplicación mide actualmente (azúcar, sal y grasa), todavía no da la opción al usuario de elegir qué nutrientes específicos quiere valorar.

### 💡Ideas para mejoras o características adicionales

* *Selección personalizada de nutrientes*
  * Permitir que el usuario defina libremente qué campos nutricionales quiere analizar (por ejemplo, fibra, proteínas, grasas saturadas o alérgenos).
    * Aunque la aplicación ya determina por sí sola qué hace que un producto sea saludable o no, esta personalización le dará un control total para verificar si ciertos componentes específicos son adecuados para su dieta.
* *Apertura al sector sanitario y de la salud*
  *  Esta funcionalidad ampliará notablemente el público objetivo, permitiendo acercar la herramienta a entornos de la salud y la sanidad.
* *Soporte para condiciones médicas específicas*
  * Muchas personas padecen patologías o condiciones que les impiden consumir ciertos ingredientes o nutrientes. Con esta mejora, tanto los usuarios como los profesionales médicos podrán comprobar de forma rápida y clara si un producto comercial afectaría o no a su salud.

