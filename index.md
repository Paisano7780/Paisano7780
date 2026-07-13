ConteoVectorial — Contador de Ganado con Visión por Computadora
📌 Resumen
Aplicación Android para vuelos con dron que realiza conteo vectorial de ganado en tiempo real mediante Edge AI. Procesa el stream de vídeo FPV del dron, detecta los keypoints de cada animal (cabeza y cola) con un modelo YOLO-Pose optimizado en NCNN, rastrea cada individuo como un vector y lo cuenta cuando cruza una línea virtual. También permite registrar capturas georreferenciadas para validación y re-entrenamiento del modelo. Resuelve el problema de conteos manuales lentos, subjetivos y difíciles de auditar desde el aire.

📊 Estado del Proyecto
Nivel de avance: 70% de la Etapa 1 (solo RGB)
Fase actual: Realizando pruebas de vuelo y reentrenamiento del modelo de detección
⚙️ Arquitectura Técnica
Hardware / Plataforma:

Drones DJI compatibles con DJI Mobile SDK V5 (inicialmente probado con DJI Mini 3 / Neo; arquitectura preparada para aeronaves DJI de consumo y enterprise).
Dispositivo móvil Android conectado al control remoto (target arm64-v8a, ej. Xiaomi Redmi Note 13 Pro).
Cámara RGB del dron, GPS, IMU, gimbal y telemetría de vuelo.
Stack de Software:

Lenguajes: Kotlin, C++17 (JNI).
Frameworks / librerías: Android SDK 35, DJI MSDK V5 + DJI UXSDK V5 (FPVWidget, BatteryWidget, GpsSignalWidget, TakeOffWidget), OpenCV 4.10.0, NCNN (inferencia YOLO-Pose), Room (SQLite), CMake, KSP, Kotlin Coroutines.
Build: Gradle con Kotlin DSL, GitHub Actions para CI/CD Android.
🚀 Descripción Funcional
Entrada de datos: recibe el stream de vídeo crudo en formato NV21 desde la cámara del dron, la telemetría del vuelo (GPS, altitud, actitud de la aeronave, ángulos del gimbal, batería, modo de vuelo) y los comandos del operador (toggle IA, toggle REC, zoom pinch, tap para enfoque/exposición y reset óptico).
Lógica principal / core del sistema: un pipeline en C++/JNI convierte el buffer YUV a RGB, lo alimenta a un modelo YOLO11 Pose en NCNN para extraer centroides y keypoints (cabeza y cola), un CentroidTracker empareja detecciones entre frames y una línea de conteo virtual incrementa el total cuando un vector cruza de arriba hacia abajo. El overlay de flechas se dibuja con una vista custom VectorOverlayView sobre el FPVWidget.
Resultado esperado / acción física: el operador visualiza en pantalla el conteo total y la orientación de cada animal detectado; al activar REC, la app persiste pares .jpg + .txt (formato YOLO-Pose) con metadatos de georreferencia en Room, almacenándolos en VectorCount_Dataset para luego exportarlos como .zip y re-entrenar el modelo.

BuscaBasura
📌 Resumen
BuscaBasura es una aplicación Android diseñada para operar desde una estación de vuelo (teléfono/tablet) conectada a un dron DJI. Su objetivo es detectar, georreferenciar y documentar focos de residuos (basurales) o humo mediante visión por computadora en tiempo real, mientras el dron sobrevuela el área de interés. El sistema combina la transmisión de video del dron, inferencia de modelos YOLOv11 optimizados para móvil y control de misión con palancas virtuales para el barrido sistemático de una zona.

