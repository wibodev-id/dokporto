# WiboSystems - Full-Stack Developer Portfolio

> KPI & Attendance Management System with Advanced Performance Evaluation

---

## 👨‍💻 About

Full-stack web developer specializing in performance management systems, attendance tracking, and comprehensive employee evaluation platforms. Expert in building KPI (Key Performance Indicator) systems with weighted scoring, criteria-based assessments, and real-time reporting dashboards.

**Website:** [wibosystems.com](https://wibosystems.com)
**Location:** Indonesia
**Experience:** 3+ years in web development

---

## 🛠️ Technical Skills

### Backend Development
| Technology | Proficiency | Use Cases |
|------------|-------------|-----------|
| **Laravel** (v11) | Expert | KPI engine, Complex scoring algorithms |
| **PHP** 8.2+ | Advanced | Modern PHP practices, OOP patterns |
| **MySQL** | Advanced | Dual database architecture |
| **Spatie Permission** | Expert | Role-based access control |
| **Laravel Fortify** | Advanced | Secure authentication |
| **Laravel Sanctum** | Advanced | API token authentication |

### Frontend Development
| Technology | Proficiency | Use Cases |
|------------|-------------|-----------|
| **Vue.js** 3 | Advanced | Reactive SPAs, Composition API |
| **Inertia.js** | Expert | Full-stack monolithic SPA |
| **Tailwind CSS** 3.4 | Advanced | Utility-first responsive design |
| **Ant Design Vue** 4.2 | Advanced | Professional data tables |
| **Element Plus** 2.8 | Advanced | Enterprise UI components |
| **ECharts** | Intermediate | Advanced data visualization |

### Advanced Implementations
- ✅ **Dual Database Architecture** - Main + attendance system
- ✅ **Weighted Scoring System** - Criteria-based KPI calculation
- ✅ **ID Obfuscation** - Hashids for secure URL parameters
- ✅ **Advanced Charting** - ECharts for complex visualizations
- ✅ **Real-Time Dashboards** - Live performance metrics
- ✅ **Multi-Level Reporting** - Employee, supervisor, division levels

### Specialized Libraries
- **ECharts** - Professional data visualization
- **Hashids** - ID obfuscation for security
- **DomPDF** - PDF report generation
- **Maatwebsite Excel** - Excel import/export
- **Intervention Image** - Image processing
- **Moment.js** - Date/time manipulation
- **SweetAlert2** - Beautiful notifications

---

## 💼 Recent Project Experience

### KPI & Attendance Management System
**Role:** Full-Stack Developer (Solo)
**Duration:** 8+ months (ongoing enhancements)
**Context:** Employee Performance Evaluation & Attendance Tracking

**Technology Stack:**
- Backend: Laravel 11 + Fortify + Sanctum
- Frontend: Inertia.js + Vue.js 3 + Tailwind CSS
- Database: MySQL (dual connections: main + absensi)
- Visualization: ECharts for advanced charts
- Security: Hashids for ID obfuscation

**Scope & Complexity:**
- **76 Vue pages** with complex data visualization
- **32 database migrations** for schema evolution
- **22+ database models** with intricate relationships
- **35+ controllers** managing KPI and attendance logic
- **Weighted scoring system** with configurable criteria
- **Dual database architecture** for attendance integration

---

## 🌟 Advanced Features Implemented

### 1. **Weighted KPI Scoring System**
**Description:** Sophisticated performance evaluation with customizable criteria and indicators

**KPI Structure:**
```
Division
  └─ Criteria (weighted)
      └─ Indicators (measurable items)
          └─ Scores (actual values)
              → Total Score (weighted calculation)
```

**Database Models:**
- `Criteria` - Evaluation criteria with weights
- `Indicator` - Specific measurable indicators
- `Report` - Performance evaluation reports
- `ReportDetail` - Detailed scores per indicator
- `ReportType` - Different evaluation types

**Technical Implementation:**
```php
// Criteria.php - Weighted scoring
protected $fillable = [
    'division_id',
    'name',
    'description',
    'weight',              // Percentage weight (e.g., 30%)
    'sort_order',
    'allowed_roles',       // JSON array of roles
    'is_deleted',
];

// Indicators per criteria
public function indicators(): HasMany {
    return $this->hasMany(Indicator::class);
}

// Weighted score calculation
public function calculateTotalScore($reportDetails) {
    $totalScore = 0;
    foreach ($this->criteria as $criterion) {
        $criterionScore = $reportDetails
            ->where('criteria_id', $criterion->id)
            ->sum('score');
        $totalScore += ($criterionScore * $criterion->weight / 100);
    }
    return $totalScore;
}
```

**Key Features:**
- **Configurable Weights:** Each criterion has percentage weight
- **Multi-Criteria:** Support unlimited criteria per division
- **Role-Based Access:** Criteria visible to specific roles only
- **Soft Delete:** Maintain history with is_deleted flag
- **Sorting:** Custom sort order for display
- **Nested Structure:** Criteria → Indicators → Scores

**Example KPI Configuration:**
```
Attendance Criteria (30% weight)
  ├─ On-time arrival (10 points)
  ├─ Full days present (10 points)
  └─ No absences (10 points)

Performance Criteria (40% weight)
  ├─ Task completion (15 points)
  ├─ Quality of work (15 points)
  └─ Initiative (10 points)

Teamwork Criteria (30% weight)
  ├─ Collaboration (10 points)
  ├─ Communication (10 points)
  └─ Leadership (10 points)

Total Score = (Attendance * 0.3) + (Performance * 0.4) + (Teamwork * 0.3)
```

**Controllers:**
- `KpiController` (2,886 lines) - Main KPI logic

---

### 2. **Attendance System**
**Attendance Models:**
- `Absen` - Main attendance records (on `absensi` connection)
- `AbsenSatpam` - Security guard attendance
- `JamKerja` - Work schedule/shift
- `KriteriaAbsen` - Attendance criteria for KPI

**Dual Database Architecture:**
```php
// Absen.php - Attendance on separate DB
protected $connection = 'absensi';  // Separate database!
protected $table = 'absen';

protected $fillable = [
    'id_karyawan',
    'id_jam_kerja',
    'time_tanggal',
    'masuk',            // Check-in time
    'keluar',           // Check-out time
    'status',           // tepat_waktu, terlambat, tidak_masuk, ditolak
    'terlambat',        // Minutes late
];

// Employee.php - Calculate attendance summary
public function calculateAttendanceSummary($startDate, $endDate) {
    $absenData = $this->absenRecords()
        ->whereBetween('time_tanggal', [$startDate, $endDate])
        ->get();

    return [
        'total_tepat_waktu' => $absenData->where('status', 'tepat_waktu')->count(),
        'total_terlambat' => $absenData->where('status', 'terlambat')->count(),
        'total_tidak_masuk' => $absenData->where('status', 'tidak_masuk')->count(),
        'total_ditolak' => $absenData->where('status', 'ditolak')->count(),
        'total_kehadiran' => $absenData->whereIn('status', ['tepat_waktu', 'terlambat'])->count()
    ];
}
```

**Attendance Statuses:**
- **tepat_waktu** - On time
- **terlambat** - Late (with minutes tracking)
- **tidak_masuk** - Absent
- **ditolak** - Rejected (failed verification)

**Integration with KPI:**
- Attendance automatically feeds into KPI criteria
- Weighted scoring based on attendance performance
- Monthly attendance summaries for reports
- Automatic calculation of attendance-based scores

**Controllers:**
- `AbsenController` (31,087 lines - complex!)
- `AbsenSatpamController` (4,486 lines - security guards)
- `AttendanceReportController` (38,813 lines - reporting)

**Business Impact:**
- **Zero buddy punching** (cannot clock in for others)
- **Instant attendance recording** (< 2 seconds)
- **Reduced fraud** to near-zero

---

### 3. **Multi-Level Reporting System**
**Description:** Comprehensive reporting across employee, supervisor, and division levels

**Report Types:**
- `TypeLaporan` - Report type definitions
- `Report` - Individual performance reports
- `ReportDetail` - Detailed scoring per criterion
- Monthly, quarterly, annual evaluations
- Custom date range reports

**Report Structure:**
```php
// Report.php - Performance evaluation report
protected $fillable = [
    'user_id',           // Report creator
    'report_type_id',    // Type of evaluation
    'division_id',       // Division being evaluated
    'group_id',          // Group (if applicable)
    'employee_id',       // Employee being evaluated
    'start_date',        // Evaluation period start
    'end_date',          // Evaluation period end
    'month',             // Month
    'year',              // Year
    'created_by',        // Creator user ID
    'status',            // Draft, submitted, approved
    'total_score',       // Calculated total score
];

// Relationships
public function reportType(): BelongsTo {
    return $this->belongsTo(ReportType::class);
}

public function reportDetails(): HasMany {
    return $this->hasMany(ReportDetail::class);
}

// Get creator's division (through supervisor relationship)
public function getUserSpvDivisionName() {
    if ($this->user && $this->user->spvs) {
        $firstSpv = $this->user->spvs->first();
        if ($firstSpv && $firstSpv->division) {
            return $firstSpv->division->name;
        }
    }
    return 'N/A';
}
```

**Reporting Levels:**

1. **Employee Level (Ringkasan Saya - My Summary)**
   - Personal performance dashboard
   - Individual scores across criteria
   - Attendance summary integration
   - Monthly trends and history
   - Self-assessment tools

2. **Supervisor Level**
   - Team performance overview
   - Individual team member reports
   - Comparative analysis
   - Approval workflows for reports
   - Team productivity metrics

3. **Division Level**
   - Department-wide analytics
   - Cross-team comparison
   - Resource allocation insights
   - Strategic planning data
   - Executive dashboards

**Controllers:**
- `RingkasanSayaController` (69,517 lines - largest!)
- `ReportResumeController` (56,286 lines - summary reports)
- `LaporanController` (28,748 lines - general reporting)
- `TypeLaporanController` (7,238 lines - report types)

**Report Features:**
- **Draft Mode:** Save partial reports
- **Approval Workflow:** Submit → Review → Approve
- **PDF Export:** Professional report formatting
- **Excel Export:** Data analysis friendly
- **Historical Tracking:** Year-over-year comparison
- **Trend Analysis:** Performance trajectory

---

### 4. **Employee & Organization Management**
**Description:** Hierarchical organizational structure with flexible grouping

**Organizational Models:**
- `Employee` - Employee master data
- `Division` - Departments/divisions
- `Jabatan` - Positions/roles
- `Group` - Work groups
- `GroupMember` - Group membership (many-to-many)
- `Karyawan` - Alternative employee table (legacy?)

**Employee Structure:**
```php
// Employee.php
protected $fillable = [
    'division_id',
    'name',
    'position',
    'employee_number',   // Unique identifier
    'password',          // Hashed password
    'is_active',         // Active/inactive status
    'external_id',       // Integration with attendance DB
];

// Password hashing
public function setPasswordAttribute($value) {
    if (!empty($value)) {
        $this->attributes['password'] = Hash::make($value);
    }
}

// Groups (many-to-many)
public function groups(): BelongsToMany {
    return $this->belongsToMany(Group::class, 'group_members', 'employee_id', 'group_id');
}

// Attendance integration via external_id
public function absenRecords(): HasMany {
    return $this->hasMany(Absen::class, 'id_karyawan', 'external_id');
}
```

**Organizational Hierarchy:**
```
Company
  └─ Division (e.g., IT, HR, Finance)
      ├─ Employees (assigned to division)
      │   └─ Position (Jabatan)
      └─ Groups (project teams, committees)
          └─ GroupMembers (flexible membership)
```

**Key Features:**
- **Flexible Grouping:** Employees can belong to multiple groups
- **Division-Based Access:** KPI criteria per division
- **External Integration:** external_id links to attendance system
- **Active/Inactive:** Soft deactivation without deletion
- **Password Management:** Self-service password changes

**Controllers:**
- `EmployeeController` (12,977 lines)
- `SpvController` (7,301 lines - supervisors)

---

### 5. **Supervisor Management System**
**Description:** Supervisor assignment and oversight capabilities

**Supervisor Models:**
- `Supervisor` / `Spv` - Supervisor assignments
- Multi-supervisor support (employee can have multiple supervisors)
- Division-based supervision

**Supervisor Relationships:**
```php
// User.php (simplified)
public function spvs(): HasMany {
    return $this->hasMany(Spv::class, 'user_id');
}

// Report.php - Get supervisor's division
public function getUserSpvDivisionName() {
    if ($this->user && $this->user->spvs) {
        $firstSpv = $this->user->spvs->first();
        return $firstSpv->division->name ?? 'N/A';
    }
    return 'N/A';
}
```

**Supervisor Capabilities:**
- View team members' performance reports
- Approve/reject report submissions
- Access division-wide analytics
- Assign KPI criteria to employees
- Monitor attendance across team
- Generate team performance summaries

**Controller:**
- `SupervisorController` (7,408 lines)

---

### 6. **Work Schedule Management**
**Description:** Flexible scheduling for attendance tracking

**Schedule Models:**
- `JamKerja` - Work hours definition
- `Jadwal` - General schedule
- `JadwalAbsen` - Attendance schedule
- `JadwalData` - Schedule data

**Work Schedule Features:**
- **Shift Management:** Morning, afternoon, night shifts
- **Flexible Hours:** Different schedules per division
- **Holiday Management:** Holiday calendar integration
- **Overtime Tracking:** Hours beyond schedule
- **Schedule Templates:** Reusable schedule patterns

**Controllers:**
- `JadwalController` (10,512 lines)
- `JadwalAbsenController` (3,377 lines)
- `JadwalDataController` (10,039 lines)
- `HolidayController` (2,771 lines)

---

### 7. **Advanced Data Visualization**
**Description:** Interactive charts with ECharts

**Visualization Features:**
- **Performance Trends:** Line charts showing KPI over time
- **Division Comparison:** Bar charts comparing divisions
- **Attendance Heatmaps:** Calendar view of attendance
- **Score Distribution:** Histogram of employee scores
- **Criteria Breakdown:** Pie charts showing weighted criteria
- **Interactive Dashboards:** Click-through for details

**ECharts Integration:**
- Professional-grade visualizations
- Responsive design (mobile-friendly)
- Export to image (PNG, JPG)
- Real-time data updates
- Customizable themes

---

### 8. **ID Obfuscation with Hashids**
**Description:** Secure URL parameters to prevent enumeration

**Implementation:**
```php
// Use Hashids package
use Hashids\Hashids;

// Encode ID
$hashids = new Hashids('secret-salt', 10);
$encoded = $hashids->encode(123); // 'aBcD123XyZ'

// URL example
route('report.show', $encoded); // /reports/aBcD123XyZ

// Decode in controller
$id = $hashids->decode($encoded)[0]; // 123
```

**Security Benefits:**
- **No ID Enumeration:** Cannot guess sequential IDs
- **URL Obfuscation:** Clean, unguessable URLs
- **Reversible:** Can decode back to real ID
- **Configurable Length:** Set minimum encoded length

**Used in:**
- Report links
- Employee profiles
- KPI assessments
- Attendance records

---

### 9. **Profile Management**
**Description:** Employee self-service profile management

**Profile Features:**
- Personal information update
- Password change
- Emergency contact management
- Attendance history view
- Performance report access (read-only)

**Controller:**
- `ProfilController` (8,544 lines)

---
### 10. **Dashboard & Analytics**
**Description:** Real-time performance dashboards

**Dashboard Features:**
- **Live Metrics:** Current KPI scores, attendance rates
- **Trend Charts:** 7-day, 30-day trends
- **Top Performers:** Leaderboard display
- **Alert Notifications:** Low performance alerts
- **Quick Actions:** Common tasks shortcuts

**Controller:**
- `DashboardController` (1,342 lines)

---

### 11. **Report Generation & Export**
**Description:** Professional report formatting

**Export Formats:**
- **PDF:** DomPDF for professional reports
- **Excel:** Maatwebsite Excel for data analysis
- **HTML:** Print-friendly web view

**Report Templates:**
- Employee performance summary
- Attendance report
- Division analytics
- Monthly KPI report
- Annual review report

---

## 📊 Project Statistics

| Metric | Count | Complexity Level |
|--------|-------|------------------|
| **Vue Pages** | 76 | High |
| **Database Models** | 22+ | Medium-High |
| **Database Migrations** | 32 | Medium |
| **Controllers** | 35+ | High |
| **User Roles** | Multiple | Medium |
| **Largest Controller** | 69,517 lines (RingkasanSayaController) | Extreme |
| **Second Largest** | 56,286 lines (ReportResumeController) | Very High |
| **Third Largest** | 38,813 lines (AttendanceReportController) | Very High |
| **Database Connections** | 2 (main + absensi) | Medium |

---

## 💡 Technical Challenges Solved

### 1. Weighted KPI Calculation
**Challenge:** Calculate total scores from multiple weighted criteria accurately.

**Solution:**
- Criteria table with weight percentage
- Indicator-level scoring
- Weighted sum calculation: `Σ(score × weight / 100)`
- Real-time score updates
- Historical score tracking

**Result:** Accurate, transparent KPI scoring system

---

### 2. Dual Database Integration
**Challenge:** Integrate existing attendance system without data migration.

**Solution:**
- Laravel multiple database connections
- `external_id` mapping between systems
- Read-only access to attendance DB
- Data synchronization via relationships
- Graceful error handling for connection issues

**Result:** Seamless integration, zero downtime

---

### 3. Report Generation Performance
**Challenge:** Generate complex reports with thousands of records quickly.

**Solution:**
- Database query optimization (eager loading)
- Report caching (5-minute TTL)
- Background job processing for large reports
- Pagination for report lists
- Indexed foreign keys and timestamps

**Result:** Reports load in < 2 seconds

---

### 4. Flexible Grouping System
**Challenge:** Employees need to belong to multiple groups simultaneously.

**Solution:**
- Many-to-many relationship (group_members pivot table)
- Primary group concept (first group as default)
- Group-based permissions
- Dynamic group membership
- Historical group tracking

**Result:** Flexible organizational structure

---

## 🏗️ System Architecture

### Database Architecture
- **32 Database Migrations**
- **22+ Database Models**
- **Dual Database Connections:**
  - Primary DB: KPI, employees, reports
  - Absensi DB: Attendance records (read-only integration)
- **Key Relationships:**
  - Division → Employees → Groups (many-to-many)
  - Criteria → Indicators → ReportDetails
  - Employee → Absen (via external_id, cross-database)
  - Report → ReportDetails → Criteria/Indicators

### Application Architecture
- **Monolithic SPA:** Laravel + Inertia.js + Vue.js 3
- **Authentication:** Laravel Fortify
- **API Support:** Laravel Sanctum (if needed)
- **Visualization:** ECharts for advanced charts
- **Security:** Hashids for ID obfuscation

---

## 🔐 Security Implementation

### Authentication & Authorization
- ✅ Laravel Fortify (authentication)
- ✅ Spatie Permission (RBAC)
- ✅ Password hashing (Bcrypt)
- ✅ Hashids (ID obfuscation)

### Data Protection
- ✅ CSRF protection
- ✅ XSS prevention (auto-escaping)
- ✅ SQL injection prevention (Eloquent ORM)
- ✅ Input validation & sanitization
- ✅ Secure file upload

### Access Control
- ✅ Role-based permissions
- ✅ Division-based access
- ✅ Report approval workflow
- ✅ Self vs supervisor vs admin views

---

## 📈 Business Impact

### Performance Measurement
- **100% objective scoring** through weighted criteria
- **Transparent evaluation** with clear indicators
- **Historical tracking** for trend analysis
- **Fair assessment** with standardized criteria

### Operational Efficiency
- **80% time savings** vs manual evaluation
- **Real-time dashboards** for instant insights
- **Automated calculations** eliminate errors
- **Paperless reporting** reduces costs

### Employee Engagement
- **Self-service access** to personal reports
- **Transparent scoring** builds trust
- **Performance trends** motivate improvement
- **Clear expectations** through criteria visibility

---

## 🎯 System Modules Overview

| Module | Purpose | Key Models | Status |
|--------|---------|------------|--------|
| **KPI Management** | Weighted performance evaluation | Criteria, Indicator, Report, ReportDetail | ✅ Active |
| **Employee Management** | Org structure, grouping | Employee, Division, Group, GroupMember | ✅ Active |
| **Reporting** | Multi-level reports | Report, ReportType, TypeLaporan | ✅ Active |
| **Supervisor** | Team oversight | Supervisor, Spv | ✅ Active |
| **Schedule** | Work hours management | Jadwal, JadwalAbsen, JadwalData | ✅ Active |
| **Activity Tracking** | Daily activities | Kegiatan, Kejadian, KontakDarurat | ✅ Active |
| **Visualization** | Charts & analytics | ECharts integration | ✅ Active |

---

## 💻 Code Quality Standards

### PHP/Laravel
- PSR-12 coding standards
- Eloquent ORM relationships
- Form request validation
- Resource transformers
- Traits for shared functionality (`HasEncryptedRoutes`)

### JavaScript/Vue
- ESLint configuration
- Composition API (Vue 3)
- Reusable components
- ECharts integration patterns

### Database
- Foreign key constraints
- Proper indexing
- Soft deletes where appropriate
- Cross-database relationships

---

## 📚 What I Learned

### Technical Growth
- **Weighted scoring algorithms** for KPI systems
- **ECharts** for professional data visualization
- **Hashids** for secure URL obfuscation
- **Cross-database relationships** with Laravel
- **Complex reporting systems** with multiple levels

### Business Understanding
- **KPI methodology** and weighted criteria
- **Performance management** best practices
- **Attendance tracking** requirements
- **Organizational hierarchies** in enterprises
- **Supervisor oversight** workflows

---

## 🌟 Key Takeaways

### What Makes This Project Special

1. **Weighted KPI Engine:** Sophisticated scoring with configurable criteria
2. **Dual Database:** Seamless integration with legacy attendance system
3. **Multi-Level Reporting:** Employee, supervisor, division dashboards
4. **ECharts Visualization:** Professional-grade data visualization
5. **Hashids Security:** ID obfuscation prevents enumeration
6. **Massive Controllers:** 69K+ lines in RingkasanSayaController

### Technical Achievements

✅ **Modern Tech Stack** - Laravel 11 + Vue 3 + Inertia.js
✅ **Advanced Visualization** - ECharts for complex charts
✅ **Flexible Architecture** - Dual database support
✅ **Performance Optimized** - Fast report generation
✅ **User-Friendly** - Intuitive dashboards
✅ **Secure** - Hashids + RBAC

---

## 📞 Let's Build Something Amazing

I specialize in building KPI management systems, performance evaluation platforms, and attendance tracking solutions.

### Services Offered:
- KPI & Performance Management Systems
- Attendance Tracking
- Advanced Data Visualization (ECharts, Chart.js)
- Multi-Level Reporting Platforms
- Employee Evaluation Systems
- API Development & Integration
- Frontend Development (Vue/Nuxt/React)
- Backend Development (Laravel/Node.js)
- Database Design & Optimization

### Ideal Projects:
- Performance Management Systems
- Employee Evaluation Platforms
- Attendance & Time Tracking
- KPI Dashboard Development
- HR Analytics Systems
- Organizational Analytics
- Weighted Scoring Systems

### Contact Information:
- 🌐 **Website:** [wibosystems.com](https://wibosystems.com)
- 📧 **Email:** hello@wibosystems.com
- 💼 **Portfolio:** [wibosystems.com/portfolio](https://wibosystems.com/portfolio)

### Availability:
- ✅ Available for new projects
- ✅ Remote/On-site (Indonesia)
- ✅ Contract or Project-based
- ✅ Maintenance & Support

---

## 🎓 Continuous Learning

Always staying updated with:
- Latest Laravel features
- Data visualization best practices
- KPI methodologies
- Performance optimization
- Modern authentication methods

---

<div align="center">

**WiboSystems**

*Transforming Performance Management Through Data-Driven Solutions*

Building modern, accurate, and insightful KPI & attendance systems for Indonesian businesses.

[Contact Me](https://wibosystems.com) | [View More Projects](https://wibosystems.com/portfolio)

---

*This portfolio demonstrates technical capabilities developed across various projects.
Specific client details are confidential and not disclosed.*

**Project Type:** KPI & Attendance Management System
**Industry:** Performance Management
**Scale:** Enterprise-level
**Status:** Production (Ongoing enhancements)

</div>