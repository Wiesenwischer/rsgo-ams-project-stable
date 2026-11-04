# Release Notes 4.0 (English)

> Major release **4.0.0** · changes since version **3.0.2**

4.0 is a **major release**. At its core is a **completely rebuilt scheduling engine** modeled on Microsoft Project, together with a thoroughly modernized interface, new web modules (reporting and capacity planning, collaboration, schedule import), rebuilt **resource and actual-hours handling**, a new **administration portal** and a new installation/rollout mechanism via **ReadyStackGo**.

> 📝 **For administrators:** Please read the section "Upgrading to 4.0 — notes for administrators" at the end of this page before updating. It lists the mandatory steps after the update and the requirements on the ams.erp version.

---

## ⭐ Highlights at a glance

| | Highlight |
|---|---|
| 1 | **New scheduling** with real predecessor/successor calculation, MS-Project-style constraints and Gantt dragging that works like MS Project |
| 2 | **Project calendars with working hours** (working days, time windows, holidays, ERP sync) |
| 3 | **Modernized interface** with consistent language (DE/EN) |
| 4 | **ERP integration** right in the Gantt (references, bills of materials, sync status) |
| 5 | **New web reports** with integrated capacity planning, colour control, user groups and PDF export |
| 6 | **Resources and actual hours**: several workstations per task, capacity replacement by project structure, actual-hours processing in the administration portal |
| 7 | **Insert sales offers and orders** into a schedule as references |
| 8 | **Schedule import from the ERP** as part of ams.project |
| 9 | **New installation mechanism via ReadyStackGo** (guided, centralized rollout) |
| 10 | **New administration portal** (web) for configuration, user permissions, maintenance mode, error queue, actual hours and client rollout |

---

## New scheduling (MS-Project-aligned)

The schedule calculation was rebuilt from the ground up (Critical Path Method, forward/backward pass):

- **Predecessor/successor links** in all four types: **Finish–Start (FS)**, **Start–Start (SS)**, **Finish–Finish (FF)**, **Start–Finish (SF)**, each with **lag/lead**. Move one task and dependent tasks follow automatically and correctly.
- **MS-Project-style constraints**: As Soon As Possible (ASAP), As Late As Possible (ALAP), Start/Finish No Earlier Than (SNET/FNET), Start/Finish No Later Than (SNLT/FNLT), Must Start/Finish On (MSO/MFO).
- **Conflicts are surfaced honestly** instead of silently "smoothed": when a date cannot be met, MS-Project-style **negative slack** with a warning is shown rather than quietly bending the inputs. If a hard constraint only ends after the project finish, the conflict list reports this as its own message ("schedule cannot be met – project finish exceeded") with both dates.
- **New warnings and messages column (indicators)** in the Gantt: a dedicated column shows every note per task at a glance – scheduling warnings (unmet constraints, negative slack, predecessor/successor conflicts, dates snapped to working days) as well as ERP sync notices (date drift or overrun). Each icon has an explanatory tooltip.
- **Summary tasks** roll up their subtasks: start, finish and duration are calculated automatically, and the bar spans from the start to the finish of the subtasks. Only "Must Start On"/"Must Finish On" fixes the respective edge; a manually scheduled summary task keeps both edges of its own bar. Subtasks outside that range are reported as a conflict. A date set by a summary task acts as an upper bound for its subtasks: they stay at their earliest dates, and a missed date shows up as negative slack.
- **Automatic or manual** scheduling per task: manually pinned tasks keep their dates but still participate in conflict detection. Their dates snap to the calendar's start and end of work. Manually scheduled subtasks stay untouched under a summary task – whether the summary is scheduled automatically or manually.
- **Milestones** with correct time-of-day handling: they consistently sit at the end of the day and behave cleanly on non-working days; a new milestone is placed on the end of the former span. Switching an ERP task to "milestone" does not move its ERP due date. Tasks with subtasks cannot be marked as milestones.
- **Gantt dragging like MS Project**: an automatically scheduled task stays automatic when dragged. It gets "Start No Earlier Than" the drop date, keeps its duration and still follows its predecessors; dragging a summary task moves its subtasks along. Holding **Alt** on release pins the task where you drop it and makes it manually scheduled. Manual tasks, milestones, "As Late As Possible" and tasks with another dated constraint are always pinned; their constraint stays stored. The same applies in the multi-project view (Gantt and timeline).
- **Resizing a task's duration by dragging** honours the calendar (weekends/holidays); the duration shown in the tooltip matches the stored one.
- **Large schedules** recalculate quickly – about 0.2 seconds per change at 5,000 tasks, even for very deeply nested plans.

