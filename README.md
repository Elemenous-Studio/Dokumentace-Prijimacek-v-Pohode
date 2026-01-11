# Prijímací zkoušky v pohodě — Administrátorská dokumentace

**Support
Email:** **piticko@elemenous.uk**

## Czech (Čeština)

1. Přehled

V této dokumentaci popisuji postupy, které používám pro administraci projektu "Přijímačky v pohodě": nasazení, správu databáze, správu uživatelů, zálohování a základní řešení problémů.

2. Požadavky

- Node.js (doporučené LTS)
- PostgreSQL (pokud používám Supabase, konfiguruji přes Supabase)
- Docker a docker-compose (pro lokální nebo produkční nasazení)

3. Instalace a konfigurace

Klonuji repozitář:

    git clone https://github.com/Elemenous-Studio/Prijimacky_v_pohode.git
    cd Prijimacky_v_pohode

Zkontroluji kořenové soubory (`package.json`, `supabase/`, `docker-compose.yml`) a nainstaluji závislosti:

    npm install

Vytvořím soubor s proměnnými prostředí (`.env` nebo `.env.local`) podle dostupné šablony (`.env.example`) a nastavím potřebné hodnoty (DB_URL, SUPABASE_URL, SUPABASE_ANON_KEY, SECRET_KEY atd.).

4. Databáze a migrace

- Pokud používám Supabase, spravuji migrace v adresáři `supabase/migrations/`.
- Spouštím migrace podle nástrojů projektu (např. Supabase CLI nebo vlastní skripty).

5. Spuštění v režimu vývoje

Spustím vývojový režim příkazem:

    npm run dev

Aplikace by měla být dostupná na http://localhost:8080 (zkontrolujte `package.json` nebo Vite konfiguraci pro přesný port).

6. Docker – sestavení a nasazení

Pro sestavení obrazu používám:

    docker build -t prijimacky-v-pohode:latest .

A pro spuštění přes `docker-compose`:

    docker-compose up --build -d

7. Správa uživatelů a rolí

- Systém využívá role (`Admin`, `Teacher`, `Student`). Jako administrátor mám plný přístup k uživatelům, testům a nastavením.
- Uživatele mohu spravovat přes administrační rozhraní (pokud existuje) nebo přímo v databázi / přes Supabase Auth.

8. Zálohy a obnova

- Pravidelně exportuji databázi (pg_dump nebo Supabase export) a ukládám zálohy mimo server.
- Testuji obnovu záloh v izolovaném prostředí.

9. Monitoring a logování

- Kontroluji logy serveru a funkce Supabase pro chyby.
- Pokud potřebuji automatické hlášení chyb, nasazuji nástroje jako Sentry nebo Logflare.

10. Bezpečnost

- Uchovávám citlivé proměnné prostředí mimo VCS.
- Pravidelně aktualizuji závislosti a aplikuji bezpečnostní záplaty.
- Omezím přístup k produkční databázi pomocí firewallů a rolí.

11. Kontakty

- Technické problémy hlásím otevřením issue v repozitáři: https://github.com/Elemenous-Studio/Prijimacky_v_pohode/issues

---

## English Part

1. Overview

In this document I describe the administrative procedures I use for the "Prijimacky v pohode" project: deployment, database management, user management, backups and basic troubleshooting.

2. Requirements

- Node.js (recommended LTS)
- PostgreSQL (or Supabase)
- Docker and docker-compose (for local or production deployment)

3. Installation & Configuration

I clone the repository:

    git clone https://github.com/Elemenous-Studio/Prijimacky_v_pohode.git
    cd Prijimacky_v_pohode

I inspect the repository root (`package.json`, `supabase/`, `docker-compose.yml`) and install dependencies:

    npm install

I create an environment file (`.env` or `.env.local`) from the provided example (`.env.example`) and set required variables (DB_URL, SUPABASE_URL, SUPABASE_ANON_KEY, SECRET_KEY, etc.).

4. Database & Migrations

- If I use Supabase, I manage migrations in `supabase/migrations/`.
- I run migrations using the project's tooling (Supabase CLI or custom scripts).

5. Running in Development

I start development mode with:

    npm run dev

