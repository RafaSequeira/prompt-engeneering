# 🗂️ Task Manager con IA — Guía Completa de Prompts para el Taller

> **App de ejemplo:** Un gestor de tareas web donde la IA ayuda a priorizar, categorizar, desglosar y gestionar el backlog de un equipo de desarrollo.

## ¿Cómo usar esta guía?

Cada sección cubre una técnica de prompting aplicada a una **feature específica** del Task Manager. El orden es progresivo: cada técnica construye sobre la anterior. Los prompts están listos para copiar y pegar en GitHub Copilot Chat, Claude o cualquier herramienta IA.

---

## La app que construimos: Task Manager Pro

Una app web con HTML, CSS y JavaScript vanilla que se construye incrementalmente:

| Feature | Técnica usada |
|---------|---------------|
| Scaffold básico de la app | Prompt Estándar |
| Sugerencia de prioridad automática | Zero-Shot |
| Asignación de categorías con formato consistente | One-Shot |
| Desglose robusto con casos borde | Few-Shot |
| Refinamiento de prompts con contexto | Ubicación del Contexto |
| Exportar tareas como JSON parseable | Salida Estructurada |
| Análisis profundo de tareas complejas | Chain of Thought |
| Prompts multi-sección organizados | Delimitadores |
| Revisión del backlog como Tech Lead | Personas |

---

## TÉCNICA 1 — Prompt Estándar

### ¿Qué es?
El tipo de prompt más básico: una instrucción directa sin ejemplos ni contexto adicional. La calidad del output depende directamente de la claridad y especificidad de la instrucción.

### El Prompt

```
Crea una aplicación web de gestión de tareas con HTML, CSS y JavaScript puro (sin frameworks).

La aplicación debe:
- Permitir agregar tareas con: título, descripción, prioridad (alta/media/baja) y categoría
- Listar todas las tareas en tarjetas visuales
- Permitir marcar tareas como completadas
- Permitir eliminar tareas
- Tener un diseño limpio y moderno con colores que diferencien las prioridades
- Guardar las tareas en localStorage

Usa una paleta de colores oscura. El código debe estar en un solo archivo index.html.
```

### ¿Por qué funciona?
Especifica **qué** (app de tareas), **cómo** (HTML/CSS/JS puro, un archivo), **qué funcionalidades** (CRUD completo) y **detalles de diseño** (paleta oscura, colores por prioridad). Sin ambigüedad.

### Comparativa con otras técnicas

| Aspecto | Prompt Estándar | Zero-Shot | One-Shot |
|---------|----------------|-----------|---------|
| Ejemplos | Ninguno | Ninguno | 1 ejemplo |
| Especificidad | Requiere ser muy explícito | Confía en el pre-entrenamiento | El ejemplo guía el formato |
| Mejor para | Tareas conocidas y bien definidas | Tareas comunes simples | Cuando el formato importa |

### Fortalezas
- Simple y rápido de escribir
- Suficiente para tareas bien definidas
- Buena línea base para iterar

### Cómo evaluar el output

**Buena respuesta:**
- Genera código funcional que corre sin errores en el navegador
- Diferencia visualmente las prioridades con colores
- Persiste datos en localStorage
- UI limpia y operable

**Señales de alerta:**
- Usa frameworks (React, Vue) cuando pediste vanilla JS
- Genera múltiples archivos cuando pediste uno solo
- El código tiene errores de sintaxis al abrir en el navegador
- Ignora alguna funcionalidad pedida

**Cómo medirlo:** Abre el archivo en el navegador. ¿Podés agregar, ver, completar y eliminar una tarea en menos de 30 segundos? Si sí, el prompt funcionó.

---

## TÉCNICA 2 — Zero-Shot

### ¿Qué es?
Solicitudes directas **sin ningún ejemplo**. El modelo responde basándose únicamente en su pre-entrenamiento. Funciona bien para tareas comunes donde el modelo tiene abundante contexto de entrenamiento.

### El Prompt

```
Dado el título y descripción de una tarea de desarrollo de software, sugiere la prioridad adecuada.

Las prioridades posibles son:
- alta: impacto crítico en producción o bloquea a otros desarrolladores
- media: importante pero no urgente, puede esperar al próximo sprint
- baja: mejora opcional o tarea de mantenimiento sin urgencia

Título: Corregir bug de autenticación en producción
Descripción: Los usuarios no pueden iniciar sesión después del deploy del viernes por la noche
```

### ¿Por qué funciona?
El modelo tiene suficiente contexto de entrenamiento sobre proyectos de software para clasificar prioridades sin ejemplos. Definir las opciones y sus criterios elimina ambigüedad en la selección.

### Comparativa con otras técnicas

| Situación | Técnica recomendada |
|-----------|---------------------|
| Tarea común con criterios claros | Zero-Shot |
| Necesitás un formato o estilo específico | One-Shot |
| Hay casos borde o variaciones complejas | Few-Shot |

