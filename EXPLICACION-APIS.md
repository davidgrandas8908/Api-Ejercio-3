# 📚 Explicación de las 3 APIs

## Resumen

Este proyecto contiene **3 APIs REST idénticas** construidas con **ASP.NET Core 9.0** que proporcionan pronósticos del clima de manera aleatoria. Son APIs de demostración diseñadas específicamente para el **taller de CI/CD**.

---

## 🌤️ API 1, API 2 y API 3 - Weather Forecast Service

### ¿Qué hacen?

Las 3 APIs son servicios REST que proporcionan **pronósticos del clima** de manera aleatoria. Son APIs de ejemplo que vienen por defecto cuando creas un proyecto Web API en .NET.

### Endpoint principal: `/weatherforecast`

**Método**: `GET`  
**URLs**: 
- **API 1**: `http://localhost:5001/weatherforecast`
- **API 2**: `http://localhost:5002/weatherforecast`
- **API 3**: `http://localhost:5003/weatherforecast`

**Respuesta**: Devuelve un array JSON con 5 pronósticos del clima para los próximos 5 días.

### Ejemplo de respuesta:

```json
[
  {
    "date": "2025-10-25",
    "temperatureC": 18,
    "temperatureF": 64,
    "summary": "Mild"
  },
  {
    "date": "2025-10-26",
    "temperatureC": 41,
    "temperatureF": 105,
    "summary": "Balmy"
  },
  {
    "date": "2025-10-27",
    "temperatureC": 4,
    "temperatureF": 39,
    "summary": "Chilly"
  },
  {
    "date": "2025-10-28",
    "temperatureC": -8,
    "temperatureF": 18,
    "summary": "Freezing"
  },
  {
    "date": "2025-10-29",
    "temperatureC": 31,
    "temperatureF": 87,
    "summary": "Hot"
  }
]
```

---

## 🔍 ¿Cómo funcionan internamente?

### 1. Definición de descripciones del clima

```csharp
var summaries = new[]
{
    "Freezing", "Bracing", "Chilly", "Cool", "Mild", 
    "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
};
```

### 2. Endpoint GET que genera pronósticos aleatorios

```csharp
app.MapGet("/weatherforecast", () =>
{
    var forecast = Enumerable.Range(1, 5).Select(index =>
        new WeatherForecast(
            DateOnly.FromDateTime(DateTime.Now.AddDays(index)), // Fecha futura
            Random.Shared.Next(-20, 55),                        // Temperatura aleatoria (-20°C a 55°C)
            summaries[Random.Shared.Next(summaries.Length)]     // Descripción aleatoria
        ))
        .ToArray();
    return forecast;
})
.WithName("GetWeatherForecast");
```

### 3. Modelo de datos

```csharp
record WeatherForecast(DateOnly Date, int TemperatureC, string? Summary)
{
    public int TemperatureF => 32 + (int)(TemperatureC / 0.5556); // Convierte °C a °F
}
```

**Explicación del modelo:**
- `Date`: Fecha del pronóstico
- `TemperatureC`: Temperatura en grados Celsius
- `TemperatureF`: Temperatura en grados Fahrenheit (calculada automáticamente)
- `Summary`: Descripción del clima (ej: "Mild", "Hot", "Freezing")

---

## 🎯 Propósito de tener 3 APIs idénticas

Para el **taller de CI/CD**, tener 3 APIs nos permite demostrar:

1. ✅ **Microservicios**: Simular una arquitectura de microservicios
2. ✅ **Orquestación**: Docker Compose manejando múltiples contenedores
3. ✅ **Kubernetes**: Desplegar múltiples servicios en un cluster
4. ✅ **Load Balancing**: Distribuir carga entre servicios
5. ✅ **Escalabilidad**: Escalar servicios independientemente
6. ✅ **CI/CD Pipeline**: Construir y desplegar múltiples imágenes Docker

---

## 🔧 Características técnicas

| Característica | Descripción |
|----------------|-------------|
| **Framework** | ASP.NET Core 9.0 (Minimal API) |
| **Lenguaje** | C# 12 |
| **Protocolo** | HTTP (sin HTTPS para simplificar) |
| **Puerto interno** | 8080 (dentro del contenedor Docker) |
| **Puertos externos** | 5001 (API 1), 5002 (API 2), 5003 (API 3) |
| **Documentación** | OpenAPI/Swagger UI |
| **Health Check** | `/health` (configurado en Helm Charts) |
| **Formato respuesta** | JSON |
| **Contenedorización** | Docker con multi-stage build |

