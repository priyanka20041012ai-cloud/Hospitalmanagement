# Hospitalmanagement
# 🏥 Medi Care Hospital (PVT) Ltd — HMS v7.0

## 📁 Folder Structure
```
MediCareHMS/
├── main.py                        ← Entry point (run this)
├── core/
│   ├── constants.py               ← All themes, colors, fonts, medicine catalog
│   └── database.py                ← DatabaseManager (Facade Pattern)
├── models/
│   ├── patient.py                 ← Patient domain model (Encapsulation)
│   ├── doctor.py                  ← Doctor domain model
│   └── appointment.py             ← Appointment domain model
├── repositories/
│   └── repositories.py            ← IRepository (ABC) + 5 concrete repos
├── services/
│   ├── billing_service.py         ← BillingService (Service Layer Pattern)
│   └── appointment_service.py     ← AppointmentService
├── gui/
│   ├── app.py                     ← MediCareHMS main window (MVC Controller)
│   ├── pages.py                   ← All page render methods (MVC View / Mixin)
│   └── dialogs.py                 ← All popup dialogs
├── utils/
│   └── helpers.py                 ← RoundBtn (Inheritance), export_bill_pdf
└── data/
    └── medicare_v7.db             ← Auto-created SQLite database
```

## 🚀 How to Run
```bash
cd MediCareHMS
python main.py
```

## 🔑 Login Credentials
| Role   | Username | Password  |
|--------|----------|-----------| 
| Admin  | admin    | admin123  |
| Doctor | doctor   | doc123    |

## 🏗️ OOP Concepts Used

### 1. Encapsulation
- `DatabaseManager`: All SQLite logic is hidden. External code calls `exe()`, `fetch()`, `fetchone()` only.
- `PatientRepository`: SQL queries are private. Callers just use `add()`, `get_all()`, `delete()`.
- `LoginScreen`: `self._attempts` is private state — the parent window never touches it.

### 2. Inheritance
- `MediCareHMS` extends `tk.Tk` — inherits the entire window management system.
- `RoundBtn` extends `tk.Button` — inherits button behavior, adds hover effect.
- All dialogs (`SMSDialog`, `LoginScreen`, `WelcomeScreen`, `EditDialog`) extend `tk.Toplevel`.
- All repositories extend `IRepository` (ABC) — they inherit the interface contract.

### 3. Abstraction
- `IRepository` (ABC): defines WHAT repositories do (add, get, update, delete), not HOW.
- `MediCareHMS._show(key)`: hides HOW a page loads — callers just say `_show("dashboard")`.
- `Patient.from_dict()`: hides HOW a Patient is created from raw DB data.

### 4. Polymorphism
- `PatientRepository.get_all()`, `DoctorRepository.get_all()`, `BillRepository.get_all()` — same method name, different SQL and data for each.
- Any repository can be used as `IRepository` type.

### 5. Composition
- `MediCareHMS` HAS-A `DatabaseManager`, HAS-A `PatientRepository`, etc.
- This is Composition over Inheritance for data access.

## 🔷 SOLID Principles

| Principle | Where Applied |
|-----------|--------------| 
| **S** — Single Responsibility | Each class has one job: `DatabaseManager` = DB only, `PatientRepository` = patients only, `BillingService` = billing logic only |
| **O** — Open/Closed | Add a new repository or page without modifying existing code. `RoundBtn` extends `tk.Button` without modifying it. |
| **L** — Liskov Substitution | Any concrete repository can replace `IRepository` — all honour the same contract. |
| **I** — Interface Segregation | `IRepository` has only 5 essential methods — no bloat. |
| **D** — Dependency Inversion | Repositories receive `DatabaseManager` via constructor (Dependency Injection). `MediCareHMS` depends on repository abstractions, not raw sqlite3. |

## 🎨 Design Patterns

| Pattern | Where Used |
|---------|-----------| 
| **Repository Pattern** | `PatientRepository`, `DoctorRepository`, etc. — separates data access from business logic |
| **Facade Pattern** | `DatabaseManager.exe/fetch/fetchone()` hides sqlite3 complexity. `_tree()`, `_stat_card()` in `app.py` hide raw Tkinter widget complexity. |
| **MVC Pattern** | Models = repositories/data, View = `pages.py`, Controller = `app.py` |
| **Service Layer Pattern** | `BillingService`, `AppointmentService` — business logic separate from UI and DB |
| **Observer Pattern** | `PharmacySelectorDialog.on_confirm` callback — notifies parent when selection confirmed |
| **Template Method** | All dialogs follow the same pattern: `__init__ → configure → _build() → grab_set()` |
| **Mixin Pattern** | Page functions in `pages.py` are imported directly into `MediCareHMS` class as methods |

## 📦 Dependencies
```bash
pip install reportlab matplotlib
```
- `tkinter` — built into Python (GUI)
- `sqlite3` — built into Python (Database)
- `reportlab` — PDF export
- `matplotlib` — charts/graphs