### Cuándo falla Zero-Shot
- Cuando el dominio es muy específico (jerga interna del equipo, contexto de negocio único)
- Cuando necesitás un formato de respuesta muy particular
- Cuando hay ambigüedad que solo un ejemplo puede resolver

### Fortalezas
- Prompts cortos y directos
- Iteración rápida
- Ideal para tareas "obvias" para el modelo

### Cómo evaluar el output

**Buena respuesta:**
- Clasifica correctamente como "alta" (bug en producción que bloquea login)
- Da una justificación breve y coherente con los criterios definidos
- No inventa criterios no mencionados en el prompt

**Señales de alerta:**
- Responde "media" cuando es obviamente "alta"
- Genera texto excesivo sin concluir con la prioridad
- Pide más información cuando ya tiene suficiente

**Ejercicio de comparación en vivo:** Cambiá la descripción a "Actualizar el README del proyecto" y observá si el modelo ajusta correctamente a "baja".

---

## TÉCNICA 3 — One-Shot

### ¿Qué es?
Incluir **un ejemplo** junto con la solicitud para mostrarle al modelo el patrón, formato o estilo esperado. El modelo aprende del ejemplo y lo aplica al caso nuevo.

### El Prompt

```
Tu tarea es asignar una categoría a una tarea de desarrollo de software.
Las categorías disponibles son: Bug, Feature, Mejora, Documentación, Infraestructura.

Ejemplo:
Entrada: "Agregar validación de email en el formulario de registro"
Categoría: Feature
Razón: Es funcionalidad nueva que no existe actualmente en el sistema.

Ahora categoriza esta tarea:
Entrada: "El botón de guardar no responde en Firefox 118"
```

### ¿Por qué funciona?
El ejemplo le muestra al modelo no solo la categoría esperada sino también el **formato de respuesta** (Categoría + Razón) y el **nivel de detalle** en la justificación. Sin el ejemplo, el modelo podría responder de muchas formas distintas.

### Comparativa: Zero-Shot vs One-Shot

```
Zero-Shot:
"Categoriza esta tarea: El botón de guardar no responde en Firefox"
→ Puede responder: "Bug", o "Es un bug", o "Categoria: Bug. Esta tarea es un...", etc.

One-Shot (con el ejemplo de arriba):
→ Responde en formato consistente: "Categoría: Bug / Razón: ..."
```

**La diferencia clave: el One-Shot no mejora necesariamente la precisión, mejora la consistencia del formato.**

### Fortalezas
- Controla el formato de respuesta sin instrucciones largas
- Establece el nivel de detalle esperado en la justificación
- Un solo ejemplo ya cambia significativamente el comportamiento

### Cómo evaluar el output

**Buena respuesta:**
- Sigue exactamente el formato del ejemplo (Categoría + Razón en dos líneas)
- Categoriza correctamente como "Bug"
- La razón es coherente y específica, no copia el ejemplo literalmente

**Señales de alerta:**
- Ignora el formato del ejemplo y responde de otra forma
- Introduce categorías no listadas ("Defecto", "Error", "Hotfix")
- La razón es genérica ("porque tiene un problema")

**Experimento para mostrar el valor:** Corré primero el Zero-Shot con 5 tareas diferentes y anotá el formato de cada respuesta. Luego corré el One-Shot con las mismas tareas. Compará la consistencia del formato entre ejecuciones.

---

## TÉCNICA 4 — Few-Shot

### ¿Qué es?
Proporcionar **dos o más ejemplos**, especialmente con **casos borde**, para que el modelo entienda el rango completo de variaciones posibles. Aprende matices que un solo ejemplo no puede cubrir.

### El Prompt

```
Desglosa la siguiente tarea de software en subtareas específicas y accionables.

---
Ejemplo 1 (tarea clara y específica):
Tarea: "Implementar login con Google OAuth"
Subtareas:
1. Crear proyecto en Google Cloud Console y obtener credenciales OAuth2
2. Instalar librería de autenticación (passport-google-oauth20 o similar)
3. Crear endpoint GET /auth/google en el backend
4. Crear endpoint GET /auth/google/callback para manejar el retorno
5. Guardar el token del usuario en la sesión
6. Agregar botón "Continuar con Google" en el formulario de login
7. Escribir tests de integración para el flujo completo

---
Ejemplo 2 (tarea ambigua):
Tarea: "Arreglar todo en el módulo de pagos"
Subtareas:
- No es posible desglosar esta tarea. Es demasiado ambigua.
- Acción requerida: Especificar qué comportamiento incorrecto se observa, en qué condiciones ocurre y qué resultado se espera.

---
Ejemplo 3 (tarea sin información suficiente):
Tarea: "x"
Subtareas:
- La tarea no contiene información suficiente para ser desglosada.
- Acción requerida: Proveer al menos un título descriptivo y una descripción del objetivo.

---
Ahora desglosa esta tarea:
Tarea: "Migrar la base de datos de MySQL a PostgreSQL"
```

