# Cấu trúc thư mục

Một dự án theo DDD thường được chia thành bốn tầng: **Domain**, **Application**, **Infrastructure**, **API**. Mỗi tầng có nhiệm vụ riêng:

* <mark style="color:green;">**Domain**</mark>: Chứa các **Entity** (thực thể nghiệp vụ cốt lõi), **Value Object** (đối tượng giá trị bất biến) và **Domain Event** (sự kiện miền). Tầng Domain mô tả các khái niệm và logic nghiệp vụ, **không phụ thuộc** vào chi tiết kỹ thuật bên ngoài (database, UI, v.v.).
* <mark style="color:green;">**Application**</mark>: Chứa các **Use Case** hoặc **Service ứng dụng** triển khai nghiệp vụ bằng cách sử dụng domain. Ở đây thường triển khai **CQRS** – tách biệt **Commands** (lệnh ghi dữ liệu) và **Queries** (truy vấn đọc dữ liệu) – với các **Handler** tương ứng. MediatR được sử dụng để kết nối các command/query với handler và quản lý việc phát các domain event.
* <mark style="color:green;">**Infrastructure**</mark>: Chứa các chi tiết kỹ thuật như **database context**, **Repository** và **Unit of Work**, tích hợp Dapper cho các truy vấn hiệu năng cao, và triển khai **Redis caching**. Tầng này cung cấp các **implementation** cho các interface trong domain/application (ví dụ repository), tách biệt hạ tầng khỏi logic nghiệp vụ​.
* <mark style="color:green;">**API**</mark>: Tầng API chứa **Controller** (đối với Web API) hoặc UI khác. Controller nhận request từ bên ngoài, chuyển đổi thành command/query và gửi vào **Application** (thông qua MediatR), sau đó trả kết quả về client. Tầng này không chứa logic nghiệp vụ mà chỉ điều phối dữ liệu giữa client và Application.

**Cấu trúc thư mục mẫu:**

```plaintext
src/
├── MyProject.Domain
│   ├── Entities
│   │   └── User.cs
│   ├── ValueObjects
│   │   └── Address.cs
│   └── Events
│       └── UserCreatedEvent.cs
├── MyProject.Application
│   ├── Commands
│   │   ├── UserProductCommand.cs
│   │   └── UserProductHandler.cs
│   ├── Queries
│   │   ├── GetUserByIdQuery.cs
│   │   └── GetUserByIdHandler.cs
│   └── Services (ứng dụng)
│       └── (các case nghiệp vụ khác,...)
├── MyProject.Infrastructure
│   ├── Persistence
│   │   ├── AppDbContext.cs
│   │   ├── Repositories
│   │   │   └── UserRepository.cs
│   │   └── UnitOfWork.cs
│   ├── Caching
│   │   └── RedisCacheService.cs
│   ├── ResourceManager.cs
│   └── (các service hạ tầng khác: logging, email,...)
└── MyProject.Api
    ├── Controllers
    │   └── UserController.cs
    └── Program.cs / Startup.cs (cấu hình DI, MediatR, etc.)
```

_Cấu trúc trên có thể điều chỉnh tùy dự án._ Ứng với mỗi <mark style="color:red;">**Bounded Context**</mark> (bối cảnh miền) trong domain, ta tạo các thư mục và class tương ứng. Tầng **Domain** độc lập, các tầng khác tùy thuộc “hướng vào trong” (theo nguyên tắc Dependency Inversion) – ví dụ, tầng Application tham chiếu Domain, tầng Infrastructure tham chiếu Application/Domain, còn API tham chiếu Application​. Cách tổ chức này giúp mã nguồn dễ mở rộng và bảo trì, vì mỗi tầng đảm nhiệm một vai trò rõ ràng.
