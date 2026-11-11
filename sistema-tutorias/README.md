# Sistema de Tutorías

Aplicación Vue 3 para consultar horarios de tutorías guardados en Supabase.

## Conectar con Supabase

1. En Supabase, abrí **SQL Editor**, pegá el contenido de `supabase/setup.sql` y ejecutalo. Crea `public.tutorias`, mantiene RLS activado, permite consultar a usuarios autenticados e inserta una tutoría de ejemplo.
2. En **Authentication → Users**, creá una cuenta de prueba. La aplicación no habilita registro libre.
3. En **Authentication → URL Configuration**, configurá `http://localhost:5173` como **Site URL** y agregá `http://localhost:5173/**` a **Redirect URLs** para la recuperación de contraseña en desarrollo.
4. Copiá `.env.example` como `.env.local` y reemplazá los valores con la **Project URL** y la **publishable key** de **Project Settings → API**.

   En PowerShell:

   ```powershell
   Copy-Item .env.example .env.local
   ```

   No compartas `.env.local` ni uses una clave `service_role` en el navegador. `.env.local` está excluido del repositorio.
5. Instalá las dependencias e iniciá el proyecto:

   ```sh
   npm install
   npm run dev
   ```

   Si Vite ya estaba ejecutándose cuando completaste `.env.local`, reinicialo para que lea las variables.
6. Iniciá sesión con la cuenta creada en Supabase. En **Horario de tutorías** debe aparecer el ejemplo de Robótica. Podés cambiar la materia desde **Table Editor → tutorias**, volver a la aplicación y actualizar la lista.

La tabla solo permite lectura a usuarios autenticados mediante la política RLS. Las altas y modificaciones de ejemplo se hacen desde Supabase Studio; la pantalla de horarios es de consulta y filtros, no de edición.

## Desarrollo

```sh
npm run build
npm run preview
```