### ¿Por qué funciona?
Los tres ejemplos cubren el **espacio completo de casos**: tarea válida, tarea ambigua y tarea vacía. El modelo aprende a manejar los casos borde en lugar de inventar una respuesta para inputs inválidos.

### Comparativa: One-Shot vs Few-Shot

| | One-Shot | Few-Shot |
|--|---------|---------|
| Ejemplos | 1 (caso ideal) | 2+ (incluyendo casos borde) |
| Aprende | Formato y estilo | Formato + manejo de variaciones |
| Cuándo usarlo | Input siempre bien formado | Input puede variar o ser inválido |
| Costo en tokens | Bajo | Medio-alto |

### Fortalezas
- Robusto ante inputs inesperados o mal formados
- Reduce alucinaciones en casos borde
- El modelo "aprende" cómo manejar el dominio

### Cómo evaluar el output

**Buena respuesta para la migración a PostgreSQL:**
- Lista subtareas técnicas específicas (backup, instalación, ajuste de queries, compatibilidad de tipos, testing, rollback plan)
- Menciona consideraciones de compatibilidad entre MySQL y PostgreSQL
- NO inventa subtareas irrelevantes

**Prueba de casos borde en vivo:**
1. Pasá la tarea "Mejorar el rendimiento" → Debe pedir contexto más específico
2. Pasá una tarea vacía o con un solo caracter → Debe rechazarla limpiamente
3. Pasá "Agregar login con GitHub" → Debe desglosarla como el Ejemplo 1

---

## TÉCNICA 5 — Ubicación del Contexto

### ¿Qué es?
El **lugar** donde colocás el contexto dentro del prompt afecta cuánto peso le da el modelo. El contexto al inicio y al final es más efectivo que en el medio — fenómeno conocido como "lost in the middle".

### Los 3 Prompts (para comparar en vivo)

**Versión A — Contexto en el medio (menos efectivo):**
```
Sugiere la prioridad para la siguiente tarea de mantenimiento.

La aplicación es un sistema bancario de alta disponibilidad usado por 500 empleados internos. 
Cualquier interrupción impacta directamente transacciones financieras en tiempo real.
El equipo mantiene un SLA de 99.9% de uptime y los deploys requieren aprobación del CTO.

Tarea: "Actualizar la librería de logging a la versión más reciente"
Justifica tu respuesta.
```

**Versión B — Contexto al inicio (más efectivo):**
```
CONTEXTO DEL SISTEMA:
La aplicación es un sistema bancario de alta disponibilidad usado por 500 empleados internos.
Cualquier interrupción impacta directamente transacciones financieras en tiempo real.
El equipo mantiene un SLA de 99.9% de uptime y los deploys requieren aprobación del CTO.

---

Dado el contexto anterior, sugiere la prioridad para esta tarea y justificala:

Tarea: "Actualizar la librería de logging a la versión más reciente"
```

**Versión C — Contexto al final (también efectivo):**
```
Sugiere la prioridad para esta tarea y justificala:

Tarea: "Actualizar la librería de logging a la versión más reciente"

---
CONTEXTO DEL SISTEMA:
La aplicación es un sistema bancario de alta disponibilidad usado por 500 empleados internos.
Cualquier interrupción impacta directamente transacciones financieras en tiempo real.
El equipo mantiene un SLA de 99.9% de uptime y los deploys requieren aprobación del CTO.
```

### ¿Por qué funciona?
Los modelos Transformer tienen **sesgos de atención hacia el inicio y el final** del contexto. El texto en el medio de un prompt largo recibe menos atención. Esto es especialmente crítico en prompts de más de 500 tokens.

### Comparativa de los 3 enfoques

| Versión | Contexto | Resultado esperado | Observación |
|---------|----------|-------------------|-------------|
| A (medio) | Entre instrucción y tarea | Prioridad media, justificación débil | El contexto "se pierde" |
| B (inicio) | Antes de todo | Alta, menciona el SLA y el impacto bancario | El contexto establece el frame desde el inicio |
| C (final) | Después de la tarea | Alta, refuerza con el SLA y la aprobación del CTO | El contexto confirma la respuesta |

### Fortalezas
- No cuesta tokens adicionales — es el mismo contenido, diferente orden
- Impacto inmediato y demostrable en vivo
- La técnica es ortogonal: aplica a todas las otras técnicas combinada

### Cómo evaluar el output

**Demo en vivo recomendada:**
1. Corré las 3 versiones en el mismo chat o en tabs paralelas
2. Compará la prioridad asignada y la calidad de la justificación
3. Contá cuántas veces menciona "SLA", "99.9%", "aprobación del CTO"

**Indicador clave:** ¿Menciona la Versión A el "SLA de 99.9%" o la "aprobación del CTO" en su justificación? Si no lo hace, el contexto se perdió.

---

## TÉCNICA 6 — Salida Estructurada

### ¿Qué es?
Técnica para obtener un **formato de respuesta consistente y predecible** usando schemas o plantillas explícitas. Esencial cuando el output va a ser consumido por código.

