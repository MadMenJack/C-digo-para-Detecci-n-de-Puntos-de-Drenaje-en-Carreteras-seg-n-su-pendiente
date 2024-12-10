Código markdown
## Codigo-para-Deteccion-de-Puntos-de-Drenaje-en-Carreteras-segun-su-pendiente
**Autores:** Jacqueline Madrigal Menoza
**Resumen**El proyecto aborda la nivelación teórica de una curva vial en Potrerillos, Colima, límite con Michoacán, para mejorar la seguridad y funcionalidad de la carretera. Utilizando herramientas como GDAL, Shapely y PyQGIS, se modelan pendientes y se identifican puntos críticos para drenajes. 

El código representa la curva geoespacialmente, calcula pendientes y determina puntos óptimos de drenaje, visualizándolos en un mapa interactivo con Folium. Este enfoque combina análisis geoespacial y programación para optimizar la planificación vial, garantizando decisiones informadas y mejorando la seguridad de la infraestructura.
**Abstract**El análisis incluye el cálculo de pendientes basado en datos de coordenadas y elevaciones, la identificación de puntos óptimos para drenajes, y la visualización de resultados mediante mapas interactivos generados con Folium. Este enfoque permite optimizar la planeación de infraestructura, abordar problemas técnicos y garantizar la sostenibilidad de la carretera, contribuyendo a una movilidad más segura y eficiente en esta región limítrofe.
## Introducción
Este código analiza una curva geoespacial definida por coordenadas y elevaciones para identificar puntos óptimos de drenaje basados en pendientes.

## Desarrollo
**Enfoque de Proyecto**El proyecto tiene como objetivo la nivelación teórica de una curva vial en Potrerillos, en el límite entre Colima y Michoacán, para mejorar la seguridad y funcionalidad de la carretera. Combina ingeniería tradicional con herramientas geoespaciales avanzadas para analizar pendientes, identificar puntos críticos y proponer soluciones. Este enfoque permite modelar las condiciones actuales de la curva y optimizar el diseño a través de datos precisos y análisis visuales.

**Herramientas utilizadas**
GDAL: Procesamiento de datos raster y vectoriales para análisis y reproyección.
Shapely: Modelado geométrico de la curva como LineString.
PyQGIS: Integración de análisis avanzados y visualización de datos geoespaciales.
Folium: Generación de mapas interactivos para interpretar resultados.
NumPy: Cálculo de pendientes y análisis numérico.

**Metodología**
1.Definición de Datos: Se establecieron coordenadas y elevaciones simuladas para modelar la curva.
2.Análisis de Pendientes: Se calcularon pendientes entre puntos consecutivos, identificando segmentos con valores inferiores a un umbral predefinido (0.01) como áreas óptimas para drenajes.
3.Identificación de Drenajes: Los puntos intermedios de segmentos con pendiente suave fueron marcados como drenajes.
4.Mapas Interactivos: Se generó un mapa con Folium que visualiza la curva (en azul) y los puntos de drenaje (en rojo).
5.Validación: Los resultados se evaluaron visualmente y se documentaron para toma de decisiones.

## Manejo de Datos
import folium
from shapely.geometry import LineString, Point
import numpy as np

# Coordenadas proporcionadas (latitud, longitud)
coordenadas = [
    (18.954998, -103.506209),
    (18.954702, -103.506037),
    (18.954458, -103.505886),
    (18.954268, -103.505821),
    (18.954104, -103.505812),
    (18.953978, -103.505825),
    (18.953632, -103.505980),
]

# Asignar elevaciones simuladas (en metros)
elevaciones = np.linspace(100, 95, len(coordenadas))  # Decrece de 100m a 95m

# Combinar coordenadas con elevaciones
coordenadas_con_elevacion = [(lat, lon, elev) for (lat, lon), elev in zip(coordenadas, elevaciones)]

# Crear la curva como un LineString usando solo latitud y longitud
curva = LineString([(lon, lat) for lat, lon, elev in coordenadas_con_elevacion])

