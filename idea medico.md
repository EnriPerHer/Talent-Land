1. El problema, con datos
En Castilla y León, el año pasado, 572 consultorios no registraron ninguna consulta, ni de medicina ni de enfermería, de los más de 3.600 que tiene la comunidad, la red más grande de España.
En pueblos de Zamora, donde antes había consulta semanal, ahora en algunos el médico aparece una o dos veces al año, y en Monumenta llevan años con una visita cada seis semanas.
En Aragón, el Colegio de Médicos de Zaragoza denuncia que en los pueblos pequeños un solo médico de cabecera atiende varias localidades, y que el verano de 2026 ha repetido el colapso de años anteriores.

El problema concreto que atacáis: cuando el médico llega a un pueblo, no sabe qué se va a encontrar. Pierde tiempo en recetas, papeles o resultados que podría haber resuelto antes, mientras alguien que necesitaba verle de verdad espera días o semanas. Y para el vecino, si no coincide con el día de consulta, tiene que coger el coche (si lo tiene) hasta el centro de salud.

2. La idea: "Antesala"

Un asistente que se encarga de todo lo que pasa antes y después de la visita del médico, para que el tiempo que está en el pueblo se dedique a quien lo necesita.

Principio clave: la IA organiza, no diagnostica. No dice a nadie qué tiene ni si es grave. Esto es lo que lo hace defendible (lo explico en riesgos).

Cómo funciona

Paso 1. El vecino avisa como sabe hacerlo

Llamando a un número normal o mandando un audio de WhatsApp: "Soy Antonio, de Villaflor, necesito la receta de la tensión y me duele la rodilla desde hace dos semanas".
También puede hacerlo un familiar o el alguacil o la persona del ayuntamiento que ayuda a los mayores.

Paso 2. Alarma inmediata (sin IA, con reglas fijas)

Si el mensaje contiene señales de alarma (dolor en el pecho, dificultad para respirar, pérdida de fuerza en un lado...), el sistema no lo clasifica ni lo deja para el día de consulta: responde al instante que llame al 112 y avisa al centro de salud.
Esta parte debe funcionar con reglas sencillas y protocolos ya validados, no con un modelo de lenguaje que puede equivocarse.

Paso 3. Clasificación administrativa
La IA ordena cada petición por tipo de gestión, no por gravedad:

Renovación de receta de un tratamiento crónico
Resultados de análisis o pruebas
Papeles (informes, bajas, certificados)
Curas, vacunas o controles → enfermería
Consulta con el médico
Visita a domicilio (persona que no puede ir al consultorio)

Esto ya existe en la sanidad pública con personas: el Servicio Gallego de Salud evaluó un sistema de gestión integral de la demanda en atención primaria (XIDE). Vosotros lo automatizáis y lo adaptáis a la realidad rural.

Paso 4. El médico recibe la agenda preparada
La mañana de la ruta, el médico ve en una pantalla:

Qué pueblos tienen pacientes y cuántos, con un resumen de una línea por persona.
Qué puede resolver antes de salir (recetas, resultados por teléfono).
Qué es para enfermería.
Las visitas a domicilio, con la ruta propuesta entre pueblos.
El médico revisa, cambia y aprueba. Siempre decide él.

Paso 5. Después de la visita

El médico deja una nota de voz y la IA genera las instrucciones para el paciente en lenguaje sencillo, que le llegan como audio o SMS: "Antonio, tome la pastilla nueva por la mañana. Si en una semana sigue el dolor, llame a este número".
Recordatorios: análisis pendientes, próxima revisión, renovación de receta.
3. Por qué cumple lo que hablamos de evitar
Criterio	Cómo lo cumple
Sin apps	Teléfono normal y audios de WhatsApp
Mala conexión	Una llamada o un audio funcionan con poca cobertura
Diseñado con los usuarios	Necesitáis a un médico rural en el equipo o como mentor (ver abajo)
Escala comarcal	Se organiza por zona básica de salud, que ya agrupa decenas de pueblos
Sin masa crítica	Funciona desde el primer paciente
4. Cómo construirlo en 48 horas
Pieza	Herramienta posible	En la demo
Entrada por voz	Twilio (llamada) o un bot de Telegram como sustituto de WhatsApp	Real
Transcripción	Whisper u otro modelo de voz a texto	Real
Alarmas	Reglas simples con lista de síntomas de alarma	Real
Clasificación y resumen	Un modelo de lenguaje con salida estructurada	Real
Panel del médico	Web sencilla	Real
Ruta entre pueblos	OpenStreetMap + optimizador de rutas	Real o simulada
Historia clínica	Datos inventados	Simulada (decidlo)
Mensajes al paciente	Voz sintética o SMS	Real

