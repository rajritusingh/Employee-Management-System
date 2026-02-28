# Arizona Automation Technology - Employee Management System

A comprehensive Flask-based web application for managing employee attendance, expenses, and administrative tasks for Arizona Automation Technology, Ahmedabad.

## Features

### Employee Features
- **Login System**: Secure authentication using email/mobile and password
- **Daily Attendance**: Submit daily attendance with check-in/check-out times
- **Expense Management**: Upload monthly expenses via Excel files
- **Profile Management**: View and update personal information
- **Mobile-Friendly UI**: Responsive Bootstrap interface

### Admin/HR Features
- **Employee Management**: Add, edit, delete employee records
- **Expense Approval**: Review and approve/reject expense submissions
- **Payment Processing**: Record refunds and send notifications
- **Reports**: Generate attendance and expense reports
- **Audit Logs**: Track all system changes and actions
- **Export Functionality**: CSV/Excel export for payroll and accounting

### Technical Features
- **Excel Upload**: Parse expense data from Excel files using pandas
- **Email Notifications**: Automated email alerts for approvals/refunds
- **SMS Notifications**: Twilio integration for SMS alerts
- **Audit Trail**: Complete logging of all administrative actions
- **Role-Based Access**: Different permissions for employees, HR, and admins

## Technology Stack

- **Backend**: Flask (Python web framework)
- **Database**: MySQL (with SQLAlchemy ORM)
- **Frontend**: HTML5, Bootstrap 5, JavaScript
- **File Processing**: pandas, openpyxl for Excel handling
- **Authentication**: Flask-Login with bcrypt password hashing
- **Email**: Flask-Mail (SMTP)
- **SMS**: Twilio API
- **Deployment**: Gunicorn + Nginx (production)

## Installation

### Prerequisites
- Python 3.8+
- MySQL 8.0+
- Virtual environment (recommended)

### Setup Steps

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd arizona-automation
   ```

2. **Create virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```



   # Email settings
   MAIL_SERVER=smtp.gmail.com
   MAIL_PORT=587
   MAIL_USE_TLS=True
   MAIL_USERNAME=your-email@gmail.com
   MAIL_PASSWORD=your-app-password

   # Twilio settings
   TWILIO_ACCOUNT_SID=your-twilio-sid
   TWILIO_AUTH_TOKEN=your-twilio-token
   TWILIO_PHONE_NUMBER=your-twilio-number

   

6. **Create Initial Admin User**
   ```bash
   python create_admin.py
   ```
   This creates an admin user with:
   - Admin

7. **Run the application**
   ```bash
   python app.py
   ```

   The application will be available at 

## Initial Admin Setup

### Admin User Workflow
1. **Login as Admin**: 
2. **Create HR Accounts**: From Admin Dashboard → Employees → Add Employee (set role as 'hr')
3. **Create Employee Accounts**: From Admin Dashboard → Employees → Add Employee (set role as 'employee')
4. **Manage System**: Admin can manage all HR and Employee accounts

### User Roles
- **Admin**: Full system access, can create/manage all users
- **HR**: Can manage employees, salaries, and expenses
- **Employee**: Can submit attendance and expenses

### Important Notes
- The initial admin account is created only once
- Admin should change the default password after first login
- All user passwords are securely hashed using bcrypt
- Role-based access control prevents unauthorized actions

## Usage

### For Employees
1. **Login**: Use your email/mobile and password
2. **Submit Attendance**: Go to "Submit Attendance" and fill in your daily attendance
3. **Upload Expenses**: Use "Upload Expenses" to submit monthly expense Excel files
4. **View History**: Check your attendance and expense history

### For Admin/HR
1. **Dashboard**: View system statistics and pending tasks
2. **Manage Employees**: Add/edit/delete employee records
3. **Review Expenses**: Approve or reject expense submissions
4. **Process Payments**: Record refunds and send notifications
5. **Generate Reports**: View attendance reports and audit logs

## Excel Format for Expenses

The expense Excel file must have the following columns:
- **Date**: YYYY-MM-DD format
- **Category**: Expense category (e.g., Travel, Food, Stationery)
- **Description**: Detailed description of the expense
- **Amount**: Numeric amount (e.g., 150.50)

