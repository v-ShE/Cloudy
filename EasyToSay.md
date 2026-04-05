# 1. Main Task: to create 2 tables
## 主表1: auth_users
```sql
create table public.auth_users (
  id uuid not null default gen_random_uuid (),
  account_name text null default ''::text,
  email character varying not null default ''::text,
  created_at timestamp with time zone not null default now(),
  updated_at timestamp with time zone not null default now(),
  status text not null default 'active'::text,
  constraint auth_users_pkey primary key (id),
  constraint auth_users_id_fkey foreign KEY (id) references auth.users (id) on update CASCADE,
  constraint auth_users_status_check check (
    (
      status = any (array['active'::text, 'disabled'::text])
    )
  )
) TABLESPACE pg_default;
```

---
## 主表2：task_progress
```sql
create table public.task_progress (
  id uuid not null default gen_random_uuid (),
  local_task_id text not null,
  modal_job_id text null default ''::text,
  project_id text null default ''::text,
  collection_id text null default ''::text,
  dataset_uid text null default ''::text,
  selected_models jsonb not null default '[]'::jsonb,
  total_items integer not null default 0,
  completed_items integer not null default 0,
  failed_items integer not null default 0,
  last_message text null default ''::text,
  submitted_at timestamp with time zone not null default now(),
  updated_at timestamp with time zone not null default now(),
  finished_at timestamp with time zone null,
  task_type text not null default ''::text,
  task_status text not null default ''::text,
  progress_percent smallint not null default '0'::smallint,
  user_id uuid not null,
  constraint task_progress_pkey primary key (id),
  constraint task_progress_user_id_fkey foreign KEY (user_id) references auth.users (id) on update CASCADE,
  constraint task_progress_progress_percent_check check (
    (
      (progress_percent >= 0)
      and (progress_percent <= 100)
    )
  ),
  constraint task_progress_task_status_check check (
    (
      task_status = any (
        array[
          'queued'::text,
          'running'::text,
          'partial_success'::text,
          'completed'::text,
          'failed'::text,
          'canceled'::text
        ]
      )
    )
  ),
  constraint task_progress_task_type_check check (
    (
      task_type = any (
        array[
          'prediction'::text,
          'analysis'::text,
          'conformer_preview'::text
        ]
      )
    )
  )
) TABLESPACE pg_default;
```

---
---
# 2. to Build a TRIGGER to Auto-Sync `auth_users` 
> (when using SDK to CRUD)
```sql
-- 创建函数：关联Supabase自带的table：auth.users与自建的table：public.auth_users
CREATE OR REPLACE FUNCTION sync()
RETURNS trigger
LANGUAGE plpgsql
SECURITY DEFINER SET search_path = public
AS $$
BEGIN
  INSERT INTO public.auth_users (id, email)
  VALUES (
    new.id, 
    new.email
  );
  RETURN new;
END;
$$;
```

```sql
-- 创建TRIGGER，明确指向 public schema下的FUNCTION sync()
CREATE TRIGGER sycn_with_auth
  AFTER INSERT ON auth.users
  FOR EACH ROW
  EXECUTE FUNCTION public.sync();
```

---
---
# 3. to CREATE RLS POLICY
```sql
CREATE POLICY "Enable users to view their own data only"
ON "public"."task_progress"
AS PERMISSIVE
FOR SELECT
TO authenticated
USING (
  (SELECT auth.uid()) = user_id
);
```

---
---
# 4. 用户信息查询（移动端，Python代码，需要提前`pip install supabase`）
```python
from supabase import create_client, Client

url: str = "MY_URL"
key: str = "MY_ANON_KEY"
supabase: Client = create_client(url, key)

# 登录（否则 auth.uid() 为空）
supabase.auth.sign_in_with_password({
    "email": "user@example.com",
    "password": "password"
})

# 查询 只返回当前登录用户的数据
data = supabase.from_("table").select("*").execute()

print(data)
```

---
---
# 5. 用Python实现CRUD
```python
'''初始化'''
from supabase import create_client, Client

# 配置信息
SUPABASE_URL = "MY_PROJECT_URL"
SUPABASE_SERVICE_KEY = "MY_SERVICE_ROLE_KEY" 
supabase: Client = create_client(SUPABASE_URL, SUPABASE_SERVICE_KEY)

# 定义表名，方便后面统一修改
TABLE_NAME = "task_progress"
```
> 详见[supabase官方文档](https://supabase.com/docs/reference/python/introduction)  
> 后端、Modal、移动端需自适应。
