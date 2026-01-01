SAIYIDAH NAFISAH BINTI ROSLI
2024287408
CDCS2703B
SIR MUHAMMAD ATIF BIN RAMLAN
In this lab exercise, I used Angular CLI with Supa base and Firebase connectivity to create a real-time chat application. The goal of this project is to introduce the idea of contemporary web application development, which includes database management, frontend, backend, and authentication. I gained an understanding of how various technologies can be integrated to create responsive, real-time apps through this exercise. 
<!-- ## Database Table Schema -->
## users table

* id (uuid)
* full_name (text)
* avatar_url (text)

## Creating a users table

```sql
CREATE TABLE public.users (
   id uuid not null references auth.users on delete cascade,
   full_name text NULL,
   avatar_url text NULL,
   primary key (id)
);
```

## Enable Row Level Security

```sql
ALTER TABLE public.users ENABLE ROW LEVEL SECURITY;
```

## Permit Users Access Their Profile

```sql
CREATE POLICY "Permit Users to Access Their Profile"
  ON public.users
  FOR SELECT
  USING ( auth.uid() = id );
```

## Permit Users to Update Their Profile

```sql
CREATE POLICY "Permit Users to Update Their Profile"
  ON public.users
  FOR UPDATE
  USING ( auth.uid() = id );
```

## Supabase Functions

```sql
CREATE
OR REPLACE FUNCTION public.user_profile() RETURNS TRIGGER AS $$ BEGIN INSERT INTO public.users (id, full_name,avatar_url)
VALUES
  (
    NEW.id,
    NEW.raw_user_meta_data ->> 'full_name'::TEXT,
    NEW.raw_user_meta_data ->> 'avatar_url'::TEXT,
  );
RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

## Supabase Trigger

```sql
  CREATE TRIGGER
  create_user_trigger
  AFTER INSERT ON auth.users
  FOR EACH ROW
  EXECUTE PROCEDURE
    public.user_profile();
```

## Chat_Messages table (Real Time)

* id (uuid)
* Created At (date)
* text (text)
* editable (boolean)
* sender (uuid)
