<p align="center">
  <img src="assets/banner.png" alt="Sidur – shift scheduling for multi-branch businesses" width="100%">
</p>

<h1 align="center">Sidur · סידור</h1>

<p align="center">
  <b>A portable, Hebrew-first shift scheduler for multi-branch businesses.</b><br>
  One diary per branch, separate sections for front desk and professional staff, week locking and live hour tracking.
</p>

<p align="center">
  <a href="https://galasulin.github.io/shift-scheduler-showcase/demo/"><img alt="Live demo" src="https://img.shields.io/badge/Live_demo-open-17181C?style=for-the-badge"></a>
  <img alt="Status" src="https://img.shields.io/badge/Status-Phase_1_in_development-2B5BA8?style=for-the-badge">
  <img alt="Platform" src="https://img.shields.io/badge/Windows-Portable-B4531A?style=for-the-badge">
  <img alt="RTL" src="https://img.shields.io/badge/UI-Hebrew_RTL-1E5B35?style=for-the-badge">
</p>

---

## The problem

A service business with five branches ran its staff schedule in separate Excel files. Hours were never fixed: they change with the season, the people and the week. Nobody could see who covers which hours, how many hours each employee was getting, and planning ahead meant copy-paste errors.

## The solution

**Sidur** replaces those spreadsheets with one clean, Hebrew-first desktop app that runs from a single folder on one PC. No server, no cloud, no installation.

| | |
|---|---|
| **Diary per branch** | A tab for every branch, with a *front desk* section and a *professional staff* section. |
| **No fixed shifts** | Every assignment is a person with their own hours that day (07:30–14:00, 16:00–21:00). One-click presets and per-employee regular hours save typing, but never lock you in. |
| **Coverage at a glance** | A coverage strip under every day shows which opening hours are covered and which are left without anyone. Informational, no nagging rules. |
| **Opening hours per branch and day** | Each branch has its own opening hours per weekday, and any date can get a one-off exception (late Wednesday, holiday eve). Changes apply from a chosen date and never rewrite past weeks. |
| **Plan ahead, then lock** | Schedule weeks and months in advance. Copy last week as a starting point, adjust the hours, lock the week. |
| **Hours at a glance** | Every employee has a live counter against a weekly target. Going over turns red. |
| **Smart warnings** | Double booking across branches is blocked. Absences, overtime and short rest periods raise warnings before you assign. |
| **Users and roles** | Personal username and password for everyone. Admin, editor (own branches only) and view-only. |
| **Portable and safe** | One `Sidur.exe`, one SQLite file, automatic backup on every launch (last 30 copies). |

---

## Screens

<table>
  <tr>
    <td colspan="2"><img src="assets/screens/02-weekly-schedule.png" alt="Weekly schedule"><br><sub><b>Weekly schedule</b> · the main screen. Branch tabs, opening hours per day, each person with their own hours, coverage strip, hour counters, absences row.</sub></td>
  </tr>
  <tr>
    <td width="50%"><img src="assets/screens/03-assign-dialog.png" alt="Assign dialog"><br><sub><b>Assign dialog</b> · pick a person, then hours (preset or free); overlaps, absences and overtime flagged before you save.</sub></td>
    <td width="50%"><img src="assets/screens/04-month-view.png" alt="Month view"><br><sub><b>Month view</b> · people and hours per day, special days, unscheduled days and locked weeks.</sub></td>
  </tr>
  <tr>
    <td width="50%"><img src="assets/screens/05-employees.png" alt="Employees"><br><sub><b>Employees</b> · multi-branch staff, weekly targets, availability.</sub></td>
    <td width="50%"><img src="assets/screens/06-settings.png" alt="Settings"><br><sub><b>Settings</b> · users and roles, opening hours and presets, backups.</sub></td>
  </tr>
  <tr>
    <td width="50%"><img src="assets/screens/01-login-a.png" alt="Login"><br><sub><b>Login</b> · personal credentials, password reveal.</sub></td>
    <td width="50%"><img src="assets/screens/01-login-b.png" alt="Login error and first-time password"><br><sub><b>Lockout and first login</b> · 5 failed attempts lock for 5 minutes; new password on first login.</sub></td>
  </tr>
</table>

> All names, branches and numbers in the screens are fictional sample data.

---

## Try it

