# Medical Database System

The Medical Database System is a relational database designed to manage healthcare-related data efficiently. It integrates information about patients, doctors, appointments, medical records, and medical facilities, ensuring data integrity, scalability, and ease of use. The schema is normalized to the Third Normal Form (3NF) to eliminate anomalies and optimize performance.

This system is ideal for hospitals, clinics, or healthcare providers looking to organize and access critical data seamlessly.

## Features
- **Patient Management**: Store and retrieve patient details like name, contact info, and medical history.
- **Doctor Scheduling**: Track doctor profiles, specializations, and appointments.
- **Appointment Tracking**: Schedule and manage appointments with status updates (e.g., Scheduled, Completed, Cancelled).
- **Medical Records**: Maintain detailed records of diagnoses, prescriptions, and test results tied to appointments.
- **Facility Management**: Manage multiple medical facilities and their associated doctors.
- **Data Integrity**: Enforce relationships and constraints to prevent errors.
- **Scalable Design**: Supports growth with minimal changes to the schema.

## System Requirements
- **Database Management System**: MySQL (version 8.0+), PostgreSQL (version 13+), or any relational DBMS supporting foreign key constraints.
- **Tools**: 
  - SQL client (e.g., phpMyAdmin, pgAdmin, or DBeaver) for setup and querying.
  - Optional: Command-line interface (e.g., MySQL Workbench or psql).
- **Hardware**: Standard server or local machine with at least 4GB RAM and 10GB storage for small-scale deployments.
- **Optional**: Git for version control if managing the schema in a repository.

## Database Schema

The database consists of six tables with clearly defined relationships:

### Entities
1. **Patients**: Stores patient details (e.g., `PatientID`, `FirstName`, `DateOfBirth`).
2. **Doctors**: Tracks doctor information (e.g., `DoctorID`, `Specialization`).
3. **Appointments**: Manages appointment schedules (e.g., `AppointmentID`, `PatientID`, `DoctorID`, `Status`).
4. **Medical Records**: Stores health records (e.g., `RecordID`, `Diagnosis`, `Prescription`).
5. **Medical Facilities**: Lists facilities (e.g., `FacilityID`, `FacilityName`).
6. **Doctor_Facilities**: Junction table linking doctors to facilities (e.g., `DoctorID`, `FacilityID`).

### Relationships
- **One-to-Many**: Patients to Appointments, Doctors to Appointments.
- **One-to-One**: Appointments to Medical Records.
- **Many-to-Many**: Doctors to Medical Facilities (via `Doctor_Facilities`).

### Normalization
The schema is in **3NF**, ensuring no insertion, update, or deletion anomalies. Primary and foreign keys enforce referential integrity, and data types are optimized for efficiency (e.g., `Integer` for IDs, `Text` for diagnoses).

