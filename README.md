# TP_LLMs

El programa corre un modelo RAG que responde preguntas sobre cigarros importados por CW Schelp y preguntas basicas sobre cigarros y procesos de producción.

El modelo corre en local, para eso se requiere tener Ollama instalado con nomic-embed-text (modelo de embeddings) y gemma4:12b (modelo generador)

En el seteo del RAG se optó por TOP_K=20, CHUNK_SIZE = 1200 
y CHUNK_OVERLAP = 250. Esto significa que el modelo elige los 20 mejores chuncks de 1200 caracteres y estos a su vez tiene un solapamiento de 250 para no perder contexto. Esta elección permite tener respuestas con mayor contexto dado la limitación de num_ctx= 16384, que son los tokens de contexto de Ollama, dado que corremos en memoria.

El pipeline es el siguiente:

El programa primero extrae los chunks de los pdf y crea los embedding usando nomic-embed-text. Luego guarda los embedding en una base vectorial de Chroma en local. 

Luego al hacerle una pregunta el modelo busca en la base vecotrial los 20 chunks con mayor similitud semantica a nuestra pregunta y llama al LLM de ollama Gemma4:12b. El modelo tiene un prompt oculto que lo limita a responder utilizando solamente la información del contexto: "Respondé la pregunta usando SOLO la información del contexto. Desarrollá la respuesta: explicá el razonamiento y mencioná todos los datos relevantes que encuentres en el contexto, no solo el dato mínimo. Si el contexto no alcanza para una parte, aclaralo para esa parte y respondé igual lo que sí puedas. Cada fragmento del contexto viene precedido por su origen entre corchetes, con el nombre del PDF y el número de página. Citá las referencias una sola vez al final de la respuesta. No cites páginas que no aparezcan en el contexto."

Para citar se hace uso de la metadata de los archivos pdf. Se utiliza la metadata para evitar que el LLM alucine e invente paginas o nombres inexistentes de los archivos.

Despues de evaluar los chunks, el LLM genera una respuesta.


## Pregunta 1: cuanto cuesta un francisco de miranda azul robusto?

### RAG

cuanto cuesta un francisco de miranda azul robusto?

=== Pregunta: cuanto cuesta un francisco de miranda azul robusto? ===

--- Documentos recuperados ---
  * [Lista_precios_por_articulo_01-09-26_reformateada.pdf p.2] C.W. SCHELP - LISTA DE PRECIOS 01 de Septiembre 2026 Precio sugerido al publico por unidad...
  * [alec-bradley-compilado.pdf p.13] EL RECORRIDO Sin dinastía tabacalera atrás. Conresultados al frente.Rubin aprendió el ofic...
  * [MACANUDO_unificado_para_eventos.pdf p.7]  fumada cremosa con sutiles notas especiadas en esta mezcla de cuerpo medio que proporcion...
  * [Lista_precios_por_articulo_01-09-26_reformateada.pdf p.3] C.W. SCHELP - LISTA DE PRECIOS 01 de Septiembre 2026 Precio sugerido al publico por unidad...
  * [FRANCISCO DE MIRANDA.pdf p.1] FRANCISCO DE MIRANDA Cigarros Premium. Hechos a Mano en La República Dominicana. Qué es el...
  * [alec-bradley-compilado.pdf p.11] EL FUNDADOR 1996Alan Rubin funda la casa enFloridaEra importador de tornillería y bulonerí...
  * [Lista_precios_por_articulo_01-09-26_reformateada.pdf p.2] nea Azul CHURCHILL es de $7700 El precio de Francisco de Miranda Linea Azul PETIT ROBUSTO ...
  * [alec-bradley-compilado.pdf p.44] PITCH ESTÁNDAR 5 minutosPara el cliente curioso en momento de calma La historia del fundad...
  * [alec-bradley-compilado.pdf p.22] BLACK MARKET · CONTRA A MANEJAR "¿Por qué tabaco panameño?" Lo que está detrás de la pregu...
  * [MACANUDO_unificado_para_eventos.pdf p.8] MACANUDO 8 sofisticación. Capa Habano Ligero ecuatoriano Capote Jalapa nicaragüense Tripa ...
  * [CC PANTER AGIO MEHARI.pdf p.21] La marca Mehari’s fue lanzada en 1976 por la histórica tabacalera holandesa Agio Cigars, u...
  * [CC PANTER AGIO MEHARI.pdf p.20] Agio es sinónimo de tradición, calidad y confianza en el mundo del tabaco. Esta marca fami...
  * [alec-bradley-compilado.pdf p.89] 04POR QUÉ PANAMÁ MERECE ESTAR ACÁ · 4 DE 4 BalanceFuerza del nicaragüense. Suavidad del do...
  * [alec-bradley-compilado.pdf p.94] Y VALIDARON ESA APUESTA DOS VECES — PRIMERO ELLOS, DESPUÉS EL MUNDO. ▲ AB · BL ACK MARKET ...
  * [alec-bradley-compilado.pdf p.31] LOGROS QUE DAN PESO A SU FIRMA Tres hitos que hay que dominar ▲ AB · DOSSIER B2B 26 / 44La...
  * [FRANCISCO DE MIRANDA.pdf p.3] * No se recomienda encendedores de bencina, porque alteran el gusto del cigarro por el olo...
  * [MACANUDO_unificado_para_eventos.pdf p.2] s de marca en los Estados Unidos y sigue siendo un pilar de la cultura del puro y de la vi...
  * [alec-bradley-compilado.pdf p.2]  éxito— hasta que encontró su camino con el Occidental Reserve, el primer cigarro que dio ...
  * [Lista_precios_por_articulo_01-09-26_reformateada.pdf p.2] 0 El precio de Francisco de Miranda Linea Amarilla GIGANTE es de $14800 FRANCISCO DE MIRAN...
  * [alec-bradley-compilado.pdf p.38] LOS TRES PROS TRANSVERSALES DE LA MARCA Tres argumentos que siempre funcionan ▲ AB · DOSSI...

