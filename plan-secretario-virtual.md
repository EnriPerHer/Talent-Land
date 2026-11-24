# Secretario Virtual — Plan del proyecto (hackathon de 8 horas)

> Nombre provisional. Reto: **04 · La IA como servicio** (Talent Land España) — agentes inteligentes que actúan.
> Entregables: **build funcional + pitch + presentación**.

---

## 1. Resumen en una frase

Un agente de IA que **quita carga administrativa a los ayuntamientos pequeños**: convoca plenos cumpliendo los plazos legales, redacta el borrador del acta a partir del audio, comunica los acuerdos a los vecinos y detecta subvenciones que encajan con lo aprobado. Prepara y ejecuta; **una persona aprueba**.

**Lema (borrador):** *"En el pueblo, una persona hace el trabajo de un departamento. Devuélvele el tiempo."*

---

## 2. Problema y contexto

- España tiene 8.131 municipios y 4.986 (el 61 %) tienen menos de 1.000 habitantes (INE, padrón 2022, citado en INAP).
- Hay déficit estructural de personal en la administración local, y los ayuntamientos que comparten secretario deben cuadrar sus convocatorias (Maldita.es).
- Castilla y León ha pedido un estatuto especial para aliviar la carga burocrática de los pequeños municipios (El Español, mayo 2026).
- Un error de forma (por ejemplo, convocar sin respetar el plazo) puede acabar en queja o en impugnación (resoluciones del Procurador del Común).

**Idea fuerza para el pitch:** la burocracia no es opcional, pero el tiempo de quien la hace sí es limitado.

---

## 3. Usuarios

| Usuario | Dolor | Qué gana |
|---|---|---|
| Alcalde/alcaldesa | Convoca, atiende a todos y firma | Menos errores y más tiempo en la calle |
| Secretario-interventor (a veces compartido entre pueblos) | Actas, convocatorias, plazos | Borradores listos para revisar |
| Concejales | Reciben la documentación tarde o mal | Convocatoria clara y con acuse |
| Vecinos | No se enteran de lo que se decide | Aviso en lenguaje claro en el móvil |

---

## 4. Alcance

### MVP (lo que se construye y se demuestra en 8 h)
1. **Convocatorias con "reloj legal".**
2. **Acta borrador a partir de audio** (con enlace al fragmento).
3. **Comunicación a vecinos** (bando en lenguaje claro + envío por Telegram).
4. **Radar de subvenciones** (BDNS) con borrador de solicitud.

### Visión (se cuenta en el pitch, no se construye)
- Registro y clasificación de instancias; respuesta a consultas frecuentes.
- Guía de empadronamiento y lista de documentos que faltan.
- Calendario de vencimientos (información pública, alegaciones, licencias, transparencia).
- Contratos menores: recoger presupuestos y borrador de informe.
- Justificación de subvenciones (recopilar facturas).

### Fuera de alcance (decirlo explícitamente)
- **No firma ni da fe pública.** La firma del acta y la fe pública son humanas.
- No sustituye la función de intervención ni da asesoramiento jurídico vinculante.
- No toma decisiones políticas.

---

## 5. Flujo general (humano en el bucle)

```
Entrada (voz / audio / documento / API)
        ↓
Agente genera borrador o propuesta
        ↓
Persona aprueba (alcalde o secretario)
        ↓
Agente ejecuta (envía, publica, agenda)
        ↓
Registro de lo hecho (log + enlace a la fuente)
```

---

## 6. Agentes y herramientas

**Orquestador:** decide qué agente actúa según la entrada y mantiene el expediente.

### 6.1 Agente de convocatorias ("reloj legal")
- **Entrada:** "Convoca pleno el jueves" (voz o texto por Telegram).
- **Lógica:** comprobar que entre la convocatoria y la sesión median al menos **dos días hábiles** (la ley lo exige salvo extraordinarias urgentes, art. 46.2.b LBRL).
  - Criterio aplicado por los defensores del pueblo: los dos días son **completos**, no cuentan ni el día de la notificación ni el de la sesión, y el sábado no es hábil. **Confirmar el criterio con un secretario real o con la ordenanza antes de la demo.**
  - Además, hay que poder acreditar la **recepción** de la convocatoria, no solo el envío.
- **Herramientas:** calendario de festivos locales (JSON), generador de PDF, envío por Telegram/email, registro de acuses.
- **Salida:** convocatoria + orden del día + notificación enviada + estado de acuses.
- **Si el plazo no cuadra:** propone la fecha válida más cercana o convocar como urgente (que requiere ratificación del pleno).