The app should be available at http://localhost:8080 (check `package.json`/Vite config for exact port).

6. Docker – Build & Deploy

To build the image I use:

    docker build -t prijimacky-v-pohode:latest .

To run with docker-compose:

    docker-compose up --build -d

7. User & Role Management

- The system uses roles (`Admin`, `Teacher`, `Student`). As an admin I have full access to users, tests and settings.
- I manage users via the admin UI (if present) or directly in the database/Supabase Auth.

8. Backups & Restore

- I regularly export the database (pg_dump or Supabase export) and store backups off-server.
- I test restores in an isolated environment.

9. Monitoring & Logging

- I review server logs and Supabase functions for errors.
- I integrate automatic error reporting (Sentry, Logflare) if needed.

10. Security

- I keep sensitive environment variables out of VCS.
- I regularly update dependencies and security patches.
- I restrict production DB access with firewalls and role-based controls.

11. Support

- I report or track issues here: https://github.com/Elemenous-Studio/Prijimacky_v_pohode/issues

12. Popis funkcí a architektury systému

### Architektura celého systému

```
┌─────────────────────────────────────────────────────────────┐
│                        FRONTEND                              │
│  React + TypeScript + Vite + Tailwind CSS + Shadcn UI      │
├─────────────────────────────────────────────────────────────┤
│ ├─ Veřejné stránky      ├─ Učitel          ├─ Administrátor │
│ │  • Úvodní stránka     │  • Dashboard      │  • Dashboard   │
│ │  • Přihlášení         │  • Třídy          │  • Uživatelé   │
│ │  • Registrace         │  • Testy          │  • Třídy       │
│ ├─ Student             │  • Povolení       │  • Výsledky    │
│ │  • Dashboard          │  • Otázky         │  • Audit logy  │
│ │  • Dostupné testy     │  • Tagy           │  • Tagování    │
│ │  • Výsledky           │                   │                │
│ └─ Editace testů                           │                │
│    • Editor otázek      └─────────────────────────────────────┘
│    • Náhled teston
│    • Tisk / PDF export
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                     API VRSTVA (Supabase)                    │
│  ├─ Autentizace (Auth)  ├─ Databáze (PostgreSQL)            │
│  ├─ Storage (Soubory)   ├─ Edge Functions (Backend logika)   │
│  └─ Real-time (WebSocket)                                    │
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                    DATABÁZE (PostgreSQL)                     │
│  ├─ users            ├─ tests          ├─ test_results      │
│  ├─ questions        ├─ question_tags  ├─ audit_logs        │
│  ├─ classes          ├─ class_members  ├─ class_tests       │
│  └─ user_roles                                               │
└─────────────────────────────────────────────────────────────┘
```

### Klíčové funkce a moduly

#### 🔐 **Autentizace a správa uživatelů**

**Soubory:** `Auth.tsx`, `AdminUsers.tsx`

| Funkce                       | Popis                                           | Details                                                                                                |
| ---------------------------- | ----------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Přihlášení (Login)**       | Uživatelé se přihlašují pomocí e-mailu a hesla  | Integrováno se Supabase Auth; automatické ověření relace; session management                           |
| **Registrace**               | Nový uživatel si vytvoří účet s přiřazením kódu | Vyžaduje registrační kód pro ověření role; kód se přiřadí při vytvoření; uživatel je propojený s rolí  |
| **Správa rolí**              | Přiřazování rolí (Admin, Teacher, Student)      | Roli lze měnit v admin panelu; ProtectedRoute kontroluje oprávnění; každá role má samostatný dashboard |
| **Reset hesla**              | Obnovení zapomenutého hesla\*\*                 | Supabase posílá e-mail s odkazem; uživatel si nastaví nové heslo                                       |
| **Odhlášení (Logout)**       | Bezpečné ukončení relace                        | Smazání session tokenu; přesměrování na přihlašovací stránku                                           |
| **Správa uživatelů (Admin)** | Přidání, úprava, smazání uživatelů              | Hromadné operace; filtrování dle role; možnost změny role a hesla                                      |

**Klíčové komponenty:**

