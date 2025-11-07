# Documentation Creation Criteria

## File Count Calculation

**Counts**: New files + Modified files (exclude: read-only, tests, auto-generated)

**Estimation**:
1. List affected components/features
2. Count new + modified implementation files
3. Apply matrix: 1-2 files (none), 3-5 files (Design Doc + Work Plan), 6+ files (ADR + Design Doc + Work Plan)

**Edge cases**: Extensive refactor in 1 file = 1 file; Component + co-located style = 1 file; 3 modified + 2 new = 5 total

## Creation Decision Matrix

| Condition | Required Documents | Creation Order |
|-----------|-------------------|----------------|
| New Feature Addition | PRD → [UXRD] → Design Doc → [ADR] → Work Plan | After PRD approval |
| ADR Conditions Met (see below) | ADR → Design Doc → Work Plan | Start immediately |
| 6+ Files | ADR → Design Doc → Work Plan (Required) | Start immediately |
| 3-5 Files | Design Doc → Work Plan (Recommended) | Start immediately |
| 1-2 Files | None | Direct implementation |

## ADR Creation Conditions (Required if Any Apply)

### 1. Type System Changes
- **Adding nested types with 3+ levels**: `type A = { b: { c: { d: T } } }`
  - Rationale: Deep nesting has high complexity and wide impact scope
- **Changing/deleting types used in 3+ locations**
  - Rationale: Multiple location impacts require careful consideration
- **Type responsibility changes** (e.g., DTO→Entity)
  - Rationale: Conceptual model changes affect design philosophy

**Type System Complexity**:

| Metric | Low | Medium | High (ADR required) |
|--------|-----|--------|---------------------|
| Nesting depth | 1-2 | 3 | 4+ |
| Generic parameters | 0-1 | 2 | 3+ |
| Union members | 1-3 | 4-6 | 7+ |
| Conditional types | 0 | 1 | 2+ |
| Usage locations | 1-2 | 3-5 | 6+ |

ADR required: 3+ metrics in "High" range

**Type change documentation** (3+ locations): Impact analysis, migration strategy, breaking changes, rollback plan

### 2. Data Flow Changes
- **Storage location changes** (DB→File, Memory→Cache)
- **Processing order changes with 3+ steps**
  - Example: "Input→Validation→Save" to "Input→Save→Async Validation"
- **Data passing method changes** (props→Context, direct reference→events)

### 3. Architecture Changes
- Layer addition, responsibility changes, component relocation

**Architecture Changes** (concrete examples requiring ADR):

| Change Type | Example | ADR Required? |
|-------------|---------|---------------|
| **Layer addition** | Add caching layer between API and UI | ✅ YES |
| **Responsibility change** | Move validation from UI to API | ✅ YES |
| **Component relocation** | Move shared utilities to library package | ✅ YES |
| **Pattern change** | Props drilling → Zustand store | ✅ YES |
| **Data flow change** | REST → GraphQL | ✅ YES |
| **State management change** | Context → Zustand | ✅ YES |

**Non-architecture changes** (no ADR):
- ❌ Rename component (no responsibility change)
- ❌ Extract utility function within same file (no relocation)
- ❌ Add new component to existing layer (no layer change)

### 4. External Dependency Changes
- Library/framework/external API introduction or replacement

### 5. Complex Implementation Logic (Regardless of Scale)
- Managing 3+ states
- Coordinating 5+ asynchronous processes

**"Managing States"** (3+ states threshold):

Count distinct application conditions (isLoading/isSuccess/isError = 3). Don't count entity properties (name/email = 1 compound state).

Examples:
- States: isLoading/isSuccess/isError = 3 ✅ | name/email/age = 1 compound ❌
- Async: 6 processes (fetchUser + fetchSettings + fetchActivity + ws + timer + tracking) → ADR required

ADR required: 3+ distinct states + transitions with business rules

**"Coordinating Async"** (5+ processes threshold):

Count: Independent API calls, WebSocket connections, timers, file streams

