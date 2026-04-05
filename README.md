# Finance Data Processing and Access Control Backend

A comprehensive backend system for managing financial records with role-based access control, built with Node.js, Express, and MongoDB.

## Features

### 1. **User and Role Management**
- User registration and authentication with JWT tokens
- Three role types: Viewer, Analyst, and Admin
- User status management (active/inactive)
- Secure password hashing with bcryptjs

### 2. **Financial Records Management**
- CRUD operations for financial entries (income/expenses)
- Multiple transaction categories
- Support for filtering by date, category, and type
- Timestamped record creation and updates
- Validation for all inputs

### 3. **Dashboard Summary Analytics**
- Total income and expense calculations
- Net balance tracking
- Category-wise breakdown
- Monthly trends visualization
- Recent activity feed
- System-wide analytics (admin only)

### 4. **Role-Based Access Control (RBAC)**
- **Viewer**: Can only view their dashboard data and recent activity
- **Analyst**: Can create, read, and update financial records; view analytics
- **Admin**: Full system access including user management and system analytics

### 5. **Data Validation and Error Handling**
- Comprehensive input validation
- Meaningful error messages
- Proper HTTP status codes
- Protection against invalid operations


### OUTPUT SCREENSHOTS
<img width="903" height="312" alt="image" src="https://github.com/user-attachments/assets/082e6995-bd3a-419a-8fa2-17640f27e685" />
<img width="903" height="315" alt="image" src="https://github.com/user-attachments/assets/1223d157-bd82-4c7b-b354-63873999b244" />
<img width="903" height="304" alt="image" src="https://github.com/user-attachments/assets/a342cc10-c16a-4c92-8629-8fafad2bc81b" />
<img width="903" height="274" alt="image" src="https://github.com/user-attachments/assets/ce6825f6-a40b-4bc7-9074-e9c2522cf77c" />
<img width="1310" height="525" alt="image" src="https://github.com/user-attachments/assets/5ec2b9cb-9dbd-4bb6-8f23-fec606fa600c" />
<img width="1163" height="575" alt="image" src="https://github.com/user-attachments/assets/69a0240f-435d-4c69-9888-8bc515577ffc" />
<img width="903" height="497" alt="image" src="https://github.com/user-attachments/assets/8548cbbf-30e5-4ab8-8c29-56cf8b9553cf" />
<img width="903" height="252" alt="image" src="https://github.com/user-attachments/assets/259837c3-adcf-4b97-8145-0d3283a3240d" />

## Project Structure

```
finance-backend/
├── src/
│   ├── index.js                 # Main server entry point
│   ├── models/
│   │   ├── User.js             # User schema and methods
│   │   └── FinancialRecord.js   # Financial record schema
│   ├── controllers/
│   │   ├── userController.js    # User management logic
│   │   ├── recordController.js  # Record management logic
│   │   └── dashboardController.js # Analytics logic
│   ├── routes/
│   │   ├── userRoutes.js       # User endpoints
│   │   ├── recordRoutes.js     # Record endpoints
│   │   └── dashboardRoutes.js  # Dashboard endpoints
│   ├── middleware/
│   │   ├── auth.js             # JWT authentication
│   │   ├── authorize.js        # Role-based authorization
│   │   └── errorHandler.js     # Global error handler
│   ├── services/               # Business logic services
│   └── utils/                  # Utility functions
├── .env                         # Environment configuration
├── package.json                # Dependencies
└── README.md                   # Documentation
```

## Installation and Setup

### Prerequisites
- Node.js (v14 or higher)
- MongoDB (local or cloud instance)
- npm or yarn

### Step 1: Clone and Install Dependencies

```bash
cd finance-backend
npm install
```

### Step 2: Configure Environment Variables

Edit `.env` file with your settings:

```env
NODE_ENV=development
PORT=3000
MONGODB_URI=mongodb://localhost:27017/finance_db
JWT_SECRET=your-secret-key-change-in-production
```

**For MongoDB Atlas Cloud:**
```env
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/finance_db
```

### Step 3: Start MongoDB

**Local MongoDB:**
```bash
# On Windows
mongod

# On macOS (if installed via Homebrew)
brew services start mongodb-community
```

**Or use MongoDB Atlas** (cloud): No local setup needed if using cloud database.

### Step 4: Start the Server

```bash
# Development mode (with auto-reload)
npm run dev

# Production mode
npm start
```

Server will be running at: `http://localhost:3000`

## API Documentation

### Base URL
```
http://localhost:3000/api
```

### Authentication
Most endpoints require a JWT token in the `Authorization` header:
```
Authorization: Bearer <your-jwt-token>
```

---

## User Endpoints

### 1. Register User
**POST** `/users/register`

**Request:**
```json
{
  "email": "user@example.com",
  "name": "John Doe",
  "password": "password123",
  "role": "viewer"  // optional: viewer, analyst, admin (default: viewer)
}
```

**Response:**
```json
{
  "success": true,
  "message": "User registered successfully",
  "user": {
    "id": "userId",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "viewer"
  }
}
```

---

### 2. Login User
**POST** `/users/login`

