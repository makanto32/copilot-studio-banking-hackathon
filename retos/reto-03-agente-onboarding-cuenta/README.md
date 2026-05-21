# Reto 03 - Agente de onboarding de cuenta

## Objetivo
Crear un agente que guie a un nuevo cliente paso a paso en la apertura de cuenta de forma clara, verificando eligibilidad y recopilando datos minimos, escalando a humano solo cuando sea necesario.

## Conceptos clave
- **Flujo conversacional**: secuencia logica de preguntas y respuestas.
- **Validacion**: verificar que datos cumplan reglas (ej: edad > 18).
- **Handoff**: transferencia a persona humana cuando es necesario.
- **Contexto**: El agente recuerda lo que client ya dijo.

## Guia paso a paso

### Paso 1: Diseno del flujo de onboarding (45 minutos)
**Diagrama del proceso:**

```
Inicio: "¡Hola! Quieres abrir una cuenta con nosotros?"
  ↓
[Paso 1] Preguntar nombre completo
  ↓
[Paso 2] Validar edad (>= 18?)
  → Si NO: "Lamento, debe ser mayor de edad. Contacta a un asesor."
  → Si SI: continua
  ↓
[Paso 3] Preguntar documento de identidad
  ↓
[Paso 4] Validar documento valido (formato correcto?)
  → Si NO: "El formato no es valido. Intenta de nuevo."
  → Si SI: continua
  ↓
[Paso 5] Preguntar proposito (ahorros, inversion, nómina?)
  ↓
[Paso 6] Confirmar datos y enviar a asesor para firma digital
  ↓
Fin: "Gracias. Un asesor se pondra en contacto en 24h."
```

### Paso 2: Crear el agente Onboarding en Copilot Studio (30 minutos)
1. Abre Copilot Studio.
2. Clickea "+ New copilot".
3. Nombre: `AgenteOnboardingCuenta`.
4. Idioma: Spanish.
5. Clickea "Create".

### Paso 3: Configurar instrucciones del agente (15 minutos)
**En seccion "Personalization" o "Instructions":**

```
Rol: Eres un asistente de onboarding amable de [BANCO]. 
Tu trabajo es guiar a nuevos clientes a traves de los pasos para abrir una cuenta.

Reglas:
1. Siempre verifica edad >= 18.
2. Pide documento (cedula, pasaporte o licencia).
3. Valida formato: 8-15 numeros sin espacios.
4. Si cliente no cumple requisitos, escala a asesor.
5. Confirma los datos ANTES de finalizar.
6. Mantén tono cálido e informativo.
7. Si cliente tiene dudas, ofrece alternativas claras.

Mensaje inicial recomendado:
"¡Bienvenido! Soy tu asistente de apertura de cuenta. 
Te guidaré en 5 pasos simples. ¿Comenzamos?"
```

### Paso 4: Crear Topics (temas) del flujo (90 minutos)
**Topic 1: Bienvenida**
```
Nombre: Bienvenida
Disparador: usuario inicia conversacion
Respuesta: "¡Bienvenido! Soy tu asistente de apertura de cuenta..."
Siguiente: ir a Topic "PedirNombre"
```

**Topic 2: PedirNombre**
```
Nombre: PedirNombre
Pregunta: "¿Cual es tu nombre completo?"
Variables: guardar en {NombreCliente}
Validacion: Si esta vacio, repetir pregunta
Siguiente: ir a Topic "ValidarEdad"
```

**Topic 3: ValidarEdad**
```
Nombre: ValidarEdad
Pregunta: "¿Que edad tienes? (escribe numero)"
Variables: guardar en {Edad}
Validacion: 
  - Si Edad < 18: ir a Topic "MenorEdad"
  - Si Edad >= 18: ir a Topic "PedirDocumento"
Siguiente: condicional
```

**Topic 4: MenorEdad** (escalamiento)
```
Nombre: MenorEdad
Respuesta: "Lamento, debes ser mayor de 18 años. 
Te conectare con un asesor que puede ayudarte con opciones alternativas."
Accion: Escalar a humano
Fin: conversacion
```

**Topic 5: PedirDocumento**
```
Nombre: PedirDocumento
Pregunta: "¿Que tipo de documento tienes? (Cedula, Pasaporte o Licencia)"
Opciones rapidas: [Cedula] [Pasaporte] [Licencia]
Variables: guardar en {TipoDoc}
Siguiente: Topic "NumeroDocumento"
```

**Topic 6: NumeroDocumento**
```
Nombre: NumeroDocumento
Pregunta: "¿Cual es el numero de tu {TipoDoc}? (sin espacios ni guiones)"
Variables: guardar en {NumDoc}
Validacion:
  - Si largo < 8 o > 15: "Formato invalido, intenta de nuevo"
  - Si valido: continua
Siguiente: Topic "PedirProposito"
```