### 6.2 Agente de actas
- **Entrada:** audio del pleno (varias voces).
- **Pasos:** transcripción con identificación de hablantes → estructura (asistentes, orden del día, deliberaciones resumidas, acuerdos, votaciones) → borrador.
- **Regla clave:** cada acuerdo lleva un **enlace al fragmento de audio** y su marca de tiempo. Si hay duda (voz poco clara, votación ambigua), pregunta en lugar de inventar.
- **Salida:** acta borrador (PDF/MD) + lista de acuerdos estructurados (JSON).

### 6.3 Agente de comunicación
- **Entrada:** lista de acuerdos aprobados.
- **Salida:** bando en lenguaje claro (máx. 120 palabras), mensaje corto para Telegram, publicación en la web de demo y tareas derivadas (responsable + plazo).

### 6.4 Agente de subvenciones
- **Entrada:** acuerdos del pleno (por ejemplo, "arreglar el camino rural", "depósito de agua").
- **Herramienta:** API REST (JSON) de la BDNS / infosubvenciones.
- **Pasos:** buscar convocatorias abiertas → puntuar el encaje (objeto, beneficiario, plazo, importe) → explicar por qué encaja.
- **Salida:** top 3 convocatorias + borrador de memoria de solicitud + fecha límite en el calendario.

---

## 7. Arquitectura mínima

- **Backend:** Python + FastAPI.
- **LLM:** Claude con *tool use* (un agente por función, orquestador simple).
- **Voz:** transcripción con identificación de hablantes (servicio externo o modelo local).
- **Interfaz del usuario:** bot de Telegram (alcalde, concejales y vecinos de demo).
- **Panel web:** estado del expediente + **log "detecté → decidí → ejecuté"** + contador de horas ahorradas (tiempo manual de referencia vs. tiempo con agente).
- **Datos:** SQLite; plantillas de acta y convocatoria; JSON de festivos; ayuntamiento **ficticio** con datos simulados.
- **Generación de documentos:** PDF desde plantillas.

### Contratos de datos (acordar en la primera hora)
```json
Convocatoria { "id", "fecha_sesion", "fecha_envio", "dias_habiles", "cumple_plazo": true, "orden_del_dia": [], "acuses": [{ "concejal", "recibido_en" }] }
Acuerdo      { "id", "texto", "votacion": { "a_favor", "en_contra", "abst" }, "audio_ts": "00:12:41" }
Acta         { "id", "asistentes": [], "acuerdos": [Acuerdo], "borrador_pdf", "estado": "pendiente_revision" }
Match        { "convocatoria_bdns", "acuerdo_id", "puntuacion", "motivo", "plazo" }
```

---

## 8. Plan de 8 horas (equipo de 3-4 personas)

| Hora | Qué | Responsable |
|---|---|---|
| 0:00–0:30 | Cerrar alcance, nombre y ayuntamiento ficticio; preparar datos de ejemplo | Todos |
| 0:30–1:00 | Repo, contratos de datos, esqueleto FastAPI + bot de Telegram | A |
| 1:00–2:30 | Reloj legal + convocatoria | A |
| 1:00–3:30 | Transcripción + acta borrador con enlaces al audio | B |
| 1:00–3:30 | Cliente BDNS + puntuación de encaje + borrador de solicitud | C |
| 2:00–4:30 | Panel con log y contador; envío de bando | C / D |
| 2:00–7:00 | **Presentación y guion del pitch (en paralelo, no al final)** | D |
| 4:30–5:30 | **Integración de punta a punta** (el flujo completo, aunque feo) | Todos |
| 5:30–6:15 | **Congelar funciones.** Pruebas con los datos de la demo y **vídeo de respaldo** | Todos |
| 6:15–7:15 | Pulir pitch y presentación | D + equipo |
| 7:15–8:00 | **3 ensayos cronometrados** | Todos |

**Regla de oro:** primero el flujo completo mínimo, después lo bonito. Nada nuevo después de la hora 5:30.

**Reparto:** A = orquestador y convocatorias · B = actas · C = subvenciones y panel · D = Telegram, demo, pitch y presentación.

---

## 9. Guion de la demo (≈ 3 min)

