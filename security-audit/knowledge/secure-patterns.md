# Padrões Seguros — Referência

Implementações de referência seguras para os padrões mais comuns.

---

## Autenticação Segura (Node.js/TypeScript)

```typescript
// auth.service.ts — implementação completa e segura

import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import crypto from 'crypto'

const SALT_ROUNDS = 12
const JWT_SECRET = process.env.JWT_SECRET!  // 32+ bytes
const JWT_ALGORITHM = 'HS256' as const
const ACCESS_TOKEN_EXPIRY = '15m'
const REFRESH_TOKEN_EXPIRY = '7d'

export async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS)
}

export async function verifyPassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash)  // timing-safe por padrão
}

export function generateAccessToken(userId: string, role: string): string {
  return jwt.sign(
    { sub: userId, role },
    JWT_SECRET,
    {
      algorithm: JWT_ALGORITHM,
      expiresIn: ACCESS_TOKEN_EXPIRY,
      issuer: process.env.JWT_ISSUER,
      audience: process.env.JWT_AUDIENCE,
    }
  )
}

export function verifyToken(token: string): jwt.JwtPayload {
  return jwt.verify(token, JWT_SECRET, {
    algorithms: [JWT_ALGORITHM],
    issuer: process.env.JWT_ISSUER,
    audience: process.env.JWT_AUDIENCE,
  }) as jwt.JwtPayload
}

export function generateSecureToken(bytes = 32): string {
  return crypto.randomBytes(bytes).toString('hex')
}
```

---

## Middleware de Autenticação + Autorização

```typescript
// middleware/auth.ts

export function authenticate(req: Request, res: Response, next: NextFunction) {
  const token = req.cookies.accessToken  // HttpOnly cookie
    ?? req.headers.authorization?.replace('Bearer ', '')
  
  if (!token) return res.status(401).json({ error: 'Authentication required' })
  
  try {
    req.user = verifyToken(token)
    next()
  } catch {
    return res.status(401).json({ error: 'Invalid or expired token' })
  }
}

export function requireRole(...roles: string[]) {
  return (req: Request, res: Response, next: NextFunction) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Insufficient permissions' })
    }
    next()
  }
}

// Verificação de ownership
export async function requireOwnership(
  getResource: (id: string) => Promise<{ userId: string } | null>
) {
  return async (req: Request, res: Response, next: NextFunction) => {
    const resource = await getResource(req.params.id)
    if (!resource) return res.status(404).end()
    if (resource.userId !== req.user.sub && req.user.role !== 'admin') {
      return res.status(403).json({ error: 'Access denied' })
    }
    next()
  }
}
```

---

## Validação de Input com Zod

```typescript
// validation/schemas.ts

import { z } from 'zod'

export const userSchema = z.object({
  email: z.string().email().max(255).toLowerCase(),
  password: z.string().min(12).max(128),
  name: z.string().min(2).max(100).trim(),
})

export const paginationSchema = z.object({
  page: z.coerce.number().int().positive().default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
})

export const idSchema = z.object({
  id: z.string().uuid(),
})

// Middleware de validação
export function validate(schema: z.ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    const result = schema.safeParse(req.body)
    if (!result.success) {
      return res.status(400).json({ errors: result.error.flatten() })
    }
    req.body = result.data  // dados limpos e tipados
    next()
  }
}
```

---

## Cookies Seguros

```typescript
// utils/cookies.ts

export const SECURE_COOKIE_OPTIONS = {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production',
  sameSite: 'strict' as const,
  path: '/',
  maxAge: 15 * 60 * 1000,  // 15 min
}

export function setAuthCookie(res: Response, token: string) {
  res.cookie('accessToken', token, SECURE_COOKIE_OPTIONS)
}

export function clearAuthCookie(res: Response) {
  res.clearCookie('accessToken', { ...SECURE_COOKIE_OPTIONS, maxAge: 0 })
}
```

---

## Query Segura com Prisma (Multi-tenant)

```typescript
// Sempre incluir tenantId nas queries
async function getOrders(userId: string, tenantId: string) {
  return prisma.order.findMany({
    where: {
      userId,          // ownership check
      tenantId,        // tenant isolation
      deletedAt: null, // soft delete
    },
    select: {          // apenas campos necessários
      id: true,
      status: true,
      total: true,
      createdAt: true,
    },
    take: 20,          // sempre limitar
    orderBy: { createdAt: 'desc' },
  })
}
```

---

## Rate Limiting

```typescript
// middleware/rateLimiter.ts
import rateLimit from 'express-rate-limit'

export const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 10,
  keyGenerator: (req) => `${req.ip}:${req.body?.email}`,
  message: { error: 'Too many attempts. Try again in 15 minutes.' },
  standardHeaders: true,
  legacyHeaders: false,
})

export const apiLimiter = rateLimit({
  windowMs: 60 * 1000,
  max: 100,
  message: { error: 'Rate limit exceeded' },
})
```

---

## Headers de Segurança (Express)

```typescript
import helmet from 'helmet'

app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      imgSrc: ["'self'", 'data:', 'https:'],
      connectSrc: ["'self'"],
      fontSrc: ["'self'"],
      objectSrc: ["'none'"],
      frameAncestors: ["'none'"],
    },
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true,
  },
}))
```
