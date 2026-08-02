# DevAtlas

Infraestructura abierta de conocimiento para developers — no una base de datos, un ecosistema de "atlas" temáticos que comparten un mismo formato de entrada reutilizable (markdown para humanos + metadatos estructurados para máquinas/IA).

## Por qué un paraguas

Cada "Atlas" (errores, vulnerabilidades, arquitectura, prompts...) resuelve un problema distinto, pero todos comparten la misma necesidad real: estructurar conocimiento disperso en foros/issues/blogs en entradas verificables, consultables por humanos, buscadores y agentes de IA por igual. En vez de reconstruir esa infraestructura una vez por tema, DevAtlas la define una sola vez y cada módulo la reutiliza.

## Estado actual (honesto)

- **[ErrorAtlas](https://github.com/Angelsistemas7/error-atlas)** — catálogo curado de errores de desarrollo, búsqueda, vínculo en vivo con issues/PRs reales de GitHub, servidor MCP y CLI. Ver su propio repo para detalle y roadmap.
- **[VulnAtlas](https://github.com/Angelsistemas7/vuln-atlas)** — catálogo curado de vulnerabilidades reales (8 entradas iniciales, extraídas de la GitHub Advisory Database oficial), con CVE/GHSA reales, causa raíz y remediación. Sin bot de sincronización ni MCP todavía.
- Todo lo demás en este documento es **visión y diseño**, no código construido. Se marca explícitamente qué existe y qué no para no vender algo que todavía no está — el mismo principio que siguen ambos módulos de nunca fabricar datos.

## El formato compartido (`AtlasEntry`)

La pieza reutilizable entre módulos: cada entrada de conocimiento (un error, una vulnerabilidad, un patrón de arquitectura) se representa como:

1. **Un archivo Markdown** — la ficha legible por humanos.
2. **Un bloque de metadatos estructurados** (JSON/YAML) — título, causas/hallazgos, soluciones/mitigaciones ordenadas, contexto (framework, versión, plataforma), tags, y enlaces a fuentes reales (issues, commits, docs oficiales) — nunca un campo de "confianza" o "frecuencia" numérico sin un cálculo real detrás.
3. **(Opcional) Casos de prueba reproducibles** — cuando el tema lo permite (ej. un error con un repro mínimo, una vulnerabilidad con un test de Semgrep/CodeQL).

Este formato es lo que permite que terceros construyan encima: un bot de Discord, una extensión de IDE, un servidor MCP, un dataset para evaluar modelos — todos leen la misma estructura.

Ver [`SPEC.md`](SPEC.md) para el esquema completo (en diseño, versionado desde v0).

## Módulos posibles (roadmap, no construidos)

Orden sugerido por qué tan reusable es la infraestructura ya construida en ErrorAtlas y qué tan claro es el valor:

| Módulo | Qué cubriría |
|---|---|
| **ErrorAtlas** ✅ | Errores de desarrollo — en producción |
| **VulnAtlas** ✅ | Vulnerabilidades y patrones inseguros de código — en producción (v0, 8 entradas). Reglas Semgrep/CodeQL reales cuando aplique, todavía sin construir |
| **PackageAtlas** | Incidentes conocidos de paquetes/dependencias (breaking changes, CVEs, deprecaciones) |
| **ArchAtlas** | Patrones y anti-patrones de arquitectura, con trade-offs documentados |
| **PromptAtlas** | Patrones de prompting que funcionan/fallan para tareas concretas, con el modelo y la fecha (esto cambia rápido y hay que fecharlo siempre) |

Módulos de infraestructura compartida, también sin construir:
- **Servidor MCP**: expone el contenido de todos los módulos a Claude/Cursor/otros clientes MCP vía consultas estructuradas.
- **CLI unificado**: mismo patrón que el CLI de ErrorAtlas, pero contra todos los módulos.
- **Dataset público exportable** (JSON/Parquet) por módulo, para quien quiera entrenar/evaluar modelos sobre el contenido.

## Principios (no negociables, heredados de ErrorAtlas)

1. **Nunca fabricar un número.** Si algo se muestra como porcentaje/score, tiene que venir de un cálculo real y reproducible (votos reales, un backtest, un conteo de fuentes), o no se muestra.
2. **Enlazar a la fuente real, no resumirla como si fuera un hecho verificado.** Un issue de GitHub se muestra con su título y estado reales, nunca con un resumen generado presentado como si fuera parte del issue.
3. **Un módulo nuevo empieza con contenido curado a mano**, no scrapeado en bruto — la automatización (bots, jobs de GitHub Actions) se conecta después de que el formato y la calidad ya están probados manualmente.

## Contribuir

Cada módulo vive en su propio repo: [error-atlas](https://github.com/Angelsistemas7/error-atlas), [vuln-atlas](https://github.com/Angelsistemas7/vuln-atlas). Este repo (`devatlas`) es donde vive la visión compartida y el esquema `AtlasEntry` — cambios al formato se proponen aquí, cambios de contenido van al repo del módulo correspondiente.

## Licencia

MIT
