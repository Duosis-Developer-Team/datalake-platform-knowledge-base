# Settings IAM UI (Users, Teams, Roles, Permissions)

## Overview

- **Users** and **Teams** use a **slide-in panel** (CSS: `.user-slide-panel`, `.team-slide-panel`) for create/edit; the directory/table stays visible beside the panel.
- **LDAP directory search** supports **OU paths**: queries starting with `OU=` search under that subtree (see `ldap_service._search_directory_users_with_cfg`).
- **Teams** may have **description** and **team roles** (`team_roles` table). Members inherit effective permissions from **user roles ∪ team roles** (`permission_service._user_role_ids`).
- **Roles** matrix groups permissions by `resource_type` in an **accordion**; each permission row shows **description** when present in `permissions.description`.
- **Permissions** catalog uses the same accordion grouping (all sections **collapsed** on first load; `value=[]` on `dmc.Accordion`).
- **Teams** Dash callbacks live in `src/pages/settings/iam/teams_callbacks.py` and must be registered by importing that module from `app.py` (same pattern as `users_callbacks` / `roles_callbacks`).

## Schema

- Migration: `sql/migrations/002_team_description_and_team_roles.sql` — `teams.description`, `team_roles` junction table.
- Applied automatically as **schema_migrations version 3** by `src/auth/auth_db_migrations.py` on **GUI startup** (`app.py` → `run_migrations`) and **admin-api startup** (`app/main.py` lifespan → `run_auth_db_migrations`). Docker image copies `sql/` and `auth_db_migrations` into the admin-api container (`services/admin-api/Dockerfile`, build `context: .`).

## Mock

- `datalake-platform-webui-mock`: `mock_admin_client` mirrors team `role_ids`, OU LDAP search samples, and effective user role display.
