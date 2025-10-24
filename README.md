# API Ejercicio 3 - CI/CD Workshop

## 🚀 APIs .NET 9.0

Este proyecto contiene 3 APIs simples para demostrar CI/CD con Docker, Kubernetes y ArgoCD.

### APIs:
- **Api1**: Puerto 5001 - http://localhost:5001/swagger
- **Api2**: Puerto 5002 - http://localhost:5002/swagger
- **Api3**: Puerto 5003 - http://localhost:5003/swagger

### Ejecutar localmente:

```bash
docker-compose up --build
```

### Endpoints de salud:
- http://localhost:5001/health
- http://localhost:5002/health
- http://localhost:5003/health

### Docker Hub:
- ricardo0889/api1:latest
- ricardo0889/api2:latest
- ricardo0889/api3:latest

