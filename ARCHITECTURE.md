# Architecture & Design Document

## System Overview

The Student Grading System is built using Object-Oriented Programming principles with a modular, scalable architecture. The system manages student records, grades, attendance, and generates comprehensive reports.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────┐
│                   Main Application                      │
│              (Menu System / CLI Interface)              │
└────────────────────┬────────────────────────────────────┘
                     │
         ┌───────────┼───────────┐
         │           │           │
    ┌────▼───┐  ┌───▼────┐  ┌──▼─────┐
    │ Grade  │  │ Auth   │  │ File   │
    │Manager │  │System  │  │Handler │
    └────┬───┘  └────┬───┘  └──┬─────┘
         │           │          │
    ┌────▼───────────┼──────────▼────┐
    │  Student Class (Core Data)     │
    │  ├─ Personal Info              │
    │  ├─ Grades & Subjects          │
    │  ├─ Attendance                 │
    │  └─ Calculations (GPA, Avg)    │
    └────────────────┬───────────────┘
                     │
    ┌────────────────▼──────────────┐
    │    Utilities & Helpers        │
    │  ├─ Grade Calculations        │
    │  ├─ String Utilities          │
    │  ├─ Input Validation          │
    │  └─ UI Formatting             │
    └───────────────────────────────┘
```

## Class Hierarchy

### 1. Student Class
**Core data model representing a student**

```cpp
Student
├─ Personal Data
│  ├─ student_id: string
│  ├─ full_name: string
│  ├─ age: int
│  ├─ gender: string
│  ├─ course_class: string
│  ├─ email: string
│  └─ phone: string
│
├─ Academic Data
│  ├─ subject_scores: map<string, double>
│  ├─ subject_grades: map<string, Grade>
│  ├─ semester_cgpas: vector<double>
│  ├─ total_score: double
│  ├─ average_score: double
│  ├─ overall_gpa: double
│  └─ overall_grade: Grade
│
├─ Attendance Data
│  ├─ attendance_records: vector<AttendanceStatus>
│  └─ total_attendance_days: int
│
└─ Methods
   ├─ addScore(subject, score)
   ├─ calculateMetrics()
   ├─ displayInfo()
   ├─ displayGradeCard()
   └─ displayTranscript()
```

### 2. GradeManager Class
**Singleton pattern - manages all operations**

```cpp
GradeManager (Singleton)
├─ Data
│  ├─ students: vector<Student>
│  ├─ student_index: map<ID, index>
│  └─ file_handler: FileHandler*
│
├─ Student Management
│  ├─ addStudent()
│  ├─ deleteStudent()
│  ├─ findStudentByID()
│  └─ searchStudents()
│
├─ Grade Operations
│  ├─ addGrade()
│  ├─ updateGrade()
│  ├─ getSortedByGPA()
│  └─ getStudentRank()
│
├─ Analytics
│  ├─ getClassAverage()
│  ├─ getPassRate()
│  ├─ getGradeDistribution()
│  └─ displayStatistics()
│
└─ Reports
   ├─ generateTranscript()
   ├─ generateClassReport()
   └─ exportToCSV()
```

### 3. Authentication Class
**Singleton pattern - handles user security**

```cpp
Authentication (Singleton)
├─ Data
│  ├─ users: vector<User>
│  ├─ current_user: User*
│  └─ is_logged_in: bool
│
├─ Core Functions
│  ├─ login()
│  ├─ logout()
│  ├─ registerUser()
│  └─ createDefaultAdmin()
│
├─ User Management
│  ├─ changePassword()
│  ├─ changeUserRole()
│  ├─ deactivateUser()
│  └─ getAllUsers()
│
└─ Persistence
   ├─ saveUsers()
   └─ loadUsers()
