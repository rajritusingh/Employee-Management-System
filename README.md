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

@echo off
call venv\Scripts\activate
venv\Scripts\python app.py


Flask==2.3.3
Flask-SQLAlchemy==3.0.5
Flask-Login==0.6.3
Flask-Mail==0.9.1
Flask-WTF==1.2.1
WTForms==3.1.2
mysql-connector-python==8.1.0
pandas==2.0.3
numpy==1.26.4
openpyxl==3.1.2
twilio==8.2.2
bcrypt==4.0.1
python-dotenv==1.0.0
gunicorn==21.2.0
celery==5.3.1
redis==4.6.0
fpdf==1.7.2


from flask import Flask, render_template, request, redirect, url_for, flash, jsonify, send_from_directory
from flask_login import LoginManager, login_user, login_required, logout_user, current_user
from flask_mail import Mail
from flask_wtf.csrf import generate_csrf
from werkzeug.security import generate_password_hash, check_password_hash
from werkzeug.utils import secure_filename
import os
import logging
import calendar
from datetime import datetime, date
from models import db, Employee, Attendance, Expense, ExpenseItem, Transaction, AuditLog, Notification, SalarySlip, PerformanceReview, DailyMenu, MenuItem, CanteenOrder, AttendanceReport
from config import Config
from utils import parse_expense_excel, send_email, send_sms, log_audit_action, export_to_csv, export_to_excel, mail
from csrf import csrf

# Try to import celery, but don't fail if not available
try:
    from celery_app import celery_app
except ImportError:
    celery_app = None

logger = logging.getLogger(__name__)

app = Flask(__name__)
app.config.from_object(Config)
print('Database URI:', app.config['SQLALCHEMY_DATABASE_URI'])

# Add datetime and calendar to Jinja2 globals
app.jinja_env.globals.update(datetime=datetime, pycal=calendar)

db.init_app(app)
mail = Mail(app)

# Initialize CSRF protection
csrf.init_app(app)

# Add CSRF token to Jinja2 globals
@app.context_processor
def inject_csrf_token():
    return dict(csrf_token=generate_csrf)

login_manager = LoginManager()
login_manager.init_app(app)
login_manager.login_view = 'auth.login'

@login_manager.user_loader
def load_user(user_id):
    return Employee.query.get(int(user_id))

# Create upload folder if it doesn't exist
os.makedirs(app.config['UPLOAD_FOLDER'], exist_ok=True)

# Serve static files
@app.route('/static/<path:filename>')
def static_files(filename):
    return send_from_directory('static', filename)

# Serve uploaded files
@app.route('/uploads/<path:filename>')
def uploaded_files(filename):
    return send_from_directory(app.config['UPLOAD_FOLDER'], filename)

# Import blueprints
from auth import auth_bp
from attendance import attendance_bp
from expenses import expenses_bp
from admin import admin_bp
from leave import leave_bp
from performance import performance_bp
from company_settings import company_settings_bp
from support import support_bp

app.register_blueprint(auth_bp, url_prefix='/auth')
app.register_blueprint(attendance_bp, url_prefix='/attendance')
app.register_blueprint(expenses_bp, url_prefix='/expenses')
app.register_blueprint(admin_bp, url_prefix='/admin')
app.register_blueprint(leave_bp, url_prefix='/leave')
app.register_blueprint(performance_bp, url_prefix='/performance')
app.register_blueprint(company_settings_bp, url_prefix='/admin/company-settings')

# Exempt the leave, support, and admin blueprints from CSRF protection
csrf.exempt(leave_bp)
csrf.exempt(support_bp)
csrf.exempt(admin_bp)

# Also exempt specific expense routes
csrf.exempt('admin.approve_expense')
csrf.exempt('admin.reject_expense')
csrf.exempt('admin.refund_expense')

