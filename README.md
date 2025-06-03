# Deuss Backend API

A robust backend API for Deuss, built with Express.js and PostgreSQL, featuring comprehensive authentication, session management, and integrations with various platforms.

## Features

- 🔐 Secure authentication with JWT and session management
- 📧 Email verification and password reset functionality
- 📝 Notes management system
- 🎯 Hacking platform integrations (TryHackMe)
- 🌐 Social media platform integrations
- 📊 User profile management
- 🔄 Session persistence with PostgreSQL
- 🐳 Docker support for easy deployment

## Quick Start

### Using Docker (Recommended)

1. Clone the repository:
```bash
git clone https://github.com/deuss-space/Profiler-Backend.git
cd Profiler-Backend
```

2. Configure Docker Compose(only if needed):
```bash
nano docker-compose.yml
```

3. Start the application:
```bash
docker-compose up -d
```

For detailed Docker deployment instructions, see [DOCKER-README.md](DOCKER-README.md).

### Manual Setup

1. Install dependencies:
```bash
npm install
```

2. Configure the application:
```bash
cp .env.example .env
```

3. Start the application:
```bash
npm run dev
```

## Configuration

### Required Environment Variables

Configure these in your `.env` :

```bash
# Change only if needed
# Database Configuration
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/deuss_db
# JWT Configuration
JWT_SECRET=change_in_prod
JWT_EXPIRES_IN=24h
# Server Configuration
PORT=3002
NODE_ENV=development
PGSSLMODE=disable
```
### Optional Environment Variables

```bash
EMAIL_VERIFICATION=false #change in prod

SMTP_FROM=noreply@mail.deuss.space #change to your mail if prod
FRONTEND_URL=http://localhost:3000 #change to your frontend *NEXTAPP* URL if in prod

# Resend Configuration
RESEND_API_KEY=resend_api #Change this to valid api key only if in prod
RESEND_FROM=noreply@mail.deuss.space #change this if in prod

```

## API Documentation

### Authentication Endpoints

#### User Registration
```http
POST /api/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "secure_password",
  "fullName": "John Doe"
}
```

#### User Login
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "secure_password"
}
```

#### Session Management
```http
GET /api/auth/session
Authorization: Bearer <jwt_token>
```

#### Password Reset
```http
POST /api/auth/forgot-password
Content-Type: application/json

{
  "email": "user@example.com"
}
```

### Notes Management

#### Get All Notes
```http
GET /api/notes
Authorization: Bearer <jwt_token>
```

#### Create/Update Note
```http
POST /api/notes
Authorization: Bearer <jwt_token>
Content-Type: application/json

{
  "content": "Note content",
  "title": "Note title",
  "tags": ["tag1", "tag2"]
}
```

#### Delete Note
```http
DELETE /api/notes/:id
Authorization: Bearer <jwt_token>
```

### Platform Integrations

#### Hacking Profiles
```http
GET /api/hacking-profiles
POST /api/connect-platform
POST /api/disconnect-platform
```

#### Social Profiles
```http
GET /api/social-profiles
POST /api/connect-social
POST /api/disconnect-social
```

#### TryHackMe Integration
```http
GET /api/tryhackme/rank/:username
GET /api/tryhackme/badges/:username
GET /api/tryhackme/rooms/:username
GET /api/tryhackme/user/:username
GET /api/tryhackme/tickets/:username
```

## Database Schema

### Users Table
```sql
CREATE TABLE deuss.users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password VARCHAR(255) NOT NULL,
  full_name VARCHAR(255) NOT NULL,
  is_verified BOOLEAN DEFAULT false,
  verification_token VARCHAR(255),
  verification_token_expiry TIMESTAMP,
  reset_token VARCHAR(255),
  reset_token_expiry TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

### Session Table
```sql
CREATE TABLE deuss.session (
  sid VARCHAR NOT NULL PRIMARY KEY,
  sess JSON NOT NULL,
  expire TIMESTAMP(6) NOT NULL
);
```

## Security Features

- JWT-based authentication
- Session management with PostgreSQL
- Password hashing with bcrypt
- Email verification
- Rate limiting
- CORS protection
- Secure cookie handling
- Input validation
- SQL injection prevention

## Development

### Prerequisites
- Node.js 16+
- PostgreSQL 15+
- Docker and Docker Compose (for containerized deployment)

### Scripts
```bash
npm run dev          # Start development server
npm start           # Start production server
npm run setup-db    # Initialize database
npm test           # Run tests
```

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request
6. Check https://deuss.space/#contribute

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For support, please:
- Create an issue in the repository
- Contact the development team
- Check the [DOCKER-README.md](DOCKER-README.md) for deployment issues 