```

### 4. FileHandler Class
**Singleton pattern - handles all file I/O**

```cpp
FileHandler (Singleton)
├─ Data
│  ├─ data_directory: string
│  ├─ students_file: string
│  └─ backup_directory: string
│
├─ Student Data I/O
│  ├─ saveStudents()
│  └─ loadStudents()
│
├─ Reporting
│  ├─ generateTranscript()
│  ├─ generateReportCard()
│  ├─ generateClassStatistics()
│  └─ generateAttendanceReport()
│
├─ Import/Export
│  ├─ exportToCSV()
│  └─ importFromCSV()
│
├─ Backup Management
│  ├─ backupStudentData()
│  ├─ restoreFromBackup()
│  └─ getAvailableBackups()
│
└─ File Utilities
   ├─ fileExists()
   ├─ deleteFile()
   └─ getFileSize()
```

### 5. Utilities Class
**Static utility functions**

```cpp
Utilities (Static)
├─ Grade Conversion
│  ├─ scoreToGrade()
│  ├─ gradeToGPA()
│  ├─ gradeToString()
│  └─ gpaToGrade()
│
├─ String Operations
│  ├─ toLowercase()
│  ├─ toUppercase()
│  ├─ stringMatch()
│  └─ trim()
│
├─ Input Validation
│  ├─ isValidEmail()
│  ├─ isValidPhone()
│  ├─ isValidScore()
│  └─ isValidAge()
│
├─ UI Formatting
│  ├─ printHeader()
│  ├─ printSuccess()
│  ├─ printError()
│  ├─ printTableHeader()
│  └─ printTableRow()
│
└─ Encryption
   ├─ encryptPassword()
   └─ verifyPassword()
```

## Design Patterns

### 1. Singleton Pattern
Used for:
- `GradeManager`: Ensures only one instance manages all student data
- `Authentication`: Single point of authentication
- `FileHandler`: Centralized file operations

**Benefits:**
- Prevents multiple instances
- Ensures data consistency
- Simplifies access

```cpp
class GradeManager {
private:
    static GradeManager* instance;
    GradeManager();  // Private constructor
public:
    static GradeManager* getInstance() {
        if (instance == nullptr) {
            instance = new GradeManager();
        }
        return instance;
    }
};
```

### 2. Encapsulation
All classes use private data members with public getters/setters:

```cpp
class Student {
private:
    std::string student_id;
    double average_score;
    
public:
    std::string getStudentID() const { return student_id; }
    void setStudentID(const std::string& id) { /* validation */ }
};
```

### 3. Factory-like Creation
Students created through manager methods:

```cpp
GradeManager* manager = GradeManager::getInstance();
Student student("ID001", "John", 20, "M", "CS101");
manager->addStudent(student);
```

### 4. Iterator Pattern
Using STL algorithms:

```cpp
std::vector<Student> failed = getFailedStudents();
std::for_each(failed.begin(), failed.end(), 
    [](const Student& s) { /* process */ });
```

## Data Flow

### Adding a Student

```
User Input
    │
    ▼
handleAddStudent()
    │
    ├─ Validate input ────────────────► Utilities::isValid*()
    │
    ▼
Student student(id, name, ...)
    │
    ├─ Constructor ────────────────────► Validate personal data
    │
    ▼
grade_manager->addStudent()
    │
    ├─ Check duplicate ────────────────► findStudentByID()
    │
    ├─ Add to vector
    │
    ├─ Update index ───────────────────► O(1) lookup
    │
    ▼
grade_manager->saveAllData()
    │
    ├─ Serialize ──────────────────────► Student data to binary
    │
    ▼
FileHandler::saveStudents()
    │
    ├─ Open file
    │
    ├─ Write binary data ──────────────► students.dat
    │
    ▼
Success/Error Message
```

### Calculating GPA

```
addScore(subject, score)
    │
    ├─ Validate score (0-100)
    │
    ├─ Store in subject_scores map
    │
    ▼
scoreToGrade() (Utilities)
    │
    ├─ 70-100 ──► A
    ├─ 60-69  ──► B
    ├─ etc...
    │
    ▼