> 📝 **Note**: Because 4.0 introduces a new scheduling engine, existing projects are migrated to the new engine on upgrade. After the update, the **revision for all projects** has to be run once in the administration portal (see the section "Upgrading to 4.0 — notes for administrators").

## Project calendars with working hours

- Real **working days and working-time windows** (e.g. Mon–Fri 08:00–17:00, multiple windows per day for lunch breaks) instead of plain calendar days.
- **Exceptions/holidays** on a dedicated sub-page per calendar; treated as non-working during scheduling.
- **ERP-synchronized calendars** are read-only in the UI (the ERP is the source) and marked "ERP-synchronized" in both the card and the table view.
- **Default/required calendar**: every project gets a calendar; without an explicit assignment a default calendar applies.
- Per-project assignment under **Settings → Calendar**.
- Detailed step-by-step guide: see the calendar guide.

## Project list & archiving

- **New schedule selection page**: a central landing page for picking and opening schedules – either as a **card view** or a **sortable table view** (toggleable, the choice is persisted). After signing in, this project list is shown directly.
- **Search and filters** by project name, manager, clerk, business unit and revision, plus **status tabs** (All / Active / Archived) with counts. The date columns ("Created", "Archived on") sort chronologically.
- **Archive a project**: schedules can be archived right from the project list. Archived projects appear under the "Archived" tab and are kept read-only. Archiving runs asynchronously with real-time progress.

## Create a project from a sales order/offer

- **Create a new project directly from a sales order or offer**: the create dialog is now web-based and prefills ERP data (e.g. order/offer number, due date, business unit); the new project stays linked to the ERP document.

## Schedule import from the ERP

- The formerly separately operated **"schedule import"** is now part of ams.project. The web page lists the ERP schedules with filtering, sorting and paging, imports a schedule as a **new project** after confirmation, shows the progress live and, per schedule, the import status and log. It is reached in the browser at the ams.project address with the path `/projectimport` (e.g. `http://<server>:5000/projectimport`).
- Only signed-in users can import; the importing user is recorded as the creator of the project.
- Imported tasks are linked to their ERP order or offer; tasks without an ERP line item are linked to the whole order. A reference to a document that does not exist in the ERP rejects the import, naming document and task.
- The calendar of the ERP schedule is taken over by its ERP code; if it is unknown, the import stops with a clear message. If single ERP values are missing, the validation names the missing value.
- With "at most one resource per task" switched on, the import creates a separate task "task name (resource)" with the same dates and links for every further resource of a task; the import log lists them.

## Insert offers & orders into a schedule

- **Sales offers and offer line items** can be inserted into an existing schedule via the **"Schedule references"** dialog (**"Assign offers"**) – either the whole offer or specific line items.
- Inserted references appear as **ERP tasks in the Gantt** (document number, type icon, description) and stay **live-synced with ams.erp**.
- **Sales orders and order line items** can be assigned the same way; existing references can be removed individually or all at once.
- **Searching offers and orders** runs on the server and narrows the list as you type instead of loading it in full up front.

## ERP integration

- **ERP references and dates** shown directly in the Gantt: labels, type icons and tooltips, including live-created ERP tasks.
- **Bill-of-materials sync** with correct duration (from the working-day span, no longer a fixed "1 day") and without incorrect milestone behavior.
- **Per-task sync status** ("in sync / out of sync") and a **"Re-sync ERP"** button.
- More robust ERP date distribution (a central per-project date hub).
- Clicking the status-bar counter **"ERP date(s)"** opens the conflict overview directly at the "ERP dates" section; a hidden detail area is shown again for that.
- **Project header to the ERP**: when a new revision is opened, business unit, project manager and clerk are written back from ams.project to the ERP project header – just as when the project information is saved. A value that is empty in ams.project also clears the value in the ERP.

## Task detail view (modernized)

