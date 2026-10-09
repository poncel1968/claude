# Glosario vivo FedPat

Agente de consulta de términos del proyecto Flock – Federación Patronal, para personas que recién llegan o que no son del negocio. Se publica como Artifact de claude.ai.

## Qué hace

- **Preguntar:** el usuario escribe un término; Claude responde en lenguaje simple usando el glosario oficial y cita los términos usados.
- **Feedback:** al final de cada respuesta pregunta si fue satisfactoria. Si no, el usuario puede enviar una aclaración.
- **Respuestas alternativas:** las aclaraciones no cambian la definición oficial; se muestran como aporte sin revisar hasta que se validan.
- **Revisar aportes (editores):** cola de aportes y de respuestas cuestionadas. Cada aporte se contrasta automáticamente con IA (glosario y, para el dueño, sus correos de Microsoft 365) y el revisor decide: validar, incorporar a la definición oficial (la anterior queda en el historial) o marcar como no válido con motivo.
- **Buscar en correos (dueño):** si un término no está, propone una definición a partir de Outlook.

## Archivos

| Archivo | Contenido |
| --- | --- |
| `glosario.html` | La página completa (HTML, CSS y JS en un solo archivo). |
| `seed/terms.json` | Los 70 términos iniciales, armados a partir de correos de agosto–octubre 2026. |

## Dependencias de la plataforma

La página usa el runtime de Artifacts de claude.ai (`window.claude.use(...)`), así que **solo funciona publicada como Artifact**. Abierta como archivo suelto o en GitHub Pages muestra la interfaz pero no tiene base de datos ni IA.

Capacidades declaradas al publicar:

```json
{
  "db": { "rules": [ { "path": "terms", "read": "view", "write": "admin" } ] },
  "user": { "scopes": ["profile"] },
  "sample": {},
  "mcp": { "servers": [ { "server": "Microsoft 365", "tools": ["outlook_email_search", "read_resource"] } ] }
}
```

## Modelo de datos (colección → campos principales)

- `terms/{slug}`: `term`, `aliases[]`, `category` (actores, negocio, sistemas, jerga), `definition`, `where`, `status` (oficial, a_confirmar), `note`, `history[]`, `updatedAt`, `updatedBy`.
- `clarifications/{id}`: `termSlug`, `termLabel`, `text`, `source`, `question`, `answer`, `feedbackId`, `authorId`, `status` (pendiente, validada, incorporada, rechazada, alternativa, descartada), `reviewNote`, `reviewedBy`, `reviewedAt`, `aiCheck`.
- `feedback/{id}`: `question`, `answer`, `satisfied`, `terms[]`, `reviewed`, `resolution`.

Solo los editores pueden modificar `terms`. Cualquier colaborador puede enviar aclaraciones y feedback.

## Criterio ante contradicciones

Prevalece la definición del área comercial o de negocio. Las contradicciones resueltas se guardan en el campo `note` del término.