--- Respuesta del modelo ---
El precio de un Francisco de Miranda Linea Azul ROBUSTO es de $7000.

En el contexto se detallan además otros precios para variaciones de la "Linea Azul" que incluyen la denominación "Robusto", los cuales podrían ser de interés:
*   Francisco de Miranda Linea Azul PETIT ROBUSTO: $7000.
*   Francisco de Miranda Linea Azul PETIT ROBUSTO GORDO: $7700.
*   Francisco de Miranda Linea Azul GRAN ROBUSTO: $8300.

[Lista_precios_por_articulo_01-09-26_reformateada.pdf — pág. 2]

> 

### Sin RAG

cuanto cuesta un francisco de miranda azul robusto?
edit
Thought for 17.6 seconds

El precio de un puro Francisco de Miranda Azul Robusto no tiene un valor fijo universal, ya que depende de varios factores:

Ubicación geográfica: El precio varía significativamente según el país (debido a impuestos y aranceles) y la ciudad donde se compre.
Tipo de establecimiento: Una tienda especializada en puros (humidor) puede tener precios distintos a una tienda de conveniencia o un distribuidor mayorista.
Promociones: Las tiendas suelen ofrecer descuentos por volumen o promociones temporales.
¿Qué puedes hacer para saber el precio exacto?

Consultar tiendas online: Busca en páginas de distribuidores de puros en tu región.
Contactar a un humidor local: Si tienes una tienda de tabaco cerca, puedes llamarles directamente para consultar el precio del modelo específico "Azul Robusto".
En general, los puros de esta línea suelen estar en un rango de precio medio-alto, pero para darte una cifra exacta, necesitaría saber en qué país o ciudad te encuentras.

## Pregunta 2: 

### RAG

> que diferencias hay entre un cigarro macanudo y uno alec bradley?

=== Pregunta: que diferencias hay entre un cigarro macanudo y uno alec bradley? ===

--- Documentos recuperados ---
  * [alec-bradley-compilado.pdf p.4] CIGAR WORLD  RESEÑA HISTÓRICA cigarworld.com Alec Bradley Cigar Co. — 1996–2026 3 III. 20...
  * [alec-bradley-compilado.pdf p.2] CIGAR WORLD  RESEÑA HISTÓRICA cigarworld.com Alec Bradley Cigar Co. — 1996–2026 1 ALEC BR...
  * [alec-bradley-compilado.pdf p.5] CIGAR WORLD  RESEÑA HISTÓRICA cigarworld.com Alec Bradley Cigar Co. — 1996–2026 4 Alan Ru...
  * [alec-bradley-compilado.pdf p.33] GATEKEEPER · PITCH Y OBJECIÓN El argumento principal PITCH CORTO: "Colaboración entre Alec...
  * [alec-bradley-compilado.pdf p.43] PITCH ESTÁNDAR 90 segundosPara el empleado de la tabaquería en momento de tráfico Alec Bra...
  * [alec-bradley-compilado.pdf p.7] QUIÉN ES ALEC BRADLEY EN 90 SEGUNDOS La única casa premiumcentroamericana fundada en1996 p...
  * [alec-bradley-compilado.pdf p.70] EL QUE HIZO QUE EL MUNDO DIJERA, SIN DUDARLO, QUE ESTA CASA JUEGA EN PRIMERA. ▲ AB · PRENS...
  * [alec-bradley-compilado.pdf p.74] LA MARCA 1996Alan Rubin funda Alec Bradley Era importador de tornillería y bulonería. Deci...
  * [alec-bradley-compilado.pdf p.52] LA MARCA 1996Alan Rubin funda Alec Bradley El primer cigarro de la casa fue un humilde Con...
  * [alec-bradley-compilado.pdf p.63] ▲ AB · PRENSADO 14 / 22 LA CONSAGRACIÓN 2011El cigarro que cambióla trayectoria de AlecBra...
  * [MACANUDO_unificado_para_eventos.pdf p.5] cioso. Combinado con estos diferentes tabacos, Macanudo ofrece un perfil de sabor complejo...
  * [alec-bradley-compilado.pdf p.42] OBJECIONES DEL CONSUMIDOR FINAL · EL ÚLTIMO ESLABÓN Cómo responde el empleado de la tabaqu...
  * [MACANUDO_unificado_para_eventos.pdf p.1] MACANUDO 1 MACANUDO LA MARCA  LA FÁBRICA  LA LÍNEA INSPIRADO COMIENZOS HUMILDES: LOS ORÍ...
  * [alec-bradley-compilado.pdf p.3] CIGAR WORLD  RESEÑA HISTÓRICA cigarworld.com Alec Bradley Cigar Co. — 1996–2026 2 El ciga...
  * [MACANUDO_unificado_para_eventos.pdf p.1]  dinero en efectivo. Y así nació la industria de los cigarros de Jamaica, así nace General...
  * [alec-bradley-compilado.pdf p.48] EL MENSAJE A TRANSMITIR EN CADA VISITA Cuando una tabaquería decide trabajar Alec Bradley,...
  * [alec-bradley-compilado.pdf p.12] EL NOMBRE Alec & BradleyLos dos hijos de Alan Rubin. Hoy activos en la siguiente etapa. Es...
  * [alec-bradley-compilado.pdf p.2]  éxito— hasta que encontró su camino con el Occidental Reserve, el primer cigarro que dio ...
  * [alec-bradley-compilado.pdf p.69] EL CIGARRO CONSAGRADO 96#1 Cigar of the Year 2011 Cigar Aficionado El único #1 absoluto qu...
  * [alec-bradley-compilado.pdf p.4] sible y profundamente satisfactorio. Project 40 Elaborado para celebrar los 40 años del ta...

