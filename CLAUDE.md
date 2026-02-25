# Platziflix - Proyecto Multi-plataforma

## Arquitectura del Sistema

Platziflix es una plataforma de cursos online con arquitectura multi-plataforma:
- **Backend**: API REST con FastAPI + PostgreSQL
- **Frontend**: Aplicación web con Next.js 15
- **Mobile**: Apps nativas Android (Kotlin) + iOS (Swift)

---

## Stack Tecnológico

### Backend (FastAPI/Python)
- **Framework**: FastAPI
- **Base de datos**: PostgreSQL 15
- **ORM**: SQLAlchemy 2.0
- **Migraciones**: Alembic
- **Container**: Docker + Docker Compose
- **Gestión dependencias**: UV (no pip, no poetry)
- **Puerto**: 8000

### Frontend (Next.js)
- **Framework**: Next.js 15 (App Router + Turbopack)
- **React**: 19.0
- **Lenguaje**: TypeScript (strict mode)
- **Estilos**: SCSS + CSS Modules
- **Testing**: Vitest + React Testing Library
- **Package manager**: Yarn
- **Fonts**: Geist Sans & Geist Mono (next/font/google)
- **Puerto**: 3000

### Mobile - Android
- **Lenguaje**: Kotlin
- **UI**: Jetpack Compose + Material 3
- **Arquitectura**: CLEAR (Clean + MVI)
- **HTTP**: Retrofit 2.9 + OkHttp
- **Imágenes**: Coil
- **Async**: Coroutines + StateFlow
- **Build**: Gradle 8.10.0 + libs.versions.toml

### Mobile - iOS
- **Lenguaje**: Swift
- **UI**: SwiftUI
- **Arquitectura**: MVVM + Repository Pattern
- **Reactive**: Combine framework
- **HTTP**: URLSession + async/await
- **Parsing**: Decodable

---

## Estructura del Proyecto