- A task's detail area is new and web-based, embedded in the desktop client.
- Tabs for **predecessors**, **discussion**, **notes (memo)** and **bill of materials (BOM)**.
- A task's constraints/dates are maintained here, consistent with the new scheduling.
- The detail view can be **detached into its own "Task-Details" window** that reliably stays in the foreground. A stuck or hidden detail area can be recovered at any time via **"Reset detail area"**; the conflicts pane is scrollable.
- **Project manager and clerk** of the project header appear in every task, including tasks created after the project information was last saved.
- **Hours picture of the assignments**: plan, distributed, actual and **remaining hours** (plan − distributed − actual per row). Workstation rows show "–" for "distributed"; if more was distributed or booked than planned, the remaining hours appear negative and in red as in the schedule. The consolidated card shows a stacked hours bar, the "Plan vs. actual" card two bars (plan and actual). The sum line, the hours bar and the Gantt columns "Planned hours"/"Remaining hours" show the same plan without double-counting hours that a workstation takes over from its group.
- **"From subtasks" block** in the "Assignments" card: it shows the planned, distributed, actual and remaining hours booked on subtasks (workstations indented), switchable "by order" / "without order"; the choice is remembered per browser. For each workstation group a **Σ line** closes the total demand of the branch (plan, actual, remaining).
- **Replacement made visible**: every workstation row states which group planning it replaces (e.g. "replaces 2 Task 1", using the task ID) or whether it counts "in addition". If a subtask carries a group set of the same group, a note on the group row points out the double count.
- **Origin of booked hours**: when a task changes its order, the row of the workstation or group states where booked hours come from, e.g. "of which 6 h from order 20260506" (several orders comma-separated). Plan, distributed, actual, remaining hours and the sum line do not change. The note appears the next time the work plan is calculated.
- **Book actual hours per assignment**: hours are booked in the row of the respective assignment; the proposed hours are the remaining time of exactly that assignment, and the booking panel states what is booked on. If only an order without line item exists, the hours are booked on the order header. A task's actual hours include all assignments, also an earlier, since replaced one; such rows are marked as inactive.
- **"Redistribute hours"** (also in the desktop client): the action asks for confirmation in a dialog, optionally offers "replay bookings from the ERP", shows progress live and reports "done" only once all capacity data has been written. If a redistribution is already running, the order is queued ("waiting – position n").
- **Assign resource**: the assignment picker opens directly in the task's workstation group, has a "Cancel" button, and a second click on a tile clears the selection.
- The switches **"Relevant for capacity planning"** and **"Relevant for cost calculation"** show the stored value and only change when switched explicitly.
- The project-wide **conflict overview** updates live and stays complete; the number in front of the task name in the header follows moves and re-ordering.
- Tables and notes also fit a narrow detail pane without horizontal scrolling or clipped columns.

## Reporting & capacity

### Web reports

- New **web reports** with **dashboards** and a **filter wizard** (configuration without SQL knowledge).
- **Interactive charts**: hot-edit, drill-down, hyperlinks, print and **PDF export** directly from the analysis page. **Enhanced PDF export**: configurable margins, page numbers, "fit to width" and optional splitting of large charts across multiple pages (N × M); the export also works in the embedded mode and exports all charts.
- **Capacity planning inside the reporting module**: capacity tiles sit alongside all other reports on the start page; every report opens with a uniform header (type icon, home and colour control as icons). Tiles of every report type are maintained in the same dialog (report types 1–5); the selected date range survives a change of report type, and tiles without a filter value show "All".
- **Capacity report** with clearer charts: configurable chart size and column count, drop-downs for report type/grouping, available-capacity and capacity-factor lines, and clearly highlighted overload notices. The "Capacity plan" and "Capacity PZE" lines can be switched off individually (also per tile); series without values do not appear in the legend. Capacity balancing fills against the selected overload line.
- **Filters**: active filters appear as removable chips, a bar's context menu offers "Clear filter", the filter row can be hidden.
- **Presentation**: dates, periods and numbers follow the interface language; tooltips appear with a delay. In the total and trend charts a bar's values are in the context menu, in the load profile the clicked value is in the menu header. The detail table shows the origin of each row and the % share per row as well.
- **Colour control**: colour sets define the bar and line colours in the Gantt report and capacity planning (palette, value overrides, semantic colours; one set is active per tenant). Fixed colours per document/line item take precedence and apply to the Gantt, capacity or both; they can also be set directly from the bar's context menu. Palettes can be exported and imported as a text file; colours are unique within a chart and across charts.
- **User groups** for visibility: a tile can belong to a group instead of "All" or a single user; groups and members are maintained on a dedicated page, and the start page shows only tiles of the user's groups.
- **Fully bilingual** (German/English) including charts, context menus, filter wizard, configuration and PDF export; the language selector is on every page.
- **Fast**: the capacity report draws the first chart after about 1.5–2 seconds; a distribution run of the capacity data finishes in about a minute even on large data volumes.
- **Offer hours** are weighted by the order probability in the load (an offer at 20 % counts 20 % of its hours); only open offers are distributed.
- **Workstation groups** are recognized as groups and shown with their name; group codes do not appear in the workstation selection.
- An illustrated **user help for the reports** ships with the application.
- The **"amsProject"** report (Reporting Services) applies the project filter correctly.

### Capacity planning & resource assignment

