<div align="center">
  
# **Resolve** 🐞

**A modern, role-based bug tracking application built with Nuxt 3 and Supabase.**

</div>

<div align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Nuxt Version](https://img.shields.io/badge/Nuxt-3.x-00DC82?logo=nuxt.js)](https://nuxt.com/)
[![Supabase](https://img.shields.io/badge/Supabase-backend-3ECF8E?logo=supabase)](https://supabase.com/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-styling-38B2AC?logo=tailwind-css)](https://tailwindcss.com/)

</div>

<br>

<div align="center">
  <img src="https://i.imgur.com/your-screenshot-url.png" alt="Resolve Dashboard Screenshot" width="800"/>
</div>

---

## ## ✨ Features

Resolve provides a comprehensive suite of tools for managing the entire lifecycle of a bug report, with distinct dashboards for each user role.

#### 👤 **Admin Dashboard**
* **Analytics Overview**: View summary cards for total users, bugs, testers, and developers.
* **Data Visualization**: See a bar graph of bug reports by their current status.
* **User Management**: **Create and delete users** directly from an animated table.
* **Bug Assignment**: **Assign any "Open" bug** to an available developer from the bug overview list.

#### 🧑‍💻 **Developer Dashboard**
* **Personalized View**: See all bugs that are "Open" or currently **assigned to you**.
* **Task Management**: **Start work** on a bug, mark it as **fixed**, or cancel progress.
* **Search & Filter**: Easily find specific bugs using a **real-time search bar** and status filters.

#### 🧪 **Tester Dashboard**
* **Bug Reporting**: A dedicated interface for **submitting new bug reports** with details like title, description, and priority.
* **Track Submissions**: View the status of all bugs you have reported.

#### 🔐 **Authentication**
* **Secure Login**: JWT-based authentication provided by Supabase.
* **Self-Registration**: Users can sign up for an account with a specific role.
* **Role-Based Access**: The application directs users to the correct dashboard based on their role after login.

---

## ## 🛠️ Tech Stack

| Technology | Purpose |
| :--- | :--- |
| **Nuxt 3** | Frontend Framework (Vue 3) |
| **Supabase** | Backend (Database, Auth, Edge Functions) |
| **Tailwind CSS** | Styling |
| **Chart.js** | Data Visualization |

---

## ## 🚀 Getting Started

Follow these instructions to get a copy of the project up and running on your local machine.

### Prerequisites

* Node.js (v18 or later)
* A free [Supabase](https://supabase.com/) account

### Installation & Setup

1.  **Clone the repository:**
    ```bash
    https://github.com/Sanket3212/Resolve.git
    cd resolve
    ```

2.  **Install dependencies:**
    ```bash
    npm install
    ```

3.  **Set up your Supabase project:**
    * Go to [supabase.com](https://supabase.com/) and create a new project.
    * Navigate to **Project Settings** > **API**.
    * Find your **Project URL** and **`anon` public key**.

4.  **Configure Environment Variables:**
    * Create a `.env` file in the root of your project by copying the example file:
        ```bash
        cp .env.example .env
        ```
    * Open the `.env` file and add your Supabase URL and Key:
        ```env
        SUPABASE_URL="YOUR_SUPABASE_URL"
        SUPABASE_KEY="YOUR_SUPABASE_ANON_KEY"
        ```

5.  **Set up the Supabase Database:**
    * Go to the **SQL Editor** in your Supabase dashboard.
    * Run the schema script from the [Supabase Schema](#-supabase-schema) section below to create the necessary tables.

6.  **Run the development server:**
    ```bash
    npm run dev
    ```
    The application should now be running on `http://localhost:3000`.

---

## ## 💾 Supabase Schema

Run the following SQL queries in your Supabase SQL Editor to set up the database tables.

### 1. `profiles` Table
This table stores user data and is linked to the `auth.users` table.

```sql
-- Create the profiles table
CREATE TABLE public.profiles (
  id uuid NOT NULL,
  updated_at timestamp with time zone NULL,
  name character varying NULL,
  email character varying NULL,
  role character varying NULL,
  CONSTRAINT profiles_pkey PRIMARY KEY (id),
  CONSTRAINT profiles_id_fkey FOREIGN KEY (id) REFERENCES auth.users(id) ON DELETE CASCADE
);

-- Set up Row Level Security for profiles
ALTER TABLE public.profiles ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Public profiles are viewable by everyone." ON public.profiles FOR SELECT USING (true);
CREATE POLICY "Users can insert their own profile." ON public.profiles FOR INSERT WITH CHECK (auth.uid() = id);
CREATE POLICY "Users can update their own profile." ON public.profiles FOR UPDATE USING (auth.uid() = id);
