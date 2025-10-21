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
-- Delete any event (admin-only)
SELECT delete_event('event-uuid-here');

-- Generate event sales report
SELECT * FROM get_event_sales_report();

-- Archive past events (e.g., older than 6 months)
SELECT * FROM archive_past_events();
```




### Database Structure

The database consists of three main tables — users, events, and tickets.
Each table has Row Level Security (RLS) enabled to ensure users can only access their own data, while admins have full control.

🧑‍💻 Users Table
| Column     | Type      | Description                                 |
| ---------- | --------- | ------------------------------------------- |
| id         | UUID      | Primary key (matches Supabase Auth user ID) |
| full_name  | TEXT      | User’s full name                            |
| email      | TEXT      | User email (unique)                         |
| role       | TEXT      | `'admin'` or `'user'`                       |
| created_at | TIMESTAMP | Record creation time                        |

Notes:

Admins can view and manage all users.

Regular users can only view or edit their own profile (except the role field).


🎤 Events Table

| Column      | Type      | Description                        |
| ----------- | --------- | ---------------------------------- |
| id          | UUID      | Primary key                        |
| admin_id    | UUID      | Foreign key referencing `users.id` |
| title       | TEXT      | Event title                        |
| description | TEXT      | Event description                  |
| location    | TEXT      | Venue or city name                 |
| event_date  | DATE      | Date of the event                  |
| price       | NUMERIC   | Ticket price                       |
| capacity    | INTEGER   | Total number of tickets available  |
| created_at  | TIMESTAMP | When the event was created         |

Notes:

Only admins (event organizers) can create, update, or delete events.

Regular users can view all events but not modify them.

RLS ensures users can’t insert or modify events unless they are admins.

🎫 Tickets Table

| Column      | Type      | Description                            |
| ----------- | --------- | -------------------------------------- |
| id          | UUID      | Primary key                            |
| event_id    | UUID      | Foreign key referencing `events.id`    |
| user_id     | UUID      | Foreign key referencing `users.id`     |
| quantity    | INTEGER   | Number of tickets purchased            |
| total_price | NUMERIC   | Auto-calculated as `price * quantity`  |
| status      | TEXT      | `'booked'`, `'cancelled'`, or `'used'` |
| created_at  | TIMESTAMP | Booking timestamp                      |

Notes:

Regular users can view and manage their own tickets only.

Admins can view all tickets to monitor event sales.

Policies prevent users from viewing or editing other users’ bookings.


<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🔐 Security Implementation <a name="security"></a>

This project demonstrates comprehensive database security using PostgreSQL Row Level Security (RLS) and role-based access control.

User Roles
Admin Role

Full CRUD (create, read, update, delete) permissions

Can manage all users, events, and bookings

Access to analytics and custom functions

User Role

Can only:

View available events

Book tickets for events

View and manage their own bookings only

Row Level Security Policies

Users Table

Users can view/update only their own record

Admins can view/update all users

Events Table

Admins have full access

Users can only view events

Bookings Table

Users can only view, insert, update, or delete their own bookings

Admins have full access to all bookings

Admin-Only Functions

delete_event(event_id UUID)
Deletes any event (uses SECURITY DEFINER).

get_sales_summary()
Returns total ticket sales and top events by revenue.

archive_past_events()
Moves completed events (older than today) to an archived state.

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
