# AtlasEntry — spec v0

Formato mínimo compartido entre módulos de DevAtlas. `v0` es literalmente el esquema que ya usa [ErrorAtlas](https://github.com/Angelsistemas7/error-atlas/blob/main/src/lib/types.ts) en producción, generalizado. Cambia solo cuando un módulo nuevo necesite un campo que los demás también puedan reusar — no se diseña de antemano para casos hipotéticos.

## Campos

```ts
type AtlasEntry = {
  slug: string;          // identificador único, kebab-case
  title: string;         // el mensaje/nombre exacto, tal como se busca
  summary: string;       // una frase
  description: string;   // explicación completa, para humanos

  causes: string[];        // causas conocidas u orígenes, en el orden en que
                            // se investigaron/confirmaron — nunca con un
                            // porcentaje inventado al lado
  solutions: {
    title: string;
    detail: string;
  }[];                      // ordenadas por probabilidad/relevancia real,
                             // no por orden de llegada

  affected: string[];    // frameworks/versiones/plataformas donde aplica
  tags: string[];         // para búsqueda y agrupación

  // Opcional, agregado cuando el módulo lo soporta:
  sources?: {
    type: "github_issue" | "github_pr" | "commit" | "doc" | "other";
    url: string;
    label: string;       // el título real de la fuente, no un resumen
  }[];
};
```

## Reglas de contenido

- `causes` y `solutions` se ordenan por lo que la evidencia real sugiere (más común primero, o más efectivo primero) — si no hay evidencia suficiente para ordenar, se listan en el orden en que se documentaron y se dice explícitamente que no están rankeadas.
- `sources` nunca lleva un campo de resumen generado — solo el título y estado reales de la fuente (esto es lo que ya hace `GithubIssuesSection.tsx` en ErrorAtlas, vía la API pública de búsqueda de GitHub).
- Ningún campo numérico de "confianza" o "frecuencia" existe en v0 — se agregará en una versión futura únicamente cuando haya una fuente real de esa señal (ej. votos de usuarios, resultado de un backtest), nunca antes.

## Implementaciones actuales

- **ErrorAtlas**: [`src/lib/types.ts`](https://github.com/Angelsistemas7/error-atlas/blob/main/src/lib/types.ts) implementa este esquema (sin el campo opcional `sources` todavía — los issues relacionados se buscan en vivo en vez de guardarse).
- **VulnAtlas**: [`src/lib/types.ts`](https://github.com/Angelsistemas7/vuln-atlas/blob/master/src/lib/types.ts) implementa `AtlasEntry` con `sources` desde el día uno, y agrega campos propios de vulnerabilidades: `cveId`, `ghsaId`, `cwes`, `severity`, `cvss` — todos tomados literalmente de la fuente (GHSA/NVD), nunca calculados. Es la primera prueba real de que el formato compartido generaliza a un segundo dominio sin romperse.
- **PackageAtlas**: [`src/lib/types.ts`](https://github.com/Angelsistemas7/package-atlas/blob/master/src/lib/types.ts) agrega `packageName`, `ecosystem`, `status`, `statusMessage` (texto literal de la fuente, nunca resumido) y `replacement`. Confirma el mismo patrón de extensión que VulnAtlas en un tercer dominio distinto.

## Extensión por módulo

Un módulo puede agregar campos propios sobre `AtlasEntry` (como hace VulnAtlas) sin que eso obligue a cambiar la v0 del esquema compartido — solo se propone un campo nuevo aquí cuando **dos o más módulos** lo necesitarían igual. Ejemplo: `severity`/`cvss` se quedan en VulnAtlas porque no aplican a ErrorAtlas; si un tercer módulo también necesitara un campo de severidad con la misma semántica, ahí se evalúa subirlo a `AtlasEntry`.

## Historial

- **v0** (2026-08-01): primera versión, extraída del tipo `ErrorEntry` ya en producción en ErrorAtlas.
- **v0, validada por segundo módulo** (2026-08-01): VulnAtlas implementa el mismo esquema base y confirma que `sources` (antes opcional/sin uso real) funciona bien como campo requerido desde el arranque de un módulo nuevo.
