# 🎓 AttendAI — Smart Attendance Monitoring System

**An intelligent, web-based attendance management platform powered by face recognition, real-time analytics, and student verification.**

AttendAI automates classroom attendance through student registration, face-based identity verification, attendance tracking, and analytics. It provides a centralized dashboard for monitoring attendance, reviewing absence history, identifying students with low attendance, and managing attendance records.

The application combines a Flask REST API, SQLite database, and responsive web interface with optional biometric face verification.

---

## ✨ Key Features

### 📊 Dashboard
- Real-time attendance statistics and daily attendance overview.
- Total students, students present today, attendance rate, and low-attendance count.
- Today's attendance records in a centralized table.
- Department-wise student distribution using doughnut charts.

### 👨‍🎓 Student Registration
- Register students with their name, register number, department, and academic year.
- Capture a face image through a webcam or upload an existing photograph.
- Search and browse registered students.
- View individual student absence history, including dates and day names.
- Filter absence history by month and year.

### 📝 Attendance Management
- View complete attendance history.
- Filter records by date, time, and attendance method.
- Manually mark attendance through a dedicated interface.
- Update attendance status between present and absent.
- Track attendance records with their recording method.

### 📈 Analytics and Reporting
- Monthly attendance trends displayed using line charts.
- Department-wise attendance comparisons using horizontal bar charts.
- Individual student attendance-rate comparisons.
- Highlight students with the lowest attendance rates.
- Identify students whose attendance falls below the configurable 75% threshold.

### 🪪 Dynamic Student ID Verification
- Search by register number or select a student from a dropdown.
- Display autocomplete suggestions while searching.
- Preview student details and face-registration status.
- Capture a live webcam image for identity verification.
- Compare the captured face against the selected student's registered face only.
- Display verification results, confidence information, and attendance status.
- Record successful and failed verification attempts with error details.

### 📷 Optional Desktop Face Scanner
- Run a standalone camera scanner independently of the web interface.
- Scan registered students or verify a specific student.
- Support a simulation mode when the optional face-recognition dependency is unavailable.

---

## 🏗️ System Architecture

```text
AttendAI/
│
├── backend/
│   ├── app.py                  # Flask REST API
│   ├── database.py             # SQLite database operations
│   ├── face_scanner.py         # Optional desktop camera scanner
│   ├── requirements.txt        # Python dependencies
│   └── known_faces/             # Registered face images
│
├── frontend/
│   ├── index.html              # Application entry point
│   ├── css/
│   │   └── style.css           # Dark-mode UI and styling
│   └── js/
│       ├── app.js              # SPA navigation, API utilities, dashboard
│       ├── registration.js     # Student registration and absence history
│       ├── attendance.js       # Attendance records and manual marking
│       ├── analytics.js        # Attendance analytics and charts
│       └── verification.js     # Student identity verification
│
├── attendance.db               # Auto-created SQLite database
└── README.md
```

### Application workflow

```text
Student Registration
        │
        ▼
Student Database ──────► Face Registration
        │
        ▼
Student ID Selection
        │
        ▼
Live Webcam Capture
        │
        ▼
Face Detection and Comparison
        │
        ├── Match ─────► Mark Attendance
        │                    │
        │                    ▼
        │              Attendance Database
        │
        └── No Match ──► Record Failed Attempt
                             │
                             ▼
                       Verification Logs

Dashboard and Analytics ◄── Database Records
```

---

## 🛠️ Technology Stack

| Component | Technology |
|---|---|
| Backend | Python 3, Flask |
| API | Flask REST endpoints |
| Database | SQLite |
| Frontend | HTML5, CSS3, Vanilla JavaScript |
| Charts | Chart.js |
| Cross-origin requests | Flask-CORS |
| Image processing | Pillow |
| Optional face recognition | `face_recognition`, dlib |
| Optional camera scanning | OpenCV |

**Architecture:** Lightweight client-server application with a modular JavaScript frontend and Flask backend.

---

## 🚀 Getting Started

### Prerequisites

Install the following before running AttendAI:

- Python 3
- pip package manager
- A modern web browser
- A webcam for live face verification
- Git (optional, for cloning the repository)

### 1. Clone the repository

