# Reto 02 - Agente FAQ de productos bancarios

## Objetivo
Construir un agente en Copilot Studio que responda preguntas frecuentes sobre productos bancarios sin necesidad de un asesor humano.

## Conceptos clave
- **Bot**: programa que responde automaticamente.
- **FAQ**: respuestas a preguntas muy comunes.
- **Knowledge base**: base de datos de respuestas confiables.
- **Escalamiento**: cuando el bot no sabe, derivar a humano.

## Guia paso a paso

### Paso 1: Recopilar top 20 preguntas frecuentes (45 minutos)
**Fuentes:**
- Revisa emails del ultimo mes al equipo de atenccion.
- Chat logs o call center transcritos.
- Tu experiencia: que pregunta repite SIEMPRE el cliente?

**Template de FAQ:**

```
1. ¿Cuales son los requisitos para abrir una cuenta?
   Respuesta: Documento de identidad vigente, validacion basica de datos.
   
2. ¿Que es la tasa de cambio y como se calcula?
   Respuesta: Es el valor de una moneda en otra. Se actualiza diariamente segun mercado.
   
3. ¿Como aumento mi cupo de tarjeta?
   Respuesta: Puedes solicitar evaluacion de cupo desde la app o hablando con un asesor.
   
[Sigue 17 mas...]
```

**Consejo:** Las mejores FAQ son sobre:
- "Como hacer..." (pasos claros).
- "Cuanto cuesta..." (precios y tarifas).
- "Que es..." (conceptos simples).

### Paso 2: Crear el agente en Copilot Studio (30 minutos)
1. Abre https://www.microsoft.com/en/copilot/copilot-studio
2. Clickea "+ New copilot".
3. Nombre: `AgenteFAQProductos` (sin espacios).
4. Idioma: Spanish.
5. Clickea "Create".

### Paso 3: Configurar el prompt inicial (15 minutos)
**En la seccion "Personalization" o "Instructions", escribe:**

```
Rol: Eres un asistente bancario amable y profesional de [NOMBRE BANCO].

Objetivo: Responder preguntas sobre nuestros productos de una forma clara y sencilla.

Tono: Formal pero accessible, nunca tecnico, siempre empático.

Reglas importantes:
1. Responde SOLO sobre productos y tarjetas. 
2. Si no sabes la respuesta, di: "No tengo esa informacion. Conectare contigo con un asesor."
3. Nunca pidas credenciales, cuenta bancaria o PIN.
4. Si el cliente pregunta por politicas muy especificas, escala a asesor.

Ejemplo de tono correcto:
- Correcto: "Tu tarjeta tiene una tasa de interes del 1.5% mensual. ¿Te ayudo con mas?"
- Incorrecto: "La TIR nominal anualizada es 18%..."
```

### Paso 4: Cargar la base de conocimiento (60 minutos)
1. Descarga el archivo `materiales/datasets-ejemplo/faq_productos.csv`.
2. En Copilot Studio, busca pestaña "Knowledge" o "Data".
3. Clickea "Add knowledge source".
4. Sube el CSV con tus 20 preguntas y respuestas.
5. Copilot indexara la info automaticamente.

**Si no hay seccion de Knowledge:**
- Crea Topics manuales (prefijos de topic).
- Ej: Topic "Preguntas_Tarjeta", dentro agrega 5 preguntas con respuesta.
- Copilot aprendera a reconocer tema y dar respuesta.

### Paso 5: Crear Topics (temas) en el flujo (45 minutos)
**Estructura sugerida:**

```
- Tema: Preguntas sobre tarjeta de credito
  Disparadores: "tarjeta", "cupo", "tasa", "anualidad"
  Respuesta: Conecta con knowledge base o respuesta hardcodeada
  
- Tema: Preguntas sobre cuenta de ahorros
  Disparadores: "cuenta", "ahorros", "tasas"
  Respuesta: Conecta con knowledge base
  
- Tema: Escalamiento a asesor
  Disparadores: "asesor", "hablar con persona", "no entiende"
  Respuesta: "Te conectare con un asesor en breve. Un momento..."
```

### Paso 6: Agregar opciones de respuesta rapida (20 minutos)
**En cada respuesta, agrega "Quick reply" o "Smart suggestions":**

Ejemplo:
```
Usuario: ¿Como aumento mi cupo?

Agente: Puedes solicitar aumento de cupo desde:
  [Opcion 1] App movil
  [Opcion 2] Llamar a asesor
  [Opcion 3] Visitar sucursal
```

Esto hara la conversion mas natural.

### Paso 7: Configurar escalamiento a humano (15 minutos)
1. En flujo principal, agrega "Escalamiento" o "Handoff".
2. Define cuando: "Si el usuario dice 'quiero hablar con persona' o despues de 3 intentos fallidos".
3. Mensaje antes de escalar: "Te voy a conectar con un asesor en breve. Un momento por favor."

### Paso 8: Pruebas de calidad (90 minutos)
**Ejecuta 10 conversaciones de prueba:**

| Test | Pregunta | Respuesta esperada | Resultado | Ajustes |
|------|----------|-------------------|-----------|----------|
| 1 | "¿Que es una tarjeta de credito?" | Explicacion simple y clara | ✓ OK | Ninguno |
| 2 | "¿Cuanto cuesta la anualidad?" | Precio especifico o rango | ✗ Fallo | Agregar a FAQ |
| 3 | "¿Como abro una cuenta?" | Pasos en orden | ✓ OK | Ninguno |
| 4 | "Dame mi saldo" | Mensaje seguro (no revelar datos) | ✓ OK | Ninguno |
| 5 | "¿Cual es el mejor producto?" | Preguntas de calificacion | ? Fallo parcial | Refinar |
| ... | ... | ... | ... | ... |

**Usa el boton "Test" o "Preview" en Copilot Studio.**

### Paso 9: Ajustes iterativos (60 minutos)
**Si una respuesta fallo:**
1. Corrige el topic o agrega mas ejemplos de entrada.
2. Mejora el prompt de ese topic.
3. Prueba de nuevo.
4. Repite hasta 8+ de 10 correctas.

### Paso 10: Documentar (20 minutos)
**Completa plantilla:**

```
# Ficha Agente FAQ Productos

## Informacion basica
- Nombre: AgenteFAQProductos
- Version: 1.0
- Fecha: [hoy]

## Preguntas soportadas (top 5)
1. ¿Cuales son los requisitos para abrir una cuenta?
2. ¿Que es la tasa de interes?
3. ...

## Pruebas ejecutadas: 10
## Pruebas exitosas: 9
## Tasa de exito: 90%

## Mejoras futuras
- Agregar info sobre seguros
- Integrar con sistema de saldos
```

## Entregables
- Agente FAQ funcional en Copilot Studio.
- Archivo `.md` con tabla de 10 pruebas y resultados.
- Lista de 5 mejoras detectadas para versiones futuras.
- Capturas de pantalla (minimo 2: flujo y resultado de test).

## Criterio de exito
✓ Al menos 80% de respuestas correctas en pruebas (8 de 10).
✓ Agente escala a humano cuando no sabe.
✓ Las respuestas son claras para cliente no tecnico.
✓ Quiz: el equipo entiende como agrega una FAQ nueva mañana.
