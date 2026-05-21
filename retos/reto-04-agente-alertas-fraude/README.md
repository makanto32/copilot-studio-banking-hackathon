# Reto 04 - Agente de alertas de fraude

## Objetivo
Diseñar un agente que comunique alertas de seguridad de forma clara y NO alarmista, guiando al cliente a verificar su identidad y tomar acciones de contencion rapidas.

## Conceptos clave
- **Alerta preventiva**: advertencia ANTES de que ocurra un fraude (transaccion sospechosa bloqueada).
- **Verificacion de identidad**: confirmar que es el cliente antes de revelar detalles.
- **Contencion**: acciones rapidas para proteger la cuenta (bloqueo, cambio de clave).
- **Tono**: AMABLE y PROFESIONAL, nunca alarmante ("No es urgencia, pero..." vs "¡ALERTA!").

## Guia paso a paso

### Paso 1: Definir tipos de alerta (30 minutos)
**Descarga archivo `materiales/datasets-ejemplo/alertas_fraude.csv` o define estas:**

| Tipo alerta | Ejemplo | Riesgo | Accion |
|-------------|---------|--------|--------|
| Consumo inusual | Usuario gasta $5000 en 1h (promedio $200/dia) | MEDIO | Confirmar transaccion |
| Geografia atipica | Transaccion en país diferente a las habituales | MEDIO-ALTO | Verificar identidad |
| Multiples intentos | 5 intentos fallidos de login en 10 min | ALTO | Bloquear cuenta temporalmente |
| Cambio de dispositivo | Login desde navegador nuevo + locacion nueva | BAJO-MEDIO | Confirmar identidad |
| Transferencia grande | Envio a cuenta nueva >= $10k | BAJO-MEDIO | Confirmacion extra |

**Agrega 5 mas segun tu banco.**

### Paso 2: Crear mensajes NO alarmistas (45 minutos)
**Template de mensaje de alerta:**

```
Tono CORRECTO (tranquilo, informativo):
"Hola {nombre}. Detectamos una actividad poco habitual en tu cuenta:
- Transaccion de ${monto} en {ubicacion} hace {tiempo}.
- Esto no coincide con tu patrón usual.

Tu cuenta esta protegida. Queremos confirmar que fuiste TU quien hizo esta transaccion.
¿Fue tu?
  [Si, fui yo] [No, yo no] [No estoy seguro]

Gracias por tu vigilancia."

Tono INCORRECTO (alarmante, asustador):
"¡¡¡ ALERTA CRITICA !!!
Se detecto FRAUDE en tu cuenta!
Tu dinero esta en PELIGRO!
CLICK AQUI AHORA para protegerte.
[Esto asusta y desconfia del cliente]
```

### Paso 3: Diseno del flujo de verificacion (60 minutos)
**Diagrama:**

```
Cliente recibe alerta
  ↓
[Paso 1] Saludo + descripcion de la alerta (sin alarmismo)
  ↓
[Paso 2] Pregunta: "¿Fuiste tu quien hizo esta transaccion/accion?"
  ├─ Si "Si, fui yo"
  │  ↓
  │  "Gracias por confirmar. Tu cuenta sigue protegida."
  │  Fin: alerta resuelta
  │
  ├─ Si "No, yo no"
  │  ↓
  │  [Paso 3A] Verificacion de identidad
  │    - Preguntar ultimos 4 digitos de documento
  │    - Preguntar respuesta a pregunta de seguridad
  │  ↓
  │  [Paso 4A] Si identidad confirmada:
  │    - "Bloqueamos la cuenta temporalmente."
  │    - "Un investigador de fraude te llamara en 1-2 horas."
  │    - Escalamiento a equipo fraude
  │
  └─ Si "No estoy seguro"
     ↓
     [Paso 3B] Preguntar mas detalles
       - "¿Reconoces la ubicacion {lugar}?"
       - "¿Fue un monto que esperabas gastar?"
     ↓
     Basado en respuesta: categorizar como "Si fui yo" o "No fui yo"
```

### Paso 4: Crear el agente en Copilot Studio (30 minutos)
1. Abre Copilot Studio.
2. "+ New copilot".
3. Nombre: `AgenteAlertasFraude`.
4. Idioma: Spanish.
5. "Create".

### Paso 5: Instrucciones del agente (20 minutos)
```
Rol: Eres especialista en seguridad de cuentas del banco.
Tu trabajo es comunicar alertas de seguridad y verificar identidad del cliente.

Reglas de oro:
1. NUNCA suenes alarmante. Mantén tono profesional y tranquilo.
2. SIEMPRE verifica identidad antes de tomar acciones extremas.
3. Si cliente dice "NO fui yo", escala INMEDIATAMENTE a equipo de fraude.
4. NUNCA pidas PIN, contraseña o numero de tarjeta completo.
5. Confirma ultimos 4 digitos de documento O respuesta a pregunta de seguridad.
6. Explica las acciones paso a paso (ej: "vamos a bloquear la cuenta...").

Mensaje inicial:
"Hola {nombre}. Tengo una noticia de seguridad. Detectamos una actividad 
que puede no ser tuya. Te voy a hacer algunas preguntas para estar seguro 
de que todo esta bien."
```

### Paso 6: Topics del flujo (90 minutos)
**Topic 1: AvisoAlerta**
```
Nombre: AvisoAlerta
Disparador: Cliente dice algo como "alerta", "fraude", o sistema inicia flujo
Respuesta: "Hola {nombre}. Detectamos actividad inusual. ¿Puedo 
explicarte?"
Opciones: [Si, explica] [Estoy ocupado] [Quiero hablar con persona]
Siguiente: condicional
```

