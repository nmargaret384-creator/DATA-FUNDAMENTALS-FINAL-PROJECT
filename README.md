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
- [🤝 Contributing](#contributing)
- [⭐️ Show your support](#support)
- [🙏 Acknowledgements](#acknowledgements)
- [❓ FAQ](#faq)
- [📝 License](#license)

---

# 📖 Data Fundamentals Final Project <a name="about-project"></a>

This  is a written version of your README with all the sections adapted for an Event Management and Ticketing database which   includes event listings, users, and bookings, plus admin control and RLS (Row Level Security).

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

🎟 Event Management – Admins can create, update, and delete events

💳 Ticket Booking – Users can purchase and manage their own event tickets

🔐 RLS & RBAC – Secure access: users see only their bookings; admins see all data

📅 Event Catalog – Dynamic list of events (name, date, location, price)

📊 Admin Dashboard Functions – View sales, top events, and user activity

🛡️ Least Privilege Principle – Every role has only the access it needs

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 💻 Getting Started <a name="getting-started"></a>

This project is designed to be deployed on Supabase. Follow these steps to set up your own instance.

### Prerequisites

To run this project you need:

- A [Supabase](https://supabase.com/) account 
- Basic understanding of SQL and PostgreSQL
- A SQL client or the Supabase SQL Editor

  

### Setup

 **Create a Supabase Project**
   - Go to [Supabase Dashboard](https://app.supabase.com/)
   - Click "New Project"
   - Choose an organization of your own  and fill in project details

     


  ### Install

  1.**Execute the Database Schema**
 
1.Create a Supabase Project
 
2.Log into Supabase and click “New Project”

3.Name your project “Event Ticketing”

4.Copy your connection string (you’ll use it later)

2. **Verify Table Creation**
   - Go to Table Editor in Supabase
   - Confirm you see: `users`, `projects`, and `tasks` tables
   - Check that sample data is populated (5+ rows per table)

3. **Enable Authentication**
   - Navigate to Authentication settings
   - Enable Email/Password or Magic Link authentication
   - Configure email templates as needed

### Usage

#### For Regular Users:

Sign up through Supabase Auth

Your user record is automatically created with role = 'user'

You can browse events, book tickets, and view your own ticket history

You can update or cancel your own ticket bookings

❌ You cannot view or modify other users’ tickets or events you didn’t create

#### For Event Organizers (Admins):

Users with role = 'admin' in the users table have full access

Can create, update, and delete events

Can view all users and all ticket sales

Can manage event capacity, pricing, and availability

Can execute admin-only SQL functions, such as:

```sql
DELETE FROM tickets
WHERE status = 'cancelled';
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

Security Features
Enable Authentication

Go to your Supabase dashboard → Authentication → Providers

Enable Email/Password sign-in (or Magic Link if preferred)

Go to Policies for each table and make sure only authenticated users can access data

```sql
-- Example for events table
CREATE POLICY "Only authenticated users can view events"
ON events
FOR SELECT
USING (auth.role() = 'authenticated');
```
This ensures that anonymous visitors (not logged in) cannot see any data.


## 👥 Authors <a name="authors"></a>

👤 **PetitKwoba**

- GitHub: [@PetitKwoba](https://github.com/PetitKwoba)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🔭 Future Features <a name="future-features"></a>
🔭 Future Features <a name="future-features"></a>

 Payment Integration – Link ticket purchases with payment gateways

 Email Notifications – Send confirmations for ticket purchases

 Event Categories – Filter events by type (music, sports, etc.)

 Mobile Optimization – Build a responsive frontend

 User Reviews – Allow feedback on attended events

🙏 Acknowledgements <a name="acknowledgements"></a>

Thanks to the Supabase team for making secure app development accessible

PostgreSQL community for RLS and RBAC documentation

Data Fundamentals instructors for teaching secure database design

📝 License <a name="license"></a>

This project is MIT
 licensed.


<p align="right">(<a href="#readme-top">back to top</a>)</p>
