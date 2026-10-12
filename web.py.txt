
from flask import Flask, render_template_string

app = Flask(__name__)

HTML = """
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Compartir mi ubicación</title>

    <link rel="stylesheet"
      href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css">

    <style>
        body {
            font-family: Arial, sans-serif;
            background: #111827;
            color: white;
            text-align: center;
            padding: 25px 15px;
        }
        .panel {
            max-width: 650px;
            margin: auto;
            background: #1f2937;
            padding: 24px;
            border-radius: 16px;
        }
        button {
            background: #7c3aed;
            color: white;
            border: 0;
            padding: 14px 22px;
            border-radius: 9px;
            font-size: 16px;
            cursor: pointer;
        }
        #map {
            height: 350px;
            margin-top: 20px;
            border-radius: 12px;
            display: none;
        }
        #status {
            margin-top: 18px;
            overflow-wrap: anywhere;
        }
    </style>
</head>
<body>
    <div class="panel">
        <h1>📍 Compartir ubicación</h1>
        <p>
            Si deseas compartir tu ubicación, pulsa el botón.
            Tu navegador te pedirá permiso. Si lo rechazas,
            no se obtendrá tu posición.
        </p>

        <button onclick="shareLocation()">
            Aceptar y compartir ubicación
        </button>

        <p id="status" role="status">
            Tu ubicación todavía no se ha solicitado.
        </p>

        <div id="map"></div>
    </div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        let map;
        let marker;

        function shareLocation() {
            const status = document.getElementById("status");

            if (!navigator.geolocation) {
                status.textContent =
                    "Este navegador no permite obtener la ubicación.";
                return;
            }

            status.textContent =
                "Esperando tu permiso y la ubicación del dispositivo...";

            navigator.geolocation.getCurrentPosition(
                function(position) {
                    const lat = position.coords.latitude;
                    const lon = position.coords.longitude;

                    document.getElementById("map").style.display = "block";

                    if (!map) {
                        map = L.map("map").setView([lat, lon], 15);

                        L.tileLayer(
                            "https://tile.openstreetmap.org/{z}/{x}/{y}.png",
                            {
                                attribution: "&copy; OpenStreetMap"
                            }
                        ).addTo(map);

                        marker = L.marker([lat, lon])
                            .addTo(map)
                            .bindPopup("Tu ubicación")
                            .openPopup();
                    } else {
                        map.setView([lat, lon], 15);
                        marker.setLatLng([lat, lon]);
                    }

                    status.textContent =
                        "Ubicación mostrada en el mapa. " +
                        "Latitud: " + lat.toFixed(5) +
                        ", longitud: " + lon.toFixed(5) +
                        ". No se ha enviado a Discord.";

                    setTimeout(function() {
                        map.invalidateSize();
                    }, 100);
                },
                function(error) 
                    const messages = {
                        1: "No diste permiso para compartir la ubicación.",
                        2: "No se pudo determinar la ubicación.",
                        3: "Se agotó el tiempo de espera."
                    };

                    status.textContent =
                        messages[error.code] ||
                        "No se pudo obtener la ubicación.";
                },
                {
                    enableHighAccuracy: true,
                    timeout: 15000,
                    maximumAge: 0
                }
            );
        }
    </script>
</body>
</html>
"""

@app.route("/")
def home():
    return render_template_string(HTML)

if __name__ == "__main__":
    app.run(host="127.0.0.1", port=5000, debug=False)