For a visual representation, see the [ER Diagram](#er-diagram) in the project documentation or generate it using tools like DBeaver or MySQL Workbench.

## Setup Instructions

### 1. Clone the Repository (Optional)
If the schema is hosted in a repository:
```bash
git clone <repository-url>
cd medical-database-system
```

### 2. Create the Database
Log in to your DBMS and create a new database:
```sql
CREATE DATABASE medical_db;
USE medical_db;
```

### 3. Execute the Schema
Run the following SQL script to create the tables and constraints:

```sql
-- Patients Table
CREATE TABLE Patients (
    PatientID INT PRIMARY KEY AUTO_INCREMENT,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    DateOfBirth DATE NOT NULL,
    Gender VARCHAR(10),
    ContactNumber VARCHAR(15),
    Email VARCHAR(100)
);

-- Doctors Table
CREATE TABLE Doctors (
    DoctorID INT PRIMARY KEY AUTO_INCREMENT,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Specialization VARCHAR(100),
    ContactNumber VARCHAR(15),
    Email VARCHAR(100)
);

-- Medical Facilities Table
CREATE TABLE Medical_Facilities (
    FacilityID INT PRIMARY KEY AUTO_INCREMENT,
    FacilityName VARCHAR(100) NOT NULL,
    Location VARCHAR(200),
    ContactNumber VARCHAR(15)
);

-- Doctor_Facilities Junction Table
CREATE TABLE Doctor_Facilities (
    DoctorID INT,
    FacilityID INT,
    PRIMARY KEY (DoctorID, FacilityID),
    FOREIGN KEY (DoctorID) REFERENCES Doctors(DoctorID) ON DELETE CASCADE,
    FOREIGN KEY (FacilityID) REFERENCES Medical_Facilities(FacilityID) ON DELETE CASCADE
);

-- Appointments Table
CREATE TABLE Appointments (
    AppointmentID INT PRIMARY KEY AUTO_INCREMENT,
    PatientID INT,
    DoctorID INT,
    AppointmentDate DATE NOT NULL,
    AppointmentTime TIME NOT NULL,
    Status VARCHAR(20) CHECK (Status IN ('Scheduled', 'Completed', 'Cancelled')),
    FOREIGN KEY (PatientID) REFERENCES Patients(PatientID) ON DELETE CASCADE,
    FOREIGN KEY (DoctorID) REFERENCES Doctors(DoctorID) ON DELETE CASCADE
);

-- Medical Records Table
CREATE TABLE Medical_Records (
    RecordID INT PRIMARY KEY AUTO_INCREMENT,
    AppointmentID INT UNIQUE,
    Diagnosis TEXT,
    Prescription TEXT,
    TestResults TEXT,
    CreatedAt DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (AppointmentID) REFERENCES Appointments(AppointmentID) ON DELETE CASCADE
);
```

### 4. Verify the Setup
- Check that all tables are created: `SHOW TABLES;`
- Verify constraints: `DESCRIBE <table_name>;` for each table.
- Optionally, insert sample data to test:
```sql
INSERT INTO Patients (FirstName, LastName, DateOfBirth, Gender, ContactNumber, Email)
VALUES ('John', 'Doe', '1985-03-15', 'M', '555-1234', 'john.doe@email.com');

INSERT INTO Doctors (FirstName, LastName, Specialization, ContactNumber, Email)
VALUES ('Alice', 'Smith', 'Cardiology', '555-5678', 'alice.smith@hospital.com');
```

### 5. Optimize (Optional)
- Add indexes for frequent queries:
```sql
CREATE INDEX idx_patient_id ON Appointments(PatientID);
CREATE INDEX idx_doctor_id ON Appointments(DoctorID);
```
- Enable foreign key checks (if not already enabled):
```sql
SET FOREIGN_KEY_CHECKS = 1;
```

## Usage

### Sample Queries
- **List all appointments for a patient**:
```sql
SELECT a.AppointmentID, a.AppointmentDate, a.AppointmentTime, d.FirstName, d.LastName
FROM Appointments a
JOIN Patients p ON a.PatientID = p.PatientID
JOIN Doctors d ON a.DoctorID = d.DoctorID
WHERE p.PatientID = 1;
```

- **Find doctors at a specific facility**:
```sql
SELECT d.FirstName, d.LastName, d.Specialization
FROM Doctors d
JOIN Doctor_Facilities df ON d.DoctorID = df.DoctorID
JOIN Medical_Facilities mf ON df.FacilityID = mf.FacilityID
WHERE mf.FacilityName = 'City Hospital';
```

- **View medical records for an appointment**:
```sql
SELECT mr.RecordID, mr.Diagnosis, mr.Prescription, mr.TestResults
FROM Medical_Records mr
WHERE mr.AppointmentID = 1;
```

### Interacting with the Database
- Use a SQL client or application to query the database.
- Integrate with a frontend (e.g., a web app built with Python/Django or Node.js) for user-friendly access.
- Regularly back up the database to prevent data loss:
```sql
mysqldump -u [username] -p medical_db > backup.sql
```

## Contributing
We welcome contributions to improve the Medical Database System! To contribute:
1. Fork the repository.
2. Create a new branch: `git checkout -b feature/your-feature`.
3. Make changes and commit: `git commit -m "Add your feature"`.
4. Push to your fork: `git push origin feature/your-feature`.
5. Submit a pull request with a clear description of changes.

Please ensure your changes maintain 3NF and include tests or documentation updates.

## License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contact
For questions, bug reports, or suggestions, please open an issue on the repository or contact the project maintainer at [support@medicaldbsystem.org](mailto:support@medicaldbsystem.org).

---

Happy data managing!