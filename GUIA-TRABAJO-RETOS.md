# Guia practica para trabajar y comprender cada reto

Esta guia te dice exactamente que hacer, en que orden, y como validar que vas bien.

## Como usar esta guia
1. Trabaja los retos en orden: 01 -> 02 -> 03 -> 04 -> 05.
2. En cada reto completa 5 bloques: Comprender, Construir, Probar, Documentar, Cerrar.
3. No avances al siguiente reto si no cumples los criterios de exito del actual.

## Flujo de trabajo estandar (aplica a todos)

### 1) Comprender (15-30 min)
- Lee objetivo, conceptos clave y criterio de exito del reto.
- Resume en 3 lineas:
  - Problema que se quiere resolver.
  - Usuario principal.
  - Metrica de exito.

### 2) Construir (45-120 min)
- Sigue los pasos del README del reto sin saltar validaciones.
- Define nombre del agente, descripcion, instrucciones y reglas de escalamiento.
- Reutiliza datasets de materiales cuando aplique.

### 3) Probar (60-120 min)
- Ejecuta pruebas en modo Test/Preview.
- Corre escenarios felices y escenarios de error.
- Registra resultado esperado vs resultado real.

### 4) Documentar (20-30 min)
- Crea un archivo de resultados por reto (markdown).
- Incluye: flujo implementado, pruebas, tasa de exito, mejoras pendientes.
- Adjunta capturas de pantalla de configuracion y pruebas.

### 5) Cerrar (10 min)
- Verifica criterios de exito del reto.
- Publica cambios al repo (commit claro por reto).
- Deja lista una mejora para el siguiente sprint.

## Que hacer en cada reto

## Reto 01 - Descubrimiento del proceso bancario
Referencia: retos/reto-01-descubrimiento-proceso-bancario/README.md

### Que debes comprender
- Cual proceso bancario tiene mayor impacto y friccion.
- Donde esta el cuello de botella que un agente si puede resolver.
- Que metrica se quiere mover en 90 dias.

### Que debes hacer
- Seleccionar 1 proceso prioritario (equipo alineado).
- Mapear proceso actual (pasos, actores, tiempo, problema, causa).
- Elegir 1 metrica principal con valor actual y objetivo.
- Definir 1 hipotesis SI-ENTONCES-PORQUE.

### Evidencia minima
- Documento de resultado (ej: reto-01-resultado.md).
- Tabla de proceso y cuello de botella identificado.
- Metrica con baseline y objetivo.

### Listo para pasar al siguiente
- El problema se entiende en lenguaje de negocio.
- La metrica es medible.
- El equipo acuerda donde el agente aporta valor.

## Reto 02 - Agente FAQ de productos
Referencia: retos/reto-02-agente-faq-productos/README.md
Dataset base: materiales/datasets-ejemplo/faq_productos.csv

### Que debes comprender
- Este agente responde FAQ y escala cuando no sabe.
- Debe ser claro, seguro y sin pedir datos sensibles.

### Que debes hacer
- Crear agente en Copilot Studio:
  - Nombre: AgenteFAQProductos
  - Descripcion: asistente FAQ de productos bancarios
  - Idioma: Spanish
- Cargar knowledge source con el CSV FAQ.
- Configurar instrucciones del agente (tono, alcance, limites).
- Crear temas clave: tarjeta, cuenta, escalamiento.
- Configurar respuestas rapidas y handoff a humano.

### Evidencia minima
- 10 pruebas ejecutadas.
- Minimo 8/10 respuestas correctas.
- Capturas de flujo y prueba.
- Lista de mejoras.

### Listo para pasar al siguiente
- Responde FAQ con claridad.
- Escala correctamente al no saber.
- No pide PIN, claves o credenciales.

## Reto 03 - Agente onboarding de cuenta
Referencia: retos/reto-03-agente-onboarding-cuenta/README.md