**Request:**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": "userId",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "viewer"
  }
}
```

---

### 3. Get All Users
**GET** `/users`

**Auth Required:** Yes (Admin only)

**Response:**
```json
{
  "success": true,
  "count": 5,
  "users": [...]
}
```

---

### 4. Get User by ID
**GET** `/users/:id`

**Auth Required:** Yes

**Response:**
```json
{
  "success": true,
  "user": {...}
}
```

---

### 5. Update User
**PUT** `/users/:id`

**Auth Required:** Yes

**Request:**
```json
{
  "name": "Jane Doe",
  "role": "analyst",      // Admin only
  "status": "inactive"    // Admin only
}
```

**Response:**
```json
{
  "success": true,
  "message": "User updated successfully",
  "user": {...}
}
```

---

### 6. Delete User
**DELETE** `/users/:id`

**Auth Required:** Yes (Admin only)

**Response:**
```json
{
  "success": true,
  "message": "User deleted successfully"
}
```

---

## Financial Records Endpoints

### 1. Create Record
**POST** `/records`

**Auth Required:** Yes (Analyst, Admin)

**Request:**
```json
{
  "amount": 5000,
  "type": "income",  // income or expense
  "category": "salary",
  "date": "2024-01-15",  // optional, defaults to today
  "notes": "Monthly salary"
}
```

**Valid Categories:**
- **Income:** salary, freelance, investment, other_income
- **Expense:** food, transportation, utilities, entertainment, healthcare, education, other_expense

**Response:**
```json
{
  "success": true,
  "message": "Record created successfully",
  "record": {...}
}
```

---

### 2. Get Records
**GET** `/records`

**Auth Required:** Yes

**Query Parameters:**
- `type` - Filter by type (income/expense)
- `category` - Filter by category
- `startDate` - Filter records from this date
- `endDate` - Filter records until this date
- `sortBy` - Sort field (default: -date)
- `page` - Page number (default: 1)
- `limit` - Records per page (default: 20, max: 100)

**Example:**
```
GET /records?type=expense&category=food&startDate=2024-01-01&endDate=2024-01-31&page=1&limit=20
```

**Response:**
```json
{
  "success": true,
  "count": 5,
  "total": 25,
  "page": 1,
  "pages": 5,
  "records": [...]
}
```

---

### 3. Get Record by ID
**GET** `/records/:id`

**Auth Required:** Yes

**Response:**
```json
{
  "success": true,
  "record": {...}
}
```

---

### 4. Update Record
**PUT** `/records/:id`

**Auth Required:** Yes (Analyst, Admin)

**Request:**
```json
{
  "amount": 5500,
  "category": "investment"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Record updated successfully",
  "record": {...}
}
```

---

### 5. Delete Record
**DELETE** `/records/:id`

**Auth Required:** Yes (Analyst, Admin)

**Response:**
```json
{
  "success": true,
  "message": "Record deleted successfully"
}
```

---

## Dashboard Analytics Endpoints

### 1. Get Dashboard Summary
**GET** `/dashboard/summary`

**Auth Required:** Yes

**Query Parameters:**
- `startDate` - Optional: filter from this date
- `endDate` - Optional: filter until this date

**Response:**
```json
{
  "success": true,
  "summary": {
    "totalIncome": 50000,
    "totalExpense": 15000,
    "netBalance": 35000,
    "totalTransactions": 45
  }
}
```

---

### 2. Get Category Breakdown
**GET** `/dashboard/category-breakdown`

**Auth Required:** Yes (Analyst, Admin)

**Query Parameters:**
- `type` - Filter by type (income/expense)
- `startDate` - Optional filter date
- `endDate` - Optional filter date

**Response:**
```json
{
  "success": true,
  "categoryBreakdown": [
    {
      "_id": "food",
      "total": 3500,
      "count": 15
    },
    {
      "_id": "transportation",
      "total": 2000,
      "count": 8
    }
  ]
}
```

---

### 3. Get Monthly Trends
**GET** `/dashboard/monthly-trends`

**Auth Required:** Yes (Analyst, Admin)

**Response:**
```json
{
  "success": true,
  "monthlyTrends": [
    {
      "month": "2024-01",
      "income": 50000,
      "expense": 15000,
      "netBalance": 35000,
      "transactions": 25
    }
  ]
}
```

---

### 4. Get Recent Activity
**GET** `/dashboard/recent-activity`

**Auth Required:** Yes

**Query Parameters:**
- `limit` - Number of records to return (default: 10, max: 50)

**Response:**
```json
{
  "success": true,
  "count": 10,
  "recentActivity": [...]
}
```

---

### 5. Get System Analytics
**GET** `/dashboard/system-analytics`

**Auth Required:** Yes (Admin only)

**Response:**
```json
{
  "success": true,
  "systemAnalytics": {
    "totalIncome": 500000,
    "totalExpense": 150000,
    "totalRecords": 450,
    "totalUniqueUsers": 12
  }
}
```

---

## Error Handling

All errors follow a consistent format:

```json
{
  "success": false,
  "message": "Error description"
}
```

### Common HTTP Status Codes

- **200** - Success
- **201** - Created
- **400** - Bad Request (validation error)
- **401** - Unauthorized (missing or invalid token)
- **403** - Forbidden (insufficient permissions)
- **404** - Not Found
- **500** - Internal Server Error

---

## Role-Based Access Control Summary

| Action | Viewer | Analyst | Admin |
|--------|--------|---------|-------|
| View own records | ✓ | ✓ | ✓ |
| View own dashboard | ✓ | ✓ | ✓ |
| Create records | ✗ | ✓ | ✓ |
| Update own records | ✗ | ✓ | ✓ |
| Delete own records | ✗ | ✓ | ✓ |
| View analytics | ✗ | ✓ | ✓ |
| View category breakdown | ✗ | ✓ | ✓ |
| View monthly trends | ✗ | ✓ | ✓ |
| Manage users | ✗ | ✗ | ✓ |
| View system analytics | ✗ | ✗ | ✓ |

---

## Testing the API

### Using Postman or cURL

**1. Register a user:**
```bash
curl -X POST http://localhost:3000/api/users/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "analyst@example.com",
    "name": "Jane Analyst",
    "password": "password123",
    "role": "analyst"
  }'