@app.route('/')
@login_required
def dashboard():
    if current_user.role == 'hr':
        return redirect(url_for('admin.hr_dashboard'))
    elif current_user.role == 'admin':
        return redirect(url_for('admin.dashboard'))

    # For employees, get dashboard data
    from utils_new import get_birthdays_today, get_upcoming_holidays, get_work_anniversaries_today, get_upcoming_birthdays, get_upcoming_work_anniversaries, get_holidays_for_month
    from models import Leave

    # Get birthdays
    birthdays = get_birthdays_today()

    # Get work anniversaries
    work_anniversaries = get_work_anniversaries_today()

    # Get upcoming birthdays
    upcoming_birthdays = get_upcoming_birthdays(limit=5)

    # Get upcoming work anniversaries
    upcoming_anniversaries = get_upcoming_work_anniversaries(limit=5)

    # Get upcoming holidays
    upcoming_holidays = get_upcoming_holidays(limit=6)

    # Get holidays for current month for calendar display
    current_year = datetime.now().year
    current_month = datetime.now().month
    month_holidays = get_holidays_for_month(current_year, current_month)

    # Get recent leave requests (last 5)
    recent_leaves = Leave.query.filter_by(employee_id=current_user.id)\
        .order_by(Leave.submitted_at.desc())\
        .limit(5)\
        .all()

    # Get leave history (approved/rejected)
    leave_history = Leave.query.filter_by(employee_id=current_user.id)\
        .filter(Leave.status.in_(['approved', 'rejected']))\
        .order_by(Leave.submitted_at.desc())\
        .limit(10)\
        .all()

    # Get notifications for the current user
    notifications = Notification.query.filter_by(employee_id=current_user.id)\
        .order_by(Notification.created_at.desc())\
        .limit(5)\
        .all()

    # Get unread notification count
    unread_count = Notification.query.filter_by(employee_id=current_user.id, is_read=False).count()

    # Get recent announcements
    from models import Announcement
    announcements = Announcement.query.join(Employee, Announcement.created_by == Employee.id).filter(
        Announcement.is_active == True,
        (
            (Announcement.target_role == current_user.role) |
            (Announcement.target_role == 'all') |
            (Employee.role.in_(['admin', 'hr']))
        ),
        ((Announcement.expiry_date.is_(None)) | (Announcement.expiry_date >= date.today()))
    ).order_by(Announcement.created_at.desc()).limit(3).all()

    # Get current expense status for the current month (only if attendance is approved)
    current_month = datetime.now().month
    current_year = datetime.now().year

    # Check if attendance report for current month is approved
    attendance_report_approved = AttendanceReport.query.filter_by(
        month=current_month,
        year=current_year,
        approval_status='approved'
    ).first() is not None

    current_expense = None
    current_expense_status = None
    if attendance_report_approved:
        current_expense = Expense.query.filter_by(
            employee_id=current_user.id,
            month=current_month,
            year=current_year
        ).first()
        current_expense_status = current_expense.status if current_expense else None

    # Get current salary status for the current month (only if attendance is approved)
    current_salary_slip = None
    current_salary_status = None
    if attendance_report_approved:
        current_salary_slip = SalarySlip.query.filter_by(
            employee_id=current_user.id,
            month=current_month,
            year=current_year
        ).first()
        current_salary_status = current_salary_slip.status if current_salary_slip else None

    # Get expense history with optional search
    expense_month = request.args.get('expense_month')
    expense_year = request.args.get('expense_year')
    query = Expense.query.filter_by(employee_id=current_user.id)
    if expense_year:
        query = query.filter_by(year=int(expense_year))
    if expense_month:
        query = query.filter_by(month=int(expense_month))
    expense_history = query.order_by(Expense.year.desc(), Expense.month.desc())\
        .limit(10)\
        .all()

    # Get salary history with optional search
    month = request.args.get('month')
    year = request.args.get('year')
    query = SalarySlip.query.filter_by(employee_id=current_user.id)
    if year:
        query = query.filter_by(year=int(year))
    if month:
        query = query.filter_by(month=int(month))
    salary_history = query.order_by(SalarySlip.year.desc(), SalarySlip.month.desc()).all()

    # Get support tickets for the current user
    from models import SupportTicket
    support_tickets = SupportTicket.query.filter_by(created_by=current_user.id)\
        .order_by(SupportTicket.created_at.desc())\
        .all()

    # Get current month's attendance summary for current employee
    current_month = datetime.now().month
    current_year = datetime.now().year
    monthly_attendances = Attendance.query.filter(
        Attendance.employee_id == current_user.id,
        Attendance.date >= date(current_year, current_month, 1),
        Attendance.date <= date.today()
    ).all()

    # Count present, absent, and late/half_day for the month
    present_count = sum(1 for att in monthly_attendances if att.status == 'present')
    absent_count = sum(1 for att in monthly_attendances if att.status == 'absent')
    late_half_day_count = sum(1 for att in monthly_attendances if att.status in ['late', 'half_day'])

    # Get all employees with their attendance status for today (keeping daily view for individual status)
    today = date.today()
    today_attendances = Attendance.query.filter_by(date=today).all()
    all_employees = Employee.query.filter_by(is_active=True).all()
    employee_attendance = []
    for emp in all_employees:
        attendance = next((att for att in today_attendances if att.employee_id == emp.id), None)
        status = attendance.status if attendance else 'absent'
        employee_attendance.append({
            'id': emp.id,
            'name': emp.name,
            'department': emp.department,
            'status': status,
            'check_in': attendance.check_in_time.strftime('%H:%M') if attendance and attendance.check_in_time else None,
            'check_out': attendance.check_out_time.strftime('%H:%M') if attendance and attendance.check_out_time else None
        })

    # Get canteen data for today
    breakfast_menu = DailyMenu.query.filter_by(date=today, meal_type='breakfast').first()
    lunch_menu = DailyMenu.query.filter_by(date=today, meal_type='lunch').first()

    # Get today's active orders for current user (exclude cancelled orders)
    todays_breakfast_order = CanteenOrder.query.filter_by(
        employee_id=current_user.id,
        order_date=today,
        meal_type='breakfast'
    ).filter(CanteenOrder.status != 'cancelled').first()
    todays_lunch_order = CanteenOrder.query.filter_by(
        employee_id=current_user.id,
        order_date=today,
        meal_type='lunch'
    ).filter(CanteenOrder.status != 'cancelled').first()

    # Get total employees and today's attendance for statistics
    total_employees = Employee.query.filter_by(is_active=True).count()
    todays_attendance_count = Attendance.query.filter_by(date=today, status='present').count()
    todays_attendance_percentage = (todays_attendance_count / total_employees * 100) if total_employees > 0 else 0
    currently_present = Attendance.query.filter_by(date=today, status='present').count()

    # Get current shift assignment for the employee
    current_shift_assignment = current_user.get_current_shift

    return render_template('dashboard.html',
                         birthdays=birthdays,
                         upcoming_holidays=upcoming_holidays,
                         month_holidays=month_holidays,
                         recent_leaves=recent_leaves,
                         leave_history=leave_history,
                         notifications=notifications,
                         announcements=announcements,
                         unread_count=unread_count,
                         current_expense_status=current_expense_status,
                         current_salary_status=current_salary_status,
                         expense_history=expense_history,
                         salary_history=salary_history,
                         support_tickets=support_tickets,
                         today=today,
                         present_count=present_count,
                         absent_count=absent_count,
                         late_half_day_count=late_half_day_count,
                         employee_attendance=employee_attendance,
                         breakfast_menu=breakfast_menu,
                         lunch_menu=lunch_menu,
                         todays_breakfast_order=todays_breakfast_order,
                         todays_lunch_order=todays_lunch_order,
                         total_employees=total_employees,
                         todays_attendance_count=todays_attendance_count,
                         todays_attendance_percentage=todays_attendance_percentage,
                         currently_present=currently_present,
                         current_shift_assignment=current_shift_assignment)

