# 🐳 Balanceador de Carga — Nginx + Apache × 2

> Proyecto de demostración de load balancing con Docker Compose en Windows.

---

## 📐 Arquitectura

```
                  ┌──────────────────┐
 Cliente ──────▶  │  Nginx (lb-nginx)│  Puerto: 8080
                  │  Reverse Proxy   │
                  └────────┬─────────┘
                           │
              Round Robin  │
               ┌───────────┼───────────┐
               ▼                       ▼
     ┌──────────────────┐   ┌──────────────────┐
     │  Apache 1         │   │  Apache 2         │
     │  (lb-apache1)     │   │  (lb-apache2)     │
     │  Backend A        │   │  Backend B        │
     └──────────────────┘   └──────────────────┘
               │                       │
               └───────────┬───────────┘
                           │
                  ┌────────────────┐
                  │ Volumen Comp.  │
                  │ ./web-content  │
                  └────────────────┘
```

## 🚀 Cómo Usar

### Requisitos
- Docker Desktop para Windows (con WSL2 backend)
- Docker Compose v2+

### Levantar el stack
```powershell
cd C:\Users\Lautaro\Desktop\Balanaceador
docker compose up -d
```

### Verificar que los 3 contenedores están activos
```powershell
docker compose ps
```

### Abrir en el navegador
```
http://localhost:8080
```

### Ver logs en tiempo real
```powershell
docker compose logs -f
```

### Detener todo
```powershell
docker compose down
```

---

## 📁 Estructura del Proyecto

```
Balanaceador/
├── docker-compose.yml          ← Orquestación de los 3 servicios
├── README.md                   ← Este archivo
├── nginx/
│   └── default.conf            ← Configuración del reverse proxy
└── web-content/                ← Volumen COMPARTIDO (ambos Apache)
    ├── index.html              ← Página premium con Canvas animado
    ├── css/
    │   └── style.css           ← Design system dark mode
    └── img/
        ├── hero.png            ← Banner hero
        ├── datacenter.png      ← Foto datacenter
        └── diagram.png         ← Diagrama de arquitectura
```


## Fichero de configuracion del .yml

<img width="694" height="873" alt="image" src="https://github.com/user-attachments/assets/547b91a5-742f-4bb5-8aaf-171e4d41fb14" />




---

## 🔑 Puntos Clave

| Concepto | Detalle |
|----------|---------|
| **Balanceo** | Round Robin (Nginx alterna entre apache1 y apache2) |
| **Volumen compartido** | `./web-content` montado en ambos Apache |
| **Puerto público** | `8080` (Nginx) |
| **Red interna** | `lb-network` (bridge) |
| **Healthchecks** | Nginx espera a que ambos Apache estén healthy |
| **Detección** | Header `X-Upstream` en la respuesta muestra qué backend respondió |

---

## 🧪 Probar el Balanceo

```powershell
# Ejecuta varias veces y verás que alterna el header X-Upstream
curl -s -D - http://localhost:8080 -o NUL | findstr "X-Upstream"
```

---

## Prueba del Balanceo en modo WEB

![alt text](image-1.png)


![alt text](image-2.png)