### El Prompt

```
Analiza la siguiente tarea de desarrollo y devuelve ÚNICAMENTE un objeto JSON válido con exactamente esta estructura. No incluyas texto adicional, markdown ni explicaciones fuera del JSON.

{
  "titulo": string,
  "prioridad": "alta" | "media" | "baja",
  "categoria": "Bug" | "Feature" | "Mejora" | "Documentación" | "Infraestructura",
  "esfuerzo_estimado": "1-2h" | "medio_dia" | "1-2_dias" | "mas_de_1_semana",
  "subtareas": [string],
  "riesgos": [string],
  "listo_para_sprint": boolean
}

Tarea a analizar:
Título: "Implementar autenticación con JWT en la API REST"
Descripción: "La API actual no tiene autenticación. Necesitamos proteger todos los endpoints con JWT. Los usuarios deben hacer login, obtener un token y usarlo en requests subsecuentes."
```

### Comparativa: Sin vs Con Salida Estructurada

**Sin Salida Estructurada:**
```
"Esta tarea tiene alta prioridad porque la API está desprotegida. 
Estimaría 2-3 días de trabajo. Las subtareas serían: instalar jsonwebtoken, 
crear el middleware... Los riesgos incluyen..."
```
→ Requiere parsing manual, formato variable en cada ejecución.

**Con Salida Estructurada:**
```json
{
  "titulo": "Implementar autenticación con JWT en la API REST",
  "prioridad": "alta",
  "categoria": "Feature",
  "esfuerzo_estimado": "1-2_dias",
  "subtareas": ["Instalar jsonwebtoken", "Crear endpoint POST /auth/login", "..."],
  "riesgos": ["Expiración de tokens no manejada", "..."],
  "listo_para_sprint": true
}
```
→ Parseable directamente con `JSON.parse()`, integrable con código.

### Fortalezas
- Output predecible y parseable sin post-procesamiento
- Reduce variabilidad entre ejecuciones del mismo prompt
- Permite integrar la IA con sistemas reales (guardar en DB, mostrar en UI)
- Base para construir agentes más complejos

### Cómo evaluar el output

**Criterios técnicos:**
- `JSON.parse(response)` no lanza error
- Todos los campos del schema están presentes
- Los valores respetan los tipos definidos ("alta" no "Alta", boolean no string)
- El array de subtareas tiene al menos 3 elementos

**Prueba de robustez:** Pedí el mismo prompt 3 veces seguidas. ¿El JSON tiene siempre la misma estructura? ¿Cambian solo los valores? Si la estructura es consistente, la técnica funciona.

**Red flag crítico:** Si el modelo devuelve ` ```json ``` ` (markdown fencing) en lugar de JSON puro, agregá al prompt: "No uses markdown code blocks. Solo el JSON, nada más."



## TÉCNICA 7 — Chain of Thought (Cadena de Pensamiento)

### ¿Qué es?
Técnica para pedirle al modelo que **muestre su razonamiento paso a paso** antes de dar la respuesta final. Descompone problemas complejos en pasos intermedios, reduciendo errores en tareas que requieren razonamiento multi-paso.

### El Prompt

```
Necesito que analices una tarea de desarrollo compleja y la planifiques para el equipo.

Antes de dar tu recomendación final, razona paso a paso respondiendo cada punto:

Paso 1 — Comprensión: ¿Qué problema de negocio resuelve esta tarea? ¿Cuál es el resultado esperado?
Paso 2 — Impacto técnico: ¿Qué partes del sistema se ven afectadas? ¿Hay cambios en la base de datos, API o UI?
Paso 3 — Dependencias: ¿Esta tarea requiere que otra esté completada primero? ¿Bloquea otras tareas?
Paso 4 — Riesgos: ¿Qué puede salir mal? ¿Hay riesgo de regresión en otras funcionalidades?
Paso 5 — Estimación: Basándote en el análisis anterior, ¿cuánto tiempo requiere esta tarea?

Conclusión final: Provee un plan de implementación con subtareas ordenadas y la estimación justificada por el análisis previo.

---
Tarea: "Implementar sistema de notificaciones en tiempo real usando WebSockets"
Descripción: Los usuarios deben recibir notificaciones inmediatas cuando una tarea es asignada, completada o comentada. Actualmente el sistema usa polling cada 30 segundos.
```

### Comparativa: Con y Sin Chain of Thought

**Sin CoT:**
```
Prompt: "¿Cuánto tarda implementar WebSockets en nuestra app?"
Respuesta: "Aproximadamente 2-3 días de trabajo." (sin contexto ni razonamiento)
```