Usad pueblos reales de una zona de salud y pacientes inventados.

5. La demo (lo que verá el jurado)
Tres "vecinos" dejan audios en directo: una receta, un dolor de rodilla y un dolor en el pecho.
El del pecho recibe al instante el mensaje de llamar al 112 y salta la alerta en el panel. Este momento es vuestro golpe de efecto: demuestra que lo primero es la seguridad.
Se abre el panel del médico: la agenda del día, dos casos resueltos antes de salir y la ruta.
El médico dicta una nota de voz y el paciente recibe un audio con las instrucciones.
6. Quién paga (y la realidad)
El cliente real es el servicio de salud de la comunidad autónoma. Problema: compra lento, y cualquier herramienta tiene que integrarse con su historia clínica, que es lo más difícil.
Vía de entrada más realista: la diputación o los ayuntamientos. Los consultorios locales los construyen y mantienen los ayuntamientos o entidades locales, y las diputaciones invierten en ellos: la de Zamora ha aprobado más de 660.000 € para modernizarlos. Pueden financiar un piloto en una zona de salud en colaboración con la gerencia de atención primaria.
Vía de crecimiento: compra pública innovadora del servicio de salud tras un piloto con resultados.

Qué medir en el piloto: desplazamientos evitados, trámites resueltos sin visita, tiempo del médico dedicado a consulta clínica y satisfacción de pacientes y profesionales.

7. Los riesgos, sin adornos

1. Regulación sanitaria: el más serio.
Un software que influye en decisiones clínicas (por ejemplo, decidir qué es urgente) probablemente se considere producto sanitario y necesite marcado CE, y además entraría en alto riesgo según la Ley de IA europea. Por eso el diseño separa:

Alarmas → reglas fijas basadas en protocolos existentes, que siempre derivan al 112.
Resto → clasificación administrativa (receta, papeles, consulta), no clínica.
Aun así, en el pitch decid claramente que el camino a producción pasa por un piloto supervisado con el servicio de salud y la validación regulatoria. Un jurado lo valorará más que si fingís que no existe.

2. Datos de salud.
Son datos especialmente protegidos. Necesitáis consentimiento claro, servidores en la UE y no usar los datos para nada más. Decidlo en el pitch.

3. Que los médicos no lo quieran.
Si lo diseñáis sin ellos, lo rechazarán. Conseguid a un médico de familia rural en el equipo o como mentor, aunque sea por videollamada. Os cambiará el diseño y dará credibilidad.

4. Que los datos se usen para cerrar consultorios.
Si el sistema muestra que un pueblo "no tiene demanda", alguien puede usarlo para justificar no ir nunca más. Es un riesgo político real. Diseñad los informes para mostrar necesidades atendidas y no atendidas, y mencionadlo: demuestra que habéis pensado en el impacto.

5. Competencia de grandes empresas.
Ya hay IA entrando en la atención primaria, pero sobre todo en transcribir la consulta: Madrid ha licitado una solución que transcribe la voz y genera informes en consultas presenciales de primaria. Vuestra diferencia es clara: vosotros actuáis antes y después de la visita, y para el medio rural disperso, donde nadie se está centrando.

6. Lo que no resuelve.
No crea médicos. Si no hay médico, no hay consulta. Lo que hace es que el médico que hay rinda más y que ningún vecino se quede sin atender por no coincidir con su visita. Decidlo así.

8. Encaje con las otras ideas

Siendo honesto: no encaja bien dentro del "secretario virtual". El cliente es distinto (sanidad frente a ayuntamiento), la regulación es distinta y los datos son mucho más sensibles. Es mejor tratarlo como proyecto independiente. Comparado con ArraigoAI y el secretario virtual: es el que más emociona y el que más se entiende en 10 segundos, pero también el que más cuesta llevar a la realidad por la regulación sanitaria. Para ganar un hackathon es muy buena opción; para montar una empresa al día siguiente, el secretario virtual es más fácil.