- `ProtectedRoute.tsx` - ověřuje roli uživatele a kontroluje přístup
- Supabase Auth - spravuje autentizaci a session

---

#### 📝 **Správa testů a editor testů**

**Soubory:** `TestLibrary.tsx`, `TestSetup.tsx`, `TestEditor.tsx`

| Funkce                | Popis                                                         | Details                                                                                 |
| --------------------- | ------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| **Vytvoření testu**   | Nový test se vytvoří v TestSetup s názvem, předmětem, popisem | Vygeneruje se unique test ID; nastaví se časový limit; možnost označit jako CERMAT test |
| **Editor testů**      | Přidávání, úprava a řazení otázek v testu                     | Drag-and-drop rozhraní; náhled otázek; podpora více typů otázek                         |
| **Náhled testu**      | Zobrazení testu v režimu pro studenta                         | Funkční tlačítka, časovače, označování otázek; Export do PDF                            |
| **Export PDF**        | Vygenerování PDF s testem nebo klíčem odpovědí                | Stránkování; formátování dle CERMAT standardu; možnost tisku                            |
| **Duplikování testu** | Vytvoření kopie existujícího testu                            | Zachová všechny otázky; vytvoří nový test ID; můžete editovat kopii                     |
| **Smazání testu**     | Odstranění testu z knihovny                                   | Permanentní smazání; není možné obnovit                                                 |
| **Filtrování testů**  | Hledání a třídění testů dle předmětu, data                    | Full-text search; filtr dle počtu otázek                                                |

**Klíčové komponenty:**

- `TestLibrary.tsx` - seznam všech testů s operacemi
- `TestSetup.tsx` - formulář pro vytvoření nového testu
- `TestEditor.tsx` - editor otázek s drag-and-drop
- `TestPreview.tsx` - náhled a export PDF

---

#### ❓ **Správa otázek a tagy**

**Soubory:** `QuestionBank.tsx`, `TagsManager.tsx`

| Funkce                 | Popis                                         | Details                                                            |
| ---------------------- | --------------------------------------------- | ------------------------------------------------------------------ |
| **Knihovna otázek**    | Centrální úložiště všech otázek               | Filtrování dle typu, testu, tagu; vyhledávání; import/export       |
| **Vytvoření otázky**   | Přidání nové otázky s textem, typu, odpověďmi | Podpora MCQ, krátké odpovědi, dlouhé odpovědi                      |
| **Tagy (štítky)**      | Kategorizace otázek pro lepší organizaci      | Lze přiřadit více tagů; filtrování dle tagů; vytváření nových tagů |
| **Správce tagů**       | Přidání, úprava, smazání tagů                 | Přehled všech tagů; počet otázek s daným tagem                     |
| **Vyhledávání otázek** | Full-text search v obsahu otázek              | Vyhledávání dle textu otázky; filtrování dle typu                  |

**Klíčové komponenty:**

- `QuestionBank.tsx` - seznam otázek s filtry
- `TagsManager.tsx` - správa tagů a jejich přiřazení

---

#### 👥 **Správa tříd a studentů**

**Soubory:** `AdminClasses.tsx`, `TeacherClasses.tsx`

| Funkce                          | Popis                                  | Details                                                                    |
| ------------------------------- | -------------------------------------- | -------------------------------------------------------------------------- |
| **Vytvoření třídy (Admin)**     | Přidání nové třídy do systému          | Přiřazení učitele; název a popis třídy                                     |
| **Přidání studentů**            | Přidání studentů do třídy              | Hromadné přidání; jednotlivé přidání; filtrování dle existujících studentů |
| **Přiřazení testu třídě**       | Zadání testu pro skupinu studentů      | Nastavení termínu; automatické přiřazení všem ve třídě                     |
| **Správa členů třídy (Učitel)** | Učitel spravuje své třídy              | Přidávání/odebírání studentů; změny v členství                             |
| **Smazání třídy**               | Odstranění třídy z systému             | Permanentní; všichni student si mohou vidět výsledky                       |
| **Export seznamu studentů**     | Vygenerování seznamu studentů ve třídě | CSV export; tisk; seřazení dle jména                                       |

**Klíčové komponenty:**