**[Open the interactive demo →](https://galasulin.github.io/shift-scheduler-showcase/demo/)**

Switch branches (each one has different opening hours), move between weeks, click "+ שיבוץ" to add a person with any hours, hover an assignment to remove it, copy last week and lock it. Hour counters, coverage strips and KPIs update live. Everything is stored only in your browser.

---

## How it works

```mermaid
flowchart LR
    U[User login<br/>Argon2 hashed passwords] --> UI[React UI<br/>Hebrew RTL]
    UI <--> CORE[Tauri core<br/>rules and validation]
    CORE <--> DB[(SQLite<br/>data/sidur.db)]
    CORE --> BK[Auto backup<br/>data/backups x30]
    CORE --> EXP[PDF and Excel export]
    BK -.optional.-> NET[Network or OneDrive folder]
```

**Data model**

```mermaid
erDiagram
    BRANCH ||--o{ SHIFT_TEMPLATE : has
    SECTION ||--o{ SHIFT_TEMPLATE : groups
    SECTION ||--o{ EMPLOYEE : contains
    EMPLOYEE }o--o{ BRANCH : "works at"
    SHIFT_TEMPLATE ||--o{ ASSIGNMENT : "filled by"
    EMPLOYEE ||--o{ ASSIGNMENT : "assigned to"
    EMPLOYEE ||--o{ ABSENCE : takes
    BRANCH ||--o{ WEEK_LOCK : "locks"
    USER ||--o{ AUDIT_LOG : writes
```

**Portable folder**

```text
Sidur\
├── Sidur.exe
└── data\
    ├── sidur.db
    └── backups\
```

| Layer | Choice | Why |
|---|---|---|
| Shell | Tauri | Small native Windows window, no browser, no open port |
| UI | React + Tailwind | Modern, fully right-to-left Hebrew interface |
| Data | SQLite | One file, no database server, trivial to back up |
| Security | Argon2, roles, lockout, idle logout, audit log | Several people can share one PC safely |
| Output | PDF + XLSX | Print a week for the branch wall, export for reports |

---

## Warning rules

| Situation | Behaviour |
|---|---|
| Overlapping hours for the same employee (any branch) | Blocked |
| Employee is on leave, sick or on reserve duty | Warning + confirm |
| Weekly hour target exceeded | Yellow warning, red counter |
| Hours outside the branch's opening hours | Yellow warning |
| Employee assigned outside their branches | Yellow warning |
| Less than 11 hours rest between two assignments | Yellow warning |
| Editing a locked week | Blocked, unlock is admin only and logged |

---

## Roadmap

- [x] Research of existing tools (Deputy, When I Work, Homebase, open-source schedulers)
- [x] Product spec, data model and warning rules
- [x] UI design, review pack and interactive prototype
- [ ] **Phase 1** · Login and users, branches, sections, employees, opening hours and presets, weekly diary with per-person hours, coverage strip, hour counters, PDF print
- [ ] **Phase 2** · Absences, warnings, week locking, copy previous week, auto backup
- [ ] **Phase 3** · Day view across branches, month view, reports, Excel export
- [ ] **Phase 4** · Excel import of existing staff, full audit log

---

## In this repository

| Path | What it is |
|---|---|
| [`demo/index.html`](demo/index.html) | Single-file interactive prototype (no build step) |
| [`docs/spec-he.md`](docs/spec-he.md) | Full product specification in Hebrew |
| [`docs/review-pack.pdf`](docs/review-pack.pdf) | 12-page review pack with every screen, a QA checklist and sign-off form |
| [`assets/screens/`](assets/screens) | Screen designs |

---

## בעברית

**סידור** היא מערכת ניידת לניהול משמרות בעסקים עם כמה סניפים. יומן לכל סניף, רובריקה של צוות שירות ורובריקה של צוות מקצועי, כל עובד משובץ בשעות שלו בלי משמרות קבועות, פס כיסוי, שיבוץ שבועות קדימה, נעילת שבוע, מונה שעות לכל עובד מול יעד, ואזהרות על כפילויות, היעדרויות וחריגות. רצה מתיקייה אחת על מחשב Windows, בלי התקנה ובלי שרת, עם כניסה אישית לכל משתמש וגיבוי אוטומטי.

האפיון המלא: [`docs/spec-he.md`](docs/spec-he.md) · מסמך הבדיקה: [`docs/review-pack.pdf`](docs/review-pack.pdf)

---

<table>
  <tr>
    <td width="96" align="center"><img src="https://github.com/galasulin.png" width="72" height="72" alt="Gal Asulin" style="border-radius:50%"></td>
    <td>
      <b>Gal Asulin</b> · IT Manager · The King<br>
      Product spec, UX design and development.<br>
      <a href="https://github.com/galasulin">github.com/galasulin</a>
    </td>
  </tr>
</table>

<sub>© 2026 Gal Asulin. Showcase project. Source code for the production app is not included. See <a href="LICENSE">LICENSE</a>.</sub>
