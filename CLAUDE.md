# API de Facturas — Convenciones del proyecto

## Stack
- Node 20+, TypeScript con `strict: true`, módulos ESM.
- Express 4, Prisma ORM con SQLite (archivo local `dev.db`).
- Validación: Zod. Logs: pino. Tests: Vitest + Supertest.
- Calidad: ESLint (typescript-eslint) + Prettier.

## Estructura
```
src/
  app.ts               # crea y configura la app de Express (sin listen)
  server.ts            # arranca el servidor (listen)
  config/env.ts        # variables de entorno validadas con Zod
  routes/              # define rutas y conecta middlewares
  controllers/         # recibe req, llama al service, responde
  services/            # lógica de negocio
  repositories/        # acceso a datos con Prisma
  schemas/             # esquemas Zod de entrada
  middlewares/         # validate, errorHandler, notFound
  errors/AppError.ts   # error de aplicación con status HTTP
  utils/logger.ts      # instancia de pino
tests/                 # tests de integración con Supertest
```

## Reglas
- Los controllers solo delegan. La lógica de negocio va en los services y el acceso a datos en los repositories.
- Usa siempre `async/await`. No uses callbacks ni cadenas de `.then()`.
- Nunca uses `any`. Si un tipo es desconocido, usa `unknown` y valídalo.
- Valida toda entrada (params, query, body) con Zod mediante el middleware `validate`.
- Para errores esperados, lanza `new AppError(status, mensaje)`. Solo el `errorHandler` responde errores; no pongas `try/catch` en los controllers.
- Usa el logger (pino), nunca `console.log`.
- Toda configuración viene de `config/env.ts`; no leas `process.env` en otros archivos.
- Funciones pequeñas con una sola responsabilidad y nombres descriptivos en español o inglés, pero consistentes (el código en inglés y los mensajes al usuario en español).
- Usa DRY con criterio: es mejor repetir un poco que crear la abstracción equivocada.
- No agregues dependencias nuevas sin preguntar.

## Forma de trabajar
1. Antes de escribir código, propón un plan (archivos a crear o modificar y por qué) y espera mi aprobación.
2. Trabaja en pasos pequeños: una funcionalidad a la vez.
3. Cada endpoint nuevo lleva sus tests: un caso exitoso y los casos de error.
4. Al terminar un paso, ejecuta `npm run lint` y `npm test`, y corrige lo que falle.
5. Si algo no está claro, pregunta en lugar de asumir.

## Comandos
- `npm run dev`: servidor en modo desarrollo.
- `npm run build`: compila TypeScript.
- `npm test`: corre los tests.
- `npm run lint` y `npm run format`: revisan y formatean el código.
- `npx prisma migrate dev`: aplica migraciones.
