# 🏫 מערכת שיבוץ תלמידים לכיתות

## ארכיטקטורה

```
backend/          ← C# ASP.NET Core 8
frontend/         ← React + TypeScript + Redux Toolkit
sample_students.csv
```

---

## Backend – הפעלה

```bash
cd backend
dotnet restore
dotnet run
# שרת רץ על http://localhost:5000
```

### API Endpoints

| Method | URL | תיאור |
|--------|-----|--------|
| GET | /api/students | כל התלמידים |
| POST | /api/students/import | ייבוא CSV (form-data: file) |
| DELETE | /api/students | מחיקת כל התלמידים |
| GET | /api/classrooms | כל הכיתות |
| POST | /api/classrooms | יצירת כיתה |
| PUT | /api/classrooms/{id} | עדכון כיתה |
| DELETE | /api/classrooms/{id} | מחיקת כיתה |
| POST | /api/sort | הפעלת מיון |
| GET | /api/sort | קבלת תוצאה אחרונה |

---

## Frontend – הפעלה

```bash
cd frontend
npm install
npm run dev
# אפליקציה רצה על http://localhost:5173
```

---

## מבנה Redux Store

```
store/
  studentsSlice.ts   → { items[], loading, error }
  classroomsSlice.ts → { items[], loading, error }
  sortSlice.ts       → { result, loading, error }
  index.ts           → configureStore + typed hooks
```

---

## פורמט CSV לייבוא תלמידים

```csv
FirstName,LastName,GradeLevel,Disabilities
דנה,כהן,1,Blind
יוסי,לוי,1,Deaf
שרה,פרץ,2,"Blind,Deaf"
מיכל,אברהם,1,
```

- **GradeLevel**: 1=א׳  2=ב׳  3=ג׳  4=ד׳  5=ה׳  6=ו׳
- **Disabilities**: ערכים אפשריים: `Blind, Deaf, Physical, Cognitive, Speech`

---

## אלגוריתם המיון (`SortingService.cs`)

1. תלמידים עם **יותר נכויות** מקבלים עדיפות (קשה יותר לשבץ)
2. לכל תלמיד מחפשים כיתה שעומדת ב-3 תנאים:
   - **שכבה זהה** (א׳, ב׳...)
   - תומכת ב**כל** הנכויות של התלמיד
   - יש **מקום פנוי**
3. מבין הכיתות המתאימות בוחרים את ה**מלאה ביותר** (לאזן עומס)
4. תלמידים ללא כיתה מתאימה → רשימת "לא שובצו"