- **Web booking dialogs** for actual hours, percent complete and completion marker. The booking list has its own filterable and sortable **workstation group** column and the **"FK"** column with the ERP code N / T / F (long text as tooltip). The **completion marker** is proposed from the completion percentage but can be overridden with Not started / Partially done / Done and is booked along; an overridden value is kept when the row is selected again. Tasks with a **pure group assignment** can be booked: they appear once per workstation of the group, with the group code highlighted in colour.
- **Several workstations per task**: a task can be assigned to several workstations of the same workstation group; each assignment has its own hours, and the ERP capacity data holds a separate record per assignment. Workstations of other groups are visible but cannot be picked; a note names the bound group. The assignment dialog in the desktop client only offers workstations of that group. Assigning the same workstation again adds the hours; changing the workstation means "remove assignment" and then "assign again". The Gantt column shows all assigned workstations.
- **Capacity replacement by project structure**: when a task or subtask plans hours on a workstation, they replace exactly one group set of the same group within its own branch of the work breakdown structure – not the earliest set of the order. A date shift therefore no longer moves group load between months. The **replacement search order** can be set per project ("line item before proximity" = default, or "proximity before line item"), in the desktop client via the menu entry of the same name next to "Project calendar".
- **At most one resource per task (configurable)**: the administrator can decide that a task may have only one workstation or workstation group. The task details (also in the desktop client) then offer no further assignment and explain why; changing hours and removing an assignment remain possible, and the system refuses a second resource on other paths as well. The setting is off by default.
- Assignments using **group resources** start on the task's start day.
- The **"Assignments and progress"** card in the desktop client uses the web capacity view in its own window and shows the resource code and the planned window.

### Actual hours

- **Actual hours per workstation and per order**: tasks that left an order no longer count there; double-counted actual hours are cleaned up on redistribution.
- **Actual hours booked in the ERP without a task find their target**: first the task that plans exactly that workstation; otherwise the group set of the workstation group; otherwise the group's budget. Each level searches at the booked line item, then the parent line item and finally the order header. Hours never move to a task of another workstation; a booking with a task reference stays on its task. Group rows therefore also show "actual" from such bookings.
- **Bookings that cannot be distributed** are not lost (no workstation, unknown workstation or no matching planning); they are listed with a reason in the redistribution result in the administration portal.
- **Process actual hours** in the administration portal (see there); only one run at a time, further orders are queued and start by themselves afterwards. An order is never queued twice.
- **Structure changes keep actual hours up to date**: when a move puts a task into another order or line item, the order immediately shows as **"Open"** with the note "structure change reported" in the administration portal and is redistributed automatically after **10** quiet minutes (one redistribution per order, even after several moves in a row). Bookings with a task reference stay on their task for good; the task shows the sum of its hours from all orders.
- **Warning before a move that leaves booked hours behind**: if you drag a task in the Gantt under a task of **another order**, a window shows the hours per order that were booked in the ERP with a task reference and stay in the old order, the affected tasks and the correction path (cancel in the ERP, book again). **OK** moves anyway, **Cancel** leaves the task where it is; without booked hours no warning appears.
- **Redistribution** puts much less load on the system and the ERP database: it produces only a fraction of the internal messages.

> 📝 **Note for administrators**: The automatic redistribution after a structure change is controlled in the administration portal under **Feature Flags**: the switch `ActualHoursStructureChangeRedistribution` is **on** by default; when switched off, orders are only marked "Open" and you start the redistribution manually. The **value** of the switch is the quiet period in minutes (default 10, allowed 1 to 1440); a change takes effect without a restart. The automatic redistribution belongs to the feature flag `ActualHoursRedistribution`: if it is off, no redistribution starts.

> 📝 **Note for administrators**: The restriction to one resource per task is switched on in the administration portal under **Feature Flags** with the switch `SingleResourceAssignmentPerWorkItem`; it is **off** by default. The change takes effect in the system after at most **30 seconds** and in the task details on the next page load or task change; no restart is needed. Tasks that already have several resources stay as they are: their hours can be corrected and resources removed, but no further one can be added. Check yourself whether such tasks exist before switching it on.

## Collaboration

- **Discussions** per task, in real time.
- **Notes (memos)** per task; optionally copied along when copying tasks.
- The discussion and notes panes use the **consistent design system** and blend in seamlessly in look-and-feel and dark mode.

## Copy & paste

- Copy tasks including **subtasks** – or **subtasks only**, without the head task.
- Optionally **include notes** when copying.
- **Resource assignments** are copied per assignment with their own hours. If only one resource per task is allowed, pasting asks and takes the first assignment per task.
- The **"Paste task(s) from clipboard"** dialog has a fixed size, one row per option and a message area with warnings, a note on "paste subtasks only" and a summary of what will be pasted.
- Clipboard entries without a matching target task are skipped instead of raising an error.

## Interface, language & usability

