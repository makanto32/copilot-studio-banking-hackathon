# Reto 05 - Agente orquestador de experiencia

## Objetivo
Conectar los 3 agentes especializados (FAQ, Onboarding, Alertas) bajo UNA sola experiencia de cliente, donde el cliente entra por un punto unico y se enruta inteligentemente sin repetir informacion.

## Conceptos clave
- **Orquestador**: agente maestro que entiende el intent y redirige.
- **Handoff inteligente**: pasar contexto (nombre, documento, etc) al siguiente agente.
- **Continuidad**: cliente no repite "Hola, soy Juan" en cada agente.
- **Experiencia unificada**: parece une sola conversacion aunque hay multiples agentes.

## Guia paso a paso

### Paso 1: Entender la arquitectura (20 minutos)
**Diagrama conceptual:**

```
Cliente inicia chat
       ↓
[ORQUESTADOR] - Agente maestro
   ↓    ↓    ↓
   ├─→ FAQ (¿que es tarjeta?)
   ├─→ ONBOARDING (abrir cuenta)
   └─→ ALERTAS (fraude detectado)
   
Ejemplo flujo:
Cliente: "Hola, tengo una duda sobre tarjetas"
Orquestador: "Entiendo, te conectare con nuestro especialista en productos"
             [HANDOFF A FAQ con contexto: {nombre: Juan, tema: tarjeta}]
FAQ: "Hola Juan, te ayudo con tarjetas. ¿Que necesitas?"
     [No repite presentacion, recuerda tema]
```

### Paso 2: Crear el agente orquestador (30 minutos)
1. Abre Copilot Studio.
2. "+ New copilot".
3. Nombre: `AgenteOrquestador`.
4. Idioma: Spanish.
5. "Create".

### Paso 3: Instrucciones del orquestador (20 minutos)
```
Rol: Eres gerente de experiencia de clientes del banco.
Tu trabajo es entender que necesita el cliente y conectarlo con el especialista correcto RAPIDAMENTE.

Reglas:
1. Escucha el intent del cliente (pregunta, problema, necesidad).
2. Clasificalo en una categoria: FAQ, Onboarding, Alertas, u Otro.
3. Transfiere al agente especializado con contexto.
4. NUNCA repitas en el siguiente agente (evita "Hola, ¿cual es tu nombre?" si ya lo preguntaste).
5. Si no entiendes, pregunta UNA VEZ claro. Si sigue confuso -> escala a humano.

Mensaje inicial:
"Hola, bienvenido al banco. Soy tu asistente personal.
¿En que te puedo ayudar hoy? (ej: preguntas sobre productos, 
abrir cuenta, seguridad, u otro)"
```

### Paso 4: Crear logic de clasificacion (60 minutos)
**Topics del orquestador:**

**Topic 1: Bienvenida**
```
Nombre: Bienvenida
Mensaje: "Hola, bienvenido. ¿En que te puedo ayudar hoy?"
Opciones rapidas:
  [Preguntas sobre productos]
  [Abrir una cuenta]
  [Alerta de seguridad]
  [Otro problema]
Siguiente: condicional (ir a clasificar segun respuesta)
```

**Topic 2: ClasificarIntent**
```
Nombre: ClasificarIntent
Input: lo que escribio cliente
Logica:
  Si (contiene palabras: "tarjeta", "tasa", "requisitos", "como aumento cupo")
    → categoria = FAQ
  Si (contiene palabras: "abrir cuenta", "nuevo cliente", "registro")
    → categoria = Onboarding
  Si (contiene palabras: "fraude", "transaccion rara", "alerta")
    → categoria = Alertas
  Si no clasifica claramente
    → preguntar clarificacion

Siguiente: Topic "TransferirAAgente"
```

**Topic 3: PreguntaClarificacion** (si no se entiende)
```
Nombre: PreguntaClarificacion
Pregunta: "Perfecto, algo mas claro: ¿Es sobre (1) Tus productos, 
(2) Abrir nuevacuenta, o (3) Un problema de seguridad?"
Opciones: [Productos] [Abrir cuenta] [Seguridad]
Siguiente: clasificar de nuevo basado en opcion
```

**Topic 4: TransferirAFAQ**
```
Nombre: TransferirAFAQ
Respuesta: "Entiendo tu pregunta sobre {tema}. 
Te conectare con el especialista en productos.
Un momento..."
Accion: 
  1. Guardar contexto en variables:
     - {NombreCliente} = nombre cliente si lo dijó
     - {TemaIntent} = tema detectado
     - {HistorialChat} = lo que ya hablamos
  2. Handoff a agente: AgenteFA Q
Siguiente: agente FAQ continua desde aqui
```

**Topic 5: TransferirAOnboarding**
```
Nombre: TransferirAOnboarding
Respuesta: "Perfecto, te guiare en la apertura de tu cuenta.
Vamos paso a paso, es muy sencillo."
Accion:
  1. Guardar {TemaIntent: Onboarding}
  2. Handoff a: AgenteOnboarding
Siguiente: agente Onboarding continua
```

**Topic 6: TransferirAAlertas**
```
Nombre: TransferirAAlertas
Respuesta: "Entendido, vamos a revisar la seguridad de tu cuenta.
Es importante que confirmes algunos datos."
Accion:
  1. Guardar {TemaIntent: Alertas}
  2. Handoff a: AgenteAlertasFraude
Siguiente: agente Alertas continua
```

### Paso 5: Configurar Handoff con contexto (45 minutos)
**La clave:
 Cada handoff debe pasar variables:**

