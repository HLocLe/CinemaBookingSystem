# Cinema Booking System

## Giới Thiệu Dự Án

Cinema Booking System là một ứng dụng web toàn diện dành cho việc quản lý và đặt vé xem phim tại các rạp chiếu phim. Hệ thống được xây dựng với kiến trúc Clean Architecture sử dụng .NET 8, cung cấp các tính năng hiện đại cho cả người dùng và quản trị viên.

## Công Nghệ Sử Dụng

**Backend:**
- NET 8 (C#)
- ASP.NET Core WebAPI
- Entity Framework Core
- PostgreSQL
- Elasticsearch (8.12.0)
- Docker & Docker Compose
- JWT Authentication
- SMTP Email Service

**Infrastructure:**
- Docker containerization
- docker-compose orchestration
- PostgreSQL database
- Elasticsearch for search functionality
- CI/CD with GitLab

## Kiến Trúc Dự Án

Dự án được tổ chức theo Clean Architecture với 4 tầng chính:

```
WebAPI/
├── WebAPI/              # Presentation Layer (Controllers, Middleware)
├── Application/         # Application Layer (Services, DTOs, Business Logic)
├── Infrastructure/      # Infrastructure Layer (Database, External Services)
└── Domain/             # Domain Layer (Entities, Interfaces, Business Rules)
```

### Các Thành Phần Chính

**Domain Layer (Domain/)**
- Các thực thể (Entities) của hệ thống
- Business rules và logic
- Các Interface định nghĩa hợp đồng

**Application Layer (Application/)**
- ViewModels (EntityVM, EntityAddVM)
- Service interfaces (IService)
- Business logic implementation
- DTO mapping configuration
- Use cases và workflows

**Infrastructure Layer (Infrastructure/)**
- Database context (Entity Framework Core)
- Unit of Work pattern implementation
- Repository implementations
- External service integrations (Payment, Email)
- Mapper configuration

**Presentation Layer (WebAPI/)**
- REST API Controllers
- HTTP request/response handling
- Route configuration
- Middleware setup
- Swagger/OpenAPI documentation

## Quy Trình Phát Triển

Khi thêm một thực thể mới vào hệ thống, tuân theo các bước sau:

1. **Tạo ViewModels**
   - EntityVM: Dùng cho API responses
   - EntityAddVM: Dùng cho API requests (thêm mới/cập nhật)

2. **Tạo Service Interface**
   - I{Entity}Service interface
   - Định nghĩa các thao tác CRUD cơ bản
   - Định nghĩa các phương thức business logic

3. **Implement Service Class**
   - {Entity}Service class
   - Sử dụng IUnitOfWork để thực hiện CRUD operations
   - Implement business logic

4. **Cấu Hình Dependency Injection**
   - Đăng ký Service Interface với Repository
   - Cấu hình Scoped lifetime

5. **Cấu Hình AutoMapper**
   - Thêm mapping configuration trong MapperConfig
   - Cấu hình Entity <-> ViewModel transformations

6. **Tạo Controller**
   - Tạo {Entity}Controller
   - Implement các endpoints dựa trên IService methods
   - Xử lý HTTP verbs (GET, POST, PUT, DELETE)

## Cấu Hình Hệ Thống

### Database Configuration

PostgreSQL được cấu hình với:
- Host: postgres-db
- Port: 5432
- Database: MovieTheater
- Username: postgres
- Password: 123456 (development only)

Database sẽ được khởi tạo từ các script trong thư mục `database/`

### Elasticsearch Configuration

Elasticsearch được sử dụng cho:
- Full-text search
- Indexing dữ liệu phim, rạp chiếu
- Performance optimization

Configuration:
- Host: elasticsearch
- Port: 9200
- Single-node cluster
- Memory: 512MB

### Cấu Hình JWT Authentication

- Secret Key: GvlKp62o31vaP3WzWsQDO3hOQpZdbhtt
- Issuer: YourAppName
- Audience: YourAppAudience
- Access Token Expiry: 15 minutes
- Refresh Token Expiry: 7 days

### Cấu Hình VnPay Payment Gateway

- TMN Code: 7QRTMNBH
- Hash Secret: 4046G9C8YY9H8WFSJG8AVB9VZNTT1D68
- Payment URL: https://sandbox.vnpayment.vn/paymentv2/vpcpay.html
- Currency: VND
- Locale: Vietnamese

### Cấu Hình Email Service

- SMTP Server: smtp.gmail.com
- Port: 587
- Sender Email: fmoviecinema@gmail.com
- Sender Name: FCinema
- SSL/TLS: Enabled

## Chạy Dự Án

### Yêu Cầu

- Docker & Docker Compose
- .NET 8 SDK (để chạy locally)
- Git

### Chạy với Docker Compose

```bash
# Build và chạy toàn bộ stack
docker-compose up -d

# Kiểm tra các services
docker-compose ps

# Xem logs
docker-compose logs -f api
```

**Services sẽ chạy tại:**
- API Application: http://localhost:8081
- Swagger UI: http://localhost:8081/swagger
- PostgreSQL: localhost:5432
- Elasticsearch: http://localhost:9200

### Chạy Locally (Development)

```bash
# Restore dependencies
dotnet restore

# Build project
dotnet build

# Run migrations
dotnet ef database update

# Run API
dotnet run --project WebAPI/WebAPI/WebAPI.csproj
```

## API Documentation

Swagger API documentation có sẵn tại `/swagger` endpoint khi ứng dụng chạy.

### Authentication

Các endpoint được bảo vệ yêu cầu JWT token trong header:
```
Authorization: Bearer {token}
```

### Response Format

Tất cả responses tuân theo format chuẩn:
```json
{
  "success": true,
  "data": {...},
  "message": "Success",
  "errors": null
}
```

## Deployment

### Production Deployment

Xem chi tiết tại `DEPLOYMENT-GUIDE.md` và `production-env-setup.md`

**Service URLs (Production):**
- API Application: http://vps.purintech.id.vn:8081
- Swagger UI: http://vps.purintech.id.vn:8081/swagger
- Health Check: http://vps.purintech.id.vn:8081/health
- Elasticsearch: http://vps.purintech.id.vn:9200

### Environment Variables (Production)

Các biến environment quan trọng:
- `ASPNETCORE_ENVIRONMENT`: Production
- `ConnectionStrings__Local`: Database connection string
- `ElasticSearch__Uri`: Elasticsearch URI
- `JwtSettings__SecretKey`: JWT secret key
- `VnPay__*`: Payment gateway settings
- `EmailSettings__*`: Email service settings
- `SWAGGER_ENABLED`: Enable/disable Swagger

## Cấu Trúc Thư Mục

```
.
├── WebAPI/                      # Main solution folder
│   ├── WebAPI/                  # API layer
│   │   ├── Controllers/         # API endpoints
│   │   ├── Middleware/          # Custom middleware
│   │   ├── Program.cs          # Application startup
│   │   └── appsettings.*.json  # Configuration files
│   ├── Application/             # Business logic layer
│   │   ├── Services/           # Service implementations
│   │   ├── DTOs/               # Data transfer objects
│   │   ├── ViewModels/         # View models
│   │   └── Mapping/            # AutoMapper config
│   ├── Infrastructure/          # Data access layer
│   │   ├── Data/               # DbContext & migrations
│   │   ├── Repositories/       # Repository implementations
│   │   └── External/           # External service integrations
│   └── Domain/                  # Business rules layer
│       ├── Entities/           # Domain entities
│       ├── Interfaces/         # Contract definitions
│       └── Constants/          # Constants & enums
├── database/                    # Database initialization scripts
├── docker-compose.yml           # Docker compose configuration
├── Dockerfile                   # API container image
├── DEPLOYMENT-GUIDE.md         # Deployment instructions
├── production-env-setup.md     # Production environment setup
└── README.md                   # This file
```

## Tính Năng Chính

**Cho Người Dùng:**
- Duyệt danh sách phim đang chiếu
- Tìm kiếm phim theo tiêu chí
- Xem chi tiết phim và suất chiếu
- Đặt vé xem phim
- Thanh toán qua VnPay
- Quản lý lịch sử đặt vé
- Nhận thông báo qua email

**Cho Quản Trị Viên:**
- Quản lý phim (thêm, sửa, xóa)
- Quản lý rạp chiếu và phòng
- Quản lý suất chiếu
- Xem báo cáo doanh thu
- Quản lý người dùng
- Xem lịch sử giao dịch

## Xử Lý Lỗi và Logging

Hệ thống sử dụng:
- Structured logging
- Exception handling middleware
- Error tracking và reporting
- Docker logs để debugging

Xem logs:
```bash
docker-compose logs -f [service-name]
```

## Database Migrations

Migrations được quản lý thông qua Entity Framework Core:

```bash
# Tạo migration mới
dotnet ef migrations add {MigrationName} --project WebAPI/Infrastructure/Infrastructure.csproj

# Apply migrations
dotnet ef database update --project WebAPI/Infrastructure/Infrastructure.csproj
```

## Bảo Mật

- JWT token-based authentication
- Password hashing
- SQL injection prevention (EF Core parameterized queries)
- CORS configuration
- HTTPS in production
- Environment variable management
- Secure password practices

## Performance Optimization

- Elasticsearch indexing
- Database query optimization
- Caching strategies
- Connection pooling
- Async/await patterns

## Hỗ Trợ và Liên Hệ

Để báo cáo bug hoặc yêu cầu tính năng mới, vui lòng tạo Issue trên GitHub.

## Phát Triển Tiếp Theo

Các tính năng có thể phát triển thêm:
- Mobile application
- Real-time notifications (WebSocket)
- Advanced analytics dashboard
- Recommendation engine
- Loyalty program
- API rate limiting
- Advanced caching strategies
- Multi-language support

## License

Dự án này được phát triển cho mục đích học tập.

## Cập Nhật Gần Đây

- Full stack deployment with Docker Compose
- PostgreSQL integration with initialization scripts
- Elasticsearch integration
- Production environment setup
- JWT authentication & authorization
- VnPay payment gateway integration
- Email notification service
- Clean Architecture implementation