```

**2. Login:**
```bash
curl -X POST http://localhost:3000/api/users/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "analyst@example.com",
    "password": "password123"
  }'
```

**3. Create a record (use token from login):**
```bash
curl -X POST http://localhost:3000/api/records \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "amount": 5000,
    "type": "income",
    "category": "salary",
    "notes": "Monthly salary"
  }'
```

---

## Database Design

### User Collection
```javascript
{
  _id: ObjectId,
  email: String (unique),
  name: String,
  password: String (hashed),
  role: String (viewer|analyst|admin),
  status: String (active|inactive),
  createdAt: Date,
  updatedAt: Date
}
```

### FinancialRecord Collection
```javascript
{
  _id: ObjectId,
  userId: ObjectId (reference to User),
  amount: Number,
  type: String (income|expense),
  category: String,
  date: Date,
  notes: String,
  createdAt: Date,
  updatedAt: Date
}
```

### Indexes
- `FinancialRecord`: { userId, date } - For fast record queries
- `FinancialRecord`: { userId, type } - For filtering by type
- `FinancialRecord`: { userId, category } - For filtering by category

---

## Security Considerations

1. **Password Security**: Passwords are hashed using bcryptjs with salt rounds
2. **JWT Tokens**: Tokens expire in 7 days
3. **Role-Based Access**: All sensitive operations require appropriate role
4. **Input Validation**: All inputs are validated before processing
5. **Error Messages**: Do not expose sensitive system information
6. **Database Credentials**: Use environment variables, never commit .env

### Recommendations for Production

1. Use HTTPS/SSL
2. Implement rate limiting
3. Add request logging and monitoring
4. Use a secrets manager (AWS Secrets, HashiCorp Vault)
5. Enable MongoDB authentication and encryption
6. Set up automated backups
7. Implement API versioning
8. Add comprehensive logging
9. Use environment-specific configurations
10. Deploy using Docker containers

---

## Assumptions and Decisions

1. **Authentication**: JWT tokens for simplicity; suitable for stateless API design
2. **Role Hierarchy**: Admin > Analyst > Viewer (admins have all permissions)
3. **Record Ownership**: Each record is tied to a user; admins can view any record
4. **Date Filtering**: Uses ISO 8601 format (YYYY-MM-DD)
5. **Pagination**: Default 20 records per page, max 100 to prevent abuse
6. **Soft Delete**: Not implemented; uses hard delete (can add timestamps for recovery)
7. **Concurrency**: MongoDB handles concurrent updates with optimistic locking
8. **Time Zone**: Uses server time (UTC); can be enhanced for user time zones

---

## Future Enhancements

1. Add pagination with cursor-based navigation
2. Implement soft delete for records
3. Add email notifications
4. Implement two-factor authentication
5. Add API rate limiting
6. Add comprehensive logging and monitoring
7. Implement GraphQL API alongside REST
8. Add email verification for registration
9. Implement budget tracking and alerts
10. Add export functionality (CSV, PDF)
11. Add recurring transaction support
12. Implement wallet/account management

---

## Troubleshooting

### MongoDB Connection Error
```
Error: connect ECONNREFUSED 127.0.0.1:27017
```
**Solution**: Ensure MongoDB is running or update MONGODB_URI in .env

### JWT Token Issues
```
Error: Invalid or expired token
```
**Solution**: Include full token in Authorization header: `Bearer <token>`

### Role Access Denied
```
Error: Access denied. Required roles: analyst, admin
```
**Solution**: User doesn't have required role. Update user role via admin endpoint.

### Duplicate Email Error
```
Error: email already exists
```
**Solution**: Email is already registered. Use a different email or login instead.

---

## Support and Questions

For issues or questions about this assignment:
1. Check the API documentation above
2. Review error messages and status codes
3. Verify role permissions in the RBAC table
4. Ensure MongoDB connection and data persistence

---









## License

MIT License - Feel free to use for learning and development.