```bash
git clone https://github.com/Jashvanthan/AttendAI.git
cd AttendAI
```

Replace the repository URL with your actual GitHub repository URL if the project is hosted under a different name.

### 2. Create a virtual environment

**Windows — PowerShell**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**Linux / macOS**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
cd backend
pip install -r requirements.txt
```

If you have not created `requirements.txt` yet, the basic web application dependencies can be installed with:

```bash
pip install Flask Flask-Cors Pillow
```

Use the project's actual dependency list as the source of truth. Additional packages may be required by the implementation.

### 4. Start the backend server

From the `backend` directory, run:

```bash
python app.py
```

Wait until Flask reports that the development server is running.

### 5. Open the application

Navigate to:

**http://localhost:5000**

The frontend must be served by the Flask application or another configured local web server. If Flask serves the frontend files automatically, no separate frontend server is required.

> **Note:** Webcam access generally requires HTTPS or a trusted local origin such as `localhost`. Opening `index.html` directly as a file may prevent browser API requests or camera access from working correctly.

---

## 🔒 Face Verification Workflow

AttendAI uses a student-specific verification process intended to reduce identity mix-ups.

1. **Identify the student:** Enter a register number or select a registered student.
2. **Retrieve the record:** Fetch the corresponding student details from the database.
3. **Check registration:** Verify that the selected student has a registered face encoding.
4. **Capture an image:** Obtain a live image from the webcam.
5. **Detect faces:** Reject attempts when no face or multiple faces are detected.
6. **Compare faces:** Compare the detected face encoding against the selected student's stored encoding.
7. **Evaluate the result:** Apply the configured face-distance threshold.
8. **Record the outcome:** On a successful match, mark attendance and log the verification result. On failure, log the attempt and display an appropriate error.

### Verification decision

```text
Selected Student
       │
       ▼
Registered Face Available?
       │
       ├── No ──► Reject Verification
       │
       ▼
Capture Live Image
       │
       ▼
Exactly One Face Detected?
       │
       ├── No ──► Reject Verification
       │
       ▼
Compare Face Encodings
       │
       ▼
Distance ≤ Configured Threshold?
       │
       ├── Yes ──► Mark Attendance
       │
       └── No ───► Log Failed Attempt
```

The configured threshold in the current implementation is `0.5`. Face-distance values are not percentages, and the threshold should be validated against representative test data before real-world use.

### Simulation mode

When the optional `face_recognition` library is unavailable, the application can run in simulation mode, according to its implementation.

In simulation mode, biometric comparison is skipped. Therefore, a successful simulated operation must not be treated as proof of a real face match.

### Enable real face recognition (optional)

Install the required dependencies:

```bash
pip install cmake
pip install dlib
pip install face_recognition
pip install opencv-python
```

On Windows, compiling dlib may require compatible C++ build tools and a supported Python environment. Installation success depends on your operating system, Python version, and available wheels.

---

## 🖥️ Running the Desktop Scanner

The optional scanner can be launched independently from the backend directory.

### Scan registered students

```bash
python face_scanner.py
```

### Verify a specific student

```bash
python face_scanner.py --student 3
```

Replace `3` with the appropriate student ID from the database.

The scanner requires the camera and optional recognition dependencies to be configured correctly.

---

## 🔌 REST API Reference

The Flask backend exposes the following endpoints.

### Student management

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/students` | List registered students |
| POST | `/api/students` | Register a new student |
| GET | `/api/students/<id>` | Retrieve student details |
| DELETE | `/api/students/<id>` | Delete a student |
| POST | `/api/students/<id>/face` | Upload or register face data |
| GET | `/api/students/<id>/absences` | Retrieve absence history |

### Attendance management

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/attendance` | List attendance records with supported filters |
| GET | `/api/attendance/today` | Retrieve today's attendance |
| POST | `/api/attendance/mark-manual` | Mark attendance manually |
| PUT | `/api/attendance/<id>` | Update an attendance record |

### Face verification and scanner

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/scanner/verify` | Verify a selected student's face |
| GET | `/api/scanner/logs` | Retrieve verification attempt logs |
| GET | `/api/scanner/status` | Check recognition-library status |

