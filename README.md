# Watchtower — Actualización automática de contenedores

## Qué es
[Watchtower](https://containrrr.dev/watchtower/) monitoriza imágenes y actualiza contenedores automáticamente cuando hay nuevas versiones.

## Características
- Actualiza servicios etiquetados
- Limpieza de imágenes antiguas (`WATCHTOWER_CLEANUP=true`)
- Programación vía cron (`WATCHTOWER_SCHEDULE`)
- Notificaciones (Shoutrrr: Telegram, Slack, Email, etc.)

## Despliegue
```bash
git clone https://git.ictiberia.com/groales/watchtower
cd watchtower
docker compose up -d
```

## Etiquetado de servicios
Para que un servicio se auto-actualice, añade:
```yaml
labels:
  - "com.centurylinklabs.watchtower.enable=true"
```

## Variables de entorno
Edita `.env` o el `docker-compose.yaml`:
- `WATCHTOWER_SCHEDULE`: cron (UTC) p.ej. `0 0 3 * * *` (diario 03:00)
- `WATCHTOWER_CLEANUP`: `true` para borrar imágenes antiguas
- `WATCHTOWER_NOTIFICATIONS` y `WATCHTOWER_NOTIFICATION_URL`: ver Shoutrrr

## Buenas prácticas
- Etiqueta solo servicios que quieras actualizar
- Usa horarios de baja actividad
- Revisa logs en actualizaciones críticas
- Mantén backups si actualizas servicios de estado

## Troubleshooting
```bash
docker logs watchtower --tail=200
```

---
Última actualización: Nov 2025

## Ejemplos de etiquetas

### Portainer (actualizable)
`yaml
labels:
  - "com.centurylinklabs.watchtower.enable=true"
`

### NGINX Proxy Manager (excluir)
`yaml
labels:
  - "com.centurylinklabs.watchtower.enable=false"
`

### Servicios críticos (incluir solo en ventanas)
- Usa WATCHTOWER_SCHEDULE para horarios de mantenimiento.
- Considera WATCHTOWER_INCLUDE_STOPPED=true si paras antes.