--- Respuesta del modelo ---
Las diferencias entre un cigarro Macanudo y uno Alec Bradley se pueden identificar a través de sus orígenes geográficos, sus métodos de fabricación, su historia y el perfil de marca que proyectan:

**1. Origen y Ubicación de Fabricación:**
*   **Alec Bradley:** Es una marca **centroamericana** fundada en 1996 en Fort Lauderdale, Florida. Sus cigarros se fabrican en la fábrica **Raíces Cubana en Honduras**, donde se utilizan técnicas artesanales de la tradición cubana.
*   **Macanudo:** Es una marca con raíces en **Jamaica** (desde la Segunda Guerra Mundial). Su historia comenzó en una pequeña fábrica jamaiquina y se convirtió en una de las marcas más importantes de la industria.

**2. Técnicas de Construcción y Tradición:**
*   **Alec Bradley:** Se destaca por la calidad de construcción en la fábrica Raíces Cubana, específicamente por la técnica del **"Entubado de Tripa"**. Esta técnica consiste en enrollar cada una de las hojas de la tripa por separado sobre sí misma para crear pequeños tubos, permitiendo que el aire circule de manera eficiente por todo el cigarro.
*   **Macanudo:** Se asocia con la experiencia en el cultivo de tabaco **Connecticut** y su producción en Jamaica.

**3. Trayectoria y Reconocimiento:**
*   **Alec Bradley:** Es descrita como una marca que "juega en primera" y que no es "una marca más". Su hito principal fue en 2011, cuando el **Prensado Churchill** fue nombrado **Cigarro del Año** por Cigar Aficionado. La marca ha logrado 16 cigarros en el Top 25 de Cigar Aficionado en 30 años.
*   **Macanudo:** Es una marca con décadas de historia. Su nombre significa "magnífico, extraordinario, excelente" en Argentina. Es reconocida por su consistencia y por haber superado diversos desafíos para convertirse en una marca líder.

**4. Portafolio y Perfil del Consumidor:**
*   **Alec Bradley:** Se posiciona como una marca **premium** para el fumador que busca algo "serio". Cuenta con una variedad de líneas con propósitos distintos: **Black Market** y **Prensado** (para quienes buscan algo medio-fuerte con historia), **Project 40** (para el día a día, accesible y sin agresividad), **Blind Faith** (mezclado por los hermanos Rubin) y **Gatekeeper** (una colaboración con el maestro Ernesto Perez-Carrillo).
*   **Macanudo:** Se destaca especialmente por la línea **Macanudo Café**, la cual es descrita como "distintivo, suave y delicioso". Es una opción muy popular tanto para **principiantes como para aficionados** debido a su complejidad y consistencia.

**Resumen de diferencias clave:**
*   **Geografía:** Alec Bradley es centroamericana (Honduras/Nicaragua) mientras que Macanudo es jamaiquina.
*   **Identidad:** Alec Bradley se enfoca en la "seriedad" y en colaboraciones de maestros (como en el caso de Gatekeeper); Macanudo se enfoca en la consistencia y en un perfil "suave y delicioso" (especialmente en su línea Café).
*   **Técnica:** Alec Bradley enfatiza el "Entubado de Tripa" de la tradición cubana en su fábrica de Honduras.

Referencias:
[alec-bradley-compilado.pdf — pág. 2]
[alec-bradley-compilado.pdf — pág. 3]
[alec-bradley-compilado.pdf — pág. 4]
[alec-bradley-compilado.pdf — pág. 5]
[alec-bradley-compilado.pdf — pág. 7]
[alec-bradley-compilado.pdf — pág. 12]
[alec-bradley-compilado.pdf — pág. 33]
[alec-bradley-compilado.pdf — pág. 43]
[alec-bradley-compilado.pdf — pág. 52]
[alec-bradley-compilado.pdf — pág. 63]
[alec-bradley-compilado.pdf — pág. 70]
[alec-bradley-compilado.pdf — pág. 74]
[MACANUDO_unificado_para_eventos.pdf — pág. 1]
[MACANUDO_unificado_para_eventos.pdf — pág. 5]

> 


### Sin RAG

Es importante empezar aclarando una distinción técnica antes de comparar: Macanudo es una marca principalmente conocida por sus cigarillos, mientras que Alec Bradley es una marca que produce tanto cigarros premium como cigarillos.

Aquí te detallo las diferencias principales en cuanto a categoría, sabor, tamaño y experiencia de fumado:

1. Categoría y Tipo de Producto
Macanudo: Se especializa en el segmento de cigarillos. Estos son más cortos, más delgados y suelen tener un proceso de fabricación que permite una producción más masiva. Están diseñados para ser fáciles de fumar y de acceso rápido.
Alec Bradley: Es una marca de cigarros premium (aunque también tienen líneas de cigarillos). Sus productos suelen ser "puros" de tamaño completo, con una construcción más artesanal y materiales seleccionados para durar más tiempo en la boca.
2. Perfil de Sabor
Macanudo: Tiende a ser muy consistente y suave. Está diseñado para el fumador que busca algo relajado, con notas de madera y crema, sin mucha complejidad pero con buen cuerpo. Es el "estándar" de calidad en el mundo de los cigarillos.
Alec Bradley: Ofrece una gama mucho más amplia. Dependiendo de la línea (por ejemplo, la serie Special Reserve o Signature Series), el sabor puede variar desde lo suave hasta lo fuerte y robusto. Suelen tener sabores más complejos, con notas de especias, cacao o frutos secos, dependiendo del origen del tabaco (República Dominicana, Nicaragua, etc.).
3. Tamaño y Duración
Macanudo: Al ser un cigarrillo, suele durar entre 20 y 45 minutos. Es ideal para una pausa corta o una charla rápida.
Alec Bradley: Al ser principalmente cigarros, suelen durar entre 45 minutos y 1.5 horas (dependiendo del tamaño como Robusto, Toro, etc.). Requieren una sesión de fumado más dedicada.
4. Construcción y Materiales
Macanudo: Utiliza una selección de tabacos de calidad para el mercado de cigarillos, pero su enfoque es la accesibilidad.
Alec Bradley: Al ser una marca de cigarros premium, utilizan procesos de fermentación y curado más largos. Sus capuchones (la parte que se quita) y sus velleros (la capa externa) suelen tener una textura y apariencia más refinadas.
Resumen comparativo:
Característica	Macanudo (Cigarillo)	Alec Bradley (Cigarro Premium)
Tipo	Cigarillo	Cigarro (Puro)
Intensidad	Generalmente suave y equilibrado	Variable (de suave a fuerte)
Complejidad	Sencilla y directa	Compleja y variada
Tiempo de fumado	Corto (20-40 min)	Largo (45-90 min)
Público objetivo	Fumadores casuales, principiantes	Fumadores recurrentes y aficionados
En conclusión: Si buscas algo rápido, fácil de fumar y con un sabor agradable y constante para una ocasión informal, el Macanudo es la elección clásica. Si buscas una experiencia de fumado más profunda, con sabores más complejos y una duración mayor (un "ritual" de fumado), un Alec Bradley es la opción superior.

## Pregunta 3: quiero venderle cigarros macanudo a un cliente, que le puedo decir para convencerlo?

### RAG

> quiero venderle cigarros macanudo a un cliente, que le puedo decir para convencerlo?

=== Pregunta: quiero venderle cigarros macanudo a un cliente, que le puedo decir para convencerlo? ===

--- Documentos recuperados ---
  * [MACANUDO_unificado_para_eventos.pdf p.1] MACANUDO 1 MACANUDO LA MARCA  LA FÁBRICA  LA LÍNEA INSPIRADO COMIENZOS HUMILDES: LOS ORÍ...
  * [MACANUDO_unificado_para_eventos.pdf p.1]  dinero en efectivo. Y así nació la industria de los cigarros de Jamaica, así nace General...
  * [MACANUDO_unificado_para_eventos.pdf p.5] cioso. Combinado con estos diferentes tabacos, Macanudo ofrece un perfil de sabor complejo...
  * [MACANUDO_unificado_para_eventos.pdf p.3] MACANUDO 3 técnica de añejamiento creativa, el lanzamiento anual sigue siendo una declarac...
  * [MACANUDO_unificado_para_eventos.pdf p.10] MACANUDO 10 perfecto para discutir entre fumadores! Macanudo Inspirado es una opción excel...
  * [MACANUDO_unificado_para_eventos.pdf p.4] MACANUDO 4 CONNECTICUT SHADE – LA CAPA COLOR CAFÉ EL CIGARRO QUE LO INICIÓ TODO Para mucho...
  * [CC PANTER AGIO MEHARI.pdf p.24] Mehari’s ofrece una experiencia elegante y accesible para quienes buscan calidad en un for...
  * [alec-bradley-compilado.pdf p.42] OBJECIONES DEL CONSUMIDOR FINAL · EL ÚLTIMO ESLABÓN Cómo responde el empleado de la tabaqu...
  * [MACANUDO_unificado_para_eventos.pdf p.2] s de marca en los Estados Unidos y sigue siendo un pilar de la cultura del puro y de la vi...
  * [alec-bradley-compilado.pdf p.44] PITCH ESTÁNDAR 5 minutosPara el cliente curioso en momento de calma La historia del fundad...
  * [MACANUDO_unificado_para_eventos.pdf p.7] MACANUDO 7 LA LÍNEA INSPIRADO La línea de cigarros Macanudo Inspirado es una colección que...
  * [alec-bradley-compilado.pdf p.13] EL RECORRIDO Sin dinastía tabacalera atrás. Conresultados al frente.Rubin aprendió el ofic...
  * [alec-bradley-compilado.pdf p.41] OBJECIONES DE LA TABAQUERÍA · LA MÁS COMERCIAL La objeción del reconocimiento "No la conoz...
  * [alec-bradley-compilado.pdf p.33] GATEKEEPER · PITCH Y OBJECIÓN El argumento principal PITCH CORTO: "Colaboración entre Alec...
  * [alec-bradley-compilado.pdf p.43] PITCH ESTÁNDAR 90 segundosPara el empleado de la tabaquería en momento de tráfico Alec Bra...
  * [alec-bradley-compilado.pdf p.54] EL CIGARRO EN MANO PrensadoRobusto5 × 50 · Medio a fuerte Característica visual única: pre...
  * [CC PANTER AGIO MEHARI.pdf p.20] Agio es sinónimo de tradición, calidad y confianza en el mundo del tabaco. Esta marca fami...
  * [alec-bradley-compilado.pdf p.89] 04POR QUÉ PANAMÁ MERECE ESTAR ACÁ · 4 DE 4 BalanceFuerza del nicaragüense. Suavidad del do...
  * [alec-bradley-compilado.pdf p.92] MARIDAJES Con qué tomar este cigarroTres vías clásicas, las tres a la altura Bourbon Dulzu...
  * [alec-bradley-compilado.pdf p.31] LOGROS QUE DAN PESO A SU FIRMA Tres hitos que hay que dominar ▲ AB · DOSSIER B2B 26 / 44La...

