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

