Book Heaven — Book Store

Book Heaven is an ASP.NET Core 8 bookstore web application. Visitors can browse, search, and sort a book catalog. Registered users can manage a shopping cart, while administrators can add books and upload cover images.

## Features

- Book catalog with search by title or author and sorting by price or publication date
- User registration, login, logout, and role-based access through ASP.NET Core Identity
- Shopping cart associated with the signed-in user
- Admin book creation with image uploads served from `wwwroot/images/books`
- Password recovery through SMTP email
- Cloudflare Turnstile verification on registration
- REST API endpoints with Swagger/OpenAPI documentation
- SQL Server persistence using Entity Framework Core migrations

## Tech stack

- C# and ASP.NET Core 8 MVC
- Razor Views, HTML, CSS, JavaScript, and Bootstrap
- Entity Framework Core 8 and SQL Server
- ASP.NET Core Identity, cookie authentication, and session state
- Swagger / Swashbuckle

## Requirements

- .NET 8 SDK
- SQL Server or SQL Server Express
- A database and a SQL Server login with permission to access it
- Optional: SMTP credentials and a Cloudflare Turnstile secret for email recovery and registration verification

## Getting started

1. Clone the repository and open the project directory.

   ```powershell
   git clone <repository-url>
   cd Book-Store-main
   ```

2. Configure local secrets. From the project directory, initialize .NET user secrets and set the connection string for your SQL Server instance and database:

   ```powershell
   dotnet user-secrets init
   dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Server=localhost\SQLEXPRESS;Database=BookData;Trusted_Connection=True;TrustServerCertificate=True;"
   ```

   Replace the example server and database names with values from your machine. The Windows account running the app must have access to that database. Do not put real credentials or API secrets in a committed settings file.

   For password reset email and Turnstile verification, configure these user-secret keys as needed:

   ```powershell
   dotnet user-secrets set "EmailSettings:SmtpServer" "<smtp-server>"
   dotnet user-secrets set "EmailSettings:Port" "587"
   dotnet user-secrets set "EmailSettings:SenderName" "<sender-name>"
   dotnet user-secrets set "EmailSettings:SenderEmail" "<sender-email>"
   dotnet user-secrets set "EmailSettings:Username" "<smtp-username>"
   dotnet user-secrets set "EmailSettings:Password" "<smtp-password>"
   dotnet user-secrets set "Captcha:SecretKey" "<turnstile-secret>"
   ```

3. Restore packages and apply the database migrations:

   ```powershell
   dotnet restore
   dotnet ef database update
   ```

   If `dotnet ef` is unavailable, install the EF Core CLI tool with `dotnet tool install --global dotnet-ef` and retry.

4. Run the application:

   ```powershell
   dotnet run --launch-profile https
   ```

   The HTTPS launch profile uses `https://localhost:7265` and `http://localhost:5014`. Swagger is available at `https://localhost:7265/swagger`. You can also select the `http` or `https` profile in Visual Studio. When running without a launch profile or explicit URL, the app uses port `5015`.

## Demo accounts

On startup, the application creates the `Admin` and `User` roles and seeds demo accounts when they do not already exist:

- Admin: `admin@example.com` / `Admin@123`
- User: `user@example.com` / `User@123`

These credentials are for local development only. Change or remove the demo seeding before deploying the application publicly.

## API overview

| Method | Route | Purpose |
|---|---|---|
| `GET` | `/ApiBook/Books` | List books |
| `POST` | `/ApiBook/Books` | Add a book (admin role required; multipart form data) |
| `DELETE` | `/ApiBook/Books/{id}` | Delete a book |
| `GET` | `/api/Books/search?title=&author=&category=` | Search books |
| `GET` | `/api/Books/sort?sortBy=price&order=asc` | Sort books by price or date |
| `POST` | `/api/UserApi/register` | Register an account |
| `POST` | `/api/UserApi/login` | Sign in |
| `POST` | `/api/UserApi/logout` | Sign out |
| `POST` | `/api/UserApi/forgot-password` | Request a password reset email |
| `POST` | `/api/UserApi/reset-password` | Set a new password using a reset token |
| `POST` | `/api/Cart/add` | Add a book to the signed-in user's cart |
| `GET` | `/api/Cart/mycart` | Get the signed-in user's cart |
| `DELETE` | `/api/Cart/delete/{bookId}` | Remove a book from the signed-in user's cart |

## Project layout

```text
Controllers/   MVC and API controllers
Data/          EF Core context and database entities
Migrations/    EF Core database migrations
Models/        View models and application models
Services/      Authentication, book, and cart services
Views/         Razor pages
wwwroot/       Static assets and uploaded book images
Program.cs     Dependency injection and application startup
```

## Configuration and security

The checked-in `appsettings.json` should contain only non-sensitive defaults. Keep database credentials, SMTP passwords, and Turnstile secrets in .NET User Secrets for development or in environment variables / a secrets manager for deployment. Rotate any credentials that have previously been committed to source control.