- `AdminClasses.tsx` - admin správa všech tříd
- `TeacherClasses.tsx` - učitel spravuje své třídy
- Dialogy pro přidávání studentů a testů

---

#### 📊 **Testy, výsledky a analýza**

**Soubory:** `StudentDashboard.tsx`, `AdminTestResults.tsx`

| Funkce                        | Popis                                        | Details                                                              |
| ----------------------------- | -------------------------------------------- | -------------------------------------------------------------------- |
| **Přiřazené testy (Student)** | Zobrazení testů zadaných třídu studenta      | Filtr dle stavu (Neotevřeno, V čekání, Dokončeno); zobrazení termínu |
| **Výsledky testu**            | Skóre, odpovědi, správné/špatné odpovědi     | Detailní přehled; vizuální zpětná vazba                              |
| **Historii testů**            | Přehled všech testů které student absolvoval | Počet pokusů; nejlepší skóre; trendy pokroku                         |
| **Správa výsledků (Admin)**   | Přehled všech výsledků všech testů           | Filtrování dle třídy, studenta, testu; export do CSV                 |
| **Analýza pokroku**           | Grafy a statistiky pokroku studenta          | Lineární graf přesnosti; rozpad dle tématu                           |
| **Porovnání tříd**            | Srovnání výkonnosti mezi třídami             | Průměrné skóre; procento úspěšnosti                                  |

**Klíčové komponenty:**

- `StudentDashboard.tsx` - studentský dashboard s testy a výsledky
- `AdminTestResults.tsx` - admin přehled všech výsledků
- Recharts grafy pro vizualizaci

---

#### 🔧 **Správa oprávnění a auditu**

**Soubory:** `TeacherPermissions.tsx`, `AuditLog.tsx`

| Funkce                  | Popis                                    | Details                                         |
| ----------------------- | ---------------------------------------- | ----------------------------------------------- |
| **Oprávnění učitele**   | Nastavení co může dělat konkrétní učitel | Vytváření testů, správa tříd, přístup k otázkám |
| **Audit logy**          | Záznamy všech akcí v systému             | Kdo, co, kdy, kde; filtrování dle typu akce     |
| **Filtrování logů**     | Hledání specifických akcí                | Dle uživatele, typu akce, datumu                |
| **Export logů**         | Vygenerování reportu akcí                | CSV export; tisk                                |
| **Mazání starých logů** | Cleanup velmi starých záznamů            | Dle data; bezpečné smazání                      |

**Klíčové komponenty:**

- `TeacherPermissions.tsx` - nastavení oprávnění
- `AuditLog.tsx` - přehled a export logů

---

### 📱 **Role a příslušné stránky**

#### Admin (Administrátor)

- `AdminDashboard` - Přehled systému, statistiky
- `AdminUsers` - Správa všech uživatelů
- `AdminClasses` - Správa všech tříd
- `AdminTestResults` - Analýza výsledků všech studentů
- `TeacherPermissions` - Nastavení práv učitelů
- `AuditLog` - Audit záznam akcí

#### Teacher (Učitel)

- `TeacherDashboard` - Přehled tříd a testů
- `TeacherClasses` - Správa vlastních tříd
- `TestLibrary` - Přístup k testům (vytváření/editace)
- `TestEditor` - Editace otázek v testech
- `QuestionBank` - Přístup k otázkám
- `TagsManager` - Správa tagů

#### Student (Student)

- `StudentDashboard` - Přiřazené testy a výsledky

#### Public (Veřejné)

- `Welcome` - Úvodní stránka
- `Auth` - Přihlášení/Registrace

---

### 🗄️ **Databáze - Hlavní tabulky**

| Tabulka           | Sloupce                                    | Popis                     |
| ----------------- | ------------------------------------------ | ------------------------- |
| **users**         | id, email, name, role, created_at          | Všichni uživatelé systému |
| **questions**     | id, text, type, difficulty, test_id, tags  | Otázky v testech          |
| **tests**         | id, name, subject, description, time_limit | Testy                     |
| **test_results**  | id, user_id, test_id, score, answers, date | Výsledky studentů         |
| **classes**       | id, name, teacher_id, description          | Třídy                     |
| **class_members** | id, class_id, user_id                      | Členství ve třídě         |
| **audit_logs**    | id, user_id, action, details, timestamp    | Záznam akcí               |
| **question_tags** | id, question_id, tag_id                    | Propojení tagů            |