**Con CoT:**
```
Paso 1: Resuelve la latencia de 30s → tiempo real. El negocio necesita colaboración fluida.
Paso 2: Afecta backend (servidor WS), frontend (cliente WS), infraestructura (load balancer debe soportar WS con sticky sessions).
Paso 3: Requiere que el módulo de autenticación esté completo para autorizar conexiones WS.
Paso 4: Memory leaks en conexiones no cerradas, incompatibilidad con proxies HTTP, escalabilidad horizontal compleja.
Paso 5: Considerando la infraestructura y los riesgos → 5-7 días, no 2-3.

Conclusión: Plan de 9 subtareas ordenadas por dependencia, con la estimación justificada.
```

### Fortalezas
- Reduce alucinaciones en tareas complejas
- La respuesta es auditable — podés revisar el razonamiento
- El proceso de razonamiento es tan valioso como la conclusión final
- Combinación poderosa: Few-Shot + Chain of Thought es una de las técnicas más efectivas

### CoT Implícito vs Explícito

```
# CoT Explícito (estructurado, como el prompt de arriba):
"Razona paso a paso respondiendo: Paso 1... Paso 2..."

# CoT Implícito (más simple y rápido):
"Piensa paso a paso antes de responder."
"Antes de dar tu respuesta, analiza cada aspecto involucrado."
```

Ambos funcionan. El explícito da más control sobre el razonamiento; el implícito es más rápido de escribir.

### Cómo evaluar el output

**Buena respuesta:**
- Responde cada paso antes de concluir
- La conclusión es **coherente con el análisis** — si en Paso 3 detectó una dependencia con autenticación, el plan la refleja
- La estimación está **justificada** por los riesgos encontrados en Paso 4

**Señales de alerta:**
- El modelo salta a la conclusión sin responder los pasos intermedios
- La conclusión contradice algo dicho en los pasos (inconsistencia interna)
- Los pasos son superficiales ("Paso 1: Es una tarea de notificaciones") sin análisis real

---

## TÉCNICA 8 — Delimitadores

### ¿Qué es?
Uso de marcadores visuales y estructurales (XML tags, guiones, comillas, markdown) para **crear límites claros** entre las partes del prompt. Ayuda al modelo a distinguir qué es instrucción, qué es contexto y qué es el input a procesar.

### El Prompt

```xml
<rol>
Eres un Scrum Master experimentado con 8 años liderando equipos de desarrollo ágil.
Tu enfoque es pragmático: valorás la entrega continua y la reducción de riesgos en cada sprint.
</rol>

<contexto_del_proyecto>
Aplicación: Task Manager Pro — Sistema web de gestión de tareas para equipos de desarrollo
Stack: React 18, Node.js 20, PostgreSQL 15
Equipo: 4 desarrolladores, 1 QA, 1 diseñador
Velocidad promedio del equipo: 32 story points por sprint de 2 semanas
Sprint actual: 5 días restantes, 8 story points consumidos de 32
</contexto_del_proyecto>

<tarea_a_evaluar>
Título: Implementar exportación de reportes a Excel
Descripción: El equipo de gerencia necesita exportar el inventario de tareas semanalmente en formato .xlsx para enviarlo por correo a los stakeholders. Debe incluir: título, estado, prioridad, responsable, fecha de creación y fecha de completado.
Solicitado por: Director de Operaciones
Urgencia reportada: Alta
</tarea_a_evaluar>

<instruccion>
Evaluá si esta tarea debe incluirse en el sprint actual o moverse al siguiente. Provee:
1. Estimación en story points con justificación técnica
2. Lista de subtareas con responsable sugerido (dev o QA)
3. Criterios de aceptación en formato Given/When/Then
4. Riesgos identificados y plan de mitigación
5. Recomendación final: ¿entra al sprint actual o al siguiente? ¿Por qué?
</instruccion>
```

### Comparativa: Sin vs Con Delimitadores

**Sin delimitadores:**
```
Eres un Scrum Master, el proyecto es un Task Manager con React y Node.js, el equipo tiene 4 devs y van 5 días del sprint con 8 story points usados de 32, hay una tarea de exportar a Excel pedida por el Director con prioridad alta, evaluá si entra al sprint y dame subtareas, criterios de aceptación y riesgos.
```
→ Difícil de leer y el modelo puede mezclar contexto con instrucción.

**Con delimitadores:** Como el ejemplo de arriba → estructura clara, respuesta más precisa y organizada.

### Tipos de delimitadores y cuándo usarlos

| Delimitador | Uso recomendado |
|-------------|----------------|
| `<xml_tags>` | Prompts complejos con múltiples secciones separadas |
| `"""triple comillas"""` | Separar texto a procesar de la instrucción principal |
| `---` (guiones) | Separar ejemplos en prompts Few-Shot |
| `# Sección` (markdown) | Prompts con headers legibles para humanos |
| `[INSTRUCCION]` | Prompts de sistema en chatbots |

### Fortalezas
- Mejora la precisión en prompts con múltiples secciones
- Hace el prompt más mantenible y legible para el equipo
- Reduce la confusión entre instrucción y contexto
- Escala bien: podés agregar o quitar secciones sin romper el prompt

### Cómo evaluar el output

