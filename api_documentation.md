# Digital Student Rank Card Management System API Documentation

This document describes all API endpoints, their expected request JSON bodies (payloads), headers, and full success/error response JSON structures.

---

## Global Response & Error Formats

### Standard Success Structure
Every API endpoint returns a standard JSON envelope:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": {}
}
```

### Standard Error Structure
Whenever a request fails (e.g. invalid credentials, validation error, permission denied), it returns:
```json
{
  "success": false,
  "message": "Description of the error"
}
```

---

## 1. Authentication Endpoints

### 1.1 User Login
* **URL**: `/api/auth/login`
* **Method**: `POST`
* **Headers**:
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "userId": "admin",
  "password": "Admin@123"
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Login successful.",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmcmVzaCI6ZmFsc2UsImlhdCI6...",
    "user": {
      "userId": "admin",
      "role": "SUPER_ADMIN"
    }
  }
}
```
> [!NOTE]
> For teachers, the `user` block is:
> `{"userId": "teacher1", "role": "TEACHER", "name": "John Doe", "teacherId": "teacher1", "department": "Science & Maths", "assignedClasses": ["6a4759faf045ac1652f4b2c8"]}`
> For students, the `user` block is:
> `{"userId": "student1", "role": "STUDENT", "name": "Alice Smith", "studentId": "student1", "classId": "6a4759faf045ac1652f4b2c8", "rollNumber": "101"}`

* **Error Response (401 Unauthorized)**:
```json
{
  "success": false,
  "message": "Invalid credentials."
}
```

---

### 1.2 User Logout
* **URL**: `/api/auth/logout`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Logout successful."
}
```

---

### 1.3 Change Password
* **URL**: `/api/auth/change-password`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "oldPassword": "CurrentPassword123",
  "newPassword": "NewPassword123"
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Password updated successfully."
}
```

---

## 2. Super Admin Endpoints (Requires `SUPER_ADMIN` authorization)

### 2.1 Create Teacher Account
* **URL**: `/api/admin/create-teacher`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "userId": "teacher1",
  "password": "Password@123",
  "name": "John Doe",
  "department": "Science & Maths",
  "teacherId": "teacher1"
}
```
* **Success Response (201 Created)**:
```json
{
  "success": true,
  "message": "Teacher account created successfully.",
  "data": {
    "teacherId": "teacher1",
    "userId": "teacher1",
    "name": "John Doe",
    "department": "Science & Maths",
    "assignedClasses": []
  }
}
```

---

### 2.2 Create Student Account
* **URL**: `/api/admin/create-student`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "userId": "student1",
  "password": "Password@123",
  "name": "Alice Smith",
  "classId": "6a4759faf045ac1652f4b2c8",
  "rollNumber": "101",
  "studentId": "student1"
}
```
* **Success Response (201 Created)**:
```json
{
  "success": true,
  "message": "Student account created successfully.",
  "data": {
    "studentId": "student1",
    "userId": "student1",
    "name": "Alice Smith",
    "classId": "6a4759faf045ac1652f4b2c8",
    "rollNumber": "101"
  }
}
```

---

### 2.3 Get All Teachers
* **URL**: `/api/admin/teachers`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "_id": "6a4759faf045ac1652f4b2cc",
      "teacherId": "teacher1",
      "userId": "teacher1",
      "name": "John Doe",
      "department": "Science & Maths",
      "assignedClasses": ["6a4759faf045ac1652f4b2c8"]
    }
  ]
}
```

---

### 2.4 Get All Students
* **URL**: `/api/admin/students`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "_id": "6a4759fbf045ac1652f4b2ce",
      "studentId": "student1",
      "userId": "student1",
      "name": "Alice Smith",
      "classId": "6a4759faf045ac1652f4b2c8",
      "rollNumber": "101"
    }
  ]
}
```

---

### 2.5 Reset Password
* **URL**: `/api/admin/reset-password/<userId>`
* **Method**: `PUT`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "newPassword": "NewPassword123"
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Password for user 'teacher1' reset successfully."
}
```

---

### 2.6 Activate / Deactivate User Account
* **URL**: `/api/admin/set-status/<userId>`
* **Method**: `PUT`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "active": false
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "User 'teacher1' has been deactivated successfully."
}
```

---