**Topic 2: DescripcionAlerta**
```
Nombre: DescripcionAlerta
Respuesta:
"Se detecto esto en tu cuenta:
- Transaccion de ${monto}
- Ubicacion: {pais}
- Hora: {hora} hace {tiempo}

Esto es diferente a tus transacciones normales.
¿Fuiste tu quien lo hizo?"
Opciones: [Si, fui yo] [No, yo no] [No estoy seguro]
Siguiente: condicional
```

**Topic 3: FueYo**
```
Nombre: FueYo
Respuesta: "Perfecto, gracias por confirmar. Tu cuenta esta segura.
¿Hay algo mas en lo que pueda ayudarte?"
Variables: guardar {Resultado: Confirmado}
Siguiente: FIN
```

**Topic 4: NoFueYo**
```
Nombre: NoFueYo
Respuesta: "Entendido. Vamos a verificar que realmente seas tu para 
proteger tu cuenta. Responde sin preocupacion."
Siguiente: Topic "VerificacionIdentidad"
```

**Topic 5: VerificacionIdentidad**
```
Nombre: VerificacionIdentidad
Pregunta: "¿Cuales son los ultimos 4 digitos de tu documento de identidad?"
Variables: guardar en {UltimosDigitos}
Validacion: verificar contra base de datos
  - Si correcto: volver a verificar (pregunta de seguridad)
  - Si incorrecto: "Eso no coincide. Intenta de nuevo."
Siguiente: Topic "PreguntaSeguridad"
```

**Topic 6: PreguntaSeguridad**
```
Nombre: PreguntaSeguridad
Pregunta: "¿Cuál es tu lugar de nacimiento?" (ejemplo: usa pregunta registrada)
Variables: guardar en {RespuestaSeguridad}
Validacion: verificar contra base de datos
  - Si correcto: ir a Topic "IdentidadConfirmada"
  - Si incorrecto: "Eso no coincide. Intenta de nuevo (2 intentos quedan)."
Siguiente: condicional
```

**Topic 7: IdentidadConfirmada**
```
Nombre: IdentidadConfirmada
Respuesta: "Identidad confirmada, gracias. Hemos bloqueado tu cuenta 
temporalmente para protegerla. Un especialista de fraude te llamara 
en 1-2 horas para investigar. Mientras, por favor no intentes hacer 
transacciones."
Accion: 
  1. Bloquear cuenta
  2. Crear ticket de fraude
  3. Escalar a equipo de Fraude
Siguiente: FIN
```

**Topic 8: NoEstoySeguro**
```
Nombre: NoEstoySeguro
Pregunta: "Hagamos algunas preguntas rapiditas. ¿Reconoces la ubicacion 
{pais}? ¿Has viajado alli recientemente?"
Variables: guardar {DetallesUbicacion}
Siguiente: condicional (si no reconoce -> escalamiento, si reconoce -> FueYo)
```

### Paso 7: Diseñar matriz de pruebas (30 minutos)
**Tabla de 5 escenarios:**

| Escenario | Situacion | Input cliente | Respuesta esperada | OK? |
|-----------|-----------|---------------|-------------------|-----|
| 1. Tranquilo | Transaccion en ubicacion conocida | "Si, fui yo en viaje" | Confirmar, terminar | ? |
| 2. Fraude real | Cliente NO reconoce ubicacion | "No, yo no" | Verificar identidad -> bloquear | ? |
| 3. Viajero confundido | Ubicacion nueva pero cliente viajando | "No estoy seguro" | Preguntar detalles -> clasificar | ? |
| 4. Identidad fallida | Cliente falla verificacion (4 digitos incorrectos) | "No recuerdo" | Ofrecer alternativa (pregunta seguridad) | ? |
| 5. Escalamiento rápido | Cliente pide hablar con persona | "Quiero a un asesor" | Conectar inmediatamente | ? |

**Ejecuta cada escenario en modo "Test".**

### Paso 8: Ajustes (45 minutos)
- Si mensaje asusta: reescribe más tranquilo.
- Si verificacion falla: agrega más opciones de identificación.
- Si escalamiento tarda: simplifica Topics.

### Paso 9: Documentacion final (20 minutos)
**Archivo `alertas-resultado.md`:**

```
# Resultado Reto 04 - Agente Alertas Fraude

## Tipos de alerta implementados: 5
- Consumo inusual
- Geografia atipica
- Multiples intentos
- ...

## Verificacion de identidad
- Ultimos 4 digitos de documento: SI
- Pregunta de seguridad: SI
- Llamada a asesor: SI

## Pruebas ejecutadas: 5
## Satisfaccion de claridad: 5/5 (clientes no asustados)

## Handoff a equipo fraude
- Automatico cuando cliente dice "No fui yo"
- Crea ticket con contexto
- Equipo tiene 1h para llamar
```

## Entregables
- Agente de alertas funcional.
- Tabla con 5 escenarios de prueba detallados.
- Documento de reglas de escalamiento a fraude.
- Capturas: mensaje de alerta + escalamiento.

## Criterio de exito
✓ Mensajes son CLAROS pero NO alarmistas.
✓ Verificacion de identidad es efectiva (sin ser intrusiva).
✓ Escalamiento a fraude es inmediato cuando se necesita.
✓ Los 5 escenarios funcionan sin friccion.
✓ Quiz: el equipo entiende diferencia entre alerta preventiva y cierre de cuenta.