@app.route('/api/welcome', methods=['GET'])
def api_welcome():
    logger.info(f"Request received: {request.method} {request.path}")
    return jsonify({'message': 'Welcome to the Flask API Service!'})

@app.route('/profile', methods=['GET', 'POST'])
@login_required
def profile():
    if request.method == 'POST':
        # Only allow admin users to update bank details and profile photo
        if current_user.role != 'admin':
            flash('Access denied. Admin privileges required.', 'error')
            return redirect(url_for('profile'))

        old_account_number = current_user.account_number
        old_ifsc_code = current_user.ifsc_code
        old_profile_photo = current_user.profile_photo

        account_number = request.form.get('account_number')
        ifsc_code = request.form.get('ifsc_code')

        current_user.account_number = account_number
        current_user.ifsc_code = ifsc_code

        # Handle profile photo upload
        if 'profile_photo' in request.files:
            file = request.files['profile_photo']
            if file and file.filename:
                filename = secure_filename(file.filename)
                # Create unique filename with timestamp
                ext = os.path.splitext(filename)[1]
                unique_filename = f"profile_{current_user.id}_{datetime.now().strftime('%Y%m%d_%H%M%S')}_{filename}"
                filepath = os.path.join(app.config['UPLOAD_FOLDER'], unique_filename)
                file.save(filepath)
                current_user.profile_photo = unique_filename

        db.session.commit()

        log_audit_action(current_user.id, 'update_bank_details', 'employees', current_user.id, {
            'account_number': old_account_number,
            'ifsc_code': old_ifsc_code,
            'profile_photo': old_profile_photo
        }, {
            'account_number': account_number,
            'ifsc_code': ifsc_code,
            'profile_photo': current_user.profile_photo
        })

        flash('Profile updated successfully!', 'success')
        return redirect(url_for('profile'))

    return render_template('profile.html')

@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404

@app.errorhandler(500)
def internal_error(e):
    db.session.rollback()
    return render_template('500.html'), 500

from announcements import announcements_bp
from calendar_bp import calendar_bp
from support import support_bp
from notifications import notifications_bp
from canteen import canteen_bp

app.register_blueprint(announcements_bp, url_prefix='/announcements')
app.register_blueprint(calendar_bp, url_prefix='/calendar')
app.register_blueprint(support_bp, url_prefix='/support')
app.register_blueprint(notifications_bp, url_prefix='/notifications')
app.register_blueprint(canteen_bp, url_prefix='/canteen')

# Add custom Jinja2 filters
@app.template_filter('nl2br')
def nl2br_filter(text):
    """Convert newlines to <br> tags"""
    if text:
        return text.replace('\n', '<br>')
    return text

if __name__ == '__main__':
    with app.app_context():
        db.create_all()
    app.run(debug=True)