📊 Estado del Proyecto
Nivel de avance: 60%
Fase actual: Pendiente de pruebas de vuelo
⚙️ Arquitectura Técnica
Hardware / Plataforma: Dron DJI compatible con DJI Mobile SDK V5 (p. ej. Mavic 3 Enterprise, M300 RTK, M350 RTK, M30/M30T, Mini 4 Pro), controlador RC-N1, dispositivo Android objetivo (ARM64, optimizado para Redmi Note 13 Pro), cámara ancha/tele del dron y datos de GPS/RTK.
Stack de Software: Kotlin + Java (Android), C++17 vía JNI, Android SDK 35 / NDK 26, DJI MSDK V5 5.16.0, UXSDK V5, Jetpack Compose, Room, OpenCV 4.10.0, NCNN, YOLOv11 (modelos .param/.bin), MapLibre (Mapbox SDK plugins), Gradle 8.4.2 y Ultralytics para validación del modelo.
🚀 Descripción Funcional
Entradas / datos de vuelo: recibe en tiempo real el stream de video en formato NV21 desde la cámara del dron, junto con la telemetría (ubicación 3D, altitud, actitud de aeronave y gimbal, batería, cantidad de satélites, modo de vuelo) y el estado de los sticks del radiocontrol; también permite importar o diseñar planes de misión en grilla y ajustar el punto de enfoque, zoom y exposición desde la pantalla táctil.
Core / lógica principal: registra el SDK DJI, muestra el FPV en un SurfaceView, ejecuta inferencia asíncrona nativa con dos redes NCNN intercambiables (modo óptico "basura" y modo pseudotérmico "humo"), dibuja bounding boxes codificadas por confianza y altitud, y controla la cámara (foco, zoom, modo foto/video). El motor de misión VirtualStickMissionEngine navega punto a punto usando Haversine y rumbo, con interruptor de seguridad por control manual y persistencia del índice de waypoint para reanudación.
Resultado esperado / acción física: el operador visualiza candidatos detectados y los confirma; por cada foco confirmado se genera un paquete de datos con imagen JPG, etiqueta YOLO .txt y metadato georreferenciado .geo (JSON), almacenado en disco y en base Room, y se puede consultar en la galería con mini-mapa. El dron puede realizar un barrido autónomo de grilla, detenerse ante RTH/batería baja y reanudar en el waypoint anterior.

AppMaintenance
📌 Resumen
AppMaintenance es una aplicación Android nativa para la gestión de mantenimiento de activos agrícolas, orientada inicialmente a drones DJI Agras y sus componentes (baterías, cargadores y controles remotos). Centraliza el registro de equipos, el seguimiento de inventario de repuestos, la ejecución de procedimientos de mantenimiento y la generación de órdenes de trabajo, con sincronización de archivos adjuntos a Google Drive.

📊 Estado del Proyecto
Nivel de avance: 50%
Fase actual: En desarrollo
⚙️ Arquitectura Técnica
Hardware / Plataforma: Dispositivos Android (smartphones / tablets) con API 26+; activos gestionados: drones DJI Agras T10, T20, T30, T40, baterías TB30/TB40, cargador T30 y control remoto RTK. Catálogo de modelos precargado desde assets/dji_agras_models.json.
Stack de Software: Kotlin, Android SDK 34, Gradle, Jetpack Compose, Room (base de datos local), Hilt (inyección de dependencias), WorkManager (tareas en segundo plano), Google Sign-In + Google Drive REST API (sincronización en la nube), Gson.
🚀 Descripción Funcional
Entrada de datos: Registra activos con número de serie, tipo, modelo, estado, sitio y atributos personalizados; mantiene inventario de repuestos, movimientos de stock, sitios, usuarios, procedimientos, pasos, respuestas, ejecuciones de mantenimiento, registros de mantenimiento y archivos adjuntos.
Lógica principal: Precarga un catálogo de tipos de activos desde un JSON; permite crear y consultar activos, órdenes de trabajo, inventario y procedimientos; controla stock mediante transacciones; y agenda un DriveUploadWorker periódico para subir adjuntos pendientes a Google Drive.
Resultado esperado: Genera un historial digital de mantenimiento, uso de repuestos y documentación adjunta, facilitando la trazabilidad de los equipos agrícolas y la sincronización de evidencia con Google Drive.
