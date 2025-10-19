# Technical Specification

## 1. Technology Stack

### Frontend
- **Framework**: React 18 with TypeScript
- **Build Tool**: Vite 5.x
- **State Management**: Zustand
- **Routing**: React Router v6
- **UI Library**: Tailwind CSS, Shadcn/ui
- **Form Handling**: React Hook Form + Zod
- **Data Fetching**: TanStack Query (React Query)
- **Key Dependencies**:
  * date-fns - Date manipulation
  * react-beautiful-dnd - Drag and drop functionality
  * framer-motion - Animation
  * axios - HTTP client

### Backend
- **Runtime**: Node.js 20 LTS
- **Framework**: Next.js API Routes
- **Language**: TypeScript
- **Database**: PostgreSQL 15 with Prisma ORM
- **Authentication**: NextAuth.js with JWT
- **API Type**: REST

### Infrastructure & DevOps
- **Hosting**: Vercel
- **Database Hosting**: Supabase
- **CI/CD**: GitHub Actions
- **Monitoring**: Sentry
- **Analytics**: PostHog

## 2. System Architecture

### Architecture Pattern
Server-Side Rendered (SSR) with Next.js App Router

### Component Diagram
```
┌─────────────────────────────────────────┐
│           User's Browser                │
│  ┌─────────────────────────────────┐   │
│  │   Next.js React Application     │   │
│  │   - Components                  │   │
│  │   - Server-side Rendering       │   │
│  │   - API Routes                  │   │
│  └─────────────┬───────────────────┘   │
└────────────────┼───────────────────────┘
                 │ HTTPS
                 ▼
┌─────────────────────────────────────────┐
│          Data Layer                     │
│  ┌──────────────┐  ┌──────────────┐   │
│  │  PostgreSQL  │  │  Redis Cache │   │
│  │  (Prisma)    │  │  (Sessions)  │   │
│  └──────────────┘  └──────────────┘   │
└─────────────────────────────────────────┘
```

## 3. Data Models & Database Schema

### User Model
```typescript
model User {
  id            String    @id @default(cuid())
  email         String    @unique
  name          String?
  passwordHash  String
  avatar        String?
  tasks         Task[]
  calendars     Calendar[]
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

model Task {
  id          String      @id @default(cuid())
  title       String
  description String?
  status      TaskStatus  @default(TODO)
  priority    Priority    @default(MEDIUM)
  dueDate     DateTime?
  user        User        @relation(fields: [userId], references: [id])
  userId      String
  createdAt   DateTime    @default(now())
  updatedAt   DateTime    @updatedAt
}

model Calendar {
  id          String      @id @default(cuid())
  name        String
  events      CalendarEvent[]
  user        User        @relation(fields: [userId], references: [id])
  userId      String
  createdAt   DateTime    @default(now())
  updatedAt   DateTime    @updatedAt
}

model CalendarEvent {
  id          String      @id @default(cuid())
  title       String
  description String?
  startTime   DateTime
  endTime     DateTime
  calendar    Calendar    @relation(fields: [calendarId], references: [id])
  calendarId  String
  createdAt   DateTime    @default(now())
  updatedAt   DateTime    @updatedAt
}

enum TaskStatus {
  TODO
  IN_PROGRESS
  COMPLETED
}

enum Priority {
  LOW
  MEDIUM
  HIGH
}
```

## 4. API Design

### Authentication Endpoints
```
POST   /api/auth/signup
POST   /api/auth/login
POST   /api/auth/logout
GET    /api/auth/me
```

### Tasks Endpoints
```
GET    /api/tasks
POST   /api/tasks
GET    /api/tasks/:id
PUT    /api/tasks/:id
DELETE /api/tasks/:id
```

### Calendar Endpoints
```
GET    /api/calendars
POST   /api/calendars
GET    /api/calendars/:id
PUT    /api/calendars/:id
DELETE /api/calendars/:id

GET    /api/calendars/:id/events
POST   /api/calendars/:id/events
```

## 5. Frontend Architecture

### Project Structure
```
/
├── src/
│   ├── app/
│   │   ├── (auth)/
│   │   ├── (dashboard)/
│   │   └── api/
│   ├── components/
│   │   ├── ui/
│   │   ├── tasks/
│   │   ├── calendar/
│   │   └── layouts/
│   ├── lib/
│   │   ├── api.ts
│   │   ├── auth.ts
│   │   └── utils.ts
│   ├── hooks/
│   ├── store/
│   ├── types/
│   └── styles/
```

## 6. Security Implementation

### Authentication Flow
1. User signup/login with email and password
2. Generate JWT token
3. Store token in httpOnly secure cookie
4. Validate token on protected routes
5. Implement refresh token mechanism

### Security Checklist
- [ ] Input validation with Zod
- [ ] Parameterized queries with Prisma
- [ ] Rate limiting on auth endpoints
- [ ] Strong password requirements
- [ ] HTTPS only
- [ ] Secure headers
- [ ] Regular dependency updates

## 7. Performance Optimization

### Frontend
- Code splitting
- Image optimization
- Aggressive caching
- Target Core Web Vitals:
  * LCP < 2.5s
  * FID < 100ms
  * CLS < 0.1

### Backend
- Database query optimization
- Redis caching
- API response times < 300ms

## 8. Testing Strategy

### Test Types
- Unit Tests: Vitest + Testing Library
- Integration Tests: Vitest + MSW
- E2E Tests: Playwright

## 9. Deployment & DevOps

### Environments
- Development: Local with hot reload
- Staging: Auto-deploy from `develop` branch
- Production: Deploy from `main` branch

## 10. MVP Development Phases

### Phase 1: Foundation (Week 1-2)
- [ ] Project setup
- [ ] Authentication
- [ ] Database schema
- [ ] Basic UI components

### Phase 2: Core Features (Week 3-4)
- [ ] Task management
- [ ] Calendar integration
- [ ] API endpoints

### Phase 3: Polish (Week 5-6)
- [ ] Error handling
- [ ] Loading states
- [ ] Responsive design

### Phase 4: Launch Prep (Week 7-8)
- [ ] Testing
- [ ] Beta user feedback
- [ ] Final optimizations