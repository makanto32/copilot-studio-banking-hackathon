# Documento Guia del Hackathon

## Caso de uso
Un banco quiere mejorar su experiencia de cliente en tres frentes:
- resolver preguntas frecuentes de productos financieros,
- asistir el onboarding de nuevas cuentas,
- detectar y comunicar alertas tempranas de posible fraude.

Se propone crear una experiencia agentica en Copilot Studio con varios agentes especializados y un agente orquestador.

## Explicacion de la iniciativa
Este hackathon esta pensado para usuarios no tecnicos (negocio, operaciones, servicio al cliente, riesgo, cumplimiento) para que aprendan haciendo:
- como definir un objetivo de negocio para un agente,
- como construir conversaciones utiles,
- como conectar conocimiento y acciones,
- como medir calidad y valor del resultado.

## Alcance
Incluye:
- diseno y creacion de agentes en Copilot Studio,
- modelado de prompts y respuestas esperadas,
- pruebas funcionales con escenarios bancarios simples,
- propuesta de adopcion inicial por equipo.

No incluye:
- desarrollo de software complejo,
- integraciones productivas en sistemas core,
- despliegue enterprise con gobierno completo.

## Objetivo del evento
Ayudar a usuarios no tecnicos a familiarizarse con Copilot Studio y con los conceptos basicos de AI agentica, guiando al cliente en un proceso practico de adopcion de AI.

## Objetivos especificos
1. Entender el concepto de agente, rol, memoria de contexto y handoff.
2. Construir agentes orientados a problemas de banca retail.
3. Probar conversaciones reales con criterios de calidad.
4. Presentar una mini hoja de ruta de adopcion para negocio.

## Ruta de adopcion para usuarios no tecnicos
1. **Descubrir**: identificar dolor de negocio y metricas base.
2. **Diseñar**: definir rol del agente, tono y limites.
3. **Construir**: crear temas, conocimiento y acciones.
4. **Validar**: probar con casos reales y ajustar.
5. **Escalar**: documentar valor y priorizar siguientes casos.

## Guia paso a paso para completar el hackathon

### Paso 1: Preparativos (30 minutos)
1. Abre https://www.microsoft.com/en/copilot/copilot-studio (requiere cuenta Microsoft o Azure AD).
2. Crea un nuevo "Copilot" o "Agent" (el panel puede variar segun version).
3. Nombralo segun el reto (ej: "Agente-FAQ-Banca").
4. Selecciona idioma: Spanish (España) o Spanish (Latinoamérica).
5. Guarda y toma nota del ID del agente.

### Paso 2: Estructura basica de cada agente (todos los retos)
1. **Definir el prompt del sistema** (instruccion inicial al agente):
   - Rol: quien es el agente, que hace.
   - Tono: profesional, sencillo, amable.
   - Limites: que NO debe hacer.
   - Ejemplo: "Eres un agente bancario que responde FAQ sobre tarjetas. Siempre sé formal pero amable. No reveles políticas confidenciales."

2. **Crear topics (temas)** en el flujo:
   - Cada tema es una ramificacion de conversacion.
   - Ej para FAQ: "Preguntas sobre tasas", "Beneficios de cuenta", "Escalamiento a asesor".

3. **Agregar formato de repuesta**:
   - Cards (tarjetas visuales con opciones).
   - Listas de opciones rapidas.
   - Texto para respuestas largas.

### Paso 3: Integrar conocimiento
1. Sube documentos (PDF, Word, CSV) con info del banco.
   - FAQ sobre productos.
   - Politicas de onboarding.
   - Guia de alertas de seguridad.
2. Usa "Knowledge" o "Documents" (segun la seccion).
3. Entrena el agente con esa base haciendo pruebas.

### Paso 4: Configurar acciones (opcionalR)
1. Algunas versiones permiten "Actions" o "Skills":
   - Llamar APIs externas.
   - Registrar datos en sistemas.
   - Enviar emails.
2. Para este hackathon, mantén simple: solo conversacion.

### Paso 5: Probar en modo demo
1. Usa el boton "Test" o "Preview".
2. Ejecuta los 5 escenarios del reto.
3. Ajusta respuestas hasta lograr 80%+ de exito.

### Paso 6: Handoff entre agentes (Reto 05)
1. En el agente orquestador, agrega "Handoff" o "Transferencia".
2. Define reglas: si el usuario pregunta X, transferir a agente FAQ; si pregunta Y, a onboarding.
3. Mantén contexto: el nuevo agente debe saber que vino del anterior.

### Paso 7: Documentar y presentar
1. Llena plantilla de ficha-agente.md para cada uno.
2. Haz capturas de pruebas.
3. Resume valor esperado: tiempo ahorrado, mejora de satisfaccion, riesgo mitigado.

## Entregable final del hackathon
Cada equipo entrega:
- 1 demo de flujo end-to-end con multiples agentes,
- 1 ficha por cada agente creado (usar template),
- 1 resumen de impacto esperado (eficiencia, satisfaccion, riesgo).

## Recursos
- Docs: https://learn.microsoft.com/en-us/microsoft-copilot-studio/
- Video intro (5 min): busca "Copilot Studio quick start" en YouTube.
- Ejemplos en este repo: `/materiales/` tiene prompts y datasets base.