---

## 🌐 Endpoints disponibles

### Todas las APIs exponen los siguientes endpoints:

| Endpoint | Método | Descripción |
|----------|--------|-------------|
| `/weatherforecast` | GET | Obtiene 5 pronósticos del clima aleatorios |
| `/openapi/v1.json` | GET | Especificación OpenAPI en formato JSON |
| `/swagger` | GET | Interfaz Swagger UI (solo en Development) |
| `/health` | GET | Health check endpoint (configurado en Kubernetes) |

---

## 🐳 Arquitectura Docker

### Dockerfile multi-stage

Cada API utiliza un Dockerfile optimizado con múltiples etapas:

1. **Base**: Imagen runtime de .NET 9.0
2. **Build**: Imagen SDK para compilar el código
3. **Publish**: Publica la aplicación optimizada
4. **Final**: Copia los archivos publicados a la imagen base

### Docker Compose

Las 3 APIs se orquestan con Docker Compose:

```yaml
services:
  api1:
    build: ./Api1
    ports: ["5001:8080"]
    
  api2:
    build: ./Api2
    ports: ["5002:8080"]
    
  api3:
    build: ./Api3
    ports: ["5003:8080"]
```

---

## ☸️ Despliegue en Kubernetes

### Helm Charts

Las APIs se despliegan en Kubernetes usando Helm Charts que incluyen:

- **Deployments**: Un deployment por cada API
- **Services**: ClusterIP services para comunicación interna
- **Ingress**: Opcional, para acceso externo
- **HPA**: Horizontal Pod Autoscaler para escalado automático
- **Health Checks**: Liveness y Readiness probes

### Configuración de recursos

```yaml
resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 250m
    memory: 256Mi
```

---

## 🚀 Cómo probar las APIs

### 1. Localmente con Docker Compose

```bash
cd Api-Ejercio-3
docker-compose up --build
```

Luego abre en tu navegador:
- http://localhost:5001/swagger
- http://localhost:5002/swagger
- http://localhost:5003/swagger

### 2. Con curl

```bash
# API 1
curl http://localhost:5001/weatherforecast

# API 2
curl http://localhost:5002/weatherforecast

# API 3
curl http://localhost:5003/weatherforecast
```

### 3. Con PowerShell

```powershell
Invoke-RestMethod -Uri "http://localhost:5001/weatherforecast"
Invoke-RestMethod -Uri "http://localhost:5002/weatherforecast"
Invoke-RestMethod -Uri "http://localhost:5003/weatherforecast"
```

---

## 📦 Imágenes Docker

Las imágenes están publicadas en Docker Hub:

- `ricardo0889/api1:latest`
- `ricardo0889/api2:latest`
- `ricardo0889/api3:latest`

---

## 🔄 Pipeline CI/CD

### Flujo completo:

1. **Código fuente**: GitHub → https://github.com/davidgrandas8908/Api-Ejercio-3
2. **Build**: Azure DevOps construye las imágenes Docker
3. **Push**: Imágenes se suben a Docker Hub
4. **Helm Charts**: GitHub → https://github.com/davidgrandas8908/-Helm-charts
5. **Deploy**: ArgoCD despliega en DigitalOcean Kubernetes
6. **GitOps**: Cambios en Git activan despliegues automáticos

---

## 💡 Próximas mejoras

Si se requiere mayor realismo, las APIs podrían diferenciarse:

- **API 1**: Weather Service (clima) ☁️
  - Endpoints: `/weatherforecast`, `/current`, `/forecast/{days}`
  
- **API 2**: Products Service (productos) 🛒
  - Endpoints: `/products`, `/products/{id}`, `/categories`
  
- **API 3**: Users Service (usuarios) 👥
  - Endpoints: `/users`, `/users/{id}`, `/profile`

---

## 📚 Referencias

- [ASP.NET Core Documentation](https://docs.microsoft.com/aspnet/core)
- [Docker Documentation](https://docs.docker.com)
- [Kubernetes Documentation](https://kubernetes.io/docs)
- [Helm Documentation](https://helm.sh/docs)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io)

---

## 👥 Autor

**Taller de CI/CD - Arquitectura de Software 1**

Universidad - 2025