---

### 🔌 **Integrace a Supabase funkce**

- **Edge Functions**: `create-user` - serverová funkce pro vytvoření nového uživatele
- **Storage**: Uložení PDF a exportů
- **Real-time**: WebSocket pro live updates
- **Auth hooks**: RPC funkce pro správu rolí a registračních kódů

---

### 🛠️ **Konfigurace a nasazení**

**Frontend:**

- React + TypeScript + Vite
- Tailwind CSS + Shadcn UI components
- React Query pro správu dat
- React Router pro navigaci

**Backend:**

- Supabase PostgreSQL (databáze)
- Supabase Auth (autentizace)
- Edge Functions (serverová logika)
- Storage (soubory a PDF)

**Deployment:**

- Docker + docker-compose pro kontejnerizaci

---

12. System functions and architecture

### System Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        FRONTEND                              │
│  React + TypeScript + Vite + Tailwind CSS + Shadcn UI      │
├─────────────────────────────────────────────────────────────┤
│ ├─ Public Pages       ├─ Teacher           ├─ Administrator  │
│ │  • Welcome          │  • Dashboard        │  • Dashboard    │
│ │  • Authentication   │  • Classes          │  • Users        │
│ │  • Registration     │  • Tests            │  • Classes      │
│ ├─ Student           │  • Permissions      │  • Results      │
│ │  • Dashboard         │  • Questions        │  • Audit Logs   │
│ │  • Available Tests   │  • Tags             │  • Tags         │
│ │  • Results           │                     │                 │
│ └─ Test Editing                            │                 │
│    • Question Editor   └─────────────────────────────────────┘
│    • Test Preview
│    • Print / PDF Export
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                     API Layer (Supabase)                     │
│  ├─ Authentication    ├─ Database (PostgreSQL)               │
│  ├─ Storage (Files)   ├─ Edge Functions (Backend Logic)      │
│  └─ Real-time (WebSocket)                                    │
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                   Database (PostgreSQL)                      │
│  ├─ users            ├─ tests          ├─ test_results      │
│  ├─ questions        ├─ question_tags  ├─ audit_logs        │
│  ├─ classes          ├─ class_members  ├─ class_tests       │
│  └─ user_roles                                               │
└─────────────────────────────────────────────────────────────┘
```

### Key Features and Modules

#### 🔐 **Authentication and User Management**

**Files:** `Auth.tsx`, `AdminUsers.tsx`

| Feature                     | Description                                     | Details                                                                                                   |
| --------------------------- | ----------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **Login**                   | Users log in with email and password            | Supabase Auth integration; automatic session verification; session management                             |
| **Registration**            | New user creates account with registration code | Requires registration code for role verification; code assigned at creation; user linked to role          |
| **Role Management**         | Assigning roles (Admin, Teacher, Student)       | Roles can be changed in admin panel; ProtectedRoute checks permissions; each role has dedicated dashboard |
| **Password Reset**          | Recovery of forgotten password                  | Supabase sends email with link; user sets new password                                                    |
| **Logout**                  | Secure session termination                      | Deletes session token; redirects to login page                                                            |
| **User Management (Admin)** | Add, edit, delete users                         | Bulk operations; filtering by role; ability to change role and password                                   |

**Key Components:**

- `ProtectedRoute.tsx` - verifies user role and controls access
- Supabase Auth - manages authentication and session

---

#### 📝 **Test Management and Editor**

**Files:** `TestLibrary.tsx`, `TestSetup.tsx`, `TestEditor.tsx`

| Feature              | Description                                                   | Details                                                                        |
| -------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| **Test Creation**    | New test created in TestSetup with name, subject, description | Generates unique test ID; sets time limit; option to mark as CERMAT test       |
| **Test Editor**      | Add, edit, and order questions in test                        | Drag-and-drop interface; question preview; support for multiple question types |
| **Test Preview**     | Display test in student mode                                  | Functional buttons, timers, question flagging; PDF Export                      |
| **PDF Export**       | Generate PDF with test or answer key                          | Pagination; CERMAT standard formatting; print option                           |
| **Test Duplication** | Create copy of existing test                                  | Preserves all questions; creates new test ID; can edit copy                    |
| **Test Deletion**    | Remove test from library                                      | Permanent deletion; cannot be recovered                                        |
| **Test Filtering**   | Search and sort tests by subject, date                        | Full-text search; filtering by question count                                  |

**Key Components:**

- `TestLibrary.tsx` - list of all tests with operations
- `TestSetup.tsx` - form for creating new test
- `TestEditor.tsx` - question editor with drag-and-drop
- `TestPreview.tsx` - preview and PDF export

---

#### ❓ **Question Management and Tags**

**Files:** `QuestionBank.tsx`, `TagsManager.tsx`

| Feature               | Description                                     | Details                                                           |
| --------------------- | ----------------------------------------------- | ----------------------------------------------------------------- |
| **Question Bank**     | Central repository of all questions             | Filtering by type, test, tag; search; import/export               |
| **Question Creation** | Add new question with text, type, answers       | Support for MCQ, short answer, long answer                        |
| **Tags (Labels)**     | Categorize questions for better organization    | Multiple tags can be assigned; filtering by tags; create new tags |
| **Tag Manager**       | Add, edit, delete tags                          | Overview of all tags; count of questions with tag                 |
| **Question Search**   | Full-text search in question content            | Search by question text; filtering by type                        |

**Key Components:**

- `QuestionBank.tsx` - list of questions with filters
- `TagsManager.tsx` - manage tags and their assignment

---

#### 👥 **Class and Student Management**

**Files:** `AdminClasses.tsx`, `TeacherClasses.tsx`

| Feature                        | Description                        | Details                                               |
| ------------------------------ | ---------------------------------- | ----------------------------------------------------- |
| **Class Creation (Admin)**     | Add new class to system            | Assign teacher; class name and description            |
| **Add Students**               | Add students to class              | Bulk add; individual add; filtering existing students |
| **Assign Test to Class**       | Assign test to group of students   | Set deadline; automatic assignment to all in class    |
| **Class Management (Teacher)** | Teacher manages their classes      | Add/remove students; membership changes               |
| **Class Deletion**             | Remove class from system           | Permanent; students can still view results            |
| **Export Student List**        | Generate list of students in class | CSV export; print; sort by name                       |

**Key Components:**

- `AdminClasses.tsx` - admin management of all classes
- `TeacherClasses.tsx` - teacher manages their classes
- Dialogs for adding students and tests

---

#### 📊 **Tests, Results and Analytics**

**Files:** `StudentDashboard.tsx`, `AdminTestResults.tsx`

| Feature                       | Description                               | Details                                                               |
| ----------------------------- | ----------------------------------------- | --------------------------------------------------------------------- |
| **Assigned Tests (Student)**  | Display tests assigned to student's class | Filter by status (Not Started, In Progress, Completed); show deadline |
| **Test Results**              | Score, answers, correct/incorrect answers | Detailed overview; visual feedback                                    |
| **Test History**              | Overview of all tests student completed   | Number of attempts; best score; progress trends                       |
| **Result Management (Admin)** | Overview of all results across all tests  | Filter by class, student, test; CSV export                            |
| **Progress Analysis**         | Graphs and statistics of student progress | Line graphs of accuracy; breakdown by topic                           |
| **Class Comparison**          | Compare performance between classes       | Average scores; success percentage                                    |

**Key Components:**

- `StudentDashboard.tsx` - student dashboard with tests and results
- `AdminTestResults.tsx` - admin overview of all results
- Recharts graphs for visualization

---

#### 🔧 **Permission Management and Audit**

**Files:** `TeacherPermissions.tsx`, `AuditLog.tsx`

| Feature                 | Description                      | Details                                           |
| ----------------------- | -------------------------------- | ------------------------------------------------- |
| **Teacher Permissions** | Set what specific teacher can do | Creating tests, managing classes, question access |
| **Audit Logs**          | Records of all actions in system | Who, what, when, where; filter by action type     |
| **Log Filtering**       | Search for specific actions      | By user, action type, date                        |
| **Log Export**          | Generate action report           | CSV export; print                                 |
| **Delete Old Logs**     | Cleanup very old records         | By date; secure deletion                          |

**Key Components:**

- `TeacherPermissions.tsx` - permission settings
- `AuditLog.tsx` - overview and log export

---

### 📱 **Roles and Corresponding Pages**

#### Admin (Administrator)

- `AdminDashboard` - System overview, statistics
- `AdminUsers` - Manage all users
- `AdminClasses` - Manage all classes
- `AdminTestResults` - Analyze results of all students
- `TeacherPermissions` - Set teacher rights
- `AuditLog` - Audit record of actions

#### Teacher (Educator)

- `TeacherDashboard` - Overview of classes and tests
- `TeacherClasses` - Manage own classes
- `TestLibrary` - Access to tests (create/edit)
- `TestEditor` - Edit questions in tests
- `QuestionBank` - Access to questions
- `TagsManager` - Manage tags

#### Student (Learner)

- `StudentDashboard` - Assigned tests and results

#### Public (Everyone)

- `Welcome` - Landing page
- `Auth` - Login/Registration

---

### 🗄️ **Database - Main Tables**

| Table             | Columns                                    | Description        |
| ----------------- | ------------------------------------------ | ------------------ |
| **users**         | id, email, name, role, created_at          | All system users   |
| **questions**     | id, text, type, difficulty, test_id, tags  | Questions in tests |
| **tests**         | id, name, subject, description, time_limit | Tests              |
| **test_results**  | id, user_id, test_id, score, answers, date | Student results    |
| **classes**       | id, name, teacher_id, description          | Classes            |
| **class_members** | id, class_id, user_id                      | Class membership   |
| **audit_logs**    | id, user_id, action, details, timestamp    | Action records     |
| **question_tags** | id, question_id, tag_id                    | Tag linkage        |

---

### 🔌 **Integrations and Supabase Functions**

- **Edge Functions**: `create-user` - server function for creating new user
- **Storage**: PDF and export storage
- **Real-time**: WebSocket for live updates
- **Auth hooks**: RPC functions for role management and registration codes

---

### 🛠️ **Configuration and Deployment**

**Frontend:**

- React + TypeScript + Vite
- Tailwind CSS + Shadcn UI components
- React Query for data management
- React Router for navigation

**Backend:**

- Supabase PostgreSQL (database)
- Supabase Auth (authentication)
- Edge Functions (server logic)
- Storage (files and PDFs)

**Deployment:**

- Docker + docker-compose for containerization

---

### **Data Flow & System Workflows**

#### Test Creation Flow

```
Teacher  TestLibrary  New Test

       TestSetup (Name, Subject, Time Limit)

       Backend: Generate Test ID

       TestEditor (Add Questions)

       PostgreSQL: Store Test