### 2.7 Create Class
* **URL**: `/api/admin/create-class`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "className": "Class 10",
  "section": "A",
  "classTeacher": "teacher1"
}
```
* **Success Response (201 Created)**:
```json
{
  "success": true,
  "message": "Class created successfully.",
  "data": {
    "_id": "6a4759faf045ac1652f4b2c8",
    "className": "Class 10",
    "section": "A",
    "classTeacher": "teacher1"
  }
}
```

---

### 2.8 Create Subject
* **URL**: `/api/admin/create-subject`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "subjectName": "Mathematics"
}
```
* **Success Response (201 Created)**:
```json
{
  "success": true,
  "message": "Subject created successfully.",
  "data": {
    "_id": "6a4759faf045ac1652f4b2c9",
    "subjectName": "Mathematics"
  }
}
```

---

### 2.9 Assign Teacher to Class
* **URL**: `/api/admin/assign-teacher`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "teacherId": "teacher1",
  "classId": "6a4759faf045ac1652f4b2c8"
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Teacher 'teacher1' successfully assigned to class '6a4759faf045ac1652f4b2c8'."
}
```

---

## 3. Teacher Endpoints (Requires `TEACHER` authorization)

### 3.1 Get Assigned Classes
* **URL**: `/api/teacher/classes`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "_id": "6a4759faf045ac1652f4b2c8",
      "className": "Class 10",
      "section": "A",
      "classTeacher": "teacher1"
    }
  ]
}
```

---

### 3.2 Get Students in Class
* **URL**: `/api/teacher/students/<classId>`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "_id": "6a4759fbf045ac1652f4b2ce",
      "studentId": "student1",
      "userId": "student1",
      "name": "Alice Smith",
      "classId": "6a4759faf045ac1652f4b2c8",
      "rollNumber": "101"
    }
  ]
}
```

---

### 3.3 Enter Student Marks
* **URL**: `/api/marks`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "studentId": "student1",
  "classId": "6a4759faf045ac1652f4b2c8",
  "subjectId": "6a4759faf045ac1652f4b2c9",
  "exam": "Final",
  "marks": 95,
  "academicYear": "2026"
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Marks entered successfully."
}
```

---

### 3.4 Edit Student Marks
* **URL**: `/api/marks/<markId>`
* **Method**: `PUT`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "marks": 98
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Marks updated successfully."
}
```

---

### 3.5 Publish Marks
* **URL**: `/api/marks/publish`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "classId": "6a4759faf045ac1652f4b2c8",
  "exam": "Final",
  "academicYear": "2026"
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Marks published and rankings computed successfully."
}
```

---

### 3.6 Get Class Rankings
* **URL**: `/api/teacher/rankings/<classId>`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Query Parameters**:
  * `exam` (Required string, e.g. `Final`)
  * `academicYear` (Required string, e.g. `2026`)
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "_id": "6a4759fcefca6c33cc1b724f",
      "studentId": "student1",
      "name": "Alice Smith",
      "classId": "6a4759faf045ac1652f4b2c8",
      "exam": "Final",
      "academicYear": "2026",
      "totalMarks": 180.0,
      "percentage": 90.0,
      "grade": "A+",
      "passed": true,
      "rank": 1,
      "publishedAt": "2026-07-03T06:43:08.236000",
      "subjectMarks": [
        {
          "subjectId": "6a4759faf045ac1652f4b2c9",
          "subjectName": "Mathematics",
          "marks": 95.0,
          "teacherId": "teacher1"
        },
        {
          "subjectId": "6a4759faf045ac1652f4b2ca",
          "subjectName": "Science",
          "marks": 85.0,
          "teacherId": "teacher1"
        }
      ]
    }
  ]
}
```

---

## 4. Student Endpoints (Requires `STUDENT` authorization)

### 4.1 Get Profile Detail
* **URL**: `/api/student/profile`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": {
    "studentId": "student1",
    "userId": "student1",
    "name": "Alice Smith",
    "rollNumber": "101",
    "classId": "6a4759faf045ac1652f4b2c8",
    "class": {
      "className": "Class 10",
      "section": "A"
    }
  }
}
```

---