```
claude-code-proyect-platzi/
├── .claude/
│   └── agents/
│       ├── architect.md
│       ├── backend.md
│       └── frontend.md
├── .cursor/
│   └── rules/
│       └── fastapi.mdc
├── .github/
│   └── workflows/
│       ├── claude.yml               # Claude Code integration (triggered by @claude mentions)
│       └── claude-code-review.yml   # Auto PR review via Claude
├── spec/
│   ├── 00_sistema_ratings_cursos.md
│   ├── 01_backend_ratings_implementation_plan.md
│   ├── 02_frontend_ratings_implementation_plan.md
│   └── 03_backend_security_review.md
├── CLAUDE.md
├── README.md
│
├── Backend/
│   ├── Dockerfile
│   ├── Makefile
│   ├── docker-compose.yml
│   ├── pyproject.toml
│   ├── uv.lock
│   ├── specs/
│   │   ├── 00_contracts.md
│   │   └── 01_setup.md
│   └── app/
│       ├── main.py                  # FastAPI app + todos los endpoints
│       ├── alembic.ini
│       ├── alembic/
│       │   └── versions/
│       │       ├── d18a08253457_create_initial_database_schema.py
│       │       └── 0e3a8766f785_add_course_ratings_table.py
│       ├── core/
│       │   └── config.py            # Pydantic Settings (lee .env)
│       ├── db/
│       │   ├── base.py              # Engine, SessionLocal, get_db()
│       │   └── seed.py              # Datos de prueba
│       ├── models/
│       │   ├── base.py              # BaseModel con timestamps + soft delete
│       │   ├── course.py            # Course + average_rating property
│       │   ├── course_rating.py     # CourseRating con CheckConstraint(1-5)
│       │   ├── course_teacher.py    # Tabla junction M2M
│       │   ├── teacher.py
│       │   └── lesson.py
│       ├── schemas/
│       │   └── rating.py            # Pydantic: RatingRequest/Response/Stats
│       ├── services/
│       │   └── course_service.py    # Business logic (CourseService class)
│       └── tests/
│           ├── test_course_rating_service.py
│           ├── test_rating_db_constraints.py
│           └── test_rating_endpoints.py
│
├── Frontend/
│   ├── package.json
│   ├── tsconfig.json
│   ├── next.config.ts
│   ├── vitest.config.ts
│   ├── .cursor/rules/               # Guías de componentes, SCSS, testing
│   └── src/
│       ├── app/
│       │   ├── layout.tsx
│       │   ├── page.tsx             # Home: grid de cursos
│       │   ├── course/[slug]/
│       │   │   ├── page.tsx         # Detalle de curso (SSR)
│       │   │   ├── error.tsx
│       │   │   ├── loading.tsx
│       │   │   └── not-found.tsx
│       │   └── classes/[class_id]/
│       │       └── page.tsx         # Video player
│       ├── components/
│       │   ├── Course/              # Card de curso (con StarRating)
│       │   ├── CourseDetail/        # Vista completa del curso
│       │   ├── StarRating/          # Estrellas SVG (half-star, accesible)
│       │   └── VideoPlayer/         # HTML5 video player
│       ├── services/
│       │   └── ratingsApi.ts        # Fetch con timeout + AbortController
│       ├── styles/
│       │   ├── reset.scss
│       │   └── vars.scss            # Variables CSS globales
│       ├── types/
│       │   ├── index.ts             # Course, Class, CourseDetail
│       │   └── rating.ts            # Rating interfaces + type guards
│       └── test/
│           └── setup.ts
│
└── Mobile/
    ├── PlatziFlixAndroid/
    │   ├── gradle/libs.versions.toml
    │   └── app/src/main/java/com/espaciotiago/platziflixandroid/
    │       ├── MainActivity.kt
    │       ├── data/
    │       │   ├── entities/CourseDTO.kt
    │       │   ├── mappers/CourseMapper.kt
    │       │   ├── network/ApiService.kt + NetworkModule.kt
    │       │   └── repositories/ (Remote + Mock)
    │       ├── domain/
    │       │   ├── models/Course.kt
    │       │   └── repositories/CourseRepository.kt (interface)
    │       ├── presentation/courses/
    │       │   ├── components/ (CourseCard, ErrorMessage, LoadingIndicator)
    │       │   ├── screen/CourseListScreen.kt
    │       │   ├── state/CourseListUiState.kt
    │       │   └── viewmodel/CourseListViewModel.kt
    │       ├── di/AppModule.kt       # Toggle USE_MOCK_DATA
    │       └── ui/theme/
    │
    └── PlatziFlixiOS/PlatziFlixiOS/
        ├── PlatziFlixiOSApp.swift
        ├── Data/
        │   ├── Entities/ (CourseDTO, ClassDetailDTO, TeacherDTO)
        │   ├── Mapper/ (CourseMapper, ClassMapper, TeacherMapper)
        │   └── Repositories/ (CourseAPIEndpoints, RemoteCourseRepository)
        ├── Domain/
        │   ├── Models/ (Course, Class, Teacher)  ← Course incluye mockCourses
        │   └── Repositories/CourseRepositoryProtocol.swift
        └── Presentation/
            ├── ViewModels/CourseListViewModel.swift  ← @MainActor + ObservableObject
            └── Views/ (CourseListView, CourseCardView)
```

---

## Modelo de Datos

### Tablas en PostgreSQL

| Tabla | Columnas Clave | Relaciones |
|-------|---------------|------------|
| `courses` | id, name, description, thumbnail, slug (unique+indexed) | Teachers M2M, Lessons O2M, Ratings O2M |
| `teachers` | id, name, email (unique+indexed) | Courses M2M |
| `lessons` | id, course_id (FK), name, description, slug, video_url | Course M2O |
| `course_ratings` | id, course_id (FK), user_id, rating (1-5 CHECK) | Course M2O |
| `course_teachers` | course_id (FK, PK), teacher_id (FK, PK) | Tabla junction |

### BaseModel (todos los modelos heredan)
- `id`: Integer PK
- `created_at`: DateTime UTC (auto)
- `updated_at`: DateTime UTC (auto-update)
- `deleted_at`: DateTime UTC (soft delete - NULL = activo)