- **Consistent language (German/English)**: all modules follow the chosen language; **language switcher** in the desktop client (persisted, changeable any time). Exception: some conflict messages in the task details (see the section "Known limitations").
- Modernized **shell**: new title bar, Fluent color palette.
- **Dark mode** (off by default, see the section "Known limitations"): once enabled, switching also applies to the embedded web modules and the WPF menu, and updates live.
- Web modules are hosted seamlessly inside the desktop client.
- **Multi-project timeline**: an **edit mode (F2)** prevents accidental date moves; moving with the mouse hits the target position exactly and follows the same rules as dragging in the Gantt.
- Consistent **weekend/holiday shading** in the Gantt.
- New built-in **customer documentation portal** (help).
- **Sign-out and switching users** in the desktop client via the user menu at the bottom of the sidebar: after signing out a fresh sign-in is required (credentials are asked for once), and the workspace refreshes by itself after switching users.
- **`amsproject://` links** open ams.project also on workstations set up with the client installer, and links with a task ID jump directly to the task.

## Administration portal (new) *(for administrators)*

Alongside the application itself there is now a dedicated, web-based **administration portal** with a modern interface (sidebar, dark mode, German/English, sign-in/out showing the signed-in user):

- **Central configuration**: system-wide settings are maintained centrally and activated via a **release switch**; clients load their settings from the server on startup – no more manual distribution of configuration files.
- **User/administrator permissions**: administrators are managed in the portal; the first person to sign in can be promoted to administrator automatically (including a recovery path after a reinstall or handover). The administrator right is kept when a user is deleted and re-invited in the identity provider.
- **Feature flags** and an **audit log** directly in the portal. A temporary read failure of the feature flags does not switch any flag off; the last known values stay in effect.
- **Maintenance mode**: the system can be put into maintenance – with an announcement and a countdown grace period; signed-in clients are warned in time.
- **Error/queue management**: failed background operations are shown clearly and can be **retried or discarded** individually.
- **Revision for all projects** (*Operations → Revisions*): opens a new revision for every active, non-archived project, with live progress per project.
- **Actual hours** (*Operations → Actual hours*): lists all sales orders with their processing state (searchable, paged), reprocesses single or "all open" orders, optionally replays missing bookings from the ERP and shows the run live in a phase bar. While an order is being processed, its actual hours briefly show 0.
- **mandant.ini import**: existing tenant settings can be read in and applied via a dialog; paths as entered (including UNC paths) are kept unchanged.
- **Client rollout from the portal**: the desktop client can be deployed directly from the portal to a network path – with **live progress**.
- **Sign-out** ends the session completely; a fresh sign-in is required afterwards.

## Installation & operations *(for administrators)*

- **New installation mechanism via ReadyStackGo (RSGO)**: the entire application is rolled out centrally and in a guided way – individual services/containers are installed and updated from a manifest, without setting up each component manually. The database migrations run automatically before the services and can safely be repeated.
- **One RSGO for multiple target servers**: the same ReadyStackGo instance can serve several environments/target servers (remote environments).
- **Guided client installer**: sets up the desktop client at the customer site (version selection, tenant number, target directory). The matching version is **fetched from the server on demand**; tenant number and target directory can be prefilled centrally from the configuration, and a permanent background service is no longer required. The ams.project client can have its own installation directory next to the ERP (central setting `ams.Project.Install.Path`), the configuration of the launcher for `amsproject://` links is filled automatically, and installation steps that print errors are reported as failures.
- **Update guide** for server (ReadyStackGo) and client (administration portal) in German and English, also as Word and PDF.
- **Logs**: more informative startup and connection logs of the client in a writable user directory (running from a read-only file share is possible); schedule recalculations above 0.5 seconds are logged as a warning on the server.

## Stability, improvements & fixes in detail

Beyond the headline features, 4.0 includes numerous refinements and bug fixes:

**Opening & session**
- Projects open reliably even after longer periods of inactivity – the session is refreshed automatically. If it has expired after all, a friendly **"session expired"** page with a fresh sign-in is shown instead of a technical error, without a redirect loop.
- Schedules open reliably in distributed installations, too.
- Users whose name contains umlauts or other special characters (e.g. `Müller`) can sign in and work.
- Notifications and live data (budget, schedule violations, tags) reconnect reliably, even when the session is renewed in the meantime.
- Switching revisions no longer makes a project load endlessly until the client stops with an out-of-memory error.
- Opening a project no longer shows a crash dialog.

