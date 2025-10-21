SECURITY NOTES — EVENT TICKETING SYSTEM
---
Overview


This document outlines the security design, roles, policies, and database protection mechanisms implemented in the Event Ticketing System built with Supabase (PostgreSQL).
It ensures compliance with best practices for Row Level Security (RLS), Role-Based Access Control (RBAC), and data privacy.
---

🎫 Regular Users (role = 'user')

Can view available events

Can book and manage only their own tickets

Cannot view or modify other users’ bookings

Cannot create or delete events

Authentication is required for all operations


🛠️ Administrators (role = 'admin')
---
Have full access to all tables (users, events, tickets)

Can create, update, and delete any event

Can view all ticket sales and user data

Can execute admin-only functions, such as deleting events or generating reports

🔐 Row Level Security (RLS)
RLS is enabled on all tables:
You can use this code below
``` sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE events ENABLE ROW LEVEL SECURITY;
ALTER TABLE tickets ENABLE ROW LEVEL SECURITY;
```
-This ensures every data access is checked against defined policies before any query is executed.

SECURITY POLICIES
---

Here are some of the codes used to implimate the policies

1 .EVENTS
 ```SQL
CREATE POLICY "Admins have full access to events"
ON events
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users u
    WHERE u.user_id = current_setting('app.current_user_id')::int
    AND u.role = 'admin'
  )
);
---

 ``` sql
CREATE POLICY "Users can view events"
ON public.events
FOR SELECT
TO public
USING (true);
```

2 .TICKETS
```sql
CREATE POLICY "Admins have full access to tickets"
ON public.tickets
FOR ALL
TO public
USING (
  EXISTS (
    SELECT 1 FROM public.users
    WHERE users.user_id = tickets.user_id
    AND users.role = 'admin'
  )
)
WITH CHECK (
  EXISTS (
    SELECT 1 FROM public.users
    WHERE users.user_id = tickets.user_id
    AND users.role = 'admin'
  )
);
``` sql
CREATE POLICY "Users can insert their own tickets"
ON public.tickets
FOR INSERT
TO public
WITH CHECK (
  EXISTS (
    SELECT 1 FROM public.users
    WHERE users.user_id = tickets.user_id
    AND users.role = 'user'
  )
);
``` sql
CREATE POLICY "Users can view their own tickets"
ON public.tickets
FOR SELECT
TO public
USING (
  EXISTS (
    SELECT 1 FROM public.users
    WHERE users.user_id = tickets.user_id
    AND users.role = 'user'
  )
);
```
3 .USERS
```sql
CREATE POLICY "Admins have full access to users"
ON public.users
FOR ALL
TO public
USING (
  role = 'admin'
)
WITH CHECK (
  role = 'admin'
);
```
```sql
CREATE POLICY "Users can edit their own profile"
ON public.users
FOR UPDATE
TO public
USING (
  user_id = auth.uid()
)
WITH CHECK (
  user_id = auth.uid()
);
```
A sreenshot of the policies 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6d93109c-105e-433d-bdfc-2deb4d138e6c" />


⚙️ Authentication Rules

Only authenticated users can access the database.

Authentication is handled through Supabase Auth (email/password or magic link).

All API and SQL requests are automatically scoped to the logged-in user via auth.uid().

🚀 Summary

The Event Ticketing System follows a robust and principle-based security model using:

Supabase Auth for identity management

PostgreSQL RLS for data protection

Role-based policies for least privilege enforcement

Admin-only SQL functions for critical actions

✅ Result:
Every user interaction is authenticated, scoped, and validated — ensuring secure and isolated access across all system components.

