---
description: Generate the project's architecture diagrams (Mermaid) from scratch - an overview, its building blocks, and detail flows on request
agent: build
---
Write the architecture diagrams of this repository into `docs/architecture.md`. Always write diagrams from scratch; never patch an existing one.

User arguments (may be empty): `$ARGUMENTS`
- empty: rewrite `docs/architecture.md` completely: overview, building blocks, and a freshly drawn version of each detail section the file already has (same topics; none if it has none).
- `<topic>`: add or replace the detail section about that topic. Leave the rest of the file alone.

## Reader

A developer who is new to the project and has not opened the code. Every diagram must make sense to them using only the diagram and the sentences around it. If a diagram needs the code to be understood, it is too detailed.

## 1. Understand the project

Read, in this order, and stop when you can answer the questions below: `README.md`, `AGENTS.md`, other docs, the build or packaging manifest (`pyproject.toml`, `package.json`, `*.csproj`, `Dockerfile`, pipeline YAML), the source tree layout, the public entry points. Read individual modules only to confirm what a building block does. If that would mean reading more than about 20 source files, use one `explore` subagent and ask it for answers to these questions, not a module list.

1. What is it (library, service, CLI, job, app), how is it delivered, and where does it run in production?
2. What goes in, what comes out, and who or what calls it in production?
3. Which 3 to 7 responsibilities make up the inside? Group modules by the job they do, never one block per module.

## 2. Diagrams, in this order

1. **Overview** (always). The project as one box between what goes in and what comes out, plus who calls it, where it runs and how it gets there. 3 to 6 nodes. No internals.
   - Data flows left to right: input → project → output → production caller.
   - Where it runs is a `subgraph` around the project node, titled with the runtime (e.g. "Databricks job cluster, Runtime 15.4 LTS"). Not a node.
   - Delivery is a dashed edge from where the package or image is published to the project node, labelled with the artefact (e.g. "wheel (pip install)"). The package is never an output of the code.
   - Only production use. Tests, local runners, check scripts, notebooks and other developer tooling in the repository never appear in the overview.
   - If the production caller is not built yet, draw it anyway with "(planned)" in its label and a dashed edge. This is the only exception to "draw what exists".
2. **Building blocks** (when the inside has more than one responsibility). The overview's input as the first node, then the 3 to 7 responsibilities from question 3, then the overview's output as the last node (input and output don't count in the 3 to 7). Follow it with a table: block | what it does | where in the code (folders or modules).
3. **Detail sections** (only when the user asks, via `$ARGUMENTS`). One flow or one concept, at most 9 nodes, opening with one sentence that places it in a building block.

Never draw more than this unless asked.

## 3. Rules for every diagram

- Node labels are plain words that describe the job ("Reads image files"), or the name of a thing a reader recognises (".session bundle (ZIP)", "Databricks"). No function names, no private or underscored names, no file paths. Code names belong in the prose and the table, never in a diagram.
- Edge labels say what flows ("ZIP members", "rows", "Arrow tables"), when it isn't obvious.
- Draw what exists (see the one exception above). Unsupported parts go in a sentence below the diagram, if at all.
- Before writing, check: can a node be removed or two merged? Does every edge carry information? Would a newcomer understand it in 30 seconds?
- Intro text says what the diagram shows, in 1 or 2 sentences. Don't list what it leaves out.

## 4. Syntax (Azure DevOps wiki and GitHub)

- Fenced ```` ```mermaid ```` blocks; `graph LR` or `graph TD`, never `flowchart`. For detail sections, `sequenceDiagram`, `stateDiagram-v2`, `erDiagram` or `classDiagram` when they fit better.
- Quote every node and edge label: `S[".session bundle (ZIP)"]`, `A -->|"rows"| B`, `A -.->|"pip install"| B`.
- No long arrows (`---->`), no edges to or from a `subgraph`, no HTML in labels (`<br>`, `<b>`...), no `style` or `classDef`.
- A `subgraph` is written `subgraph R["Runtime name"]` ... `end`. Edges may cross into it but must point at a node inside it, never at the subgraph itself.
- If mermaid-cli (`mmdc`) is available, validate the file: `mmdc -i docs/architecture.md -o /tmp/diagrams-check.md`. Otherwise say it was not validated.

## 5. Output

    # Architecture

    <One paragraph: what the project is and what it does.>

    ## Overview
    <1 or 2 sentences>
    <mermaid>

    ## Building blocks
    <1 or 2 sentences>
    <mermaid>
    <table: block | what it does | where in the code>

    ## <Detail topic>
    ...

If the README doesn't link to `docs/architecture.md` yet, add a Markdown link in one line, where the README already points to other docs. Do not commit. Report the diagrams written, and whether Mermaid was validated.
