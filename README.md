# 📗 Table of Contents

- [📖 About the Project](#about-project)
  - [🛠 Built With](#built-with)
    - [Tech Stack](#tech-stack)
    - [Key Features](#key-features)
- [💻 Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Setup](#setup)
  - [Install](#install)
  - [Usage](#usage)
  - [Database Structure](#database-structure)
- [🔐 Security Implementation](#security)
  - [User Roles](#user-roles)
  - [Row Level Security Policies](#row-level-security-policies)
  - [Admin-Only Functions](#admin-only-functions)
- [👥 Authors](#authors)
- [🔭 Future Features](#future-features)
- [⭐️ Show your support](#support)
- [🙏 Acknowledgements](#acknowledgements)
- [📝 License](#license)

---

# 📖 Data Fundamentals Final Project <a name="about-project"></a>

This  is a written version the README with all the sections adapted for an Event Management and Ticketing database which   includes event listings, users, and bookings, plus admin control and RLS (Row Level Security).

This is a secure event ticketing platform built using Supabase (PostgreSQL). It allows users to browse events, purchase tickets, and manage bookings  while administrators can manage events, users, and sales analytics.

The project demonstrates Row Level Security (RLS), Role-Based Access Control (RBAC), and secure database design principles to ensure users only access their own data.


## 🛠 Built With <a name="built-with"></a>

### Tech Stack <a name="tech-stack"></a>


<details>
<summary>Database</summary>
  <ul>
    <li><a href="https://www.postgresql.org/">PostgreSQL 15+</a></li>
  </ul>
</details>

<details>
<summary>Security</summary>
  <ul>
    <li>Row Level Security (RLS)</li>
    <li>Role-Based Access Control (RBAC)</li>
    <li>Supabase Auth</li>
  </ul>
</details>

### Key Features <a name="key-features"></a>

1. 🎟️ Event Ticketing System

A relational database structure that manages users, events, and tickets — allowing users to browse events, purchase tickets, and manage their bookings securely.

2. 👥 Role-Based Access Control (RBAC)

Implements two distinct roles:

Admin: Has full CRUD (Create, Read, Update, Delete) access to all tables (events, tickets, and users).

User: Can only view and modify their own data — ensuring data isolation and privacy.

3. 🛡️ Row Level Security (RLS)

Every table (users, events, tickets) is protected by Row Level Security policies, ensuring users only see or modify records associated with their account.

4. 🔐 Supabase Auth Integration

Authentication is managed through Supabase Auth using email/password or magic links.
Only verified and authenticated users can interact with the database.

5. ⚙️ Admin-Only Functions

Includes custom SQL functions such as:

delete_event(event_id) – Admin-only deletion of events.

view_user_activity() – Summarizes user ticket purchases and event participation.

These are protected with SECURITY DEFINER to ensure only admins can execute them.

6. 📊 Secure Data Management

Implements the principle of least privilege, ensuring users have the minimal permissions needed to perform their tasks while maintaining system integrity.

7. 🗂️ Well-Structured Database Schema

users → Stores user details and roles

events → Stores event information

tickets → Links users to events and manages ticket purchases

Each table contains appropriate foreign keys and timestamps for auditability.
<p align="right">(<a href="#readme-top">back to top</a>)</p>

💻 Getting Started <a name="getting-started"></a>

This project is designed to demonstrate database security implementation in Supabase using an Event Ticketing System.
It builds on the database created in your previous project — you will use the same tables but focus on adding user roles, Row Level Security (RLS), and access control policies.

🧩 Prerequisites

To run and test this project, you need:

A Supabase
 account

Access to your existing Supabase project from the Data Tools Final Project

Basic understanding of SQL and PostgreSQL

The Supabase SQL Editor or any SQL client (e.g., DBeaver, pgAdmin)

⚙️ Setup

Open Your Existing Supabase Project

Log in to Supabase Dashboard

Open the project where your tables (users, events, tickets) already exist

Enable Row Level Security (RLS)

Go to Table Editor → Select a table → Row Level Security

Toggle Enable RLS for all three tables (users, events, tickets)

Create User Roles

In your users table, ensure there’s a role column with values 'admin' or 'user'

Example SQL: 

```sql
ALTER TABLE users ADD COLUMN role TEXT DEFAULT 'user';
```
Add Row Level Security Policies

Open the SQL Editor in Supabase

Paste and run your security policies for each table:

Users can only view or edit their own data

Admins have full access to all records

Users can insert and view only their own tickets

Admins can manage all events and tickets

(Example policies are included later in the security notes.md)

Enable Authentication

Go to Authentication → Providers

Enable Email/Password or Magic Link

Ensure that only authenticated users can interact with the database

▶️ Usage
For Regular Users:

Sign up or log in via Supabase Auth

Your user record is automatically created with role = 'user'

You can:

Browse available events

Book tickets for events

View and manage (update/cancel) your own tickets

You cannot access or modify:

Other users’ profiles

Events or tickets created by other users

For Admins:

Users with role = 'admin' in the users table have full access

Admins can:

Create, update, and delete any event

View all users and ticket sales

Manage event pricing, capacity, and availability

Execute admin-only SQL functions, such as:
 ```sql
 DELETE FROM tickets WHERE status = 'cancelled';
```

### Database Structure

The database consists of three main tables — users, events, and tickets.
Each table has Row Level Security (RLS) enabled to ensure users can only access their own data, while admins have full control.

🧑‍💻 Users Table

| Column     | Type                      | Description                  |
| ---------- | ------------------------- | ---------------------------- |
| user_id    | `serial primary key`      | Unique user identifier       |
| email      | `text unique not null`    | User’s email address         |
| full_name  | `text`                    | User’s full name             |
| role       | `text`                    | Either `'admin'` or `'user'` |
| created_at | `timestamp default now()` | When the account was created |

🎤 Events Table

| Column     | Type                                | Description                 |
| ---------- | ----------------------------------- | --------------------------- |
| event_id   | `serial primary key`                | Unique ID for each event    |
| event_name | `text`                              | Name of the event           |
| event_date | `date`                              | When the event will happen  |
| location   | `text`                              | Where the event is held     |
| price      | `numeric(10,2)`                     | Default ticket price        |
| created_by | `integer references users(user_id)` | Admin who created the event |
| created_at | `timestamp default now()`           | When event was created      |


🎫 Tickets Table

| Column      | Type                                  | Description                      |
| ----------- | ------------------------------------- | -------------------------------- |
| ticket_id   | `serial primary key`                  | Unique ticket number             |
| event_id    | `integer references events(event_id)` | The event this ticket belongs to |
| user_id     | `integer references users(user_id)`   | The user who bought the ticket   |
| seat_number | `text`                                | Optional seat number or category |
| status      | `text`                                | e.g., `'active'`, `'cancelled'`  |
| created_at  | `timestamp default now()`             | Purchase date                    |

🔐 User Roles & Permissions Summary
🧑‍💻 1. Users Table
| Role      | Access Level                                   | Description                                                                                              |
| --------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| **Admin** | ✅ Full access (SELECT, INSERT, UPDATE, DELETE) | Can manage all user accounts, update roles, and remove inactive users.                                   |
| **User**  | 🔍 Read-only (SELECT, UPDATE own record)       | Can view and update their own profile information, but **cannot** change their role or view other users. |

🎤 2. Events Table
| Role      | Access Level               | Description                                                                        |
| --------- | -------------------------- | ---------------------------------------------------------------------------------- |
| **Admin** | ✅ Full access              | Can create, update, and delete any event. Also manages pricing and event capacity. |
| **User**  | 🔍 Read-only (SELECT only) | Can view all available events but **cannot** modify or delete them.                |

🎟️ 3. Tickets Table
| Role      | Access Level      | Description                                                                                                                           |
| --------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| **Admin** | ✅ Full access     | Can view, update, and delete any ticket — useful for handling refunds, cancellations, or fraud.                                       |
| **User**  | ✏️ Limited access | Can create (book) their own tickets and view, update, or cancel only their own bookings. Cannot see tickets belonging to other users. |



## 👥 Authors <a name="authors"></a>

👤 **Author1**

- GitHub: https://github.com/nmargaret384-creator
- LinkedIn: www.linkedin.com/in/wanjikunyambura
  




## 🔭 Future Features <a name="future-features"></a>
🚀 As this project evolves, the following enhancements will be added to improve user experience and system scalability:

Payment Integration – Connect ticket purchases with trusted payment gateways (e.g., Stripe or PayPal).

  1.Email Notifications – Automatically send confirmation emails for successful ticket purchases or event updates.

  2.Event Categories & Filters – Enable users to browse events by category (Music, Sports, Tech, etc.).

  3.Mobile Optimization – Improve responsive design for seamless access on mobile and tablet devices.

  4.User Reviews & Ratings – Allow users to share feedback and rate events they’ve attended.

🙏 Acknowledgements <a name="acknowledgements"></a>

Special thanks to:

The Supabase Team for providing a powerful, developer-friendly backend platform.

The PostgreSQL Community for comprehensive documentation on RLS (Row Level Security) and role-based access control.

Data Fundamentals Instructors, for guiding secure database design principles and real-world implementation.

Everyone who contributed feedback and testing during the development phase.

📝 License <a name="license"></a>

This project is MIT Licensed — meaning you’re free to use, modify, and distribute it with proper credit to the author.


<p align="right">(<a href="#readme-top">back to top</a>)</p>