### 4.2 Get Student Marks
* **URL**: `/api/student/marks`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Query Parameters** (Optional):
  * `exam` (string, e.g. `Final`)
  * `academicYear` (string, e.g. `2026`)
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "_id": "6a4759fcefca6c33cc1b724d",
      "studentId": "student1",
      "classId": "6a4759faf045ac1652f4b2c8",
      "subjectId": "6a4759faf045ac1652f4b2c9",
      "teacherId": "teacher1",
      "exam": "Final",
      "marks": 95.0,
      "academicYear": "2026",
      "createdAt": "2026-07-03T06:43:08.179000",
      "updatedAt": "2026-07-03T06:43:08.179000"
    }
  ]
}
```

---

### 4.3 Get Student Report Card
* **URL**: `/api/student/report-card`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Query Parameters** (Optional):
  * `exam` (string, e.g. `Final`)
  * `academicYear` (string, e.g. `2026`)
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "_id": "6a4759fcefca6c33cc1b724f",
      "studentId": "student1",
      "name": "Alice Smith",
      "classId": "6a4759faf045ac1652f4b2c8",
      "exam": "Final",
      "academicYear": "2026",
      "totalMarks": 180.0,
      "percentage": 90.0,
      "grade": "A+",
      "passed": true,
      "rank": 1,
      "publishedAt": "2026-07-03T06:43:08.236000",
      "subjectMarks": [
        {
          "subjectId": "6a4759faf045ac1652f4b2c9",
          "subjectName": "Mathematics",
          "marks": 95.0,
          "teacherId": "teacher1"
        },
        {
          "subjectId": "6a4759faf045ac1652f4b2ca",
          "subjectName": "Science",
          "marks": 85.0,
          "teacherId": "teacher1"
        }
      ]
    }
  ]
}
```

---

### 4.4 Get Student Rank & Grade Overall Status
* **URL**: `/api/student/rank`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Query Parameters** (Optional):
  * `exam` (string, e.g. `Final`)
  * `academicYear` (string, e.g. `2026`)
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "exam": "Final",
      "academicYear": "2026",
      "rank": 1,
      "totalMarks": 180.0,
      "percentage": 90.0,
      "grade": "A+",
      "passed": true
    }
  ]
}
```

---

## 5. Metadata Endpoints (Requires standard JWT login)

### 5.1 Get All Classes List
* **URL**: `/api/classes`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "_id": "6a4759faf045ac1652f4b2c8",
      "className": "Class 10",
      "section": "A",
      "classTeacher": "teacher1"
    }
  ]
}
```

---

### 5.2 Get All Subjects List
* **URL**: `/api/subjects`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": [
    {
      "_id": "6a4759faf045ac1652f4b2c9",
      "subjectName": "Mathematics"
    }
  ]
}
```

---

## 6. Exam Management & Extensions (Phase 3)

### 6.1 Create Exam
* **URL**: `/api/exams`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "examId": "EXM_TEST_V2",
  "examName": "Final Term 11",
  "classId": "6a4773f94fd0b9c4a4b38a26",
  "academicYear": "2026",
  "term": "Term 2",
  "maxMarks": 100,
  "passMarks": 35,
  "startDate": "2026-11-01",
  "endDate": "2026-11-10"
}
```
* **Success Response (201 Created)**:
```json
{
  "success": true,
  "message": "Exam created successfully.",
  "data": {
    "examId": "EXM_TEST_V2",
    "examName": "Final Term 11",
    "classId": "6a4773f94fd0b9c4a4b38a26",
    "academicYear": "2026",
    "term": "Term 2",
    "maxMarks": 100,
    "passMarks": 35,
    "startDate": "2026-11-01",
    "endDate": "2026-11-10",
    "status": "DRAFT",
    "createdBy": "teacher2",
    "createdAt": "2026-07-03T09:05:16.965864+00:00",
    "updatedAt": "2026-07-03T09:05:16.965864+00:00"
  }
}
```

---

### 6.2 Get Exams List
* **URL**: `/api/exams`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  "message": "Operation completed successfully.",
  "data": [
    {
      "examId": "EXM_TEST_001",
      "examName": "Mid Term 11",
      "classId": "6a4773f94fd0b9c4a4b38a26",
      "academicYear": "2026",
      "term": "Term 1",
      "maxMarks": 100,
      "passMarks": 40,
      "status": "DRAFT",
      "createdBy": "teacher2"
    }
  ]
}
```
*(Note: Teachers only retrieve exams they created; Admins retrieve all exams).*

---

### 6.3 Get Exam Details
* **URL**: `/api/exams/<examId>`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": {
    "examId": "EXM_TEST_001",
    "examName": "Mid Term 11",
    "classId": "6a4773f94fd0b9c4a4b38a26",
    "academicYear": "2026",
    "term": "Term 1",
    "maxMarks": 100,
    "passMarks": 40,
    "status": "DRAFT",
    "createdBy": "teacher2"
  }
}
```

