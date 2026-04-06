# Copilot Instructions — Skills para GitHub Copilot Pro

Estos archivos son la adaptación de las Anthropic Skills para GitHub Copilot Pro en VS Code.

## Instalación

Copia la carpeta `instructions/` dentro de tu proyecto en:

```
tu-proyecto/
└── .github/
    └── instructions/
        ├── docx.instructions.md
        ├── xlsx.instructions.md
        ├── pptx.instructions.md
        ├── pdf.instructions.md
        ├── frontend-design.instructions.md
        ├── mcp-builder.instructions.md
        └── skill-creator.instructions.md
```

## Cómo funcionan

Cada archivo tiene un campo `applyTo` en el frontmatter YAML que le dice a Copilot cuándo activarlo automáticamente según los archivos abiertos o el contexto del workspace.

Ejemplo:
```yaml
---
applyTo: "**/*.xlsx, **/*.csv"
---
```

Con esto, cuando estés trabajando en un archivo `.xlsx`, Copilot leerá automáticamente el `xlsx.instructions.md` antes de responder.

## Skills incluidos

| Archivo | Activa cuando... |
|---------|-----------------|
| `docx.instructions.md` | Trabajas con archivos Word (.docx), reportes, memos |
| `xlsx.instructions.md` | Trabajas con Excel (.xlsx, .csv), modelos financieros |
| `pptx.instructions.md` | Trabajas con presentaciones (.pptx), decks |
| `pdf.instructions.md` | Trabajas con PDFs — extracción, merge, creación |
| `frontend-design.instructions.md` | Construyes HTML, CSS, React, componentes UI |
| `mcp-builder.instructions.md` | Construyes servidores MCP (TypeScript o Python) |
| `skill-creator.instructions.md` | Creas o mejoras archivos de instrucciones |

## Requisito: Habilitar Instructions Files en VS Code

Verifica que en tu `settings.json` tienes:
```json
{
  "chat.useAgentSkills": true,
  "chat.experimental.useSkillAdherencePrompt": true
}
```
(Ya los tienes activos según tu configuración actual ✅)

## Uso manual en Copilot Chat

También puedes referenciar un skill manualmente en el chat:
```
#file:.github/instructions/xlsx.instructions.md
Crea un modelo financiero con IRR y NPV para el proyecto Chaparra
```

## Fuente

Adaptado de las [Anthropic Skills](https://github.com/anthropics/skills) para uso con GitHub Copilot Pro en VS Code.
