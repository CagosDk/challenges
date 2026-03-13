# Challenges — Home Assistant Integration + Lovelace Card 🧹👧👦

[![HACS](https://img.shields.io/badge/HACS-Default-blue.svg)](https://hacs.xyz)
[![Downloads](https://img.shields.io/github/downloads/CagosDk/Udfordringer/total)](https://github.com/CagosDk/Udfordringer/releases)

A family-friendly chores system for Home Assistant — **integration + Lovelace card bundled in one package**.

- **The integration** is the local data & sync engine: it stores children, tasks, points, and an optional reward shop as Home Assistant entities and services.
- **The card** is the UI: an **Admin view** for parents and a **Kid view** for each child.

No cloud. No telemetry. Fast, local, and built for daily use.

If you find this project useful, you can support me on Ko-fi 💙  
[![Buy me some debugging time on Ko-fi](https://img.shields.io/badge/%F0%9F%90%9E_Buy_me_some_debugging_time_on-Ko--fi-2ea44f?style=for-the-badge)](https://ko-fi.com/qlerup)

---

## Screenshots 📸

<img width="2290" height="1125" alt="image" src="https://github.com/user-attachments/assets/74f144b6-eb41-4f65-9ccf-d5fba1914383" />
<img width="2287" height="245" alt="image" src="https://github.com/user-attachments/assets/c23c1b43-0470-4d76-9641-05e34fa2a879" />

---

## Highlights (Features)

- 🧑‍💻 **Three views in one card**: `admin` (parents), `kid` (child), and **overview** (recent tasks)
- 👧👦 **Children management**: add, rename, remove, set colors, view points & pending approvals
- 🏷️ **Categories**: create categories, assign to tasks, and control category sort order
- ✅ **Full task lifecycle**: assigned → in progress → awaiting approval → approved/rejected
- 🚦 **Extra task states**: unassigned, taken (first-claim), and overdue
- ⏱️ **Deadlines + early bonus**: optional due date, bonus points if done early
- 🔁 **Repeat & schedules**: weekly or monthly schedules + weekday repeat plan
- 🎯 **Auto-assign**: repeat tasks can auto-assign to one or more children
- ⚡ **Quick rules**: one‑tap completion, skip approval, and “fastest wins” mode
- ♻️ **Auto recycle**: automatically re-create approved repeat tasks
- 📅 **Persist unfinished tasks**: carry to next day and optionally mark overdue
- 🧮 **Points management**: add/reset points per child, quick add/remove
- 🏆 **Scoreboard**: optional ranking by points
- 🛒 **Reward shop**: items with price, icon/image, active toggle, and purchase history
- 🧰 **Shop automations**: action steps + delays on purchase (service calls, timers)
- 🖼️ **Media upload**: shop images uploaded to `/local/challenges/`
- 🎉 **Kid UX**: daily/weekly task sections, confetti, and completion sound
- 🎨 **Color theming**: customize button and points colors
- 🧪 **Developer mode**: debug controls like manual overdue marking
- 🌍 **Internationalization**: English, Danish, and more

## What you get ✨

### UI (Lovelace Card)
- **Two modes:** `admin` (parent) 🧑‍💻 and `kid` (child) 🧒
- **Children management:** add, rename, remove, view points & pending approvals
- **Task lifecycle:** Assigned → In progress → Awaiting approval → Approved
- **Repeat & auto-assign:** plan repeating chores on specific weekdays
- **Icon picker:** search & pick Material Design Icons
- **Scoreboard:** optional ranking of children by points
- **Reward shop:** items with images, prices, and optional HA actions on purchase
- **Multi-language i18n:** includes English, Danish, and more

### Backend (Integration / Sync Engine)
- Persists **children, tasks, points, shop items and purchase history** as HA entities
- Exposes services in the `challenges` domain (called by the card)
- **Daily rollover at 00:00** for repeated tasks
- Maintenance helper to remove leftovers from older versions

---

## Installation

### HACS (recommended)

[![Open this repository in HACS](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=CagosDk&repository=Udfordringer)

1. In HACS → **Integrations** → **⋯ → Custom repositories**
2. Add this repository URL as **Integration**:
   ```
   https://github.com/CagosDk/Udfordringer
   ```
3. Install **Challenges**
4. **Restart** Home Assistant
5. Go to **Settings → Devices & Services → Add Integration → Challenges** and press **Submit**

### Lovelace Resource (Card JS)

Depending on your HACS setup, the resource may be added automatically.

If you don’t see the card in the UI editor:
1. Go to **Settings → Dashboards → Resources**
2. Add the resource that HACS installed (typically under `/hacsfiles/...`), e.g. something like:
   - `/hacsfiles/Udfordringer/challenges-card.js`
3. Set type to **JavaScript Module**
4. Reload the dashboard

> Tip: In HACS, open the installed entry and check the “Instructions” panel — it shows the exact resource path.

---

## Quick start (Card configs) ⚙️

### Admin view (parents)

```yaml
type: custom:challenges-card
mode: admin
show_scoreboard: true
```

### Kid view (for a specific child)

```yaml
type: custom:challenges-card
mode: kid
child: "Emma"
```

> If the child list is empty in the editor, you can type the name manually.

---

## How it works 🧠

The integration creates entities that the card reads from `hass.states`, and services that the card calls to update data.

### Entities created 🧱

#### 1) One sensor per child — **Points**
- **Name:** `Challenges Points {Child Name}`
- **State:** current points (integer)
- **Attributes:** `child_id`, `child_name`, `slug`, task counts, plus a minimal list of tasks for that child

#### 2) All tasks (collection)
- **Entity:** `sensor.challenges_tasks_all`
- **State:** number of tasks
- **Attributes:** `tasks` (full list)

#### 3) Shop (optional)
- **Entity:** `sensor.challenges_shop`
- **State:** number of active items
- **Attributes:** `items` and `purchases`

---

## Task lifecycle 🔄

1. **Assigned** 📌 → Visible and linked to a child
2. **In progress** 🔧 → Kid pressed **Start**
3. **Awaiting approval** 📨 → Kid pressed **Complete task**
4. **Approved** 🥳 → Parent approves, points are awarded

Other states you may see: **Unassigned** and **Rejected**.

---

## Repeat & Auto-assign 🔁📅

- Choose weekdays (Mon–Sun) for a task to repeat
- Optionally pick a default child (`repeat_child_id`) to auto-assign on those days
- Save the plan — the card calls `challenges.set_task_repeat`

If **Auto-assign** is active, manual assignment can be disabled for that task (the UI will indicate this).

---

## Reward Shop 🛒🎁

Create rewards kids can buy with points:
- Title, price, optional icon & image
- Toggle **Active** to show/hide in kid view
- Purchase history is stored in HA

### Advanced shop actions ⚙️⏱️

Rewards can trigger Home Assistant actions when purchased:
- **Service steps** (e.g. turn on a switch)
- **Delay steps** (seconds/minutes/hours)
- Run steps in order

Example: “Xbox time 30 min”
1) `switch.turn_on`
2) delay 1800s
3) `switch.turn_off`

---

## Services (domain: `challenges`) 🛠️

### Children admin
- `challenges.add_child`
- `challenges.rename_child`
- `challenges.remove_child`
- `challenges.add_points`
- `challenges.reset_points`

### Tasks
- `challenges.add_task`
- `challenges.assign_task`
- `challenges.set_task_status`
- `challenges.approve_task`
- `challenges.delete_task`
- `challenges.set_task_repeat`
- `challenges.set_task_icon`

### Shop
- `challenges.add_shop_item`
- `challenges.update_shop_item`
- `challenges.delete_shop_item`
- `challenges.buy_shop_item`
- `challenges.upload_shop_image` (saves to `/config/www/challenges/` for `/local/challenges/<file>`)

### Maintenance
- `challenges.purge_orphans` — remove leftovers from older versions

---

## Daily rollover (00:00) 🌙

Every night at **00:00**, the integration:
1. Removes old assigned tasks from previous days (templates can remain)
2. Creates today’s tasks for any chores that match `repeat_days`
   - If `repeat_child_id` is set → assign to that child
   - Otherwise → use the task’s current assignment as the target

---

## Internationalization 🌍

The card includes multiple languages (including English and Danish) and localizes based on your Home Assistant language settings.

---

## Troubleshooting 🧰

- **Card shows no data**
  - Make sure the integration is installed, configured, and HA was restarted
- **No children listed**
  - Create children in Admin view (or call `challenges.add_child`)
- **No tasks**
  - Create one in Admin view (or call `challenges.add_task`)
- **Shop empty for kids**
  - Ensure items are **Active**
- **Shop images not showing**
  - Use `challenges.upload_shop_image`, then reference `/local/challenges/<filename>`
- **Leftover sensors/devices after upgrade**
  - Run `challenges.purge_orphans`

---

## Upgrading from the old split repos (card + sync) ♻️

If you previously installed **two separate HACS repos**:
1. Remove the old entries from HACS (card + sync)
2. Install this bundled repo
3. Verify your Lovelace **Resources** point at the new JS file (remove old resource entries if needed)
4. Restart Home Assistant

---

## License

MIT — see `LICENSE`.
