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

### User Roles

#### Admin Role
- **Full Access**: Can view, insert, update, and delete all data in all tables
- **Administrative Functions**: Can execute admin-only custom functions
- **User Management**: Can manage user accounts and roles

#### User Role  
- **Restricted Access**: Can only view and modify their own data
- **Data Isolation**: Cannot access other users' projects or tasks
- **Role Protection**: Cannot change their own role to admin

### Row Level Security Policies

All tables have RLS enabled with specific policies:

#### Users Table Policies
- ✅ Users can view their own profile
- ✅ Users can update their own profile (except role)
- ✅ Admins can view all users
- ✅ Admins have full access to manage users

#### Projects Table Policies
- ✅ Users can view/create/update/delete their own projects
- ✅ Admins have full access to all projects

#### Tasks Table Policies
- ✅ Users can view/create/update/delete their own tasks
- ✅ Admins have full access to all tasks

### Admin-Only Functions

1. **delete_project(project_id UUID)**
   - Deletes any project regardless of ownership
   - Uses SECURITY DEFINER for elevated privileges

2. **get_user_statistics()**
   - Returns aggregated statistics about users and their activity
   - Useful for admin dashboards and reporting

3. **archive_old_projects()**
   - Automatically archives projects completed over 90 days ago
   - Returns count of archived projects

For detailed security documentation, see [security_notes.md](./security_notes.md).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 👥 Authors <a name="authors"></a>

👤 **PetitKwoba**

- GitHub: [@PetitKwoba](https://github.com/PetitKwoba)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🔭 Future Features <a name="future-features"></a>

Potential enhancements for this project:

- [ ] **Audit Logging**: Add database triggers to track all admin actions for compliance
- [ ] **Two-Factor Authentication**: Implement 2FA requirement for admin accounts
- [ ] **Advanced Reporting**: Create additional statistical functions for project analytics
- [ ] **Data Export**: Add admin functions to export data in various formats
- [ ] **Email Notifications**: Set up automated notifications for project updates

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->

## 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome!

Feel free to check the [issues page](../../issues/).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## ⭐️ Show your support <a name="support"></a>

If you found this project helpful in understanding database security and Row Level Security implementation, please give it a ⭐️!

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGEMENTS -->

## 🙏 Acknowledgments <a name="acknowledgements"></a>

- Thanks to the Supabase team for excellent documentation on RLS
- PostgreSQL community for robust security features
- Data Fundamentals course instructors for project guidance

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- FAQ -->

## ❓ FAQ <a name="faq"></a>

**How do I make a user an admin?**

Update the user's role in the users table (requires admin access):
```sql
UPDATE users SET role = 'admin' WHERE email = 'user@example.com';
```

**Why can't I see other users' data?**

This is by design! Row Level Security ensures users can only access their own data. Only admin users can see all data.

**How do I test the security policies?**

Sign in as different users (admin and regular user) and try to access various data. Regular users should only see their own projects and tasks.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->

## 📝 License <a name="license"></a>

This project is [MIT](./LICENSE) licensed.

<p align="right">(<a href="#readme-top">back to top</a>)</p>