**Topic 7: PedirProposito**
```
Nombre: PedirProposito
Pregunta: "¿Para que usaras principalmente tu cuenta?"
Opciones: [Ahorros] [Inversion] [Recibir nomina] [Negocios] [Otro]
Variables: guardar en {Proposito}
Siguiente: Topic "Confirmacion"
```

**Topic 8: Confirmacion**
```
Nombre: Confirmacion
Respuesta (resumen): "Resumen de tu solicitud:
- Nombre: {NombreCliente}
- Edad: {Edad}
- Documento: {TipoDoc} {NumDoc}
- Proposito: {Proposito}

¿Es todo correcto?"
Opciones: [Si, es correcto] [No, corregir]
  - Si "Si": ir a Topic "Exitoso"
  - Si "No": preguntar que cambiar
Siguiente: condicional
```

**Topic 9: Exitoso**
```
Nombre: Exitoso
Respuesta: "¡Excelente! Hemos recibido tu solicitud.
Un asesor se pondra en contacto en las proximas 24 horas para finalizar.
Te enviaremos un email de confirmacion.

¿Hay algo mas que necesites?"
Accion: Guardar solicitud en sistema (si esta disponible)
Opciones: [Terminar] [Volver al menu]
```

### Paso 5: Agregar validaciones (45 minutos)
**Usa variables y condiciones:**
- En cada input, valida antes de guardar.
- Ejemplo: si documento no cumple formato, repite pregunta sin frustrar cliente.
- Usa mensajes claros: "Ese formato es invalido. Ejemplo correcto: 123456789"

### Paso 6: Configurar manejo de excepciones (30 minutos)
**Escenarios de escalamiento a humano:**

| Escenario | Condicion | Accion |
|-----------|-----------|--------|
| Edad insuficiente | < 18 años | Escalar inmediatamente |
| Documento no reconocido | Formato desconocido | Pedir aclaracion, si falla, escalar |
| Cliente confundido | 3+ preguntas de "que es esto?" | Ofrecer llamada con asesor |
| Solicitud rechazada | Validacion de fraude falla* | Escalar a equipo de fraude |

*Si tu sistema tiene validacion anti-fraude integrada.

### Paso 7: Pruebas con 5 escenarios (90 minutos)
**Ejecuta como cliente en modo "Test":**

| Escenario | Cliente | Resultado esperado | OK? | Notas |
|-----------|---------|-------------------|-----|-------|
| 1. Exitoso | 25 años, cedula valida, proposito claro | Confirmacion y escalamiento a asesor | ? | |
| 2. Menor edad | 16 años | Escalamiento inmediato y empatico | ? | |
| 3. Documento invalido | Numero con espacios | Pedir reformateo sin frustrar | ? | |
| 4. Cambio de datos | Ingresa bien, luego quiere corregir | Permitir correccion y reconfirmar | ? | |
| 5. Abandono | Usuario dice "no quiero" a mitad | Mensaje amable y oferta de callback | ? | |

**Completa la tabla mientras pruebas.**

### Paso 8: Ajustes basados en pruebas (60 minutos)
- Si escenario fallo: ajusta tema (pregunta mas clara, validacion mejor).
- Si mensaje confunde: reescribe con lenguaje mas simple.
- Si escalamiento no funciona: verifica topic "handoff".

### Paso 9: Documento final (20 minutos)
**Crea archivo `onboarding-resultado.md` con:**

```
# Resultado Reto 03 - Agente Onboarding

## Flujo de pasos
1. Bienvenida
2. Nombre
3. Edad (validacion)
4. Tipo documento
5. Numero documento (validacion)
6. Proposito de uso
7. Confirmacion
8. Envio a asesor

## Validaciones implementadas
- Edad >= 18
- Documento: 8-15 caracteres, numerico
- nombre: no vacio

## Pruebas ejecutadas: 5
## Pruebas exitosas: 5

## Puntos fuertes
- Flujo claro y rapido
- Mensajes empaticos
- Escalamiento inteligente

## Areas de mejora
- Agregar validacion de email
- Integrar con CRM para evitar duplicados
```

## Entregables
- Flujo de onboarding funcional en Copilot Studio.
- Documento con politica de escalamiento (cuando y como).
- Tabla de 5 escenarios de prueba con resultados.
- Capturas: al menos flujo general y un escalamiento exitoso.

## Criterio de exito
✓ Los 5 escenarios de prueba se ejecutan sin errores criticos.
✓ Cliente entiende cada paso sin ambiguedad.
✓ Escalamiento a humano funciona y es empatico.
✓ Datos se validan antes de guardar.
✓ Quiz: el equipo entiende como agregar un paso nuevo mañana.