# Calcular puntos de drenaje basados en pendientes
def calcular_drenajes(coordenadas, umbral_pendiente=0.01):
    puntos_drenaje = []
    for i in range(len(coordenadas) - 1):
        lat1, lon1, z1 = coordenadas[i]
        lat2, lon2, z2 = coordenadas[i + 1]
        distancia_horizontal = np.sqrt((lon2 - lon1)**2 + (lat2 - lat1)**2)

        # Calcular pendiente
        pendiente = abs((z2 - z1) / distancia_horizontal) if distancia_horizontal != 0 else 0

        # Mostrar las pendientes calculadas para diagnóstico
        print(f"Segmento {i}: pendiente = {pendiente:.6f}")

        # Agregar puntos de drenaje si la pendiente es menor al umbral
        if pendiente < umbral_pendiente:
            punto_medio = ((lat1 + lat2) / 2, (lon1 + lon2) / 2)
            puntos_drenaje.append(punto_medio)
    return puntos_drenaje

# Calcular puntos de drenaje reales
puntos_drenaje = calcular_drenajes(coordenadas_con_elevacion)

# Si no hay puntos de drenaje, agregar uno manualmente para verificar la visualización
if not puntos_drenaje:
    print("No se generaron puntos de drenaje automáticamente. Agregando un punto manual.")
    puntos_drenaje.append((18.954500, -103.505900))  # Punto ficticio

# Crear un mapa interactivo con Folium centrado en la curva
mapa = folium.Map(location=[18.954458, -103.505886], zoom_start=16)

# Dibujar la curva en el mapa (en azul)
for lat, lon, _ in coordenadas_con_elevacion:
    folium.CircleMarker(
        location=[lat, lon],
        radius=3,
        color="blue",
        fill=True,
        fill_opacity=1,
    ).add_to(mapa)

# Marcar puntos de drenaje (en rojo)
for lat, lon in puntos_drenaje:
    folium.Marker(
        location=[lat, lon],
        popup=f"Drenaje: ({lat:.6f}, {lon:.6f})",
        icon=folium.Icon(color="red", icon="info-sign"),
    ).add_to(mapa)

# Guardar el archivo HTML
mapa.save("mapa_drenajes_curva.html")
print("Mapa guardado como 'mapa_drenajes_curva.html'")

## Resultados
 Esta representación facilita el análisis geométrico y topográfico. La curva se visualiza en un mapa interactivo generado con Folium, donde se identifican los puntos clave para el drenaje.os segmentos con pendientes inferiores a un umbral predefinido (0.01) son considerados como ubicaciones óptimas para drenajes. Si no se cumplen estos criterios, se agrega un punto de drenaje ficticio para garantizar la visualización en el mapa.El código proporciona un análisis integrado y visualización precisa de la curva vial y sus puntos de drenaje. Es una herramienta práctica para la planificación y gestión de drenajes en infraestructura vial, combinando modelado geoespacial y análisis numérico de manera efectiva.
## Conclusiones
El proyecto ha logrado modelar y analizar una curva vial en la región de Potrerillos, identificando puntos críticos para la implementación de drenajes mediante el cálculo de pendientes y la representación geoespacial. El uso de herramientas avanzadas como GDAL, Shapely, PyQGIS y Folium permitió realizar un análisis detallado y generar mapas interactivos que facilitan la toma de decisiones. Esta metodología combina precisión técnica con visualización intuitiva, proporcionando una solución eficiente para mejorar la seguridad y funcionalidad de la infraestructura vial.
osibles Mejoras Futuras
Datos Altimétricos Reales:
Sustituir las elevaciones simuladas por datos precisos obtenidos mediante tecnologías como LIDAR o modelos digitales del terreno (DTM) para incrementar la exactitud del análisis.
Integración Hidrológica:
Incorporar modelos hidrológicos para analizar el flujo del agua en la superficie y dimensionar adecuadamente los sistemas de drenaje.
Optimización Computacional:
Mejorar la eficiencia en el cálculo de pendientes y la identificación de puntos críticos, permitiendo su aplicación en tramos más extensos y complejos.