### Analytics and departments

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/analytics` | Retrieve analytics data |
| GET | `/api/analytics/low-attendance` | List students below the attendance threshold |
| GET | `/api/departments` | List departments |

**API notes**

- `<id>` represents the relevant database identifier.
- Request bodies, response structures, status codes, and supported query parameters depend on the implementation in `backend/app.py`.
- For exact request and response examples, consult the corresponding Flask route definitions.

---

## 🗄️ Database Design

AttendAI uses SQLite to store student records, attendance information, face data, and verification history.

| Table | Purpose |
|---|---|
| `students` | Student identity, register number, department, and year |
| `face_embeddings` | Stored face encodings associated with students |
| `attendance` | Attendance date, time, status, and method |
| `verification_log` | Face-verification attempts and their outcomes |

The database is automatically created by the application if its initialization logic is configured to do so.

### Data relationships

- A student can have multiple attendance records.
- A student can have a registered face encoding.
- A student can have multiple absence records over time.
- Verification logs preserve a history of successful and failed attempts.

Refer to `backend/database.py` for the actual schema, constraints, and relationships.

---

## 🔐 Security and Privacy Considerations

Because AttendAI handles student information and biometric data, responsible deployment is essential.

- **Protect biometric data:** Restrict access to registered face images and stored encodings.
- **Enforce authorization:** Protect student-management and attendance-modification endpoints from unauthorized access.
- **Validate inputs:** Validate register numbers, uploaded images, IDs, and attendance updates on the backend.
- **Prevent duplicate attendance:** Enforce appropriate database constraints and transaction handling.
- **Audit changes:** Record attendance modifications and verification outcomes appropriately.
- **Protect sensitive logs:** Avoid exposing unnecessary student or biometric information in logs and API responses.
- **Use secure deployment settings:** Configure HTTPS, appropriate CORS origins, and secure production server settings.
- **Obtain appropriate consent:** Establish clear policies for biometric data collection, retention, deletion, and access.

> **Privacy notice:** The presence of a verification log or a face-encoding table does not itself guarantee privacy or security. Actual protections depend on access controls, storage design, retention policies, and deployment configuration.

---

## 🧪 Testing Checklist

Before deploying the application, verify the following scenarios:

- [ ] Register a student with valid information.
- [ ] Reject invalid or duplicate register numbers.
- [ ] Upload a valid face image.
- [ ] Handle missing face registration.
- [ ] Handle images containing no face or multiple faces.
- [ ] Verify a matching face and record attendance.
- [ ] Reject a non-matching face and record the failed attempt.
- [ ] Prevent duplicate attendance for the same student and session.
- [ ] Mark attendance manually and update the record.
- [ ] Filter attendance and absence history correctly.
- [ ] Validate analytics and low-attendance calculations.
- [ ] Handle unavailable recognition dependencies gracefully.
- [ ] Test unauthorized requests and invalid API input.

---

## 🗺️ Future Enhancements

Potential improvements include:

- Role-based access for administrators, faculty, and students.
- Exportable attendance reports in CSV and PDF formats.
- Automated notifications for low attendance.
- Timetable-based attendance sessions.
- Stronger liveness detection and anti-spoofing measures.
- Automated tests for API endpoints and database operations.
- Docker-based development and deployment.
- Configurable attendance policies and reporting periods.

These are proposed enhancements and should not be interpreted as existing functionality.

---

## 🤝 Contributing

Contributions that improve functionality, reliability, accessibility, testing, or documentation are welcome.

1. Fork the repository.
2. Create a feature branch.
3. Make a focused change.
4. Test the affected functionality.
5. Submit a pull request with a clear description of the change.

Please avoid committing databases containing real student information, face images, biometric encodings, credentials, or other sensitive data.

---

## 📄 License

Add a `LICENSE` file specifying the license under which you intend to distribute this project. Until a license is included, users should not assume that they have permission to reuse or redistribute the code.

---

## 👨‍💻 Project Summary

**AttendAI — Smart Attendance Monitoring System**

A Python-based attendance platform combining a Flask backend, SQLite database, interactive web dashboard, student registration, attendance analytics, and optional face recognition.

**Built with:** Python · Flask · SQLite · HTML · CSS · JavaScript · Chart.js · OpenCV (optional)

*Designed to make attendance management more organized, traceable, and efficient.* 🎓
