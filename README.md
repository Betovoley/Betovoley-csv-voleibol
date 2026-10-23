Scouting de vóley en CSV: Liga Argentina (2007-2024) y Superliga Brasileña (2022-2026)
Acciones de scout de partidos de vóley masculino, convertidas de archivos DataVolley (`.dvw`)
a tablas CSV para trabajar en Python/pandas. Cada fila de la tabla principal es un toque
(saque, recepción, armado, ataque, bloqueo, defensa o free ball) con su evaluación, zonas,
jugador, rotación y marcador.
21 datasets: 17 temporadas de la Liga Argentina de Voleibol (LVA) y 4 de la Superliga
Brasileña, cada uno en su propia carpeta dentro de `datos/`.
3.292 partidos y 3.068.262 acciones sin duplicados (3.089.567 acciones contando los archivos
duplicados). Tres partidos de Sudamericanos están a la vez en un dataset argentino y en uno brasileño.
Origen: archivos de scouting de analistas de rendimiento, no estadísticas oficiales de
las ligas (detalles en De dónde salen los datos).
---
Contenido
Estructura del repositorio
Qué cubre cada dataset
Uso rápido
Reglas antes de analizar
De dónde salen los datos
Cómo se procesaron
Diccionario de datos
Códigos de DataVolley
Limitaciones conocidas
Regenerar o agregar temporadas
---
Estructura del repositorio
```
README.md                         este archivo
requirements.txt                  dependencias (pandas)
scripts/dvw_a_csv.py              conversor .dvw → CSV
ejemplos/ejemplo_rapido.py        carga un dataset y calcula dos indicadores
datos/
  Liga_Argentina_de_Voleibol_2007-2008_masculina/
  ...
  Liga_Argentina_de_Voleibol_2023-2024_masculina/
  Superliga_Brasilera_2022-2023_masculina/
  ...
  Superliga_Brasilera_2025-2026_masculina/
```
Cada carpeta de `datos/` tiene siempre los mismos 7 archivos:
Archivo	Una fila por…	Para qué sirve
`acciones.csv.gz`	acción de scout	La tabla principal. Comprimida porque GitHub no acepta archivos de más de 100 MB; pandas la lee directo
`partidos.csv`	archivo `.dvw` (≈ partido)	Fecha, competición, equipos, resultado, scouter, duplicados y validaciones
`sets.csv`	set	Marcador final y parciales (8, 16 y 21 puntos)
`jugadores.csv`	jugador en un partido	Planteles tal como figuran en cada archivo, con el `jugador_uid` unificado
`jugadores_unificados.csv`	jugador	Un registro por jugador, con todas las variantes de nombre que se agruparon
`errores.csv`	línea o archivo descartado	Lo que no se pudo interpretar o se excluyó, y el motivo
`NOTAS.md`	—	Resumen y problemas específicos de esa temporada. Conviene leerlo antes de usar el dataset
---
Qué cubre cada dataset
"Partidos" cuenta partidos distintos (sin duplicados); "Acciones" es el total sin duplicados
(`es_principal = True`). La columna de la liga principal corresponde a
`tipo_competicion = "Liga (LVA)"` en Argentina y `"Superliga"` en Brasil.
Liga Argentina de Voleibol (LVA)
Temporada	Carpeta	Partidos	Liga	Otras competiciones	Acciones	Fechas	Observaciones
2007-08	`Liga_Argentina_de_Voleibol_2007-2008_masculina`	93	93	—	65.662	oct-2007 → mar-2008	Solo fase regular; sin rol de jugadores
2008-09	`Liga_Argentina_de_Voleibol_2008-2009_masculina`	88	88	—	70.571	oct-2008 → mar-2009	Sin la final
2009-10	`Liga_Argentina_de_Voleibol_2009-2010_masculina`	132	132	—	105.304	nov-2009 → abr-2010	Final UPCN–Bolívar
2010-11	`Liga_Argentina_de_Voleibol_2010-2011_masculina`	164	162	Entrenamiento 2	138.384	ago-2010 → may-2011	Final UPCN–Bolívar (7 partidos)
2011-12	`Liga_Argentina_de_Voleibol_2011-2012_masculina`	159	159	—	140.484	oct-2011 → abr-2012	Final UPCN–Boca
2012-13	`Liga_Argentina_de_Voleibol_2012-2013_masculina`	159	159	—	140.062	nov-2012 → abr-2013	Final UPCN–Buenos Aires Unidos
2013-14	`Liga_Argentina_de_Voleibol_2013-2014_masculina`	131	131	—	118.624	nov-2013 → abr-2014	Final UPCN–Lomas
2014-15	`Liga_Argentina_de_Voleibol_2014-2015_masculina`	116	114	Amistosos 2	100.289	nov-2014 → abr-2015	Final UPCN–Bolívar (5 partidos)
2015-16	`Liga_Argentina_de_Voleibol_2015-2016_masculina`	128	128	—	122.708	nov-2015 → abr-2016	Final UPCN–Bolívar (5 partidos)
2016-17	`Liga_Argentina_de_Voleibol_2016-2017_masculina`	225	134	Amistosos, Copa ACLAV, Copa Argentina, Copa Bolívar, Copa Máster, Pre Sudamericano y otras (91)	209.298	sep-2016 → abr-2017	
2017-18	`Liga_Argentina_de_Voleibol_2017-2018_masculina`	229	136	Amistosos, Copa ACLAV, Sudamericano, Copa Argentina, Torneo Krosno y otras (93)	218.384	sep-2017 → abr-2018	Final UPCN–Bolívar
2018-19	`Liga_Argentina_de_Voleibol_2018-2019_masculina`	176	114	Amistosos, Copa ACLAV, Supercopa, Copa Desafío y otras (62)	178.080	sep-2018 → abr-2019	Final Bolívar–Obras
2019-20	`Liga_Argentina_de_Voleibol_2019-2020_masculina`	135	97	Amistosos, Copa RUS, Super 4, Sudamericano y otras (38)	127.571	sep-2019 → mar-2020	Termina en cuartos (pandemia)
2020-21	`Liga_Argentina_de_Voleibol_2020-2021_masculina`	80	56	Copa RUS, amistosos (24)	85.126	nov-2020 → abr-2021	Final UPCN–Ciudad
2021-22	`Liga_Argentina_de_Voleibol_2021-2022_masculina`	159	119	Copa ACLAV, amistosos, Supercopa, Copa Ciudad, Mundial de Clubes (40)	164.095	oct-2021 → abr-2022	
2022-23	`Liga_Argentina_de_Voleibol_2022-2023_masculina`	185	143	Copa ACLAV, amistosos, Supercopa, Sudamericano (42)	189.298	sep-2022 → abr-2023	90 % del scout de un solo scouter
2023-24	`Liga_Argentina_de_Voleibol_2023-2024_masculina`	59	50	Super 4, amistosos, Sudamericano (9)	61.415	oct-2023 → abr-2024	Cobertura parcial; muchos scouters distintos
Superliga Brasileña
Temporada	Carpeta	Partidos	Superliga	Otras competiciones	Acciones	Fechas	Observaciones
2022-23	`Superliga_Brasilera_2022-2023_masculina`	171	140	Sudamericano 11, Mundial 9, Copa do Brasil 7, Mineiro 3, Supercopa 1	158.896	sep-2022 → abr-2023	Fase regular: 129 de 132 partidos
2023-24	`Superliga_Brasilera_2023-2024_masculina`	248	150	Mineiro 32, Paulista 30, Sudamericano 14, Copa do Brasil 7, Mundial 6, amistosos 5, Catarinense 3, Supercopa 1	230.647	ago-2023 → abr-2024	Fase regular completa
2024-25	`Superliga_Brasilera_2024-2025_masculina`	240	148	Mineiro 31, Paulista 16, Mundial 16, Sudamericano 16, Copa do Brasil 5, amistosos 4, Paulista Sub-21 3, Supercopa 1	232.031	ago-2024 → may-2025	Fase regular completa
2025-26	`Superliga_Brasilera_2025-2026_masculina`	215	148	Mineiro 24, Sudamericano 15, Mundial 11, Copa do Brasil 6, amistosos 6, Paulista 4, Supercopa 1	211.333	ago-2025 → may-2026	Fase regular completa
En la Superliga, las competiciones estaduales (Mineiro, Paulista, Catarinense) y los torneos
internacionales están incompletos: solo figuran los partidos que se scoutearon.
---
Uso rápido
```bash
pip install -r requirements.txt
python ejemplos/ejemplo_rapido.py
```
Un dataset:
```python
import pandas as pd

ruta = "datos/Superliga_Brasilera_2024-2025_masculina/"
acc = pd.read_csv(ruta + "acciones.csv.gz", low_memory=False)
partidos = pd.read_csv(ruta + "partidos.csv")

acc = acc[acc["es_principal"]]                         # 1) sin partidos duplicados
liga = acc[acc["tipo_competicion"] == "Superliga"]     # 2) solo la liga ("Liga (LVA)" en Argentina)
```
Todos los datasets juntos:
```python
from pathlib import Path
import pandas as pd

partes = []
for carpeta in sorted(Path("datos").iterdir()):
    df = pd.read_csv(carpeta / "acciones.csv.gz", low_memory=False)
    df.insert(0, "dataset", carpeta.name)              # identifica liga y temporada
    partes.append(df)
acc = pd.concat(partes, ignore_index=True)

acc = acc[acc["es_principal"]]
acc = acc.drop_duplicates(["match_id", "linea_scout"])  # 3 partidos están en dos datasets (ver abajo)
```
Las tablas se unen así:
Unión	Columnas
`acciones` ↔ `partidos` / `sets`	`match_id`
`acciones` ↔ `jugadores`	`match_id` + `equipo` + `jugador_numero` = `numero`
`acciones` / `jugadores` ↔ `jugadores_unificados`	`jugador_uid`
---
Reglas antes de analizar
Filtrar siempre por `es_principal`. Algunos partidos están scouteados en dos o tres
archivos (versión de TV, otro scouter, local y visitante invertidos). `es_principal = True`
deja uno solo por partido. Sin ese filtro, esos partidos se cuentan dos o tres veces.
Filtrar por `tipo_competicion`. Muchos datasets incluyen amistosos, copas, torneos
internacionales y algunos entrenamientos (`Entrenamiento`, `Amistoso (interno)`), que hay
que excluir para cualquier estadística de equipo.
La cobertura no es pareja. En Argentina ninguna temporada está completa y algunos equipos
tienen muchos más partidos que otros: hay que normalizar por partido o por acción antes de
comparar. En la Superliga 2023-24, 2024-25 y 2025-26 la fase regular está completa.
Los códigos dependen del scouter. Las combinaciones de ataque (`X5`, `W4`…), las llamadas
del armador, los tempos e incluso algunas evaluaciones varían entre scouters. Lo más
comparable entre temporadas y ligas es fundamento + evaluación. El scouter de cada
archivo está en `partidos.scouter`.
Para seguir a un jugador, usar `jugador_uid`, no el nombre ni el número de camiseta. El
uid está unificado dentro de cada dataset; entre temporadas suele coincidir, pero no está
garantizado.
Partidos que aparecen en dos datasets: tres partidos de Sudamericanos están en un dataset
argentino y en uno brasileño (São José–Policial y Monteros–Policial de 2024; Sada–Policial
de 2023). Si se combinan datasets, hay que quitarlos una vez. Los dos de 2024 tienen el mismo
`match_id`; el de 2023 vino en archivos con otro nombre, así que tiene `match_id` distinto.
Leer `NOTAS.md` de cada carpeta: ahí están los archivos excluidos, las fechas corregidas,
los marcadores que no validan y los equipos renombrados.
---
De dónde salen los datos
Fuente: archivos de scouting en formato DataVolley 4 (`.dvw`) registrados por analistas
de rendimiento durante o después de cada partido, en vivo o en video. No son estadísticas
oficiales de la LVA ni de la CBV: reflejan el criterio de cada scouter.
Quién los hizo: la mayoría de los archivos son de un mismo analista (figura como
"Betovoley" o "Beto" en `partidos.scouter`), con aportes de scouters de otros clubes y de
VolleyMetrics. En la Superliga 2022-23, el 96 % del scout es de otro scouter.
Qué se dejó afuera: entrenamientos internos, partidos de ligas extranjeras (Italia, Japón,
Polonia, Portugal, Francia), un partido femenino que vino por error, archivos de prueba,
archivos vacíos o corruptos y copias de respaldo. Cada caso está detallado en el `NOTAS.md` de
su temporada y, si se excluyó un archivo leído, también en `errores.csv`.
Conversión: con `scripts/dvw_a_csv.py`, incluido en el repo, sin edición manual de los datos.
Las únicas correcciones manuales (renombrar un rival mal cargado, reclasificar una competición
o excluir un archivo) están escritas en el propio script, en `OVERRIDES_EQUIPO` y `EXCLUIR`.
> **Uso**: datos de scouting compartidos para trabajos académicos. Antes de publicarlos o
> redistribuirlos fuera de este grupo, consulten con el autor.
---
Cómo se procesaron
Lectura: cada `.dvw` (también `.leg`, que es el mismo formato) se lee en UTF-8 o, si
falla, en Windows-1252. Se separan las secciones de partido, equipos, sets, planteles y scout.
Scout: cada línea de acción se descompone en sus campos (equipo, jugador, fundamento,
tipo, evaluación, combinación, zonas, subtipo, etc.). Se ignoran las líneas de control
(armador en cancha, cambios, tiempos, fin de set). Las líneas que no se pueden interpretar
van a `errores.csv`.
Rallies y marcador: las acciones se agrupan en rallies entre puntos; a cada acción se le
asigna el marcador antes y después, quién ganó el rally, quién sacó y si es side-out o break point.
Validación: el marcador final de cada set reconstruido desde el scout se compara con el
de la cabecera (`partidos.validacion_marcador`).
Normalización de equipos: los nombres se unifican (patrocinadores, mayúsculas, tildes,
abreviaturas: "Itambé Minas" → Minas Tênis Clube, "PSM VOLEY" → Puerto San Martín). El nombre
original queda en `equipo_*_original`.
Competición: se clasifica cada archivo (liga, copa, amistoso, etc.) a partir de la
cabecera y del nombre de archivo, en `tipo_competicion`. El texto original queda en
`competicion_original`.
Fechas: si la fecha de la cabecera falta o es imposible, se corrige con el nombre del
archivo, invirtiendo día y mes o con el año correcto de la temporada. Si no se puede
corregir, queda vacía. Todo cambio queda en `partidos.fecha_corregida`.
Duplicados: dos archivos son el mismo partido si coinciden equipos, fecha (±1 día) y
marcador de cada set, aunque local y visitante estén invertidos. Se marca `grupo_duplicado`
y `es_principal` queda en el archivo con más acciones.
Jugadores: las variantes de nombre del mismo jugador se agrupan en un `jugador_uid`
(ver `jugadores_unificados.csv`).
---
Diccionario de datos
Convenciones: `local`/`visitante` se refieren al equipo local y visitante del archivo .dvw,
que no siempre coincide con quién jugaba de local. Los textos están en UTF-8. Las celdas
vacías significan "sin dato en el scout".
`acciones.csv.gz`
Columna	Tipo	Descripción
`match_id`	texto	Identificador del partido (`m` + hash del nombre de archivo). Se repite en todas las tablas
`accion_id`	entero	Número de fila dentro del dataset (se reinicia en cada dataset)
`fecha`	fecha	Fecha del partido (AAAA-MM-DD), copiada de `partidos`
`tipo_competicion`	texto	Competición normalizada, copiada de `partidos`
`es_principal`	bool	`False` si el archivo es un duplicado de otro partido, copiado de `partidos`
`set`	entero	Número de set (1-5)
`rally_n`	entero	Número de rally dentro del partido
`marcador_local`, `marcador_visitante`	entero	Marcador del set antes del rally
`marcador_local_despues`, `marcador_visitante_despues`	entero	Marcador al terminar el rally
`punto_ganado_por`	texto	`local` o `visitante`
`gana_rally_equipo_accion`	bool	`True` si el equipo que hizo la acción ganó el rally
`equipo`	texto	`local` o `visitante`
`equipo_nombre`, `rival_nombre`	texto	Nombre normalizado del equipo que hace la acción y de su rival
`jugador_numero`	entero	Número de camiseta
`jugador_nombre`	texto	Nombre como figura en ese archivo (APELLIDO NOMBRE, sin tildes)
`jugador_uid`	texto	Identificador unificado del jugador (vacío si el número no está en el plantel)
`jugador_canonico`	texto	Nombre elegido para ese `jugador_uid`
`jugador_rol`	texto	Armador, Punta, Opuesto, Central o Líbero (vacío si el scouter no lo cargó)
`fundamento_cod`, `fundamento`	texto	S Saque, R Recepción, E Armado, A Ataque, B Bloqueo, D Defensa, F Free ball, & Código verde
`tipo_cod`, `tipo`	texto	Tipo de saque/recepción o tempo del balón (tabla)
`evaluacion_cod`, `evaluacion`	texto	`#` `+` `!` `-` `/` `=` (tabla)
`combinacion`	texto	Código de combinación de ataque (solo en A). Depende del scouter
`llamada_armador`	texto	Llamada del armador (K1, K7…; solo en E). Depende del scouter
`objetivo_armado`	texto	Destino del armado (F adelante/4, C centro/3, B atrás/2, P pipe, S armador)
`zona_inicio`, `zona_fin`	entero	Zona de cancha 1-9 donde empieza y termina el balón (zonas)
`subzona_fin`	texto	Subzona A-D dentro de `zona_fin`
`subtipo`, `subtipo_desc`	texto	En ataque: H potente, P colocado (roll), T toque (tip)
`num_jugadores`	entero	En ataque: número de bloqueadores (0-4)
`especial`	texto	Código especial de DataVolley (p. ej. tipo de error), sin traducir
`custom`	texto	Campo libre del scouter, sin traducir
`equipo_saque`	texto	`local` o `visitante`: quién sacó en ese rally
`fase_juego`	texto	`Side-out` si la acción es del equipo que recibe; `Break point` si es del que sacó
`fase_ataque_raw`	texto	Campo original de DataVolley en ataques (r = tras recepción, p = transición)
`fase_punto_raw`	texto	Campo original en la acción que cierra el punto (s side-out, p break point)
`rotacion_local`, `rotacion_visitante`	entero	Zona (1-6) donde está el armador de cada equipo = rotación
`coord_inicio`, `coord_medio`, `coord_fin`	entero	Coordenada DataVolley del balón (casi siempre vacía antes de 2023)
`inicio_x`, `inicio_y`, `medio_x`, `medio_y`, `fin_x`, `fin_y`	entero	Esa coordenada en la grilla de DataVolley (x 1-100, y 1-101)
`hora`	texto	Hora en que se registró la acción
`video_seg`	entero	Segundo del video asociado
`local_p1` … `local_p6`	entero	Número de camiseta del local en cada zona (1-6) en ese momento
`visit_p1` … `visit_p6`	entero	Ídem para el visitante
`codigo`	texto	Código DataVolley original de la acción, para auditar
`linea_scout`	entero	Número de línea en la sección de scout del `.dvw`
`partidos.csv`
Columna	Tipo	Descripción
`match_id`	texto	Identificador del partido
`archivo`	texto	Nombre del archivo `.dvw` original
`fecha`	fecha	Fecha del partido (corregida si hacía falta; vacía si no se pudo determinar)
`fecha_corregida`	texto	Qué se corrigió en la fecha y por qué (vacío si no hubo cambios)
`hora`	texto	Hora de inicio según la cabecera
`temporada_original`, `competicion_original`, `fase_original`	texto	Textos tal como los escribió el scouter
`tipo_competicion`	texto	Competición normalizada (`Liga (LVA)`, `Superliga`, `Copa ACLAV`, `Amistoso`…)
`equipo_local_original`, `equipo_visitante_original`	texto	Nombres de equipo como venían en el archivo
`equipo_local`, `equipo_visitante`	texto	Nombres normalizados
`sets_local`, `sets_visitante`	entero	Sets ganados (solo cuenta sets terminados)
`resultado_sets`	texto	Marcador de cada set, local-visitante, separado por `;`
`partido_completo`	bool	`False` si el archivo no tiene un partido terminado
`ganador`	texto	Equipo ganador (vacío si el partido está incompleto)
`scouter`	texto	Quién hizo el scout, según el archivo (sin e-mails)
`software`	texto	Programa y versión que generaron el archivo
`encoding_original`	texto	Codificación del archivo original (`utf-8` o `cp1252`)
`n_acciones`	entero	Acciones extraídas de ese archivo
`n_lineas_no_reconocidas`	entero	Líneas del scout descartadas (ver `errores.csv`)
`validacion_marcador`	texto	`OK` si el scout reproduce el marcador de cada set; si no, la diferencia
`grupo_duplicado`	texto	Mismo valor para archivos que son el mismo partido (vacío si no hay duplicado)
`es_principal`	bool	`True` en el archivo que se usa para ese partido
`sets.csv`
Columna	Tipo	Descripción
`match_id`	texto	Identificador del partido
`set`	entero	Número de set
`puntos_local`, `puntos_visitante`	entero	Marcador final del set
`parcial_8`, `parcial_16`, `parcial_21`	texto	Marcador en los tiempos técnicos (8, 16 y 21 puntos), si el scouter los cargó
`duracion_min`	entero	Duración del set en minutos
`set_completo`	bool	`False` si el set no llegó a terminar (25 o 15 puntos con 2 de diferencia)
`jugadores.csv`
Columna	Tipo	Descripción
`match_id`	texto	Identificador del partido
`equipo`	texto	`local` o `visitante`
`equipo_nombre`	texto	Nombre normalizado del equipo
`numero`	entero	Número de camiseta en ese partido
`jugador_id_dv`	texto	ID interno del jugador en la base de DataVolley del scouter (no es comparable entre scouters)
`apellido`, `nombre`	texto	Como figuran en el archivo
`jugador_std`	texto	APELLIDO NOMBRE en mayúsculas y sin tildes
`rol_cod`, `rol`	texto	1 Líbero, 2 Punta, 3 Opuesto, 4 Central, 5 Armador
`es_libero`	bool	Marcado como líbero en el plantel
`pos_inicial_set1` … `pos_inicial_set5`	texto	Zona de inicio en cada set (`*` = entró desde el banco; vacío = no jugó)
`jugador_uid`, `jugador_canonico`	texto	Identificador unificado y nombre canónico
`jugadores_unificados.csv`
Un jugador por fila. Las variantes del mismo jugador ("HILLING MATHEW" / "Hilling Matthew
Alexander", errores de tipeo, apellidos faltantes) se agrupan con estas reglas, siempre dentro
de un mismo dataset:
Mismo equipo y mismo número: se unen si todas las palabras del nombre más corto están en el
otro (se aceptan errores de tipeo).
Mismo equipo y distinto número: se unen solo si coincide el apellido y todo el nombre más corto.
Distinto equipo: se unen solo si el nombre es idéntico (traspasos, selecciones juveniles).
Dos registros que aparecen en el mismo partido nunca se unen.
Columna	Tipo	Descripción
`jugador_uid`	texto	Identificador: el nombre canónico en formato `APELLIDO_NOMBRE` (con el equipo agregado si hay homónimos)
`jugador_canonico`	texto	Nombre elegido (la variante más frecuente y completa)
`equipos`, `numeros`	texto	Equipos y números con los que aparece, separados por `|`
`variantes`	texto	Todas las formas en que figuraba el nombre
`n_partidos`	entero	Partidos en los que aparece
`union`	texto	Motivo de la agrupación (`única`, `exacta`, `variante de nombre, mismo número`, `distinto número`, `cambio de equipo`)
`revisar`	bool	`True` si la unión es dudosa (distinto número o cambio de equipo) y conviene verificarla a mano
`errores.csv`
Columna	Tipo	Descripción
`match_id`	texto	Partido (vacío si se excluyó el archivo entero)
`archivo`	texto	Archivo `.dvw`
`linea_scout`	entero	Línea del scout que no se pudo interpretar (vacío si es el archivo entero)
`contenido`	texto	Texto original de la línea
`motivo`	texto	Por qué se descartó (código no reconocido, archivo vacío o corrupto, excluido, etc.)
---
Códigos de DataVolley
Siguen la convención estándar de DataVolley 4. Cada scouter puede redefinirlos, así que,
ante cualquier duda, conviene revisar la columna `codigo`.
Evaluación (`evaluacion_cod`)
	`#`	`+`	`!`	`-`	`/`	`=`
Saque	Ace	Positivo (rival ataca con dificultad)	OK, sin primer tiempo	Negativo (rival ataca cómodo)	Positivo (rival no puede atacar)	Error
Recepción	Perfecta	Positiva	OK, sin primer tiempo	Negativa (ataque limitado)	Mala / devuelta	Error
Armado	Perfecto	Positivo	OK	Negativo	Malo	Error
Ataque	Punto	Positivo	Bloqueado para reataque	Negativo (defendido fácil)	Bloqueado (punto rival)	Error
Bloqueo	Punto	Positivo	Pobre (rival reataca)	Negativo	Invasión	Error
Defensa / Free ball	Perfecta	Positiva	OK	Negativa	Pasa directo al otro campo	Error
Código verde (`&`)	Punto para el equipo					Error / punto para el rival
El código verde registra puntos o errores sin un fundamento técnico (rotación, invasión,
red, tarjeta, etc.).
Ejemplo de indicadores clásicos:
Eficiencia de ataque = (`#` − `=` − `/`) / total de ataques
Side-out = % de rallies ganados por el equipo que recibe (`fase_juego = "Side-out"`)
Recepción positiva = (`#` + `+`) / total de recepciones
Tipo (`tipo_cod`)
Código	Saque / recepción	Resto de fundamentos (tempo)
H	Flotado	Alta
M	Salto flotado	Media
Q	Salto potente	Rápida (primer tiempo)
T	Topspin	Tensa
U	—	Súper
F	—	Veloz
N	Híbrido	Deslizada (slide)
O	Otro	Otra
Zonas
Zonas de cancha vistas desde el propio campo (la red arriba):
```
   red
 4 | 3 | 2
 7 | 8 | 9
 5 | 6 | 1
```
Las zonas 1-6 son las de rotación; 7, 8 y 9 son la franja media de la cancha (entre la zona de
ataque y el fondo) que usa DataVolley: el ataque desde zona 8 es el pipe. `subzona_fin` (A-D) divide cada zona en cuadrantes.
---
Limitaciones conocidas
Cobertura: ninguna temporada argentina está completa, y en 2023-24 hay solo 59 partidos.
La Superliga 2022-23 tiene 129 de los 132 partidos de la fase regular.
Calidad del scout variable: en las temporadas más viejas (2007-2011) faltan roles de
jugadores y hay líneas en formatos antiguos que se descartaron.
Coordenadas: casi inexistentes, salvo en 2023-24 (≈ 40 % en la LVA). Para análisis
espacial, usar `zona_inicio`/`zona_fin`.
Marcadores: en algunos partidos el scout no reproduce exactamente el marcador oficial
(falta o sobra un punto). Están identificados en `validacion_marcador`.
Jugadores: la unificación es automática. Las filas con `revisar = True` pueden tener
errores (dos jugadores distintos unidos, o uno separado en dos uids).
Equipos: la normalización mantiene el nombre de la época cuando no es seguro que se trate
del mismo club. Por ejemplo, Farma Conde São José y São José Vôlei quedaron separados, aunque
son el mismo club.
---
Regenerar o agregar temporadas
```bash
pip install -r requirements.txt
python scripts/dvw_a_csv.py ZIP_O_CARPETA_CON_DVW datos/Nombre_De_La_Liga_AAAA-AAAA_masculina
```
El nombre de la carpeta de salida debe incluir la temporada (`AAAA-AAAA`), que se usa para
validar fechas. Si incluye "Superliga", la liga principal se etiqueta `Superliga`; si no,
`Liga (LVA)`.
Para equipos nuevos, agregar el alias en `EQUIPOS_STD` del script; para correcciones
puntuales, usar `OVERRIDES_EQUIPO` o `EXCLUIR`.
Después de cualquier cambio en el script, conviene volver a convertir una temporada ya
publicada y comparar los CSV, para confirmar que no cambió nada inesperado.