### Que debes comprender
- Es un flujo guiado por pasos con validaciones.
- El valor principal es reducir friccion de apertura.

### Que debes hacer
- Crear agente AgenteOnboardingCuenta.
- Implementar flujo con topics:
  - Bienvenida
  - Nombre
  - Edad (validacion >= 18)
  - Tipo y numero de documento (formato)
  - Proposito
  - Confirmacion
  - Cierre o escalamiento
- Definir excepciones y handoff (menor edad, validacion fallida, confusion).

### Evidencia minima
- 5 escenarios de prueba ejecutados.
- Politica de escalamiento documentada.
- Captura de validaciones y confirmacion final.

### Listo para pasar al siguiente
- Flujo sin ambiguedad.
- Validaciones funcionando.
- Escalamiento empatico y consistente.

## Reto 04 - Agente alertas de fraude
Referencia: retos/reto-04-agente-alertas-fraude/README.md
Dataset base: materiales/datasets-ejemplo/alertas_fraude.csv

### Que debes comprender
- Seguridad con comunicacion no alarmista.
- Identidad primero, accion despues.

### Que debes hacer
- Crear agente AgenteAlertasFraude.
- Definir tipos de alerta y mensajes tranquilos.
- Implementar flujo:
  - Fue yo
  - No fui yo
  - No estoy seguro
- Agregar verificacion de identidad (sin pedir datos sensibles).
- Configurar bloqueo/ticket/escalamiento cuando aplique.

### Evidencia minima
- 5 escenarios de prueba con tabla.
- Regla clara de escalamiento a fraude.
- Capturas de alerta y contencion.

### Listo para pasar al siguiente
- Tono profesional no alarmista.
- Verificacion efectiva.
- Escalamiento rapido cuando hay riesgo real.

## Reto 05 - Orquestador de experiencia
Referencia: retos/reto-05-orquestador-experiencia-cliente/README.md

### Que debes comprender
- Este reto integra los otros 3 agentes.
- El cliente entra por un solo punto y no repite informacion.

### Que debes hacer
- Crear agente AgenteOrquestador.
- Definir clasificacion de intent: FAQ, Onboarding, Alertas, Otro.
- Implementar handoff con contexto:
  - NombreCliente
  - TemaIntent
  - HistorialChat
- Probar transiciones entre agentes y cambios de tema.

### Evidencia minima
- 3 flujos end-to-end exitosos.
- Prueba de continuidad de contexto entre agentes.
- Diagrama de handoffs.

### Listo para cerrar hackathon
- Enrutamiento correcto y natural.
- Contexto compartido sin perdida.
- Experiencia percibida como una sola conversacion.

## Plan sugerido de ejecucion (2 dias)

### Dia 1
- Manana: Reto 01 y Reto 02.
- Tarde: Pruebas, ajustes y documentacion de Reto 02.

### Dia 2
- Manana: Reto 03 y Reto 04.
- Tarde: Reto 05, pruebas end-to-end, demo final.

## Errores comunes y como evitarlos
- Error: avanzar sin metrica clara en Reto 01.
  - Evita: define baseline y objetivo antes de construir agentes.
- Error: FAQ sin knowledge suficiente.
  - Evita: asegura 20 preguntas utiles en el CSV y prueba cobertura.
- Error: pedir datos sensibles en prompts.
  - Evita: regla explicita de seguridad en instrucciones.
- Error: handoff sin contexto en Reto 05.
  - Evita: pasa variables clave en cada transferencia.

## Checklist final
- [ ] Reto 01 con problema y metrica definidos.
- [ ] Reto 02 con 10 pruebas y >= 80% de exito.
- [ ] Reto 03 con 5 escenarios y validaciones activas.
- [ ] Reto 04 con tono no alarmista y escalamiento a fraude.
- [ ] Reto 05 con 3 flujos end-to-end y contexto compartido.
- [ ] Documentacion y capturas completas por reto.
