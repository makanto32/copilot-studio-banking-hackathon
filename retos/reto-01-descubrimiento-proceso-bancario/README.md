# Reto 01 - Descubrimiento del proceso bancario

## Objetivo
Definir un proceso bancario objetivo y priorizar un problema claro para resolver con agentes.

Este reto es el fundamento de todo lo que sigue: si defines bien el problema, los agentes que crees tendran impacto real.

## Guia paso a paso

### Paso 1: Seleccionar el proceso bancario (30 minutos)
**Opciones sugeridas:**
1. **Apertura de cuenta**: Cliente nuevo quiere abrir cuenta. Hoy: 5 pasos manuales, 20 min promedio.
2. **Consultas de tarjeta**: Cliente pregunta limites, tasas, beneficios. Hoy: llamada a centro, 10 min espera.
3. **Reclamos**: Cliente reporta transaccion sospechosa. Hoy: formulario + investigacion manual, 3-5 dias.
4. **Transferencias internacionales**: Cliente necesita instrucciones. Hoy: documentacion densa, muchas preguntas repetidas.

**Actividad:**
- Vota en el equipo cual problema afecta MAS tu area.
- Si no sabes, elige "Consultas de tarjeta" (mas simple para comenzar).

### Paso 2: Mapear el proceso actual (45 minutos)
**Crea un diagrama simple (texto, no necesita software):**

```
Cliente -> [Punto 1] -> [Cuello botella?] -> [Punto 2] -> [Resolucion o escalamiento]

Ejemplo - Apertura de cuenta:
Cliente quiere cuenta
  -> Asesor explica opciones (LENTO: sin info clara, cliente confundido)
  -> Valida identidad (OK: rapido)
  -> Verifica datos de ingreso (LENTO: requiere documentacion manual)
  -> Se aprueba y se activa (OK)
```

**Completa esta tabla:**

| Paso | Actores | Tiempo hoy | Problema | Causa |
|------|---------|-----------|----------|-------|
| 1. ? | ? | ? min | Clientes no entienden opciones | Info no clara |
| 2. ? | ? | ? min | ? | ? |
| 3. ? | ? | ? min | ? | ? |

### Paso 3: Identificar el cuello de botella (20 minutos)
**Preguntate:**
1. ¿En que paso los clientes se frustran?
2. ¿Donde se pierde mas tiempo?
3. ¿Donde hay mas errores o abandono?
4. ¿Que paso se repite para muchos clientes?

**Marca el paso donde un agente PUEDE ayudar:**
- Explicar opciones (si es solo informacion) ✓ Agente puede hacer
- Procesar pago (si requiere sistema legacy) ✗ Agente no puede
- Responder preguntas (si hay FAQ) ✓ Agente puede hacer

### Paso 4: Definir la metrica objetivo (15 minutos)
**Elige UNA metrica principal:**

| Metrica | Hoy | Objetivo con agente | Como medir |
|---------|-----|-------------------|------------|
| Tiempo de respuesta | 10 min | 2 min | Cronometro o logs |
| % preguntas resueltas sin escalamiento | 60% | 85% | Contador de chats |
| Satisfaccion cliente (NPS/CSAT) | 6/10 | 8/10 | Encuesta post-chat |
| % abandono de cliente | 25% | 10% | Funnel de conversion |
| Costo por atencion | $5 | $0.50 | CRM interno |

**Tu metrica:**
- Nombre: ________________
- Valor actual: ________________
- Objetivo (en 90 dias): ________________

### Paso 5: Hipotesis de mejora con agente (20 minutos)
**Modelo de hipotesis:**
```
SI [descripcion del agente]
ENTONCES [resultado esperado]
PORQUE [razon tecnica o de negocio]

Ejemplo:
SI creamos un agente que explica opciones de tarjeta en tiempo real
ENTONCES reducimos tiempo de atencion de 10 min a 2 min
PORQUE el cliente recibe respuesta inmediata sin esperar asesor
```

**Tu hipotesis:**
1. ________________________________________
2. ________________________________________
3. ________________________________________

## Actividades
1. Seleccionar un proceso (ejemplo: apertura de cuenta, reclamos, consultas de tarjeta).
2. Mapear actores, pasos y cuellos de botella.
3. Definir metrica principal (tiempo de respuesta, abandono, error, satisfaccion).
4. Redactar una hipotesis de mejora con AI.

## Entregables
- Mapa simple del proceso actual (tabla o diagrama texto).
- Problema priorizado y metrica objetivo cuantificada.
- Declaracion de valor esperada con hipotesis clara.
- Documento: `reto-01-resultado.md` en tu carpeta de equipo.

## Criterio de exito
✓ El problema es entendible por personas de negocio (sin jerga tecnica).
✓ La metrica es medible en menos de 90 dias.
✓ Se identifica claramente donde el agente puede ayudar.
✓ El equipo esta alineado en el problema a resolver.
