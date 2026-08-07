---
name: supabase-crud
description: Safe SSR data fetching, Zod schema validations, and RLS policy setup in AntEater.
---

# Skill: Supabase CRUD Operations

## Objective
Establish safe, consistent patterns for database access, querying, mutations, and row-level security (RLS) enforcement using Supabase SSR clients in Next.js 14+.

## Guidelines & Architecture

### 1. Client Selection Rule
- **NEVER** import `@supabase/supabase-js` directly in UI components.
- **Server Components, Server Actions & API Routes:** Use the SSR server client wrapper.
  ```typescript
  import { createClient } from '@/lib/supabase/server'
  ```
- **Client Components:** Use the SSR client component wrapper.
  ```typescript
  import { createClient } from '@/lib/supabase/client'
  ```

### 2. Authorization Enforcement (STRICT)
Always authenticate the user and enforce `user_id` scope in queries.
- Before executing any operation, fetch the authenticated user:
  ```typescript
  const supabase = await createClient()
  const { data: { user } } = await supabase.auth.getUser()
  if (!user) return { error: 'No autorizado' }
  ```
- Explicitly scope all updates, deletes, and select queries to the authenticated user ID:
  ```typescript
  const { data, error } = await supabase
    .from('commitments')
    .select('*')
    .eq('user_id', user.id)
  ```
- When inserting, explicitly set `user_id` to the authenticated user's ID:
  ```typescript
  const { error } = await supabase.from('commitments').insert({
    ...data,
    user_id: user.id
  })
  ```

### 3. Mutations & Revalidation
Always call `revalidatePath` to refresh cache after data mutations:
```typescript
import { revalidatePath } from 'next/cache'

// After mutation succeeds:
revalidatePath('/compromisos')
revalidatePath('/')
```

### 4. Transactions / Multi-table operations
When performing sequential related operations where one depends on another, handle failure cleanly (e.g. revert/rollback the first step if the second fails):
```typescript
// Example: Creating an expense first, then updating a commitment
const { data: expenseData, error: expenseError } = await supabase
  .from('expenses')
  .insert({ ... })
  .select()
  .single()

if (expenseError) return { error: expenseError.message }

const { error: commitmentError } = await supabase
  .from('commitments')
  .update({ expense_id: expenseData.id })
  .eq('id', commitmentId)

if (commitmentError) {
  // Revert/Delete the created expense if commitment fails to link
  await supabase.from('expenses').delete().eq('id', expenseData.id)
  return { error: commitmentError.message }
}
```

### 5. Zod Schema Validation (MANDATORY before Mutation)
Always validate client input payloads using Zod schemas before performing inserts or updates in Supabase.
- Define schemas clearly (usually placing them in a shared file or inside the action module).
- Use `safeParse` to handle failure gracefully without crashing the process.

Example:
```typescript
import { z } from 'zod'

const commitmentInputSchema = z.object({
  title: z.string().min(3, "El título debe tener al menos 3 caracteres"),
  amount: z.number().positive("El monto debe ser mayor a cero"),
  category: z.string()
})

export async function createCommitment(payload: unknown) {
  // 1. Validar esquema
  const validation = commitmentInputSchema.safeParse(payload)
  if (!validation.success) {
    return {
      error: 'Datos de entrada inválidos',
      validationErrors: validation.error.format()
    }
  }

  // 2. Extraer datos tipados y seguros
  const safeData = validation.data

  // 3. Proceder con la operación en Supabase...
  const supabase = await createClient()
  // ...
}
```
