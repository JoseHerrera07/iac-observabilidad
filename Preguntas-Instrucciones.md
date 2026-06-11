# Preguntas e Instrucciones — Laboratorio de Observabilidad
**Alumno:** Herrera Angulo Jose Miguel - 000245501  
**Curso:** Infraestructura como Código

---

## Instrucciones para validar el trabajo

### 1. Clonar el repositorio
```bash
git clone https://github.com/JoseHerrera07/iac-observabilidad.git
cd iac-observabilidad
```

### 2. Levantar el stack
```bash
docker compose up -d --build
```

### 3. Verificar que todos los servicios estén corriendo
```bash
docker compose ps
```

### 4. Acceder a los servicios
| Servicio | URL |
|---|---|
| Frontend | http://localhost:8080 |
| Backend | http://localhost:3001/metrics |
| Grafana | http://localhost:3000 (admin/admin) |
| Prometheus | http://localhost:9090 |
| Loki | http://localhost:3100 |
| Alloy | http://localhost:12345 |

### 5. Validar el dashboard
- Ingresar a Grafana en http://localhost:3000
- Ir a Dashboards y abrir **"Observabilidad - Herrera Jose"**
- Verificar los 4 paneles: CPU contenedor, CPU host, Logs de aplicación, Logs de infraestructura

### 6. Validar la alarma
- Ir a Alerting → Alert rules y verificar la regla **"CPU backend > 50%"**
- En el frontend http://localhost:8080 presionar **"Generar carga de CPU (30s)"**
- Observar cómo la alarma pasa de Normal → Pending → Firing

### 7. Validar el ciclo alarma → log
- Con la alarma en Firing, ir al panel "Logs de infraestructura"
- Verificar que aparecen logs con `msg="Sending alerts to local notifier"`

---

## Respuestas a las Preguntas 

### 1. ¿Por qué necesitamos Loki además de Prometheus si ya tenemos `/metrics`?

Porque Prometheus en si solo almacena métricas como % de CPU, memoria o  peticiones por segundo mientras Loki se encarga de almacenar logs  como eventos o errores y en esta actividad se puede ver justamente como Prometheus nos muestra el % de CPU del host y del contenedor, pero para ver mensajes como ERROR o WARN del backend y frontend necesitamos a Loki. Basicamente son dos tipos de datos completamente distintos que requieren herramientas distintas.

---

### 2. ¿Qué ventaja aporta que las fuentes de datos de Grafana estén aprovisionadas como código?

La ventaja se puede ver desde que levantamos el stack con docker compose up -d --build al inicio de la actividad , se ve como Prometheus y Loki ya estan configurados en Grafana sin tener que hacer algun clic o algun paso manual extra, ya que están definidos en archivos de configuracion versionables. En si le ventaja es que no se tiene que configurar manualmente cada vez ya que cuando se levanta el stack ya esta todod listo 

---

### 3. ¿Por qué el panel "CPU contenedor" y el panel "CPU host" pueden mostrar valores distintos? ¿Cuál usarías para alertar sobre una aplicación concreta?

Porque el panel cpu del host mostraba el consumo total de la maquina incluyendo todos los procesos del sistema operativo y los contenedores, mientras que el panel del cpu contenedor backend mostraba unicamente el consumo del contenedor del backend. 
Para alertar sobre una aplicación concreta usaria el panel del contenedor ya que que mide específicamente los contenedores y no el host completo.
Ademas si llegara a usar el del host la alarma podria dispararse por culpa de otro proceso que no tiene nada que ver.


---

### 4. ¿Qué diferencia hay entre el evaluation interval y el pending period de una alarma?

La diferencia es que el evaluation interval nos dice cada cuánto Grafana evalua si la cpu superaba el 50%, es decir que cada 10 segundos , mientras que el pending period es cuánto tiempo debe mantenerse superado este 50% antes de dar la alarma (Firing), eneste caso lo pusimos en 30 segundos . (asi esta configurado segun las indicaciones)