**Scheduling**
- Date-bound milestones (start/finish no earlier/later than) keep their day on non-working days, or move correctly to the next working day.
- "As late as possible" (ALAP) respects the earliest feasible start date and preserves predecessor/successor order.
- Summary tasks no longer produce spurious "snapped to working day" notices; their duration column shows the actual duration of the subtasks, and a summary task no longer collapses to a single point when a task is moved underneath it.
- A new task under an ERP order whose due date lies in the past is not placed before the project start.
- A changed project start date is applied even when it crosses the day boundary at midnight.
- The first drag of a newly created task does not lengthen it.
- Tasks can be deleted in projects with a "Purchase requisitions for RFQ" group; deleting that group no longer removes the "Assemblies" group along with it.
- Predecessor/successor links are shown reliably in the overview.

**ERP integration**
- Correct labels and type icons, including for live-created ERP tasks; fixed a display crash for certain live-created ERP tasks.
- No more date offset (−1 day) on ERP plan dates.
- Inherited order references no longer carry an incorrect ERP due date.

**Reports & capacity**
- Data refreshes by itself; load errors are surfaced instead of swallowed; a login loop ("HTTP 431") is fixed, and a distribution blocked by a stuck lock runs through.
- The Gantt report does not open with an empty chart side; the timeline report shows its dates also with "Editable = Yes"; the configuration table reliably shows its rows.
- The capacity line no longer runs twice across the chart over a year change; font, filter placeholders and axis titles use the application font.
- Correct date format in the export columns; overload notices are shown correctly even at 0 hours.
- Six Gantt columns that stayed empty fill again.
- The reports start page passes the user ID safely as a parameter and does not log it in plain text.

**Administration portal**
- Language switching (DE/EN) works reliably; icons and forms render correctly; navigation is legible in dark mode; fixed saving of the configuration.

**Interface & desktop**
- The WPF menu follows dark mode (neutral grey instead of AMS blue).
- The Windows taskbar icon is shown; the "loading configuration" splash text is localized; the startup configuration is retried automatically on a timeout.
- The print preview of the reports opens in an in-app window with the correct project association.
- Form fields no longer lose their values when they are only shown behind a condition.
- Leaving a plan cleanly closes the active plan and its menus.
- Copy/paste of notes: fixed a crash on paste; clearing a note removes the note icon from the table.
- The bill-of-materials list sorts the task ID numerically.
- The "Predecessors"/"Successors" headings use the accent colour like every other section heading.

**Installation & operations**
- Numerous robustness improvements to the client installer (on-demand download, prefilling of target directory and tenant number, correct handling of UNC paths and placeholders).
- The platform service and installer start reliably even with certain log-level settings.
- Updating the databases is more robust: mandatory fields added after the fact, re-applying the same seed data, names too long for the ERP columns, databases owned by the runtime login and retired service endpoints no longer make the update abort.
- The capacity data of the reports is also built on older ERP versions (ERP building blocks missing there drop out of the report); a diagnostic script is available for the capacity distribution. The other requirements on the ERP version are listed in the section "ERP requirements".

## Changed / Removed

- **Gantt dragging**: automatically scheduled tasks stay automatic when dragged and get "Start No Earlier Than"; a task is only pinned (manual) with the Alt key or for manual tasks, milestones, ALAP and tasks with another dated constraint.
- **Changing the workstation**: means "remove assignment" and then "assign again"; assigning the same workstation again adds the hours.
- **"Relevant for cost calculation"** is ON by default for new tasks, including pure group assignments, and only changes when switched explicitly.
- **Milestones**: tasks with subtasks can no longer be marked as milestones.
- **Capacity report**: offer hours are weighted by the order probability and only open offers are distributed; capacity replacement between workstation group and workstation follows the project structure instead of the start date. The load shown can therefore differ visibly from 3.0.
- The **"Type curve"** field in the **"Assign resource"** and **"Change target value"** dialogs has been removed. It is no longer needed in the new web workflow; a task's stored type-curve value is preserved and continues to be processed. The database objects required for this are provided by the ERP (see the section "ERP requirements").
- **Removed**: the legacy **report type 6**, the separate **tile administration** and outdated background data sources; tile data now lives with the remaining report configurations.
- **Removed**: the formerly separately operated "Schedule Import" application – importing now runs only through the integrated page and requires sign-in.
- **Removed**: direct interfaces for redistributing actual hours; redistributions run via the administration portal or the "Redistribute hours" dialog.

---

## Upgrading to 4.0 — notes for administrators

4.0.0 is installed via **ReadyStackGo** from the **stable channel** (manifest repository `https://github.com/Wiesenwischer/rsgo-ams-project-stable`, containing **ams.project 4.0.0** and **IdentityAccess 3.4.1**). The source is not yet preconfigured in ReadyStackGo; add it under **Settings → Stack Sources → Add → Git Repository**. The individual steps are described in the installation guide and the update guide.

