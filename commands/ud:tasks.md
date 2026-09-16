---
description: Show all open tasks across every Taskmaster tag in the current project — no LLM calls, reads tasks.json directly
---

Run the following bash command to display all open tasks across every tag in this project's Taskmaster database. Do not summarize, explain, or add commentary — just execute the command and display the output as-is.

!`python3 -c "
import json, os, sys

tasks_file = os.path.join(os.getcwd(), '.taskmaster', 'tasks', 'tasks.json')
if not os.path.exists(tasks_file):
    print('No .taskmaster/tasks/tasks.json found in current directory.')
    sys.exit(1)

with open(tasks_file) as f:
    data = json.load(f)

OPEN = {'pending', 'in-progress', 'blocked'}
STATUS_ICON = {'pending': '🟡', 'in-progress': '🔵', 'blocked': '🔴'}
PRIORITY_ICON = {'high': '⬆', 'medium': '➡', 'low': '⬇'}

found_any = False
total = 0

for tag, section in data.items():
    tasks = section.get('tasks', [])
    open_tasks = []

    for t in tasks:
        if t.get('status') in OPEN:
            open_tasks.append({
                'id': t['id'],
                'title': t['title'],
                'status': t.get('status', ''),
                'priority': t.get('priority', ''),
                'deps': ', '.join(str(d) for d in t.get('dependencies', [])) or '—',
            })
        for st in t.get('subtasks', []):
            if st.get('status') in OPEN:
                open_tasks.append({
                    'id': f\"{t['id']}.{st['id']}\",
                    'title': f\"↳ {st['title']}\",
                    'status': st.get('status', ''),
                    'priority': st.get('priority', t.get('priority', '')),
                    'deps': '—',
                })

    if not open_tasks:
        continue

    found_any = True
    total += len(open_tasks)
    print(f'\n### [{tag}] — {len(open_tasks)} open task(s)\n')
    print(f'| ID | Title | Status | Priority | Deps |')
    print(f'|---|---|---|---|---|')
    for t in open_tasks:
        s = t['status']
        p = t['priority']
        icon_s = STATUS_ICON.get(s, '') + ' ' + s
        icon_p = PRIORITY_ICON.get(p, '') + ' ' + p if p else '—'
        print(f\"| {t['id']} | {t['title']} | {icon_s} | {icon_p} | {t['deps']} |\")

if not found_any:
    print('✅ No open tasks — all done or deferred.')
else:
    print(f'\n**Total: {total} open tasks**')
"`