1. **(30 s) Convocatoria.** El alcalde dice por voz "pleno el viernes". El agente detecta que, por un festivo, no llega a los dos días hábiles, propone una fecha válida, envía la convocatoria y muestra los acuses.
2. **(45 s) Acta.** Se reproduce un pleno simulado de 90 s con tres voces. Aparece el acta borrador con los acuerdos y, al pulsar uno, suena el fragmento de audio.
3. **(30 s) Comunicación.** Sale el bando en lenguaje claro y llega el mensaje a un móvil (Telegram).
4. **(45 s) Subvenciones.** El agente encuentra una convocatoria real que encaja con un acuerdo y deja el borrador de solicitud con la fecha límite.
5. **(30 s) Cierre.** Se muestra el panel: acciones ejecutadas y tiempo manual frente a tiempo con agente.

**Plan B:** audio pregrabado, datos de respaldo en local y vídeo de la demo completa.

---

## 10. Pitch (3 minutos)

1. **Problema (30 s):** historia de una persona (el secretario que cubre varios pueblos) + dato (4.986 municipios con menos de 1.000 habitantes).
2. **Por qué no basta con un asistente (20 s):** no hay que preguntar nada; el agente detecta, decide y ejecuta con aprobación.
3. **Demo (90 s):** el guion anterior, acortado.
4. **Impacto y modelo (30 s):** horas ahorradas por pleno y por semana; cómo se ofrecería (diputaciones, mancomunidades, gestorías municipales).
5. **Siguiente paso (10 s):** piloto con un ayuntamiento real y un secretario que valide los borradores.

---

## 11. Presentación (7 diapositivas, pocas palabras)

1. Portada: nombre + lema.
2. Problema: dato + persona.
3. Solución: el flujo en un diagrama.
4. Demo (captura o vídeo de respaldo).
5. Arquitectura: agentes + herramientas + persona que aprueba.
6. Impacto y modelo de negocio.
7. Equipo y siguiente paso.

---

## 12. Riesgos y mitigaciones

| Riesgo | Mitigación |
|---|---|
| El acta inventa algo | Enlace al audio por acuerdo + revisión humana obligatoria + preguntar si hay duda |
| Error en el cómputo de plazos | Verificar con un secretario real o con la normativa; mostrar el cálculo paso a paso en el panel |
| Privacidad de las voces | Consentimiento y datos simulados en la demo |
| Fallo de red o de API en directo | Audio y respuestas de la BDNS en caché local + vídeo de respaldo |
| Alcance excesivo | Solo 4 flujos; el resto va como visión |
| Exceso de confianza en la IA | Mensaje claro: borradores, no firma ni fe pública |

---

## 13. Métricas de éxito

- Flujo completo funcionando de punta a punta, sin intervención manual salvo las aprobaciones.
- Convocatoria con plazo correcto en los casos de prueba (incluido un festivo).
- Acta con todos los acuerdos enlazados a su audio.
- Al menos una convocatoria real de la BDNS encajada con un acuerdo.
- Tiempo manual de referencia vs. tiempo con agente, **medidos por vosotros** (no uséis cifras que no podáis respaldar).

---

## 14. Checklist final

- [ ] Alcance congelado en la hora 5:30
- [ ] Demo ensayada 3 veces con cronómetro
- [ ] Vídeo de respaldo grabado
- [ ] Audio y datos en local (sin depender de la red)
- [ ] Móvil con Telegram listo y notificaciones activas
- [ ] Presentación exportada en PDF (por si falla el proyector)
- [ ] Una frase clara sobre qué NO hace (firma, fe pública)
- [ ] Reparto de quién habla en el pitch y quién maneja la demo

---

## 15. Fuentes

- INAP — datos de municipios (INE, padrón 2022): https://revistasonline.inap.es/index.php/DA/article/download/11284/12684
- Maldita.es — vacantes de funcionarios y secretarios compartidos: https://maldita.es/malditodato/20230616/vacantes-funcionarios-ayuntamientos-secretarios/
- El Español — petición de estatuto para pequeños municipios: https://www.elespanol.com/castilla-y-leon/region/20260515/castilla-leon-exige-blindar-unidad-funcionarios-administracion-local-toda-espana/1003744247443_0.html
- Derecho Local — art. 46.2.b LBRL y art. 80 ROF (dos días hábiles): https://derecholocal.es/?p=12732
- Procurador del Común — cómputo de días hábiles y acreditación de recepción: https://procuradordelcomun.org/archivos/resoluciones/1_1781609311.pdf
- datos.gob.es — Base de Datos Nacional de Subvenciones (API REST JSON): https://datos.gob.es/catalogo/e05250001-base-de-datos-nacional-de-subvenciones