### ERP requirements

Before the update, check that your **ams.erp version provides the following database objects itself** – from 4.0.0 on, ams.project no longer creates them. Please agree on the required ams.erp version with ams.erp support before the update.

- the 19 capacity views `SPAV12…`, `Typkurvenverteilung` and `amsKapaData`,
- `amsKapaDataMenu` (with the columns `RECORDID`, `BEZEICH80`, `LINK`), `amsKapaDashboardView`, `tvf_GetKapaDashboardParameter` as well as `ContextArbPlatz` / `ContextArbPlatzData`,
- in the table `prmd100` the columns `ZEIT_SOLL`, `ZEIT_IST`, `ZEIT_AZE` and `FK_APL` (if they are missing, some capacity views are skipped during the update).

Existing databases in which these objects are already present keep them. If they are missing, the capacity reports among others fail.

**Changes to the ERP database on the first update** (automatically, by the migration):

- The table `RESD000` gets its primary key on `RECORDID` (previously `TASKID`); duplicate `RECORDID` values are cleaned up by a fixed rule, and if change tracking is on for the table, it is briefly switched off and on again. The first refresh run of the reports afterwards is a full run. From then on `RESD000` holds one record per assignment instead of per task.
- To refresh the reports, the migration switches **change tracking** on for the database (7 days retention) and for the tables `SPAD050`, `RESD000`, `PRMD030`, `PRMD100`, `ANGD430`, `TMSD800` and `ANGD300`; each of these tables needs a primary key for that. It also adds a database-wide DDL trigger (`capa_tgrDDLCTHeilung`) that triggers a full refresh run after one of these tables is re-created, and **SQL Agent jobs** for the ongoing refresh. The admin login (`DB_ADMIN_USER`, `sysadmin`) creates them; without SQL Agent the jobs are skipped. If a later ams.erp update changes the primary key of one of these tables, coordinate it with ams.erp support.
- The triggers on the booking table `PRMD060` report actual-hours bookings to the ams.project ERP integration.
- Every time a new revision is opened, ams.project writes business unit, project manager and clerk back to the ERP project header (`APJD000`); an empty value in ams.project clears the ERP value.

### Mandatory steps in this order

1. **Add the stable source in ReadyStackGo** and sync it (*Settings → Stack Sources → Add → Git Repository*, URL `https://github.com/Wiesenwischer/rsgo-ams-project-stable`).
2. **Install or update IdentityAccess 3.4.1.** 3.4.1 registers the interface of the schedule import with the identity provider; without it the import cannot be used.
3. **Install or update ams.project 4.0.0.** The database migrators run automatically before the services (see the section "ERP requirements").
4. Run **administration portal → Operations → Revisions → "Start for all projects"** (revision for all projects; **mandatory**). Only then do project manager and clerk appear in every task, and capacity replacement by project structure takes effect – until then the previous start-date rule applies per project. Inactive and archived projects are not part of the run. **Side effect:** every opened revision writes business unit, project manager and clerk back to the ERP project header.
5. **Process actual hours**: the update sets already processed orders back to "Open" once. The automatic run over all orders is **off** by default – start "**Process all open orders**" under **Operations → Actual hours** in the administration portal, or switch the automatic run on with the stack variable `CAPACITYPLANNING_ACTUALHOURS_AUTOMATIC_REDISTRIBUTION` (see below). Only then are existing orders calculated by the new rules (one capacity record per assignment, target search for bookings without a task, sum per order). The option "Replay bookings from the ERP" is off by default and makes the run considerably longer.
6. **"amsProject" report (Reporting Services)**: if you use the report, deploy the current report file to the report server again – please agree on this with ams support.
7. **Roll out the desktop client** (administration portal → *Provisioning* → *Client download*). Gantt dragging, the warning before moving booked hours, copying per assignment, the Gantt column with all workstations and the new paste dialog require the new client.

### Switching from 3.0.x

If ams.project 3.0.x still runs on docker-compose, the switch-over is one deployment of 4.0.0 against the existing databases, accompanied by ams support. The steps are described in the section "Switching from 3.0.x to 4.0" of the update guide. In addition to the mandatory steps above:

- **Actual hours with ERP replay**: start "Process all open orders" with the option "Replay bookings from the ERP" switched on. Only then does the actual-hours ledger also contain the bookings that 3.0 had not processed yet and the bookings from the update period. The run takes considerably longer with this option.
- **Maintenance window for the ERP**: during the switch-over the migrators change triggers, keys and change tracking of ERP tables and lock them briefly. Plan the update in a maintenance window in which nobody saves in the ERP.
- **Project calendars**: projects keep the ERP calendar they are planned with. Projects without a calendar of their own get the ERP calendar `STANDARD`; if the ERP has none, the calendar "Demo".
- **New EventStore volumes**: ReadyStackGo creates new volumes for the EventStore; the EventStore data of the 3.0 installation is not taken over. The revision for all projects fills the EventStore again.

