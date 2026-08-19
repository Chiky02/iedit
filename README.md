# IEDIT Buzón de Sugerencias y Noticias

Aplicación web para gestionar noticias institucionales, roles administrativos y un buzón de sugerencias anónimas.

## Qué incluye
- CRUD completo de noticias
- CRUD completo de usuarios por parte del administrador
- Gestión de roles y permisos en formato CRUD
- Buzón de sugerencias público con protección básica contra envíos masivos
- Página pública de noticias con paginación y filtros por fecha, categoría y tipo
- Autenticación admin con JWT y cookies HTTP-only
- Seeder con datos iniciales
- Paleta de colores verde/amarillo inspirada en la imagen institucional

## Estructura principal
- `app/page.tsx` — Página pública de noticias y envío de sugerencias
- `app/admin/page.tsx` — Panel administrativo para gestionar noticias, usuarios, roles y sugerencias
- `app/login/page.tsx` — Login de administrador
- `app/actions.ts` — Server actions para crear/editar/eliminar contenidos y usuarios
- `app/api/suggestions/route.ts` — Endpoint público para recibir sugerencias
- `components/` — Componentes de UI especializados
- `lib/auth.ts` — JWT y cookies seguras
- `prisma/schema.prisma` — Modelo de datos actualizado
- `prisma/seed.ts` — Seed con admin, permisos y noticias de ejemplo

## Configuración local
1. Copia el archivo de ambiente:
   ```bash
   cp .env.example .env
   ```
2. Pega `DATABASE_URL` (pooled) y `DIRECT_URL` (direct) de Prisma Postgres. Ajusta `AUTH_SECRET`.
3. Instala dependencias:
   ```bash
   npm install
   ```
4. Aplica migraciones y seed:
   ```bash
   npm run migrate:prod
   npm run prisma:seed
   ```
5. Inicia la app:
   ```bash
   npm run dev
   ```

## Conectar Prisma Postgres en Vercel (dejar Supabase)

El código ya usa Prisma + PostgreSQL. En Vercel hay que crear la base y sustituir las URLs de Supabase.

1. En el proyecto de Vercel: **Storage** → **Create Database** → **Prisma Postgres** (Marketplace). Elige región y el plan Free.
2. **Connect** esa base al proyecto. Vercel inyecta `DATABASE_URL` (pooled, host `pooled.db.prisma.io`).
3. En la ficha de la base, **Open in Prisma**, copia la URL **direct** (host `db.prisma.io`) y en Vercel → **Settings → Environment Variables** crea:
   - `DIRECT_URL` = URL directa (`db.prisma.io`, `sslmode=require`)
   - `AUTH_SECRET` = el mismo valor que en `.env` (si no existe ya)
   - Aplica a Production (y Preview si quieres)
4. Si todavía hay variables de Supabase (`DATABASE_URL` antigua, `DIRECT_URL` de `supabase.com`, `DB_HOST`, etc.), bórralas o reemplázalas. `DATABASE_URL` debe apuntar a `pooled.db.prisma.io`, no a Supabase.
5. Sube este código (incluye `prisma/migrations` y `vercel.json`) y **Redeploy**. El build corre `prisma migrate deploy` y crea las tablas.
6. Una vez desplegado, carga el admin de prueba (una sola vez):
   ```bash
   npx vercel env pull .env
   npm run prisma:seed
   ```

Tras eso puedes borrar o pausar el proyecto de Supabase: la app ya no lo usa.

## Credenciales de demostración
- Email: `admin@colegio.com`
- Password: `admin123`

## Notas de seguridad
- JWT firmado con `AUTH_SECRET`
- Cookie `auth-token` con `HttpOnly`, `SameSite=strict` y `path=/`
- Contraseñas cifradas con `bcryptjs`
- Formulario de sugerencias protegido contra envíos repetidos desde el mismo punto

## Dependencias clave
- Next.js 16
- React 19
- Prisma + PostgreSQL (Prisma Postgres en Vercel)
- jose
- bcryptjs

## Comandos útiles
- `npm run dev` — Desarrollo
- `npm run build` — Build de producción
- `npm run start` — Iniciar producción
- `npm run prisma:seed` — Seed de datos