### Propiedades calculadas en Course
- `average_rating`: promedio de ratings activos (deleted_at IS NULL)
- `total_ratings`: conteo de ratings activos

---

## API Endpoints Completos

**Base URL**: `http://localhost:8000`

### Root & Health
| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | `/` | Mensaje de bienvenida |
| GET | `/health` | Health check + conectividad DB |

### Cursos
| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | `/courses` | Lista todos los cursos (incluye rating stats) |
| GET | `/courses/{slug}` | Detalle: curso + teachers + lessons + ratings |
| GET | `/classes/{class_id}` | Detalle de clase/lección con video_url |

### Sistema de Ratings
| Método | Ruta | Status | Descripción |
|--------|------|--------|-------------|
| POST | `/courses/{course_id}/ratings` | 201 | Crear/actualizar rating (upsert) |
| GET | `/courses/{course_id}/ratings` | 200 | Listar todos los ratings del curso |
| GET | `/courses/{course_id}/ratings/stats` | 200 | Stats agregadas (avg, total, distribución) |
| GET | `/courses/{course_id}/ratings/user/{user_id}` | 200/204 | Rating específico de un usuario |
| PUT | `/courses/{course_id}/ratings/{user_id}` | 200 | Actualizar rating existente |
| DELETE | `/courses/{course_id}/ratings/{user_id}` | 204 | Soft delete del rating |

### Schemas de Rating (Pydantic)
```python
RatingRequest:    user_id: int (>0), rating: int (1-5)
RatingResponse:   id, course_id, user_id, rating, created_at, updated_at
RatingStats:      average_rating: float, total_ratings: int, rating_distribution: Dict[int, int]
ErrorResponse:    detail: str, error_code: str | None
```

---

## Comandos de Desarrollo

### Backend (SIEMPRE via Docker)
```bash
cd Backend
make start           # docker-compose up -d (iniciar todo)
make stop            # docker-compose down
make restart         # docker-compose restart
make build           # docker-compose build
make logs            # docker-compose logs -f
make clean           # Limpieza total con volúmenes
make migrate         # alembic upgrade head
make create-migration # alembic revision --autogenerate
make seed            # Poblar datos de prueba
make seed-fresh      # Limpiar y repoblar desde cero
```

> **IMPORTANTE**: Antes de ejecutar cualquier comando del Backend, verificar que el contenedor esté corriendo. Revisar `Makefile` para los comandos disponibles y usarlos siempre.

### Frontend
```bash
cd Frontend
yarn dev             # Servidor de desarrollo (Turbopack)
yarn build           # Build de producción
yarn start           # Servidor de producción
yarn test            # Ejecutar tests (Vitest)
yarn lint            # ESLint
```

### Android
```bash
cd Mobile/PlatziFlixAndroid
./gradlew assembleDebug     # Build debug APK
./gradlew test              # Unit tests
./gradlew connectedAndroidTest  # Instrumented tests
```

---

## URLs del Sistema

| Servicio | URL |
|----------|-----|
| Backend API | http://localhost:8000 |
| Swagger UI | http://localhost:8000/docs |
| ReDoc | http://localhost:8000/redoc |
| Frontend Web | http://localhost:3000 |
| Android Emulator → API | http://10.0.2.2:8000 |

---

## Base de Datos (Docker)

```
Usuario:   platziflix_user
Password:  platziflix_password
Database:  platziflix_db
Puerto:    5432
```

### Migraciones Alembic
- **Ubicación**: `Backend/app/alembic/versions/`
- **Config**: `Backend/app/alembic.ini`
- **Crear**: `make create-migration` (requiere contenedor activo)
- **Aplicar**: `make migrate`
- **Migraciones existentes**:
  - `d18a08253457` → Schema inicial (courses, teachers, lessons, course_teachers)
  - `0e3a8766f785` → Tabla course_ratings

---

## Patrones de Desarrollo

### Backend
- **Arquitectura**: Service Layer Pattern (`CourseService` en `services/`)
- **DI**: FastAPI `Depends(get_db)` para sesiones de DB
- **Soft Delete**: Todos los modelos tienen `deleted_at` (NULL = activo)
- **Upsert**: Rating se crea o actualiza si ya existe para el mismo user+course
- **Agregación**: Stats de ratings usan SQL directo para eficiencia