```PSEUDOCODIGO
HANDOFF_A_FAQ:
  - Pasar {NombreCliente}: para que FAQ diga "Hola {nombre}, te ayudo con..."
  - Pasar {TemaIntent}: para que FAQ sepa de que hablamos
  - Pasar {HistorialChat}: para que FAQ vea que ya preguntaste X
  
Ejemplo:
Orquestador: "¿Cual es tu nombre?"
Cliente: "Juan"
Orquestador GUARDA: {NombreCliente} = "Juan"
Orquestador TRANSFIERE a FAQ con contexto
FAQ RECIBE: {NombreCliente} = "Juan"
FAQ RESPONDE: "Hola Juan, te ayudo con tarjetas. ¿Que necesitas?"
[Juan no repite nombre]
```

### Paso 6: Flujo completo de prueba (90 minutos)
**Escenario: Cliente con pregunta, luego se cuenta de fraude**

```
1. Cliente escribe: "Hola, tengo duda sobre la tasa de mi tarjeta"
   Orquestador: Clasifica como FAQ
   Orquestador: "Te conectare con nuestro especialista en productos"
   [HANDOFF]
   
2. FAQ recibe contexto: {nombre?, tema: tarjeta}
   FAQ: "Claro, te explico las tasas. ¿Que tipo de tarjeta tienes?"
   Cliente: "La oro"
   FAQ: "La tasa de tarjeta oro es 1.5% mensual..."
   
3. [Cliente ha resuelto pregunta]
   FAQ: "¿Necesitas algo mas?"
   Cliente: "Mira, tengo una alerta en la app de una transaccion rara"
   
4. FAQ detecta que NO es FAQ (es Alerta)
   FAQ: "Entiendo, eso es importante. Voy a conectarte con nuestro 
       especialista de seguridad."
   [HANDOFF a Alertas]
   
5. Alertas recibe contexto: {nombre: Juan?, tema: alerta, 
                             historial: trabajo tarjeta oro}
   Alertas: "Hola Juan, vamos a revisar esa transaccion en tu 
           tarjeta oro. Detectamos..."
   [Alertas continua, sin repetir nombre ni tema]
```

### Paso 7: Pruebas end-to-end (120 minutos)
**Ejecuta 3 flujos completos en modo "Test":**

| Flujo | Inicio | Expected Journey | OK? | Notas |
|-------|--------|------------------|-----|-------|
| 1 | "Pregunta sobre tarjeta" | Orq → FAQ | ? | ¿FAQ recibio contexto? |
| 2 | "Quiero abrir cuenta" | Orq → Onboarding | ? | ¿Continuidad de nombres? |
| 3 | "Tengo alerta de fraude" | Orq → Alertas | ? | ¿Alertas tomo contexto? |

**Métricas a verificar:**
- ¿Cliente repitio informacion? (NO = exito)
- ¿Handoff fue transparente? (fluyó natural)
- ¿Contexto se paso correctamente? (siguiente agente "supo")

### Paso 8: Manejo de transiciones (45 minutos)
**Dentro de flujo, cliente puede cambiar de tema:**

```
Cliente en FAQ (sobre tarjeta): "Ah, pero quiero abrir otra cuenta"

Opcion A (MALA): FAQ intenta resolver (fuera de su dominio)
Opcion B (BUENA): FAQ detecta tema nuevo, hace handoff a Onboarding

Implementacion:
FAQ tiene logica: "Si cliente menciona 'abrir cuenta', 
transferir a Onboarding con contexto del cliente"
```

**Valida que cada agente pueda detectar cambio de tema.**

### Paso 9: Optimizacion basada en pruebas (30 minutos)
- Si un handoff es lento: simplifica Topics del orquestador.
- Si el contexto no se pasa: debugging en variables de Copilot Studio.
- Si escalamiento a humano es necesario: agrega ruta "Hablar con asesor".

### Paso 10: Documentacion final (20 minutos)
**Archivo `orquestador-resultado.md`:**

```
# Resultado Reto 05 - Agente Orquestador

## Arquitectura
- Orquestador (maestro)
  ├─ FAQ (especialista)
  ├─ Onboarding (especialista)
  └─ Alertas (especialista)

## Reglas de enrutamiento
- Si menciona: tarjeta, producto, tasa --> FAQ
- Si menciona: cuenta nueva, registro --> Onboarding
- Si menciona: fraude, alerta, seguridad --> Alertas

## Contexto compartido
- {NombreCliente}
- {TemaIntent}
- {HistorialChat}
- {DocumentoIdentidad} (si confirmado)

## Pruebas flujo completo: 3
## Exito (sin repetir informacion): 3/3

## Mejoras futuras
- Agregar logica para cambio de tema DENTRO de un agente
- Integrar con CRM para historial completo
- Escalamiento automatico si 3 handoffs en 5 minutos
```

## Entregables
- Orquestador funcional que enruta a los 3 agentes.
- Matriz de 3 flujos end-to-end exitosos con contexto trasmitido.
- Diagrama de handoffs (texto o visual).
- Demo en vivo si es posible (video o captura secuencial).

## Criterio de exito
✓ Cliente entra por un punto unico (Orquestador).
✓ Se redirige al agente correcto sin friccion.
✓ Contexto (nombre, tema, historial) se pasa correctamente.
✓ Cliente NO repite informacion en transiciones.
✓ Flujo completo multi-agente se siente como UNA conversacion.
✓ Quiz: equipo entiende como agregar un 4to agente mañana.