**Buena respuesta:**
- Responde los 5 puntos de `<instruccion>` en orden
- Los criterios de aceptación están en formato Given/When/Then
- La recomendación final es coherente con la estimación y los riesgos detectados
- Usa la velocidad del equipo (32 SP disponibles, 24 libres) para justificar si entra al sprint

**Experimento de ablation en vivo:**
Quitá los XML tags y dejá el mismo contenido como un solo párrafo de texto plano. ¿Cambia la calidad o el orden de la respuesta? Esto muestra el valor concreto de los delimitadores.

---

## TÉCNICA 9 — Personas

### ¿Qué es?
Técnica para asignarle al modelo un **rol específico** que oriente su perspectiva y tono. Una persona no da capacidades nuevas — orienta hacia un subconjunto del conocimiento del modelo más relevante para ese rol.

### El Prompt

```
Actúa como un Tech Lead con 10 años de experiencia en desarrollo de software backend, especializado en arquitectura de sistemas distribuidos y gestión de deuda técnica.

Tu perspectiva es pragmática y orientada a riesgos: priorizás la estabilidad del sistema sobre nuevas features, pero entendés la presión del negocio. Cuando revisás un backlog, siempre preguntás "¿qué pasa si esto NO se hace?" antes de priorizar.

Revisá el siguiente backlog del Task Manager Pro y:
1. Asigná una prioridad (crítica / alta / media / baja) a cada tarea
2. Identificá cuáles representan deuda técnica acumulada
3. Señalá dependencias entre tareas (¿cuál debe hacerse antes?)
4. Recomendá las 5 tareas para el próximo sprint con justificación técnica

---
Backlog:
- [ ] Migrar de MySQL 5.7 a PostgreSQL 15
- [ ] Agregar paginación en la lista de tareas (actualmente carga todo el dataset)
- [ ] Corregir memory leak en el servicio de notificaciones WebSocket
- [ ] Actualizar todas las dependencias de npm (última actualización: 8 meses)
- [ ] Implementar búsqueda full-text en título y descripción de tareas
- [ ] Refactorizar el módulo de autenticación (código de 2019, sin tests)
- [ ] Agregar exportación a CSV
- [ ] Implementar dark mode en la UI
- [ ] Configurar alertas de monitoreo en producción
- [ ] Agregar logs estructurados (actualmente solo console.log en producción)
```

### ¿Por qué funciona?
El rol de "Tech Lead orientado a riesgos" hace que el modelo pese diferente cada tarea. El mismo backlog evaluado por un "Product Manager orientado a features" daría una priorización completamente distinta. La persona orienta **qué aspectos del conocimiento del modelo** son más relevantes.

### Comparativa: Misma tarea, diferentes personas

```
Tarea: "Refactorizar el módulo de autenticación (código de 2019, sin tests)"

Tech Lead:
→ Prioridad ALTA. Deuda técnica crítica con riesgo de seguridad. Sin tests, cualquier cambio
  puede introducir vulnerabilidades. Debe hacerse antes de agregar nuevas features de acceso.

Product Manager:
→ Prioridad BAJA. No entrega valor visible al usuario. Mover al siguiente trimestre.
  El sistema funciona actualmente, aunque sea con código viejo.

CTO:
→ Prioridad MEDIA. Agendar para Q2 como parte de un tech debt sprint dedicado.
  Requiere aprobación presupuestaria para el tiempo invertido.
```

### Personas útiles para equipos de desarrollo

| Persona | Cuándo usarla |
|---------|--------------|
| Tech Lead | Priorización de backlog técnico y deuda técnica |
| Senior Developer | Review de código, arquitectura y patrones |
| QA Engineer | Identificar casos de prueba y edge cases |
| UX Designer | Evaluar flujos de usuario y usabilidad |
| Product Manager | Priorizar por valor de negocio |
| DevOps Engineer | Evaluar viabilidad de despliegue e infraestructura |
| Security Analyst | Identificar vulnerabilidades y riesgos |

### Limitaciones importantes de Personas
- **No da capacidades nuevas al modelo** — solo orienta las existentes
- Una persona de "experto en Rust" no hace al modelo mejor programador en Rust
- Evitar personas demasiado vagas ("Eres un experto") — ser específico en el rol, perspectiva y criterio de decisión

### Fortalezas
- Cambia la perspectiva sin cambiar el contenido del prompt
- Múltiples personas sobre el mismo input = análisis multi-dimensional
- Reduce respuestas genéricas orientando el enfoque del modelo

### Cómo evaluar el output

**Buena respuesta del Tech Lead:**
- Las tareas críticas (memory leak, deps desactualizadas, auth sin tests) tienen prioridad alta o crítica
- Dark mode e exportación CSV tienen prioridad baja
- Las dependencias son correctas (migrar DB antes de features que requieren SQL avanzado)
- La justificación usa lenguaje técnico coherente con el rol

**Experimento de comparación en vivo:**
Corré el mismo backlog con dos personas distintas: "Tech Lead" y "Product Manager". Compará qué tareas priorizan diferente y discutilo con el equipo — esa discusión es el aprendizaje real.