---

### 6.4 Update Exam Properties
* **URL**: `/api/exams/<examId>`
* **Method**: `PUT`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "examName": "Updated Exam Name",
  "passMarks": 45
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Exam updated successfully.",
  "data": {
    "examId": "EXM_TEST_001",
    "examName": "Updated Exam Name",
    "classId": "6a4773f94fd0b9c4a4b38a26",
    "academicYear": "2026",
    "term": "Term 1",
    "maxMarks": 100,
    "passMarks": 45,
    "status": "DRAFT",
    "createdBy": "teacher2"
  }
}
```

---

### 6.5 Delete Exam
* **URL**: `/api/exams/<examId>`
* **Method**: `DELETE`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Exam and its associated marks/report cards deleted successfully."
}
```

---

### 6.6 Bulk Save Marks
* **URL**: `/api/exams/<examId>/marks/bulk`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
  * `Content-Type: application/json`
* **Request Body**:
```json
{
  "students": [
    {
      "studentId": "student3",
      "subjects": [
        {"subjectId": "6a4773f94fd0b9c4a4b38a27", "marks": 90},
        {"subjectId": "6a4773f94fd0b9c4a4b38a28", "marks": 80}
      ]
    },
    {
      "studentId": "student4",
      "subjects": [
        {"subjectId": "6a4773f94fd0b9c4a4b38a27", "marks": 90}
      ]
    }
  ]
}
```
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Marks saved successfully."
}
```

---

### 6.7 Get Mark Entry Spreadsheet Data
* **URL**: `/api/exams/<examId>/marksheet`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Marksheet sheet retrieved successfully.",
  "data": {
    "exam": {
      "examId": "EXM_TEST_001",
      "examName": "Mid Term 11",
      "classId": "6a4773f94fd0b9c4a4b38a26",
      "status": "DRAFT",
      "maxMarks": 100,
      "passMarks": 40
    },
    "classDetails": {
      "_id": "6a4773f94fd0b9c4a4b38a26",
      "className": "Class 11",
      "section": "B"
    },
    "subjects": [
      {
        "_id": "6a4773f94fd0b9c4a4b38a27",
        "subjectName": "Maths 11"
      }
    ],
    "students": [
      {
        "studentId": "student3",
        "name": "Charlie Brown",
        "rollNumber": "201"
      }
    ],
    "existingMarks": [
      {
        "studentId": "student3",
        "subjectId": "6a4773f94fd0b9c4a4b38a27",
        "marks": 90.0,
        "teacherId": "teacher2"
      }
    ]
  }
}
```

---

### 6.8 Publish Exam (Calculate Dense Ranks)
* **URL**: `/api/exams/<examId>/publish`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Exam published and rankings computed successfully."
}
```

---

### 6.9 Unlock Published Exam (Super Admin Only)
* **URL**: `/api/exams/<examId>/unlock`
* **Method**: `POST`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Exam unlocked and status reset to DRAFT successfully."
}
```

---

### 6.10 Get Exam Statistics
* **URL**: `/api/exams/<examId>/statistics`
* **Method**: `GET`
* **Headers**:
  * `Authorization: Bearer <jwt_access_token>`
* **Request Body**: `None`
* **Success Response (200 OK)**:
```json
{
  "success": true,
  "message": "Statistics retrieved successfully.",
  "data": {
    "highestMark": 170.0,
    "lowestMark": 160.0,
    "average": 166.67,
    "passPercentage": 0.0,
    "failPercentage": 100.0,
    "gradeDistribution": {
      "A+": 0,
      "A": 0,
      "B": 0,
      "C": 0,
      "D": 0,
      "E": 0,
      "F": 3
    },
    "topStudents": [
      {
        "studentId": "student3",
        "name": "Charlie Brown",
        "totalMarks": 170.0,
        "rank": 1
      },
      {
        "studentId": "student4",
        "name": "Diana Prince",
        "totalMarks": 170.0,
        "rank": 1
      },
      {
        "studentId": "student5",
        "name": "Ethan Hunt",
        "totalMarks": 160.0,
        "rank": 2
      }
    ],
    "subjectWiseAverage": [
      {
        "subjectId": "6a4773f94fd0b9c4a4b38a27",
        "subjectName": "Maths 11",
        "average": 88.33
      },
      {
        "subjectId": "6a4773f94fd0b9c4a4b38a28",
        "subjectName": "Science 11",
        "average": 78.33
      }
    ]
  }
}
```