--- Respuesta del modelo ---
Para convencer a un cliente de comprar cigarros Macanudo, puedes utilizar los siguientes argumentos basados en la historia, la calidad y la variedad de la marca:

**1. El prestigio y significado del nombre:**
Puedes destacar que la palabra "Macanudo" no es una elección al azar; en Argentina significa "magnífico, extraordinario, excelente, estupendo, maravilloso, genial, pistonudo, bacán o bacano". Además, el nombre tiene una conexión real: el rey Eduardo quedó fascinado con la palabra (que aprendió de jugadores de polo argentinos) y la convirtió en el nombre de referencia para una nueva marca de puros que buscaba ganarse el favor del Rey.

**2. La versatilidad para diferentes tipos de fumadores (Macanudo Café):**
Si el cliente es nuevo en el mundo de los cigarros o busca algo equilibrado, puedes ofrecerle el **Macanudo Café**. Es uno de los cigarros más populares del mundo y un pilar de la marca. Sus puntos fuertes son:
*   **Perfil de sabor:** Es descrito como "distintivo, suave y delicioso".
*   **Accesibilidad:** Es lo suficientemente suave para que lo disfruten los principiantes, pero lo suficientemente complejo para que los fumadores veteranos vuelvan a él.
*   **Calidad reconocida:** Ha pasado décadas como una de las marcas premium más vendidas debido a su consistencia y a que suele obtener calificaciones de más de 90 puntos.
*   **Composición única:** Logra un perfil complejo combinando una capa de Connecticut Shade con un capote mexicano de San Andreas y hojas de tripa de Piloto cubano mexicano y dominicano.

**3. Opciones para quienes buscan algo más intenso (Línea Inspirado):**
Si el cliente busca algo con más carácter, puedes dirigirlo a la línea **Inspirado** (lanzada en 2014), diseñada para una nueva generación que busca una experiencia más intensa y diversa:
*   **Macanudo Inspirado:** Es ideal para quienes buscan un cigarro con "más cuerpo y carácter", con un perfil robusto y complejo, perfecto para momentos especiales o para acompañar una buena bebida.
*   **Inspirado Orange:** Es una opción sofisticada con un toque especiado sutil y una fumada cremosa. Es un cigarro premiado (clasificado entre los 25 mejores por Cigar Aficionado) y se elabora en Honduras con tabacos de Honduras, Nicaragua y República Dominicana.

**4. La excelencia de la hoja Connecticut Shade (Macanudo Gold Label):**
Para el cliente que busca una experiencia de alta gama con la tradición de la marca, puedes mencionar el **Macanudo Gold Label**. Este se lanza para demostrar que una hoja Connecticut Shade "verdaderamente excelente" puede destacar por su calidad y consistencia.

**Resumen de argumentos por perfil de cliente:**
*   **Para el principiante:** Macanudo Café (suave, delicioso y consistente).
*   **Para el que busca sofisticación y premios:** Inspirado Orange (cremoso y con notas especiadas).
*   **Para el que busca potencia y cuerpo:** Macanudo Inspirado (robusto y complejo).
*   **Para el que busca la esencia de la marca:** Macanudo Gold Label.

Referencias:
[MACANUDO_unificado_para_eventos.pdf — pág. 1]
[MACANUDO_unificado_para_eventos.pdf — pág. 3]
[MACANUDO_unificado_para_eventos.pdf — pág. 4]
[MACANUDO_unificado_para_eventos.pdf — pág. 5]
[MACANUDO_unificado_para_eventos.pdf — pág. 7]
[MACANUDO_unificado_para_eventos.pdf — pág. 10]

> 

### Sin RAG

Vender un producto de alta gama como los cigarros Macanudo no se trata de vender "tabaco", sino de vender una experiencia, estatus y tradición. Macanudo es una marca reconocida por su calidad, sofisticación y equilibrio.

Aquí tienes varias estrategias y argumentos dependiendo del tipo de perfil de cliente que tengas enfrente:

1. Si el cliente es un "Conocedor" (Ya fuma y busca calidad)
Aquí debes apelar a la sofisticación y los detalles técnicos.

Enfoque en la consistencia: "Lo que hace especial a Macanudo es su consistencia. Cada puro está seleccionado para ofrecer un perfil de sabor equilibrado desde la primera hasta la última calada. No es un cigarro que te 'golpea', sino que evoluciona en tu paladar."
Enfoque en la herencia: "Macanudo es una marca con historia. Al encender uno, no estás solo fumando, estás participando en una tradición de maestros tabaqueros que han perfeccionado el arte de la selección de hojas."
Frase clave: "Si buscas un cigarro que sea elegante, con cuerpo pero sin ser agresivo, Macanudo es el estándar de oro."
2. Si el cliente es un "Novato" (Busca algo premium pero no sabe mucho)
Aquí debes apelar a la facilidad de disfrute y la calidad superior.