---

## Guía práctica: Prompts incrementales para el Task Manager

### ¿Qué queremos lograr?
Construir un Task Manager paso a paso, donde cada prompt agrega una feature real y cada técnica se usa en el contexto más adecuado.

### Feature 1 — App base con tareas, lista, contadores y filtros (Prompt Estándar)
Usa Prompt Estándar para crear la base de la app: la funcionalidad es clara y no necesita ejemplos adicionales.

```
Crea una aplicación web de gestión de tareas con HTML, CSS y JavaScript puro.
La aplicación debe:
- Permitir agregar tareas con título, descripción, prioridad (alta/media/baja) y categoría
- Mostrar la lista de tareas en un panel a la derecha
- Mostrar contadores de tareas por estado: pendiente, en progreso, completada
- Permitir filtrar la lista por estado y por categoría
- Permitir marcar tareas como completadas y eliminar tareas
- Guardar y cargar las tareas desde localStorage
- Tener una UI donde el formulario quede a la izquierda y la lista a la derecha
- Generar código en un solo archivo index.html
```

¿Por qué funciona?
- La funcionalidad es directa y bien definida
- El prompt estándar es suficiente para establecer la estructura principal
- Sirve como punto de partida para ampliar la app con nuevas features

### Feature 2 — Agregar categorías y dropdown principal (One-Shot)
Para añadir categorías administrables, One-Shot es ideal porque necesitamos un formato preciso del dropdown y el comportamiento de filtrado.

```
Mejorá la app para que soporte categorías dinámicas en un dropdown.
Ejemplo:
- El formulario permite crear categorías nuevas.
- En la pantalla principal hay un select con todas las categorías disponibles.
- Al seleccionar una categoría en el select, la lista de tareas se filtra automáticamente.
- Si no se selecciona categoría, se muestran todas las tareas.

Generá el código completo de la aplicación con esta funcionalidad.
```

¿Por qué usar One-Shot?
- El ejemplo fija el comportamiento esperado del select
- Reduce la variabilidad en la estructura de la UI
- Es útil cuando necesitamos que el modelo repita un patrón concreto

### Feature 3 — Status drag and drop al estilo Azure DevOps (Few-Shot y Delimitadores)
Mover tareas por estado es una interacción más compleja. Usamos Few-Shot para mostrar el patrón actual versus el deseado, y delimitadores para separar claramente la instrucción de los ejemplos.

```
Cambia la app para que las tareas se muevan entre estados con drag and drop.

---
Ejemplo 1 (actual con botones):
- Cada tarea tiene botones "Mover a En progreso" y "Mover a Completada".
- La lista es estática y el estado cambia al hacer clic.

Ejemplo 2 (deseado con drag and drop):
- La interfaz muestra zonas para Pendiente, En progreso y Completada.
- Cada tarjeta de tarea es draggable.
- Al arrastrar una tarjeta a otra zona, el estado de la tarea cambia.
- El nuevo estado se guarda en localStorage.

Ahora generá el código completo con drag and drop para mover tareas entre estados.
```

¿Por qué funciona?
- Few-Shot permite comparar el diseño antiguo con el nuevo
- Delimitadores clarifican la estructura del prompt
- Esto reduce el riesgo de que el modelo genere un flujo de interacción incorrecto

### Feature 4 — Adjuntos limitados y guardado en localStorage en base64 (Salida Estructurada)
Para esta funcionalidad necesitamos un schema consistente de datos. Salida Estructurada es la mejor técnica porque el modelo entrega un objeto bien definido.

```
Agregá la funcionalidad de adjuntar archivos a las tareas con estas reglas:
- Tipos permitidos: .txt, .docx, .xml, .json
- Tamaño máximo por archivo: 50 MB
- Cada archivo debe transformarse a base64 y guardarse en localStorage
- La estructura de cada adjunto en localStorage debe ser JSON lowerCase con campos:
  {
    "name": string,
    "type": string,
    "size": number,
    "content_base64": string,
    "uploaded_at": string
  }
- La UI debe mostrar los archivos adjuntos dentro de cada tarjeta de tarea y permitir descargarlos.
```

¿Por qué Salida Estructurada?
- Porque la persistencia en localStorage exige un formato consistente
- Permite validar fácilmente la estructura y el lowerCase
- Evita que el modelo entregue campos inconsistentes o mal nombrados

### Feature 5 — JSON lowerCase consistente y cambio fácil de categoría (Ubicación del Contexto)
Cuando la consistencia del formato es crítica, coloca el requisito al inicio del prompt. Esta es una aplicación clara de Ubicación del Contexto.

