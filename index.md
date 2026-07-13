Rio_Salado: AI-HydroFlow Navigation System
DJI Enterprise AI Challenge 2026 — Autonomous Level-4 hydrological surveillance platform for the Cuenca del Salado, Buenos Aires Province, Argentina.

CI/CD — Manifold 3 ARM64 Platform ROS 2 License

📋 Summary
AI-HydroFlow Salado is a Level-4 autonomous UAV system designed for real-time tracking of surface water runoff in the ultra-flat pampean terrain of the Cuenca del Salado. By fusing centimetre-level RTK positioning, LiDAR-derived Digital Terrain Models (DTM), and thermal water segmentation, the system computes the dominant hydrological flow direction at 10 Hz and autonomously navigates the UAV along active drainage paths — enabling early-warning flood modelling and environmental assessment missions without human intervention.

🛠 Hardware Architecture
Component	Model	Role
UAV	DJI Matrice 350 RTK	Aerial platform — IP55 weatherproofing, 38 min endurance
Onboard Computer	DJI Manifold 3 (NVIDIA Jetson Orin NX)	ARM64 edge inference at 10–20 TOPS
LiDAR	DJI Zenmuse L2	High-density point cloud → DTM generation
Thermal Camera	DJI Zenmuse H30T	LWIR thermal segmentation of water surfaces
GNSS	Integrated D-RTK 2 network	Centimetre-level WGS-84 positioning
Obstacle Avoidance	Omnidirectional optical + Radar CSM	Active collision avoidance
🔬 Core Innovation — Thermo-Topographic Gradient Navigation
The Challenge: Ultra-Flat Terrain
The Cuenca del Salado presents slopes often below 0.3 %, making standard optical flow or slope-based navigation unreliable. Raw LiDAR point clouds contain micro-relief noise that masks the true macro-drainage gradient.

2-D Gaussian DTM Smoothing
Raw LiDAR elevations are smoothed with a 2-D Gaussian filter (σ = 2.0 pixels ≈ 0.5 m at 0.25 m/px resolution) to suppress high-frequency noise while preserving the drainage macro-gradient:

Z_smooth = G_σ * Z_raw
Implementation (src/hydro_logic.py):

from scipy.ndimage import gaussian_filter

def apply_gaussian_filter(matrix, sigma=2.0):
    return gaussian_filter(matrix, sigma=sigma)
Numerical Flow Gradient (∇z)
The dominant flow direction is derived from the numerical gradient of the smoothed DTM, negated to point downhill, then gated by a binary water mask from thermal segmentation:

∇z = (∂Z/∂x, ∂Z/∂y)
flow_direction = −∇z / ‖∇z‖      # unit vector pointing downhill (ENU frame)
grad_row, grad_col = np.gradient(smoothed_dtm)
v_east  =  (-grad_col)[water_mask].mean()   # column axis → East
v_north = -(-grad_row)[water_mask].mean()   # row↓ = south, so negate for North
The final bearing blends the topographic gradient (weight 0.7) with the thermal-gradient bearing (weight 0.3) for robustness during LiDAR occlusion by dense vegetation.

🤖 ROS 2 Node Architecture
/dji_osdk/rtk_position      ──►┐
/dji_osdk/lidar_pointcloud  ──►│  HydroFlowPilot Node  ──► /dji_osdk/velocity_cmd
/dji_osdk/thermal_image     ──►┘
/dji_osdk/battery_percentage ─►
Update rate: 10 Hz control loop (src/main.py)
Cruise altitude: 70 m AGL (RTK-corrected altitude hold)
Coordinate frame: WGS-84 horizontal / ENU local velocity frame
Failsafe System
Condition	Automated Response
Battery ≤ 20 %	rtk_manager.select_nearest_node() → centimetre-precision RTK landing
Obstacle < 5 m	Lateral avoidance manoeuvre + resume heading
Power-line radar alert	+50 m altitude gain + waypoint reroute
RTK signal lost > 5 s	Hover + broadcast distress beacon
Geofence breach	Immediate return-to-home
🐳 Setup & Deployment
Prerequisites
Docker with Buildx (for ARM64 cross-compilation)
Python ≥ 3.8 with numpy, scipy, pytest
Local Development (x86-64)
# Install Python dependencies
pip install -r requirements.txt