Enfoque en la suavidad: "Si quieres empezar con algo de alta gama, Macanudo es la elección perfecta. Es un cigarro 'amigable' pero muy sofisticado. No es fuerte ni irritante, tiene una textura cremosa que permite disfrutar el aroma sin esfuerzo."
Enfoque en el estatus: "Es una marca que proyecta distinción. Es el tipo de cigarro que eliges cuando quieres celebrar un momento especial o simplemente darte un lujo personal."
Frase clave: "Es la puerta de entrada perfecta al mundo de los cigarros de lujo; es sofisticado sin ser complicado."
3. El argumento de la "Experiencia Sensorial" (Para cualquiera)
Vende el momento de relax.

El ritual: "Un Macanudo no es para fumarse rápido. Es para un café, una buena conversación o un momento de reflexión. Su aroma es complejo (notas de madera, tierra y especias) y es diseñado para ser saboreado lentamente."
La calidad de la hoja: "La diferencia está en el tabaco seleccionado. Macanudo utiliza hojas premium que permiten que el humo sea denso y aromático, creando una atmósfera de relajación inmediata."
Tips de ventas extra para cerrar la venta:
La técnica de la comparación (Upselling): Si el cliente está mirando una marca más barata o común, dile: "Esa opción es buena para un uso diario, pero si lo que buscas es una experiencia superior, donde el sabor sea más complejo y la construcción sea impecable, tienes que probar el Macanudo. La diferencia en la calidad de la hoja es notable."
Usa adjetivos poderosos: En lugar de decir "es rico", usa palabras como: cremoso, sofisticado, equilibrado, persistente, robusto (si es un modelo fuerte), elegante o clásico.
Crea exclusividad: "No todo el mundo reconoce la calidad de un Macanudo a primera vista, pero quienes lo conocen, siempre vuelven a él."
Resumen de "Bullet Points" para tener en mente:
Tradición: Historia y prestigio.
Equilibrio: Sabor constante de principio a fin.
Suavidad: Una experiencia placentera, no agresiva.
Calidad: Selección de tabacos de primera clase.
¿Cuál de estos perfiles es el que más te cuesta convencer? Puedo darte un guion más específico si me dices qué objeción te suelen poner.

## Pregunta 4: cuanto cuesta un Cafe Creme?

### RAG

> cuanto cuesta un cafe creme?

=== Pregunta: cuanto cuesta un cafe creme? ===