```
CONTEXTO:
La aplicación debe usar siempre JSON en localStorage con claves en lowerCase. Ninguna estructura de datos debe usar mayúsculas ni camelCase.

Instrucción:
- Guarda las tareas en localStorage con este esquema:
  {
    "id": string,
    "titulo": string,
    "descripcion": string,
    "prioridad": "alta" | "media" | "baja",
    "categoria": string,
    "estado": "pendiente" | "en_progreso" | "completada",
    "adjuntos": [
      {
        "name": string,
        "type": string,
        "size": number,
        "content_base64": string,
        "uploaded_at": string
      }
    ]
  }
- Permite cambiar la categoría de una tarea fácilmente desde la UI con un select dentro de la tarjeta o un menú rápido.
- Asegurate de que todo el localStorage use lowerCase y sea parseable como JSON.
```

¿Por qué funciona?
- El requisito de esquema va al inicio para que el modelo lo tenga presente
- Evita que se mezclen estilos de JSON diferentes en la misma app
- Refuerza la consistencia de los datos almacenados

### Técnica sugerida para cada feature
- App base: Prompt Estándar
- Dropdown de categorías: One-Shot
- Drag and drop de estado: Few-Shot + Delimitadores
- Adjuntos en base64: Salida Estructurada
- JSON lowerCase y cambio de categoría: Ubicación del Contexto

### Cómo evaluar la implementación incremental
- La primera versión debe funcionar como app de tareas básica
- La segunda debe incorporar un dropdown de categorías que filtre correctamente
- La tercera debe mover tareas por drag and drop entre tres estados
- La cuarta debe aceptar archivos permitidos, limitar a 50 MB, convertir a base64 y guardar en localStorage
- La quinta debe mostrar exclusivamente JSON lowerCase en localStorage y permitir cambiar categoría de forma sencilla

---

---

## Tabla de Referencia Rápida

| Técnica | Cuándo usarla | Costo en tokens | Dificultad |
|---------|--------------|----------------|-----------|
| Prompt Estándar | Tarea simple y bien definida | Bajo | ⭐ |
| Zero-Shot | Tarea común, sin formato específico | Bajo | ⭐ |
| One-Shot | Necesitás controlar el formato de respuesta | Bajo-Medio | ⭐⭐ |
| Few-Shot | Hay casos borde o variaciones posibles | Medio | ⭐⭐⭐ |
| Ubicación del Contexto | Prompt largo con mucho contexto | Ninguno extra | ⭐⭐ |
| Salida Estructurada | El output será consumido por código | Medio | ⭐⭐⭐ |
| Chain of Thought | Problema complejo que requiere razonamiento | Alto | ⭐⭐⭐ |
| Delimitadores | Prompt con múltiples secciones separadas | Bajo | ⭐⭐ |
| Personas | Necesitás una perspectiva o rol específico | Bajo | ⭐⭐ |

---

## Combinaciones Poderosas

### Few-Shot + Chain of Thought
La combinación más efectiva para tareas complejas con razonamiento demostrado:
```
Ejemplo:
Tarea: "Implementar caché con Redis"
Razonamiento:
- Paso 1: Resuelve la latencia en queries frecuentes...
- Paso 2: Afecta la capa de servicio y la consistencia de datos...
- Paso 3: Depende de tener Redis instalado en el servidor...
Subtareas: [lista ordenada]

Ahora analiza esta tarea usando el mismo proceso:
Tarea: "Implementar rate limiting en la API"
```

### Delimitadores + Salida Estructurada + Personas
Para sistemas de IA integrados en producción:
```xml
<rol>Eres un analizador automático de tareas de software.</rol>
<tarea>{{ título y descripción }}</tarea>
<formato_de_respuesta>
Devuelve SOLO este JSON sin texto adicional: { "prioridad": ..., "subtareas": [...] }
</formato_de_respuesta>
```

### Personas + One-Shot
Para que el modelo adopte el estilo de respuesta del rol desde el primer ejemplo:
```
Eres un Tech Lead. Cuando revisás código, siempre respondés con este formato:

Ejemplo:
"[RIESGO ALTO] El loop en línea 45 es O(n²). Para 10k registros causará timeouts.
Solución propuesta: reemplazar con un Map para lookup O(1). Impacto: 2h de trabajo."

Ahora revisá este código: ...
```

---
### Otras técnicas de prompting 
| Técnica | Cuándo usarla | Costo en tokens | Dificultad |
|---------|--------------|----------------|-----------|
| Prompt Chaining | Flujo multi-paso, output de uno alimenta el siguiente | Medio (múltiples llamadas) | ⭐⭐⭐ |
| Self-Critique | Necesitás output de alta calidad sin revisión externa | Alto (3 pasos) | ⭐⭐⭐ |
| Negative Prompting | El modelo tiende a responder de forma vaga o genérica | Ninguno extra | ⭐⭐ |
| Step-Back Prompting | Tarea compleja donde el modelo puede ir a la solución obvia | Alto (2 pasos) | ⭐⭐⭐ |


*Guía preparada para el Taller de Prompt Engineering — App de ejemplo: Task Manager Pro*
*Basado en el curso Practical Prompt Engineering for Developers — Frontend Masters*