### Frontend
- **Routing**: Next.js App Router (file-based)
- **Data Fetching**: Server Components + `fetch` con `no-store`
- **Styling**: CSS Modules (`.module.scss`) + variables globales en `vars.scss`
- **Testing**: Vitest con jsdom, alias `@/` → `./src/`
- **HTTP con timeout**: `fetchWithTimeout` en `ratingsApi.ts` (10s default, AbortController)

### Android
- **Arquitectura**: CLEAR = Clean Architecture + MVI
- **Estado**: `CourseListUiState` (data class) + `CourseListUiEvent` (sealed class)
- **Mock toggle**: `USE_MOCK_DATA` en `di/AppModule.kt`
- **Base URL emulador**: `http://10.0.2.2:8000/`

### iOS
- **ViewModel**: `@MainActor` + `ObservableObject` + `@Published`
- **Repository**: Protocol-based (`CourseRepositoryProtocol`)
- **Preview data**: `Course.mockCourses` (6 cursos estáticos)
- **Error handling**: `handleError(_ error: Error) -> String` en ViewModel

---

## CI/CD - GitHub Actions

### `claude.yml`
- **Trigger**: Comentarios con `@claude` en issues y PRs
- **Acción**: Ejecuta Claude Code como agente

### `claude-code-review.yml`
- **Trigger**: PRs abiertos o actualizados
- **Acción**: Review automático de código con Claude
- **Criterios**: calidad, bugs, performance, seguridad, cobertura de tests

---

## Funcionalidades Implementadas

| Feature | Backend | Frontend | Android | iOS |
|---------|---------|----------|---------|-----|
| Catálogo de cursos | ✅ | ✅ | ✅ | ✅ |
| Detalle de curso | ✅ | ✅ | - | - |
| Sistema de ratings (1-5★) | ✅ | ✅ | - | - |
| Reproductor de video | ✅ | ✅ | - | - |
| Health check | ✅ | - | - | - |
| Soft delete | ✅ | - | - | - |
| Navegación por slug | ✅ | ✅ | - | - |
| Búsqueda de cursos | - | - | - | ✅ |
| Tests unitarios | ✅ | ✅ | ✅ | - |

---

## Convenciones de Código

| Plataforma | Naming | Ejemplo |
|-----------|--------|---------|
| Python (Backend) | snake_case | `course_service`, `get_db` |
| TypeScript (Frontend) | camelCase + PascalCase (componentes) | `ratingsApi`, `StarRating` |
| Kotlin (Android) | camelCase + PascalCase (clases) | `CourseListViewModel`, `uiState` |
| Swift (iOS) | camelCase + PascalCase (structs/classes) | `CourseListViewModel`, `mockCourses` |

---

## Consideraciones Críticas

1. **Docker obligatorio** para el backend (DB + API no pueden correr sin Docker)
2. **TypeScript strict** en Frontend - no `any` implícito
3. **Testing requerido** para nuevas funcionalidades en todas las plataformas
4. **Migraciones automáticas** con Alembic para cualquier cambio de esquema DB
5. **API REST** como única fuente de datos para Frontend y Mobile
6. **Soft delete** - nunca borrar registros físicamente, usar `deleted_at`
7. **Backend commands**: siempre verificar que el contenedor Docker esté activo antes de ejecutar comandos; usar los targets del `Makefile`
8. **Ratings validation**: el campo `rating` tiene un `CheckConstraint` en DB (1-5), validado también en Pydantic

---

## Archivos de Especificación

- `spec/00_sistema_ratings_cursos.md` → Especificación del sistema de ratings
- `spec/01_backend_ratings_implementation_plan.md` → Plan de implementación backend
- `spec/02_frontend_ratings_implementation_plan.md` → Plan de implementación frontend
- `spec/03_backend_security_review.md` → Revisión de seguridad
- `Backend/specs/00_contracts.md` → Contratos de la API
- `Backend/specs/01_setup.md` → Instrucciones de setup
