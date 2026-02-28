# Amar Pathagar Backend

A trust-based book sharing platform backend built with **Clean Architecture** and **Domain-Driven Design**.

## Architecture

This backend follows **eventrizo-backend's clean architecture pattern** with:

- **Domain Layer** - Pure business entities with no dependencies
- **Service Layer** - Business logic with port interfaces
- **Repository Layer** - Data access implementations
- **REST Layer** - HTTP delivery (handlers, middleware, responses)
- **Infrastructure Layer** - External dependencies (database, logger)

## Tech Stack

| Category | Technology |
|----------|-----------|
| **Language** | Go 1.23 |
| **Framework** | Gin |
| **Database** | PostgreSQL 15 |
| **DB Driver** | lib/pq |
| **Logging** | Zap (structured) |
| **Auth** | JWT |
| **Hot Reload** | Air |
| **Container** | Docker |

## Quick Start

```bash
# 1. Copy environment file
cp .env.example .env

# 2. (Optional) Edit .env to set admin credentials
# ADMIN_USERNAME=admin
# ADMIN_EMAIL=admin@amarpathagar.com
# ADMIN_PASSWORD=admin123
# ADMIN_FULL_NAME=System Administrator

# 3. Start development (with hot reload)
make dev

# 4. Run migrations
make migrate-up

# 5. Create admin user
make seed

# 6. Test the API
curl http://localhost:8080/health

# 7. View API Documentation
open http://localhost:8080/docs
```

**Or use the quick-start command:**
```bash
make quick-start  # Runs dev + migrate + seed + logs
```

## Default Admin Credentials

After running `make seed`, you can login with:
- **Username**: admin (or value from `ADMIN_USERNAME` in .env)
- **Email**: admin@amarpathagar.com (or value from `ADMIN_EMAIL` in .env)
- **Password**: admin123 (or value from `ADMIN_PASSWORD` in .env)

⚠️ **IMPORTANT**: Change the admin password immediately after first login in production!

## API Documentation

The API documentation is automatically served at `/docs` when the application starts.

- **Swagger UI**: http://localhost:8080/docs
- **OpenAPI Spec**: http://localhost:8080/docs/swagger.yaml

The documentation includes:
- All API endpoints with request/response examples
- Authentication requirements
- Request/response schemas
- Interactive API testing interface

You can also view the raw OpenAPI 3.0 specification in `docs/swagger.yaml`.

## Project Structure

```
backend/
├── cmd/
│   ├── main.go              # Bootstrap
│   └── server.go            # Dependency injection
├── internal/
│   ├── domain/              # Domain entities (8 files)
│   │   ├── error.go
│   │   ├── user.go
│   │   ├── book.go
│   │   ├── idea.go
│   │   ├── review.go
│   │   ├── donation.go
│   │   ├── bookmark.go
│   │   └── notification.go
│   ├── auth/                # Auth service
│   ├── book/                # Book service
│   ├── user/                # User service
│   ├── idea/                # Idea service
│   ├── review/              # Review service
│   ├── donation/            # Donation service
│   ├── bookmark/            # Bookmark service
│   ├── successscore/        # Success score service
│   ├── notification/        # Notification service
│   ├── repository/          # Data access layer
│   ├── rest/
│   │   ├── handler/         # HTTP handlers
│   │   ├── middleware/      # Auth, CORS, Logger
│   │   └── response/        # Response helpers
│   ├── infrastructure/
│   │   ├── db/postgres/     # Database connection
│   │   └── logger/          # Zap logger
│   └── config/              # Configuration
├── .air.toml                # Hot reload config
├── docker-compose.yml       # Production
├── docker-compose.dev.yml   # Development
├── Dockerfile               # Multi-stage build
└── Makefile                 # Commands
```

## API Endpoints

### Authentication
- `POST /api/v1/auth/register` - Register user
- `POST /api/v1/auth/login` - Login user
- `GET /api/v1/me` - Get current user (protected)

### Books
- `GET /api/v1/books` - List books
- `GET /api/v1/books/:id` - Get book
- `POST /api/v1/books` - Create book (protected)
- `POST /api/v1/books/batch` - Batch upload books via CSV (protected)
- `PATCH /api/v1/books/:id` - Update book (protected)
- `DELETE /api/v1/books/:id` - Delete book (protected)

### Users
- `GET /api/v1/users/:id/profile` - Get user profile
- `GET /api/v1/leaderboard` - Get leaderboard

### Reading Ideas
- `POST /api/v1/ideas` - Create idea (protected)
- `GET /api/v1/books/:bookId/ideas` - Get ideas for book
- `POST /api/v1/ideas/:id/vote` - Vote on idea (protected)

### Reviews
- `POST /api/v1/reviews` - Create review (protected)
- `GET /api/v1/users/:id/reviews` - Get user reviews

### Donations
- `POST /api/v1/donations` - Create donation (protected)
- `GET /api/v1/donations` - List donations

### Bookmarks
- `POST /api/v1/bookmarks` - Create bookmark (protected)
- `DELETE /api/v1/bookmarks/:bookId` - Delete bookmark (protected)
- `GET /api/v1/bookmarks` - Get user bookmarks (protected)

## Development Commands

```bash
make dev          # Start with hot reload
make up           # Start production mode
make down         # Stop containers
make restart      # Restart backend
make logs         # View logs
make db-shell     # Access database
make migrate-up   # Run migrations
make seed         # Create admin user
make test         # Run tests
make lint         # Run linter
make build        # Build binary
make clean        # Clean up
make help         # Show all commands
```

## Environment Variables

See `.env.example` for all configuration options:

```bash
# Database
DB_HOST=postgres
DB_PORT=5432
DB_USER=library_user
DB_PASSWORD=library_pass
DB_NAME=online_library

# Server
PORT=8080
GIN_MODE=debug

# JWT
JWT_SECRET=your-secret-key-change-in-production
```

## Success Score System

Users earn/lose points based on actions:

| Action | Points |
|--------|--------|
| Return book on time | +10 |
| Return book late | -15 |
| Positive review (4-5 stars) | +5 |
| Negative review (<3 stars) | -10 |
| Post reading idea | +3 |
| Idea upvoted | +1 |
| Idea downvoted | -1 |
| Lost book | -50 |
| Donate book | +20 |
| Money donation | +10 |

## Architecture Benefits

✅ **Clean Architecture** - Clear separation of concerns  
✅ **Testability** - Easy to mock via port interfaces  
✅ **Maintainability** - Domain logic isolated  
✅ **Scalability** - Easy to add new domains  
✅ **Type Safety** - UUID types, domain entities  
✅ **Structured Logging** - Production-ready with Zap  
✅ **Graceful Shutdown** - Context-based cancellation  

## Testing

```bash
# Run tests
make test

# Run with coverage
make test-cover

# Run specific package
go test ./internal/auth/...
```

## Deployment

### Docker Production

```bash
# Build and start
make up

# Check status
docker ps

# View logs
docker logs amar-pathagar-backend
```

### Standalone Binary

```bash
# Build
make build-binary

# Run
./amar-pathagar-api
```

## Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

## License

MIT License

---

**Built with Clean Architecture principles** 🏗️
