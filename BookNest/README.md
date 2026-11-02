# 🌿 BookNest — Modern Library Management System

> **A modern, full-featured Campus Library Management System built with C++17, Qt6/Qt5, SQLite, and CMake.**  
> Crafted with a warm cream and forest-green aesthetic, procedural book cover graphics, responsive sidebar navigation, and persistent database storage. Designed for academic mini-projects and college software engineering demonstrations.

---

## 📖 Table of Contents
1. [Key Features](#-key-features)
2. [Design & Aesthetics](#-design--aesthetics)
3. [Architecture & Project Structure](#-architecture--project-structure)
4. [Database Schema (SQLite)](#-database-schema-sqlite)
5. [Prerequisites & Dependencies](#-prerequisites--dependencies)
6. [Building & Running the Application](#-building--running-the-application)
7. [Feature Walkthrough](#-feature-walkthrough)
8. [Design Patterns & OOP Highlights](#-design-patterns--oop-highlights)
9. [College Viva & Presentation Tips](#-college-viva--presentation-tips)

---

## ✨ Key Features

- **Personalized Student Authentication & Profiling**:
  - Student enters their name and selects their academic year/class (1st–4th Year, Postgraduate) and course/department.
  - Automatic profile creation and persistent session recovery with existing profile switcher.
- **📚 Study Library (Curriculum-Aligned)**:
  - Dynamically filters textbooks and references based on the student's year, course, and subjects (e.g., Data Structures, Operating Systems, Engineering Math).
- **✨ Recommended for You**:
  - Algorithmic recommendation engine prioritizing books tailored to student academic level, department, and custom reading preferences.
- **🧭 Explore Books (Beyond the Syllabus)**:
  - Curated catalog of non-curriculum books across genres: Novels, Sci-Fi, World History, Astrophysics, Software Philosophy, and Biographies.
- **🔖 My Reading List**:
  - One-click bookmarking (★) allowing students to curate their personal reading wishlist for future study.
- **📜 Reading History & Circulation**:
  - Comprehensive timeline tracking every borrowed book, issue dates, due dates, return dates, overdue alerts, and direct re-issue actions.
- **📂 Collapsible Sidebar Dropdowns**:
  - **Currently Issued (N)**: Expandable drawer displaying active borrowings, due-date countdown chips ("Due in 8 days" or "⚠️ Overdue"), and an instant **Return** button.
  - **Previous Issues (N)**: Expandable drawer displaying completed borrows and historical return dates.
- **⚙️ Profile & System Settings**:
  - Edit student details, update course/year, adjust reading preference keywords, change appearance themes, and reset demo data with a single click.
- **Robust Issue & Return Transactions**:
  - Real-time stock decrement and restoration in SQLite.
  - Strict validation preventing borrowing of out-of-stock books or duplicate active issues by the same student.

---

## 🎨 Design & Aesthetics

| Component | Theme Token / Palette | Visual Purpose |
| :--- | :--- | :--- |
| **Primary Brand** | `#1F4324` (Deep Forest Green) | Headers, sidebar, primary action buttons |
| **Accent Tone** | `#C88A3E` (Warm Gold / Honey) | Foil stamps, ratings, bookmark badges |
| **Base Surface** | `#F7F5EE` (Soft Warm Paper Cream) | Main dashboard canvas, reduces eye strain |
| **Card Surface** | `#FFFFFF` with `#E2DCCE` border | Elevated rounded cards with subtle drop shadows |
| **Badges & Tags** | `#E9F0EA` (Soft Sage) | Category tags, course chips, availability indicators |

- **BookNest Logo**: Beautifully rendered top-left emblem featuring an open book nestled with an emerald leaf and gold ribbon.
- **Procedural Book Cover Art Engine (`CoverArtGenerator`)**: Dynamically paints high-resolution book covers with 3D spine illumination, foil borders, category emblems, and ribbon bookmarks without relying on external image dependencies.

---


## 💾 Database Schema (SQLite)

The SQLite database (`booknest.db`) is automatically initialized and seeded with 22 initial textbooks and literature classics on first run:

### 1. `students` Table
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `id` | INTEGER | PRIMARY KEY AUTOINCREMENT | Unique student identifier |
| `name` | TEXT | NOT NULL | Full student name |
| `academic_year`| TEXT | NOT NULL | "1st Year", "2nd Year", "3rd Year", etc. |
| `course` | TEXT | NOT NULL | Department / Degree Program |
| `preferences` | TEXT | | Comma-separated reading interests |
| `theme` | TEXT | DEFAULT 'Cream & Forest' | Visual style preference |
| `avatar_color` | TEXT | | Procedural avatar background hex |
| `created_at` | DATETIME| DEFAULT CURRENT_TIMESTAMP| Registration timestamp |

### 2. `books` Table
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `id` | INTEGER | PRIMARY KEY AUTOINCREMENT | Unique book identifier |
| `title` | TEXT | NOT NULL | Title of the publication |
| `author` | TEXT | NOT NULL | Author(s) |
| `category` | TEXT | NOT NULL | Subject / Genre tag |
| `isbn` | TEXT | UNIQUE | International Standard Book Number |
| `target_year` | TEXT | | Academic target year ("1st Year", "All", etc.) |
| `course` | TEXT | | Department curriculum target |
| `is_academic` | INTEGER| DEFAULT 1 | 1 = Study Library, 0 = Explore Books |
| `total_copies`| INTEGER| DEFAULT 3 | Total institutional copies |
| `available_copies`| INTEGER| DEFAULT 3 | Currently unborrowed copies |
| `description` | TEXT | | Overview and syllabus relevance |
| `cover_color` | TEXT | | Primary spine/cover palette |
| `cover_accent`| TEXT | | Accent foil color |
| `rating` | REAL | DEFAULT 4.5 | Average rating (1.0 - 5.0) |
| `published_year`| INTEGER| | Year of publication |
| `pages` | INTEGER| | Number of pages |

### 3. `issues` Table
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `id` | INTEGER | PRIMARY KEY AUTOINCREMENT | Circulation issue identifier |
| `student_id` | INTEGER | FOREIGN KEY (`students.id`)| Student who borrowed the book |
| `book_id` | INTEGER | FOREIGN KEY (`books.id`) | Borrowed book |
| `issue_date` | TEXT | NOT NULL (YYYY-MM-DD) | Date borrowed |
| `due_date` | TEXT | NOT NULL (YYYY-MM-DD) | Due date (14-day borrowing duration) |
| `return_date` | TEXT | (NULL if active) | Date returned to inventory |
| `status` | TEXT | DEFAULT 'ISSUED' | 'ISSUED', 'RETURNED', 'OVERDUE' |

### 4. `reading_list` Table
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `id` | INTEGER | PRIMARY KEY AUTOINCREMENT | Wishlist entry identifier |
| `student_id` | INTEGER | FOREIGN KEY (`students.id`)| Student owner |
| `book_id` | INTEGER | FOREIGN KEY (`books.id`) | Bookmarked book |
| `added_date` | TEXT | NOT NULL (YYYY-MM-DD) | Date added |

---

## 🛠 Prerequisites & Dependencies

To build and run **BookNest**, ensure the following tools are installed:

- **C++ Compiler**: Supporting **C++17** (GCC 9+, Clang 10+, or MSVC 2019+)
- **Qt Framework**: **Qt 6.x** (or **Qt 5.15+**)
  - Required Qt modules: `Core`, `Gui`, `Widgets`, `Sql` (with the standard SQLite driver)
- **CMake**: Version **3.16** or higher
- **Build System**: **Ninja** or **Make** (or Visual Studio IDE / Qt Creator)

---



## 🧭 Feature Walkthrough

### 1. Student Access & Login
- Launch the application to open the **Student Access** dialog.
- Enter your name (e.g., `Alex Rivers`) and select your Academic Year (`2nd Year`) and Department (`Computer Science & Engineering`).
- You can also choose from existing registered students in the dropdown.
- Click **Enter Library Dashboard →** to transition seamlessly to your customized interface.

### 2. Study Library
- Displays textbooks matching your current year and curriculum.
- Real-time search instantly filters books by title, author, subject, or ISBN.
- Click **Borrow** to issue the book for 14 days, decreasing available stock and updating your sidebar circulation drawers.
- Click **Info** to inspect the full overview, publication year, ISBN, page count, and inventory progress.

### 3. Recommended for You
- Automatically analyzes your academic standing and chosen reading preferences (e.g., *Algorithms, AI, Sci-Fi*) to present intelligent book recommendations.

### 4. Explore Books
- Switch to novels, history, and scientific literature unrelated to class curricula (e.g., *Dune*, *Sapiens*, *A Brief History of Time*, *1984*, *Atomic Habits*).

### 5. My Reading List
- Save books with the ☆/★ button on any card or detail dialog.
- Access saved books directly in the **My Reading List** view for quick review and one-click borrowing.

### 6. Reading History
- Displays an interactive circulation ledger detailing every book you've ever borrowed.
- Shows real-time overdue statuses, remaining days, and return timestamps.
- Features a **Return Book** button for active borrows and a **Borrow Again** button for returned books.

### 7. Collapsible Sidebar Drawers
- Click **▼ Currently Issued** in the sidebar to expand/collapse your active loans. Each card includes an inline **Return** button for quick returns.
- Click **▶ Previous Issues** to view past borrowing history without leaving your current page.

### 8. Student Settings & Demo Reset
- Modify your profile details or add new reading interest tags.
- Includes a **Reset Library to Default Catalog** button to quickly refresh the database for live demonstration.

---

## 💡 Design Patterns & OOP Highlights

This project demonstrates several essential Software Engineering design patterns:

1. **Singleton Pattern (`DatabaseManager`)**:
   - Manages a single shared SQLite connection across the entire application lifecycle, preventing database locking and ensuring thread safety.
2. **Model-View-Controller (MVC)**:
   - Data models (`Models.h`) encapsulate state and business logic.
   - UI views (`BookCardWidget`, `SidebarWidget`, `MainWindow`) display information.
   - Database and event slots control mutations and enforce constraints.
3. **Observer Pattern (Qt Signals & Slots)**:
   - Decoupled components communicate through Qt signals (`issueRequested`, `returnRequested`, `readingListToggled`).
4. **Procedural Factory (`CoverArtGenerator`)**:
   - Dynamically instantiates custom visual artifacts (book covers, logos, avatars) on demand with zero external asset dependencies.
5. **ACID Transactions (`DatabaseManager::issueBook` and `returnBook`)**:
   - Ensures atomicity by using SQLite transactions (`m_db.transaction()` and `m_db.commit()`), guaranteeing inventory counts and issue records stay synchronized.

---



*Developed with C++17 & Qt for Academic & College Mini-Project Demonstrations.*
