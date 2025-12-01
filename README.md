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
Si prefieres variables en vez de flags, puedes usar:
```yaml
environment:
  - WATCHTOWER_LABEL_ENABLE=true
  - WATCHTOWER_CLEANUP=true
  - WATCHTOWER_SCHEDULE=0 30 3 * * *
  - WATCHTOWER_TIMEOUT=30s
```

## Buenas prácticas
- Etiqueta solo servicios que quieras actualizar
- Usa horarios de baja actividad (madrugada)
- Revisa logs tras cambios de versiones críticas
- Mantén backups si actualizas servicios con datos persistentes
- Excluye servicios que gestionas manualmente (`enable=false`)

## Notificaciones

Watchtower usa **env_file** para cargar variables desde `.env`, simplificando la configuración.

### Configuración básica

1. Copia el archivo de ejemplo:
```bash
cp .env.example .env
```

2. Edita `.env` con tu configuración:
```env
WATCHTOWER_NOTIFICATIONS=shoutrrr
WATCHTOWER_NOTIFICATION_URL=smtp://tu-servidor-smtp:25/?from=noreply@midominio.com&to=admin@midominio.com&subject=Watchtower%20-%20Actualizaciones
WATCHTOWER_NOTIFIER_LEVEL=info
WATCHTOWER_NOTIFICATIONS_REPORT=true
```

3. Reinicia Watchtower:
```bash
docker compose up -d
```

### SMTP sin autenticación (puerto 25)

Ideal para servidores relay internos o Microsoft 365 Mail Protection:

```env
# Microsoft 365 Mail Protection (sin autenticación)
WATCHTOWER_NOTIFICATION_URL=smtp://midominio-com.mail.protection.outlook.com:25/?from=noreply@midominio.com&to=admin@midominio.com&subject=Watchtower%20-%20Actualizaciones
```

**Nota sobre Microsoft 365 Mail Protection:**
- Endpoint format: `tudominio-com.mail.protection.outlook.com` (reemplaza `.` del dominio por `-`)
- Puerto 25, sin autenticación ni TLS
- Requiere configuración previa de conector de entrada en Microsoft 365
- Solo acepta correos desde IPs autorizadas (configura en Exchange Admin Center)

### SMTP con autenticación (puerto 587)

Para servidores que requieren credenciales:

```env
WATCHTOWER_NOTIFICATION_URL=smtp://USUARIO:PASS@smtp.example.com:587/?from=watchtower@midominio.com&to=ops@midominio.com&subject=Watchtower%20Actualizaciones&starttls=Yes
```

**Nota:** Escapa caracteres especiales en usuario/contraseña si es necesario.

### Múltiples destinatarios

Separa direcciones con comas:
```env
WATCHTOWER_NOTIFICATION_URL=smtp://servidor:25/?from=noreply@midominio.com&to=admin@midominio.com,devops@midominio.com&subject=Watchtower%20-%20Actualizaciones
```

### Múltiples canales

Separa URLs con punto y coma:
```env
WATCHTOWER_NOTIFICATION_URL=smtp://servidor:25/?from=noreply@midominio.com&to=admin@midominio.com;telegram://TOKEN@telegram?channels=CHAT_ID
```

## Troubleshooting
```bash
docker logs watchtower --tail=200
```

---
Última actualización: Nov 2025 (compose con flags)

## Ejemplos de etiquetas

### Portainer (actualizable)
```yaml
labels:
  - "com.centurylinklabs.watchtower.enable=true"
```

### NGINX Proxy Manager (excluir)
```yaml
labels:
  - "com.centurylinklabs.watchtower.enable=false"
```

### Servicios críticos (actualizar manualmente)
- Programa ventanas con `--schedule`
- Opcional: parar primero y usar `WATCHTOWER_INCLUDE_STOPPED=true`