Example requiring ADR: fetchUser + fetchSettings + fetchActivity + WebSocket + autoSave timer + tracking = 6 processes

## Detailed Document Definitions

### PRD (Product Requirements Document)

**Purpose**: Define business requirements and user value

**Includes**:
- Business requirements and user value
- Success metrics and KPIs (measurable format)
- User stories and use cases
- MoSCoW prioritization (Must/Should/Could/Won't)
- MVP and Future phase separation
- User journey diagram (required)
- Scope boundary diagram (required)

**Excludes**:
- Technical implementation details (→Design Doc)
- Technical selection rationale (→ADR)
- **Implementation phases** (→Work Plan)
- **Task breakdown** (→Work Plan)

### UXRD (UX Requirement Documentation)

**Purpose**: Define UX-specific requirements that translate business goals (PRD) into user experience specifications before technical design.

**Includes**:
- User flows and interaction patterns (Mermaid diagrams)
- Wireframe/layout guidance (ASCII design mockups allowed)
- Component behavior specifications (states: default/hover/active/focus/disabled/loading/error)
- Accessibility requirements (WCAG level, ARIA roles/labels, keyboard navigation)
- Responsive behavior across breakpoints
- Content and microcopy requirements for all UI states
- Design system integration notes (e.g., shadcn/ui components, tokens)

**When Required**:
- Any change that impacts UI/UX (new screens/components, major flow changes, accessibility/compliance changes)

**UX Impact Levels**:

| Level | Definition | UXRD Required? |
|-------|------------|----------------|
| Major | User journey/navigation changes | ✅ YES |
| Medium | Component behavior/layout changes | ✅ YES |
| Minor | Visual-only changes | ❌ NO |

**Indicators** (any met → Major): Navigation structure, journey steps, state transitions, new interactions, accessibility changes

Decision: Major OR Medium → UXRD required

**Workflow Position**:
- PRD → UXRD (if UX-impacting) → Design Doc → ADR → Work Plan

**Gating Rules**:
- Design Doc cannot be accepted if a required UXRD does not exist and is not linked
- ADRs that record decisions affecting UX must reference the corresponding UXRD

**Excludes**:
- Low-level technical implementation details (→Design Doc)
- Technology selection and trade-offs (→ADR)
- Schedule/tasks (→Work Plan)

### ADR (Architecture Decision Record)

**Purpose**: Record technical decision rationale and background

**Includes**:
- Decision (what was selected)
- Rationale (why that selection was made)
- Option comparison (minimum 3 options) and trade-offs
- Architecture impact
- Principled implementation guidelines (e.g., "Use dependency injection")

**Excludes**:
- Implementation schedule, duration (→Work Plan)
- Detailed implementation procedures (→Design Doc)
- Specific code examples (→Design Doc)
- Resource assignments (→Work Plan)

### Design Document

**Purpose**: Define technical implementation methods in detail

**Includes**:
- **Existing codebase analysis** (required)
  - Implementation path mapping (both existing and new)
  - Integration point clarification (connection points with existing code even for new implementations)
- Technical implementation approach (vertical/horizontal/hybrid)
- **Technical dependencies and implementation constraints** (required implementation order)
- Interface and type definitions
- Data flow and component design
- **E2E verification procedures at integration points**
- **Acceptance criteria (measurable format)**
- Change impact map (clearly specify direct impact/indirect impact/no ripple effect)
- Complete enumeration of integration points
- Data contract clarification
- **Agreement checklist** (agreements with stakeholders)
- **Prerequisite ADRs** (including common ADRs)

**Required Structural Elements**:
```yaml
Change Impact Map:
  Change Target: [Component/Feature]
  Direct Impact: [Files/Functions]
  Indirect Impact: [Data format/Processing time]
  No Ripple Effect: [Unaffected features]

Interface Change Matrix:
  Existing: [Method name]
  New: [Method name]
  Conversion Required: [Yes/No]
  Compatibility Method: [Approach]
```

**Excludes**:
- Why that technology was chosen (→Reference ADR)
- When to implement, duration (→Work Plan)
- Who will implement (→Work Plan)

### Work Plan

**Purpose**: Implementation task management and progress tracking

**Includes**:
- Task breakdown and dependencies (maximum 2 levels)
- Schedule and duration estimates
- **Copy E2E verification procedures from Design Doc** (cannot delete, can add)
- **Phase 4 Quality Assurance Phase (required)**
- Progress records (checkbox format)

**Excludes**:
- Technical rationale (→ADR)
- Design details (→Design Doc)

**Phase Division Criteria**:
1. **Phase 1: Foundation Implementation** - Type definitions, interfaces, test preparation
2. **Phase 2: Core Feature Implementation** - Business logic, unit tests
3. **Phase 3: Integration Implementation** - External connections, presentation layer
4. **Phase 4: Quality Assurance (Required)** - Acceptance criteria achievement, all tests passing, quality checks

**Three Elements of Task Completion Definition**:
1. **Implementation Complete**: Code is functional
2. **Quality Complete**: Tests, type checks, linting pass
3. **Integration Complete**: Verified connection with other components

## Creation Process

1. **Problem Analysis**: Change scale assessment, ADR condition check
2. **ADR Option Consideration** (ADR only): Compare 3+ options, specify trade-offs
3. **Creation**: Use templates, include measurable conditions
4. **Approval**: "Accepted" after review enables implementation

## Storage Locations

| Document | Path | Naming Convention | Template |
|----------|------|------------------|----------|
| PRD | `docs/prd/` | `[feature-name]-prd.md` | `template.md` |
| UXRD | `docs/uxrd/` | `[feature-name]-uxrd.md` | `template.md` |
| ADR | `docs/adr/` | `ADR-[4-digits]-[title].md` | `template.md` |
| Design Doc | `docs/design/` | `[feature-name]-design.md` | `template.md` |
| Work Plan | `docs/plans/` | `YYYYMMDD-{type}-{description}.md` | `template.md` |

*Note: Work plans are excluded by `.gitignore`

## ADR Status
`Proposed` → `Accepted` → `Deprecated`/`Superseded`/`Rejected`

## AI Automation Rules
- 5+ files: Suggest ADR creation
- Type/data flow change detected: ADR mandatory
- Check existing ADRs before implementation

## Diagram Requirements

Required diagrams for each document (using mermaid notation):

| Document | Required Diagrams | Purpose |
|----------|------------------|---------|
| PRD | User journey diagram, Scope boundary diagram | Clarify user experience and scope |
| UXRD | User flow diagram, Wireframe/layout diagram | Clarify interaction and layout requirements |
| ADR | Option comparison diagram (when needed) | Visualize trade-offs |
| Design Doc | Architecture diagram, Data flow diagram | Understand technical structure |
| Work Plan | Phase structure diagram, Task dependency diagram | Clarify implementation order |

## UXRD Cross-Linking and Compliance

- PRD should indicate whether UX impact exists and link to the created UXRD when applicable
- Design Docs must link the relevant UXRD for UX-impacting changes (review gate)
- ADRs that document UX-affecting decisions must reference the UXRD that specified the UX requirements

## Common ADR Relationships
1. **At creation**: Identify common technical areas (logging, error handling, async processing, etc.), reference existing common ADRs
2. **When missing**: Consider creating necessary common ADRs
3. **Design Doc**: Specify common ADRs in "Prerequisite ADRs" section
4. **Compliance check**: Verify design aligns with common ADR decisions

## Pre-Implementation Scope Estimation (5-11 min)

**Process**:
1. **List changes** (2-5 min): Feature → Components/files affected
2. **Count files** (1-2 min): New + modified implementation files
3. **Check ADR conditions** (2-3 min): Type (3+ locations?), Architecture, Async (5+?), States (3+?)
4. **Apply matrix** (1 min): Files + conditions → Required docs

**Example**: "Add profile editing" → 5 files (page, validation, API, upload, header) + Avatar type in 4+ locations → ADR + Design Doc + Work Plan