gradeToGPA() (Utilities)
    │
    ├─ A ──► 4.0
    ├─ B ──► 3.0
    ├─ etc...
    │
    ▼
calculateMetrics()
    │
    ├─ Average = sum / count
    │
    ├─ Overall grade from average
    │
    ▼
overall_gpa = gradeToGPA(overall_grade)
```

## File Structure

### Binary Data Format

#### students.dat
```
[uint64: student_count]
For each student:
  [uint64: id_length][string: id]
  [uint64: name_length][string: name]
  [int32: age]
  [uint64: gender_length][string: gender]
  [uint64: course_length][string: course]
  [uint64: email_length][string: email]
  [uint64: phone_length][string: phone]
  [uint64: subject_count]
  For each subject:
    [uint64: subject_length][string: subject]
    [double: score]
```

#### users.dat
```
[uint64: user_count]
For each user:
  [uint64: username_length][string: username]
  [uint64: password_length][string: password_hash]
  [int32: role]
  [bool: is_active]
```

### Text Reports Format

Reports are generated in human-readable text format:
```
========================================
        ACADEMIC TRANSCRIPT
========================================
Generated: 2026-05-08 14:30:00

STUDENT INFORMATION
-------------------------------------------
Student ID:     STU001
Full Name:      John Doe
...

ACADEMIC RECORD
-------------------------------------------
Subject    Score    Grade    GPA
-------------------------------------------
Math       85.00    B        3.0
...
```

## Memory Management

### Object Lifecycle
1. **Creation**: Student objects created in stack or vector
2. **Storage**: Stored in GradeManager's vector
3. **Access**: Direct pointers to objects (safe within vector scope)
4. **Destruction**: Automatic when vector is destroyed

### Singleton Cleanup
```cpp
// Singletons are created dynamically
static GradeManager* instance = new GradeManager();

// Destructor automatically called:
~GradeManager() {
    saveAllData();  // Save before cleanup
}
```

## Performance Optimizations

### 1. O(1) Student Lookup
```cpp
// Using map index for fast lookup
std::map<std::string, size_t> student_index;
// Instead of O(n) linear search
```

### 2. STL Algorithms
```cpp
// Efficient sorting and searching
std::sort(students.begin(), students.end());
std::find_if(students.begin(), students.end(), predicate);
```

### 3. Binary File I/O
```cpp
// Binary format is more compact than text
// Faster read/write than CSV
// Approximately 60% smaller file size
```

## Extensibility Points

### Adding New Features

1. **New Student Properties**:
   - Add member variable to Student class
   - Implement getter/setter
   - Update serialization in FileHandler

2. **New Subjects**:
   - Uses map-based storage (flexible)
   - Automatically handled

3. **New Reports**:
   - Implement in FileHandler::generateReport()
   - Call from GradeManager

4. **New User Roles**:
   - Add to UserRole enum
   - Update Authentication logic

## Security Considerations

### Current Implementation
- Simple Caesar cipher for password hashing (demo purposes)
- Input validation for all user data
- Session management

### Production Recommendations
1. Use bcrypt for password hashing
2. Implement role-based access control (RBAC)
3. Add audit logging
4. Encrypt sensitive files
5. Implement user session timeouts

## Testing Strategy

### Unit Tests
- Test Grade calculations
- Test Student creation/validation
- Test sorting/filtering functions

### Integration Tests
- Test save/load cycle
- Test login/authentication
- Test report generation

### Performance Tests
- Large student dataset (10,000+)
- Memory usage monitoring
- File I/O benchmarking

## Future Architecture Improvements

1. **Database Layer**: Replace FileHandler with SQL interface
2. **API Layer**: RESTful API for mobile apps
3. **Cache Layer**: Redis for performance
4. **Async Operations**: Threading for file I/O
5. **MVC Pattern**: Separate model and view completely
6. **Event System**: Observer pattern for real-time updates

---

**For implementation details, refer to code comments.**