# Run unit tests
PYTHONPATH=src pytest tests/unit/test_hydro_logic.py -v
Docker (ARM64 — DJI Manifold 3)
The Docker image is based on NVIDIA L4T r35.2.1 (nvcr.io/nvidia/l4t-base:r35.2.1), the official NVIDIA Linux-for-Tegra image compatible with the Manifold 3's Jetson Orin NX.

# Build for ARM64 (requires QEMU on x86 host)
docker buildx build \
  --platform linux/arm64 \
  -t ai-hydroflow-salado:latest \
  -f docker/Dockerfile .

# Run tests inside the ARM64 container
docker run --rm --platform linux/arm64 \
  ai-hydroflow-salado:latest
Production Deployment
# OTA update to Manifold 3 via LTE link
docker push <registry>/ai-hydroflow-salado:latest
# Manifold 3 pulls the new image automatically via the onboard OTA agent
⚙️ CI/CD Pipeline
GitHub Actions (.github/workflows/ci_cd_manifold.yml) runs three sequential jobs on every push to main or develop:

Unit Tests (x86-64) — Fast pytest validation of hydro_logic.py with coverage report.
Build ARM64 Docker Image — Cross-compiles the L4T image via QEMU ARM64 emulation.
Integration Tests (ARM64 via QEMU) — Runs the full pytest suite inside the ARM64 container to validate the binary-compatible build.


Specialist in UAV-based environmental sensing, ROS 2 navigation systems and edge AI deployment on NVIDIA Jetson platforms.

© 2026 Emiliano Perez — AI-HydroFlow Salado. Submitted to DJI Enterprise AI Challenge 2026.

DroneInventoryScanner — Escáner de inventario con drone
📌 Resumen
Aplicación Android diseñada para escanear códigos de barras de forma manos libres durante operaciones de inventario con drones. Conecta un escáner de anillo Bluetooth mediante SPP (Serial Port Profile), valida los códigos recibidos, detecta duplicados, da feedback auditivo en español y exporta los registros a CSV, todo mientras corre como un servicio en primer plano que permite usar otras apps (p. ej. DJI Fly) al mismo tiempo.

📊 Estado del Proyecto
Nivel de avance: 80%
Fase actual: Realizando pruebas de vuelo y ajustes de lectura de códigos de barra
⚙️ Arquitectura Técnica
Hardware / Plataforma:

Dispositivo Android con Bluetooth clásico (mínimo Android 8.0 / API 26, target API 34).
Dispositivo de referencia: Xiaomi Redmi Note 13 Pro.
Escáner de anillo Bluetooth compatible con SPP (Serial Port Profile), UUID 00001101-0000-1000-8000-00805F9B34FB (ej. Eyoyo/Ring Scanners, JR Model HC-Z38W).
Se usa junto con drones DJI como herramienta complementaria a DJI Fly; no integra SDK de dron.
Stack de Software:

Lenguaje: Kotlin.
Plataforma: Android SDK, Gradle 7.4.2, Kotlin 1.8.0.
Arquitectura: MVVM + Clean Architecture (data, bluetooth, service, ui, overlay, session).
Librerías principales: Android Jetpack (ViewModel, LiveData, Lifecycle, AppCompat, ConstraintLayout, Material Design Components), Kotlin Coroutines.
Sistema: Bluetooth SPP, Text-to-Speech en español (es_ES), MediaStore API para exportar a Downloads (con fallback a File API en Android 9-), SharedPreferences para sesión, WindowManager para overlays flotantes.
Testing: JUnit 4, kotlinx-coroutines-test, AndroidJUnit4, Espresso.
CI/CD: GitHub Actions (build de APK, ejecución de tests unitarios, release automático y commit de APK a release/).
🚀 Descripción Funcional
Entrada de datos: el usuario configura una sesión (Cliente y Sector) y conecta un escáner de anillo Bluetooth. Cada código de barras escaneado llega por SPP, se limpia de caracteres de control (\r, \n), se valida y se registra en memoria con timestamp.
Lógica principal: un ScannerService en primer plano mantiene la conexión Bluetooth con auto-reconexión de hasta 5 intentos; ScanRepository detecta duplicados usando un jitter filter de 2 segundos y permite forzar la carga si el usuario lo decide; OverlayService muestra HUDs flotantes de éxito o duplicado sobre otras apps, mientras el TTS da feedback de voz en español.
Resultado esperado: al finalizar la sesión se exporta un archivo CSV a la carpeta pública Downloads con el nombre [Cliente]_[Sector]_[AAAAMMDD_HHMMSS].csv, listo para auditoría o integración con sistemas de inventario, sin interrumpir la operación del dron.



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
