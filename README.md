🚁 Portfolio de Proyectos Tecnológicos: Desde el Aire

Bienvenido a mi portfolio de desarrollo. Aquí presento mis principales soluciones de software orientadas a potenciar las capacidades de vehículos aéreos no tripulados (UAVs), integrando visión por computadora, telemetría y procesamiento de datos en tiempo real.

🌊 AI-HydroFlow Rio Salado <Seleccionado para Showcase en el DJI AI Challenge 2026>

¿Qué hace?: Plataforma de vigilancia hidrológica autónoma para el rastreo en tiempo real de escurrimientos de agua en terrenos ultraplanos (Cuenca del Salado).

Hardware utilizado: Dron DJI Matrice 350 RTK, computadora a bordo DJI Manifold 3, cámara térmica y escáner topográfico LiDAR.

Lenguajes de programación: Python, ROS 2.

Mecánica de funcionamiento: El sistema procesa datos topográficos y térmicos en pleno vuelo para calcular hacia dónde corre el agua. Usando esta información, toma el control del dron y lo hace navegar de forma totalmente autónoma siguiendo el cauce natural del terreno, sin necesidad de que un piloto lo guíe.

📦 DroneInventoryScanner

¿Qué hace?: Aplicación Android para escanear códigos de barras con manos libres durante auditorías de inventario que involucran vuelos con drones.

Hardware utilizado: Dispositivo Android y escáner de anillo Bluetooth.

Lenguajes de programación: Kotlin.

Mecánica de funcionamiento: Se conecta por Bluetooth al escáner que el piloto lleva en el dedo. Corre en segundo plano (permitiendo tener la pantalla de vuelo del dron abierta al mismo tiempo), evita que se escanee dos veces el mismo producto, da confirmaciones por voz en español para no tener que mirar la pantalla, y exporta el inventario final a un archivo Excel/CSV.

🐄 ConteoVectorial

¿Qué hace?: Aplicación móvil que procesa el video del dron en tiempo real para realizar conteo automático de ganado mediante Inteligencia Artificial.

Hardware utilizado: Drones DJI (ej. Mini 3), dispositivo móvil Android y cámara RGB del dron.

Lenguajes de programación: Kotlin, C++ (JNI).

Mecánica de funcionamiento: Analiza el video en vivo de la cámara del dron detectando cada animal. Identifica la cabeza y la cola para saber en qué dirección se mueven, los sigue (trackea) en la pantalla y los suma automáticamente a la cuenta total cuando cruzan una línea virtual trazada por el usuario, eliminando el error del conteo manual.

♻️ BuscaBasura

¿Qué hace?: Sistema de visión por computadora para detectar, documentar y georreferenciar basurales clandestinos o focos de humo desde el aire.

Hardware utilizado: Drones DJI Enterprise (Mavic 3E, M300, etc.), control remoto y dispositivo Android.

Lenguajes de programación: Kotlin, Java, C++.

Mecánica de funcionamiento: El operador marca una zona y el dron realiza un barrido automático. Mientras vuela, la IA analiza el video para detectar basura o humo. Al encontrar un foco, guarda de forma automática una foto del lugar exacto con sus coordenadas GPS, creando un reporte listo para su auditoría en un mapa.

🛠️ AppMaintenance

¿Qué hace?: Plataforma móvil nativa para gestionar el mantenimiento, historial y stock de repuestos de drones agrícolas y sus periféricos.

Hardware utilizado: Dispositivos Android, Drones DJI Agras (T10 a T40), baterías y cargadores.

Lenguajes de programación: Kotlin.

Mecánica de funcionamiento: Centraliza el registro de todos los equipos. Permite a los técnicos seguir el paso a paso de los manuales de mantenimiento, descuenta los repuestos utilizados del inventario de forma automática, y sincroniza toda la evidencia (fotos, reportes de reparación) directamente en la nube usando Google Drive.