```

---

### **5 Key Use Cases**

**1. Teacher Creates & Assigns Test:** TestLibrary New Test TestEditor Assign to Class

**2. Student Takes Test:** StudentDashboard Select Test View Score

**3. Teacher Reviews Progress:** TeacherDashboard Select Class View Results Analyze Trends

**4. Admin Manages Users:** AdminDashboard AdminUsers CRUD Set Roles

**5. Admin Monitors System:** AdminDashboard AuditLog Filter & Export

---

### **Authentication & Security**

- JWT Token based authentication via Supabase
- Role-based access control (RBAC): Admin, Teacher, Student
- Password hashing with bcrypt
- Session management with auto-logout
- Audit logging of all actions

---

### **Data Storage**

- Frontend Cache: React Query (5 min TTL)
- Browser Storage: localStorage for JWT token
- Database: PostgreSQL (Supabase)
- File Storage: Supabase Storage for PDFs

---

### **Troubleshooting**

| Issue            | Solution                          |
| ---------------- | --------------------------------- |
| Test not visible | AdminClasses Add student to class |
| Login failed     | Reset password; check credentials |
| Slow loading     | Enable pagination; clear cache    |
| PDF broken       | Check browser console; update CSS |

---

**Last Updated:** January 2026
**Status:** Active Development by Elemenous Studio