--- Documentos recuperados ---
  * [CC PANTER AGIO MEHARI.pdf p.3] Café Crème fue el primer cigarro pequeño del mundo y ha sido líder europeo y de su categor...
  * [CC PANTER AGIO MEHARI.pdf p.11] Café Crème Original es un clásico europeo que redefine la experiencia del cigarrito mecani...
  * [CC PANTER AGIO MEHARI.pdf p.8] Ficha de Producto Café Crème Beige Aromatización: Suave aromatización Fortaleza: Suave 🔸 B...
  * [CC PANTER AGIO MEHARI.pdf p.7] Ficha de Producto Café Crème Original Aromatización: Natural Fortaleza: Media 🔸 Blend (mez...
  * [CC PANTER AGIO MEHARI.pdf p.9] Ficha de Producto Café Crème Filter RED Aromatización: Natural Fortaleza: Muy Suave 🔸 Blen...
  * [CC PANTER AGIO MEHARI.pdf p.10] Ficha de Producto Café Crème Finos Original Aromatización: Natural Fortaleza: Media 🔸 Blen...
  * [Lista_precios_por_articulo_01-09-26_reformateada.pdf p.1] C.W. SCHELP - LISTA DE PRECIOS 01 de Septiembre 2026 Precio sugerido al publico por unidad...
  * [alec-bradley-compilado.pdf p.62] LA TRIPA ENTRANDO EN JUEGO ▲ AB · PRENSADO 13 / 22 El cuero se mantiene como base, pero ah...
  * [alec-bradley-compilado.pdf p.84] LA HOJA PANAMEÑA ENTRANDO EN JUEGO ▲ AB · BL ACK MARKET 13 / 24 ¿Sienten cómo cambia? El c...
  * [MACANUDO_unificado_para_eventos.pdf p.8] MACANUDO 8 sofisticación. Capa Habano Ligero ecuatoriano Capote Jalapa nicaragüense Tripa ...
  * [alec-bradley-compilado.pdf p.79] EN BOCA Cuero ·PimientaCapóte de Sumatra. Pura especia controlada. Lo que están sintiendo ...
  * [alec-bradley-compilado.pdf p.92] MARIDAJES Con qué tomar este cigarroTres vías clásicas, las tres a la altura Bourbon Dulzu...
  * [MACANUDO_unificado_para_eventos.pdf p.1]  dinero en efectivo. Y así nació la industria de los cigarros de Jamaica, así nace General...
  * [MACANUDO_unificado_para_eventos.pdf p.7]  fumada cremosa con sutiles notas especiadas en esta mezcla de cuerpo medio que proporcion...
  * [MACANUDO_unificado_para_eventos.pdf p.4] MACANUDO 4 CONNECTICUT SHADE – LA CAPA COLOR CAFÉ EL CIGARRO QUE LO INICIÓ TODO Para mucho...
  * [MACANUDO_unificado_para_eventos.pdf p.5] cioso. Combinado con estos diferentes tabacos, Macanudo ofrece un perfil de sabor complejo...
  * [alec-bradley-compilado.pdf p.13] EL RECORRIDO Sin dinastía tabacalera atrás. Conresultados al frente.Rubin aprendió el ofic...
  * [alec-bradley-compilado.pdf p.94] Y VALIDARON ESA APUESTA DOS VECES — PRIMERO ELLOS, DESPUÉS EL MUNDO. ▲ AB · BL ACK MARKET ...
  * [alec-bradley-compilado.pdf p.83] 02El corazón de lacharla CHOCOLATE Y CUERO · LA HOJA PANAMEÑA 20-25 MINUTOS ▲ AB · BL ACK ...
  * [CC PANTER AGIO MEHARI.pdf p.14] Capa: Natural Sabor/aroma: clasico de tabaco Fortaleza: Media Caracteristicas Distintivas:...

--- Respuesta del modelo ---
El costo de un cigarro Café Crème depende de la variedad específica del producto, ya que existen diferentes versiones con precios distintos según la lista de precios:

*   **Café Crème FINOS ORIGINAL:** $1200
*   **Café Crème FINOS BEIGE (Vanilla):** $1200
*   **Café Crème ORIGINAL:** $1400
*   **Café Crème BLUE:** $1400
*   **Café Crème RED (Arome):** $1400
*   **Café Crème BEIGE (Vainilla):** $1400
*   **Café Crème BROWN (Coffee):** $1400
*   **Café Crème FILTER RED:** $1800

[Lista_precios_por_articulo_01-09-26_reformateada.pdf — pág. 1]

> 

### Sin RAG

El precio de un café crème varía considerablemente dependiendo de en qué país estés y del tipo de establecimiento donde lo compres.

Aquí tienes un desglose aproximado según el contexto:

1. En España y Europa:
Cafeterías de barrio o tradicionales: Entre 1,50 € y 2,50 €.
Cafeterías de especialidad (Specialty Coffee): Entre 3,00 € y 4,50 € (aquí el precio sube por la calidad del grano y el método de preparación).
Cadenas internacionales (como Starbucks): Entre 3,50 € y 5,50 €, dependiendo de la personalización y el tamaño.
2. En México y Latinoamérica:
Cafeterías locales o puestos: Puede costar desde $30 hasta $80 pesos mexicanos (aprox. 1,50 € - 4 €).
Cafeterías de cadena o de especialidad: Suele rondar entre los 80 y 150 pesos mexicanos (aprox. 4 € - 7,50 €).
Factores que influyen en el precio:
Ubicación: Un café en una zona turística o en un aeropuerto siempre será más caro que uno en una calle secundaria.
Tipo de grano: El café de especialidad (granos seleccionados de origen específico) es más caro que el café comercial.
Tamaño: Un café crème pequeño suele ser más barato que una versión "grande" o "grande con extra leche".
Personalización: Añadir sabores, jarabes o tipos de leche vegetales (almendra, avena, soja) suele añadir un cargo extra de entre 0,50 € y 1,50 €.
En resumen: Si buscas algo económico, busca una cafetería local; si buscas una experiencia de sabor específica, una cafetería de especialidad justificará un precio más alto.

## Pregunta 5: que caracteristicas tiene Panter?

### RAG

que caracteristicas tiene Panter?

=== Pregunta: que caracteristicas tiene Panter? ===

--- Documentos recuperados ---
  * [CC PANTER AGIO MEHARI.pdf p.12] Panter es una marca de cigarritos pequeños (puritos) originaria de los Países Bajos. Fue l...
  * [CC PANTER AGIO MEHARI.pdf p.16] Panter fue pensada para ofrecer cigarritos modernos, elegantes y llenos de sabor, elaborad...
  * [alec-bradley-compilado.pdf p.79] EN BOCA Cuero ·PimientaCapóte de Sumatra. Pura especia controlada. Lo que están sintiendo ...
  * [alec-bradley-compilado.pdf p.92] MARIDAJES Con qué tomar este cigarroTres vías clásicas, las tres a la altura Bourbon Dulzu...
  * [alec-bradley-compilado.pdf p.22] BLACK MARKET · CONTRA A MANEJAR "¿Por qué tabaco panameño?" Lo que está detrás de la pregu...
  * [alec-bradley-compilado.pdf p.86] 01POR QUÉ PANAMÁ MERECE ESTAR ACÁ · 1 DE 4 Suelo volcánicoEl V olcán Barú le aporta a la z...
  * [alec-bradley-compilado.pdf p.89] 04POR QUÉ PANAMÁ MERECE ESTAR ACÁ · 4 DE 4 BalanceFuerza del nicaragüense. Suavidad del do...
  * [alec-bradley-compilado.pdf p.15] EXCEPCIÓN IMPORTANTE A TENER CLARA La línea Gatekeeper no se hace en RaícesCubanas.Gatekee...
  * [alec-bradley-compilado.pdf p.21] BLACK MARKET · EL CONCEPTO La apuesta territorialTabaco panameño + suelo volcánico + genét...
  * [alec-bradley-compilado.pdf p.84] LA HOJA PANAMEÑA ENTRANDO EN JUEGO ▲ AB · BL ACK MARKET 13 / 24 ¿Sienten cómo cambia? El c...
  * [alec-bradley-compilado.pdf p.13] EL RECORRIDO Sin dinastía tabacalera atrás. Conresultados al frente.Rubin aprendió el ofic...
  * [alec-bradley-compilado.pdf p.36] 06 · PRÓXIMAMENTE KintsugiCuerpo medio · capa hondureña de Trojes RATED 92 · TOP 25 COTY 2...
  * [alec-bradley-compilado.pdf p.87] 02POR QUÉ PANAMÁ MERECE ESTAR ACÁ · 2 DE 4 Genética cubanaLos productores panameños no apr...
  * [alec-bradley-compilado.pdf p.48] EL MENSAJE A TRANSMITIR EN CADA VISITA Cuando una tabaquería decide trabajar Alec Bradley,...
  * [CC PANTER AGIO MEHARI.pdf p.6] Los cigarros mecanizados se producen en nuestras fabricas alrededor del mundo. Si bien las...
  * [alec-bradley-compilado.pdf p.44] PITCH ESTÁNDAR 5 minutosPara el cliente curioso en momento de calma La historia del fundad...
  * [CC PANTER AGIO MEHARI.pdf p.20] Agio es sinónimo de tradición, calidad y confianza en el mundo del tabaco. Esta marca fami...
  * [MACANUDO_unificado_para_eventos.pdf p.9] iva o amargura.  Sabores únicos: El suelo volcánico aporta una complejidad mineral que pu...
  * [MACANUDO_unificado_para_eventos.pdf p.8] MACANUDO 8 sofisticación. Capa Habano Ligero ecuatoriano Capote Jalapa nicaragüense Tripa ...
  * [alec-bradley-compilado.pdf p.40] OBJECIONES DE LA TABAQUERÍA · MARGEN Y ROTACIÓN Las dos objeciones de números "El margen n...

--- Respuesta del modelo ---
Panter es una marca de cigarritos pequeños (puritos) con las siguientes características:

*   **Origen y fabricación:** Es una marca originaria de los Países Bajos, producida por la empresa Agio Cigars, la cual es reconocida por fabricar cigarros hechos a máquina de alta calidad desde principios del siglo XX.
*   **Atributos físicos y de sabor:** Se caracteriza por su tamaño compacto, un sabor suave y una experiencia de fumada corta pero intensa. Se destaca por ofrecer un equilibrio entre calidad y carácter, además de ser una opción que combina la tradición europea con un estilo refinado y contemporáneo.
*   **Variedades:** Cuenta con una variedad de versiones con o sin sabor, tales como Panter Blue, Dessert y Vanilla.
*   **Composición y materiales:** Está elaborada con tabacos seleccionados de Indonesia, Brasil y América Central, y está envuelta en hoja natural.
*   **Presentación:** Se presenta en estuches metálicos, lo que le otorga una imagen moderna y elegante.
*   **Posicionamiento en el mercado:** Es una de las marcas más vendidas en Europa dentro del segmento de cigarros pequeños y cuenta con presencia en más de 60 países. Su popularidad se debe a su practicidad y al equilibrio entre calidad y precio.
*   **Relación con la marca madre (Agio):** Al formar parte de la línea Agio, la marca se asocia con la tradición, la calidad y la confianza. Sus productos se caracterizan por ofrecer una experiencia de fumada suave, limpia y consistente, siendo una opción accesible y de excelente calidad.

[CC PANTER AGIO MEHARI.pdf — pág. 12]
[CC PANTER AGIO MEHARI.pdf — pág. 16]
[CC PANTER AGIO MEHARI.pdf — pág. 20]

> 

### Sin RAG 

Es importante aclarar primero que, desde un punto de vista biológico, la palabra "Pantera" no se refiere a una especie única de animal, sino que es un término utilizado para describir a ciertos felinos grandes que tienen un pelaje oscuro (melanismo).

Dependiendo de la región, una pantera es generalmente un leopardo (en África y Asia) o un jaguar (en América).

Aquí tienes las características principales que comparten estos animales (las "panteras"):

1. Características Físicas
Pelaje (Melanismo): Su característica más distintiva es el color negro o muy oscuro. Esto es una variación genética llamada melanismo. Aunque el pelaje se ve negro a simple vista, bajo una luz intensa se pueden apreciar las manchas y rosetas originales de la especie.
Físico Robusto: Son animales musculosos y fuertes, diseñados para la potencia y la velocidad. Tienen hombros anchos y patas poderosas.
Garras Retráctiles: Poseen garras que pueden esconder y sacar, lo que les permite caminar en silencio y atrapar a sus presas con firmeza.
Sentidos Agudos:
Vista: Excelente visión nocturna, lo que les permite cazar en la oscuridad.
Oído: Capacidad de detectar sonidos sutiles de presas moviéndose entre la maleza.
Olfato: Un sentido del olfato muy desarrollado para rastrear y detectar posibles peligros.
2. Comportamiento y Hábitos
Cazadores Solitarios: A diferencia de los leones, las panteras son animales solitarios. Cazan por su cuenta y defienden su territorio individualmente.
Acecho y Emboscada: No suelen perseguir a sus presas a largas distancias como los lobos. En su lugar, se acercan sigilosamente y atacan de forma repentina (emboscada).
Actividad Nocturna: Son principalmente nocturnas o crepusculares (activas al amanecer y atardecer), lo que les da una ventaja táctica para cazar.
Territorialidad: Marcan su territorio mediante orina, marcas en los árboles y vocalizaciones para advertir a otros individuos.
3. Habilidades de Supervivencia
Agilidad: Son extremadamente ágiles, capaces de escalar árboles rápidamente o nadar largas distancias (especialmente los jaguares).
Sigilo: Tienen la capacidad de moverse casi sin hacer ruido, ocultándose perfectamente en la vegetación densa.
Fuerza de Mordida: Tienen una de las mordidas más potentes entre los felinos, capaz de romper huesos o penetrar caparazones de tortugas.
Resumen de diferencias según la región:
Pantera (Leopardo): Común en África y Asia. Es más esbelta y ágil.
Pantera (Jaguar): Común en América (Centro y Sur). Es más robusta y tiene una mandíbula más fuerte.
En resumen, la "pantera" es el símbolo de la fuerza, el sigilo y la elegancia en el mundo animal.