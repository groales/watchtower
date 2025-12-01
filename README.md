# Watchtower — Actualización automática de contenedores

## Qué es
[Watchtower](https://containrrr.dev/watchtower/) monitoriza imágenes y actualiza contenedores automáticamente cuando hay nuevas versiones.

## Características
- Actualiza solo contenedores etiquetados (flag `--label-enable`)
- Limpieza de imágenes antiguas (flag `--cleanup`)
- Programación vía cron con flag `--schedule`
- Tiempo de gracia en parada (`--stop-timeout`)
- Notificaciones (Shoutrrr: Telegram, Slack, Email, etc.)

## Despliegue
```bash
git clone https://git.ictiberia.com/groales/watchtower
cd watchtower
docker compose up -d
```

## Etiquetado de servicios
Para que un servicio se auto-actualice, añade la label:
```yaml
labels:
  - "com.centurylinklabs.watchtower.enable=true"
```
Para excluir explícitamente:
```yaml
labels:
  - "com.centurylinklabs.watchtower.enable=false"
```

## Configuración (flags usados)
El `docker-compose.yaml` utiliza flags en lugar de variables de entorno para mayor claridad:
```yaml
command:
  - --label-enable          # Solo contenedores con label enable=true
  - --cleanup               # Elimina imágenes antiguas tras actualizar
  - --schedule=0 30 3 * * * # Ejecuta cada día a las 03:30 (TZ configurado)
  - --stop-timeout=30s      # Tiempo de gracia al parar contenedores
```

## Buenas prácticas
- Etiqueta solo servicios que quieras actualizar
- Usa horarios de baja actividad (madrugada)
- Revisa logs tras cambios de versiones críticas
- Mantén backups si actualizas servicios con datos persistentes
- Excluye servicios que gestionas manualmente (`enable=false`)

## Configuración de compatibilidad

Watchtower requiere especificar la versión de API de Docker para compatibilidad:

```yaml
environment:
  - TZ=Europe/Madrid
  - DOCKER_API_VERSION=1.44
```

Esto asegura que Watchtower funcione correctamente con versiones modernas de Docker Engine.

## Troubleshooting
```bash
docker logs watchtower --tail=200
```

---
Última actualización: Nov 2025 (compose con flags)