### Features and switches that ship switched off

| Switch | Default | Effect | Where |
|---|---|---|---|
| `SingleResourceAssignmentPerWorkItem` | **Off** | At most one resource per task. Check the tasks that already have several resources before switching it on. | Administration portal → Feature Flags |
| `ActualHoursStructureChangeRedistribution` | **On** (value 10) | Redistribution of an order after a structure change; value = quiet period in minutes (1–1440). | Administration portal → Feature Flags |
| Automatic run after the update | **Off** | One-time redistribution of the actual hours of all orders after an update (only with `ActualHoursRedistribution` on). Switch it on only once a run over the orders of your environment keeps the expected duration; then restart the capacity service. | Stack variable `CAPACITYPLANNING_ACTUALHOURS_AUTOMATIC_REDISTRIBUTION` |
| Self-healing of actual hours | **Off** | A booking for an order that was never processed triggers a complete redistribution. | Stack variable `CAPACITYPLANNING_ACTUALHOURS_SELFHEALING` |
| ERP replay during processing | **Off** (per run) | Replays missing bookings from the ERP. | Option in the portal or in the "Redistribute hours" dialog |
| `EnablePreviewFeatures` | **Off** | Shows the switch to the not yet complete dark mode in the desktop client; takes effect on the next start of the client. | Administration portal → Feature Flags |

### New stack variables (ReadyStackGo)

| Variable | Default | Meaning |
|---|---|---|
| `CAPACITYPLANNING_ACTUALHOURS_AUTOMATIC_REDISTRIBUTION` ("Automatic redistribution of actual hours after an update") | `false` | see the table above; an empty value counts as off |
| `CAPACITYPLANNING_ACTUALHOURS_SELFHEALING` ("Self-healing redistribution of actual hours") | `false` | see the table above |
| `ANALYTICS_DEMO_DATA_ENABLED`, `ANALYTICS_DEMO_DB_USER`, `ANALYTICS_DEMO_DB_PASSWORD`, `DB_DEMO_USER`, `DB_DEMO_PASSWORD` | empty / `false` | test systems only (demo data for the reports); **leave empty** on production systems |

### New services in the stack

- **Schedule import** (web interface under `/projectimport`).
- A background service that prepares an upcoming web schedule. It has no user interface yet, builds its read model for all projects in the background (throttled) after every start and shows the progress in its health status.

### Effects on existing data

- Project manager and clerk are filled in per project from the existing tasks; projects without any value receive them with the revision for all projects.
- "Distributed" and "remaining hours" of the task details are recalculated during the update; notes on structure changes and on the origin of booked hours appear after the next calculation of the work plan.
- The order/line-item keys for the "From subtasks" block are filled in once in the background after the update.
- Existing tasks keep their stored cost switch.
- Summary tasks that became manual through earlier dragging stay manual; they can be set to "Automatic" in the detail area.
- **Report filters from 3.0**: saved reports with a filter written as SQL text lose that filter, because 4.0 only takes over filters from the filter wizard. The report itself is kept and shows all projects until the filter is created again in the wizard.
- **Project roles and permissions from 3.0** are removed by the update. ams.project 4.0 currently has no permission management for projects; administrators are managed in the administration portal.
- Capacity reports show different load values after the update (weighted offer hours, replacement by project structure, actual hours by target search).

---

## Known limitations

- **Dark mode**: not yet complete – among others, the Gantt chart and some WPF dialogs do not follow the dark design yet. It is therefore off by default; the switch in the desktop client only appears once the feature flag `EnablePreviewFeatures` is on.
- **Conflict messages**: the headings and texts of the conflict list in the task details appear in German even in the English interface.
- **Remaining hours in the reports**: the reports can still differ from the task details in the remaining hours; task details and schedule already use the new formula (plan − distributed − actual).
- **Budget actual hours**: actual hours distributed to a budget do not yet appear with the budget in the task details.
- **Holidays from holiday calendars**: 4.0 takes over holidays from the exception days of the ERP calendars. Rule-based holidays from an ERP holiday calendar are not taken over and are not considered in scheduling.
- **Bill-of-materials Gantt**: dragging in the bill-of-materials Gantt does not yet honour the calendar.
- **Assignment dialog in the desktop client**: it does not list all assignments of a task; they are shown in the task details.
- **Schedule import**: there is no menu entry in the desktop client and no link in the project list yet; the page is opened via its address.