Example:
| Date       | Category | Description          | Amount |
|------------|----------|----------------------|--------|
| 2024-01-15 | Travel   | Bus fare to office   | 50.00  |
| 2024-01-16 | Food     | Lunch with client    | 200.00 |

## Deployment

### Production Setup (Ubuntu VPS)

1. **Install system dependencies**
   ```bash
   sudo apt update
   sudo apt install python3 python3-pip nginx mysql-server redis-server
   ```

   

3. **Set up application**
   ```bash
   # Clone and setup as above
   # Install gunicorn
   pip install gunicorn
   ```

4. **Create systemd service**
   ```bash
   sudo nano /etc/systemd/system/arizona-automation.service
   ```

   Add:
   ```
   [Unit]
   Description=Arizona Automation Flask App
   After=network.target

   [Service]
   User=ubuntu
   Group=ubuntu
   WorkingDirectory=/path/to/app
   Environment="PATH=/path/to/venv/bin"
   ExecStart=/path/to/venv/bin/gunicorn -w 4 -b 0.0.0.0:8000 app:app
   Restart=always

   [Install]
   WantedBy=multi-user.target
   ```

5. **Configure Nginx**
   ```bash
   sudo nano /etc/nginx/sites-available/arizona-automation
   ```

   Add:
   ```
   server {
       listen 80;
       server_name your-domain.com;

       location / {
           proxy_pass http://127.0.0.1:8000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
       }

       location /static {
           alias /path/to/app/static;
       }
   }
   ```

6. **Enable and start services**
   ```bash
   sudo systemctl enable arizona-automation
   sudo systemctl start arizona-automation
   sudo ln -s /etc/nginx/sites-available/arizona-automation /etc/nginx/sites-enabled
   sudo systemctl restart nginx
   ```

## Security Considerations

- **Password Security**: Bcrypt hashing for passwords
- **Session Management**: Secure session handling with Flask-Login
- **Input Validation**: Server-side validation for all forms
- **File Upload Security**: Restricted file types and size limits
- **SQL Injection Prevention**: SQLAlchemy ORM prevents SQL injection
- **XSS Protection**: Template escaping and input sanitization
- **CSRF Protection**: Flask-WTF for CSRF tokens
- **Audit Logging**: Complete audit trail for sensitive operations

## API Endpoints

### Authentication
- `POST /auth/login` - User login
- `POST /auth/logout` - User logout
- `POST /auth/register` - User registration

### Attendance
- `GET/POST /attendance/submit` - Submit attendance
- `GET /attendance/history` - View attendance history
- `GET /attendance/api/today` - Get today's attendance status

### Expenses
- `GET/POST /expenses/upload` - Upload expense Excel
- `GET /expenses/history` - View expense history
- `GET /expenses/details/<id>` - View expense details
- `GET /expenses/export/<id>` - Export expense to Excel

### Admin
- `GET /admin/dashboard` - Admin dashboard
- `GET/POST /admin/employee/add` - Add employee
- `GET/POST /admin/employee/edit/<id>` - Edit employee
- `POST /admin/employee/delete/<id>` - Delete employee
- `GET /admin/expenses` - Manage expenses
- `POST /admin/expense/approve/<id>` - Approve expense
- `POST /admin/expense/reject/<id>` - Reject expense
- `POST /admin/expense/refund/<id>` - Process refund
- `GET /admin/reports/attendance` - Attendance reports
- `GET /admin/audit-logs` - View audit logs

## Development

### Running Tests
```bash
python -m pytest
```

### Code Formatting
```bash
black .
flake8 .
```

### Database Migrations
```bash
flask db migrate
flask db upgrade
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is proprietary software for Arizona Automation Technology.

## Support

For support, contact the development team at riturajsingh65907@gmail.com

## Next Steps Checklist

- [ ] Set up development environment
- [ ] Configure database and environment variables
- [ ] Test user registration and login
- [ ] Test attendance submission
- [ ] Test expense upload and parsing
- [ ] Test admin functions
- [ ] Configure email and SMS services
- [ ] Set up production deployment
- [ ] Implement additional features (notifications, reports)
- [ ] Add comprehensive testing
- [ ] Performance optimization
- [ ] Security audit


