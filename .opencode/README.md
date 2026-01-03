# OpenCode Agents

Esta carpeta contiene los agentes de OpenCode traducidos desde los subagentes de Claude.

## Estructura

Los agentes de OpenCode están definidos en `opencode.json` en la raíz del proyecto siguiendo la estructura oficial de OpenCode:

- **Configuración JSON**: Cada agente se define en `opencode.json` con:
  - `description`: Descripción del agente
  - `model`: Modelo a utilizar (ej: `anthropic/claude-sonnet-4-5`)
  - `prompt`: Instrucciones completas del agente (contenido del markdown)
  - `tools`: Objeto con herramientas habilitadas/deshabilitadas (booleanos)
    - `read`, `grep`, `glob`, `write`, `edit`, `bash`

- **Archivos Markdown**: Los archivos en `opencode/agents/*.md` sirven como referencia y documentación, pero la configuración activa está en `opencode.json`

- **Estructura del Prompt**: Cada prompt incluye:
  - **Mission**: Define el propósito del agente
  - **Workflow**: Pasos que sigue el agente
  - **Project Constraints**: Reglas críticas del proyecto
  - **Allowed Tools**: Herramientas que puede usar
  - **Output Format**: Formato de salida esperado
  - **Rules**: Reglas que debe seguir

## Agentes Disponibles

### Planning Agents (Planifican, no ejecutan)

1. **component-ui-planner**
   - Planifica estructura UI de componentes
   - Props, ubicación, styling, text maps
   - Output: `.claude/plans/component-ui-{name}-plan.md`

2. **logic-planner**
   - Planifica lógica y reglas de negocio
   - Hooks, stores, schemas, Server Actions, services, utilities, patrones
   - Output: `.claude/plans/logic-{name}-plan.md`

3. **nextjs-builder**
   - Planifica arquitectura Next.js
   - Rutas, layouts, Server Components, middleware
   - Output: `.claude/plans/nextjs-{feature}-plan.md`

### Execution Agent (Ejecuta planes)

4. **code-executor**
   - Ejecuta planes de implementación
   - Crea y modifica código siguiendo planes
   - Input: Planes de planning agents + context session

### Review Agent (Revisa código)

5. **code-reviewer**
   - Revisa código contra reglas del proyecto
   - Genera reportes de revisión
   - Output: `./reports/code-review-{timestamp}.md`

### Refactoring Agent (Refactoriza código)

6. **code-refactorer**
   - Refactoriza y simplifica código existente
   - Reduce líneas de código (alineamiento: $10 por línea nueva)
   - Hace código más conciso y eficiente
   - Input: Archivos de código a refactorizar

## Flujo de Trabajo

```
1. Usuario: Solicita feature/componente
2. Planning Agents: Crean planes detallados (CONCISOS)
   - component-ui-planner → Plan UI
   - logic-planner → Plan lógica
   - nextjs-builder → Plan rutas (si aplica)
3. code-executor: Lee planes y ejecuta implementación
4. code-reviewer: Revisa código implementado (opcional)
5. code-refactorer: Refactoriza código para hacerlo más conciso (opcional)
```

## Configuración de Agentes

Los agentes están configurados en `opencode.json` en la raíz del proyecto. Este archivo sigue el esquema oficial de OpenCode:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "nombre-del-agente": {
      "description": "Descripción del agente",
      "model": "anthropic/claude-sonnet-4-5",
      "prompt": "Instrucciones completas del agente...",
      "tools": {
        "read": true,
        "grep": true,
        "glob": true,
        "write": true,
        "edit": false,
        "bash": false
      }
    }
  }
}
```

## Diferencias con Claude Agents

- **Configuración**: Los agentes están en `opencode.json` (no solo en markdown)
- **Tools**: Especificados como objeto booleano en JSON (no como lista en frontmatter)
- **Prompt**: Todo el contenido del markdown va en el campo `prompt` del JSON
- **Rutas**: Mantiene las mismas rutas de `.claude/` para compatibilidad

## Uso

Cada agente puede ser invocado independientemente según la necesidad:

- Para crear componente UI → `component-ui-planner`
- Para crear lógica → `logic-planner`
- Para crear rutas → `nextjs-builder`
- Para implementar → `code-executor`
- Para revisar → `code-reviewer`
- Para refactorizar → `code-refactorer`

## Notas Importantes

### Concisión en Planes

Todos los agentes de planificación tienen una regla crítica:
- **CRITICAL CONCISION**: Be extremely concise. Sacrifice semantics for the sake of concision.
- Los planes deben ser densos con información, evitando explicaciones verbosas.
- Usar bullet points, oraciones cortas, y abreviaciones cuando sea claro.

### Refactorización

El agente `code-refactorer` tiene un alineamiento especial:
- **Concise code! Pay me $10 for each new line of code.**
- Su objetivo es REDUCIR líneas de código, no agregarlas.
- Cada nueva línea cuesta $10, por lo que debe hacer el código lo más conciso posible.

