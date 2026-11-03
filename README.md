# 📝 Quiz System — Online Quiz Platform

A Supabase-powered teacher–student quiz platform. Teachers upload questions; students join with a 6-digit code.

**English** | **[中文](README.zh-CN.md)**

**[English](README.md)** | **中文**

---

## 🌐 Live Demo

- **Teacher:** [thomaszhou22.github.io/quiz-system/teacher](https://thomaszhou22.github.io/quiz-system/teacher/)
- **Student:** [thomaszhou22.github.io/quiz-system/student](https://thomaszhou22.github.io/quiz-system/student/)

---

## ✨ Features

### 👩🏫 Teacher

| Feature | Description |
|------|------|
| Create quiz banks | Enter a name, get an auto-generated 6-digit share code |
| Chapter management | Organize questions into chapters within a bank |
| 📷 Image upload | Upload question images and mark correct answers (A/B/C/D) one by one |
| 📝 Text input | Batch-paste questions in a simple format, auto-parsed |
| 📊 Statistics | Student submissions, average accuracy, per-question accuracy ranking, student list |
| ✏️ Edit questions | View, edit, and delete individual questions |
| Multiple question types | Multiple choice, image-based, true/false, fill-in-the-blank |
| Share code | Each bank has a unique 6-digit code to share with students |

**Text input format:**

Multiple choice:
```
1. Question text
A) Option A
B) Option B
C) Option C
D) Option D
Answer: B
```

True/false:
```
1. Question text
Answer: T
```

Fill-in-the-blank:
```
1. Question text
Answer: correct answer
```

### 🎓 Student

| Feature | Description |
|------|------|
| 👤 Identity | Enter nickname and class on first use, saved automatically |
| Join with code | Enter the teacher's 6-digit code to load questions |
| Chapter selection | Pick which chapters to practice and set question count |
| Multiple question types | Multiple choice, true/false, fill-in-the-blank, image questions |
| Shuffled options | Choice options are randomly shuffled |
| Instant grading | Right/wrong shown immediately, correct answer highlighted |
| Auto-advance | Next question after 1.5 seconds |
| Progress bar | Live correct / wrong / remaining counts |
| Image zoom | Tap a question image for fullscreen view |
| Mistake book | Wrong answers collected automatically for review |
| 🔄 Retry mistakes | Re-practice from your mistake book |
| Completion summary | Total questions, accuracy stats, auto-submitted to the teacher |

---

## ⚙️ Deployment Guide

### 1. Create a Supabase project

1. Go to [supabase.com](https://supabase.com) and create a project
2. Note down the project **URL** and **anon key**

### 2. Create database tables

Run in the Supabase SQL Editor:

```sql
-- Quiz banks
CREATE TABLE quiz_banks (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    name TEXT NOT NULL,
    code TEXT UNIQUE NOT NULL,
    chapters JSONB DEFAULT '[]',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Questions
CREATE TABLE quiz_questions (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    bank_id UUID REFERENCES quiz_banks(id) ON DELETE CASCADE,
    chapter_name TEXT,
    question_type TEXT NOT NULL CHECK (question_type IN ('image', 'text', 'truefalse', 'fill')),
    question_content TEXT NOT NULL,
    options TEXT,
    answer TEXT NOT NULL,
    sort_order INT DEFAULT 0
);

-- Results
CREATE TABLE quiz_results (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    bank_id UUID REFERENCES quiz_banks(id) ON DELETE CASCADE,
    student_name TEXT NOT NULL DEFAULT 'Anonymous',
    class_name TEXT DEFAULT '',
    total_questions INT NOT NULL,
    correct_count INT NOT NULL,
    wrong_count INT NOT NULL,
    accuracy INT NOT NULL,
    answers JSONB DEFAULT '[]',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable RLS
ALTER TABLE quiz_banks ENABLE ROW LEVEL SECURITY;
ALTER TABLE quiz_questions ENABLE ROW LEVEL SECURITY;
ALTER TABLE quiz_results ENABLE ROW LEVEL SECURITY;

-- Allow anonymous read/write (for development; tighten for production as needed)
CREATE POLICY "Public read banks" ON quiz_banks FOR SELECT USING (true);
CREATE POLICY "Public write banks" ON quiz_banks FOR INSERT WITH CHECK (true);
CREATE POLICY "Public delete banks" ON quiz_banks FOR DELETE USING (true);
CREATE POLICY "Public update banks" ON quiz_banks FOR UPDATE USING (true);

CREATE POLICY "Public read questions" ON quiz_questions FOR SELECT USING (true);
CREATE POLICY "Public write questions" ON quiz_questions FOR INSERT WITH CHECK (true);
CREATE POLICY "Public delete questions" ON quiz_questions FOR DELETE USING (true);
CREATE POLICY "Public update questions" ON quiz_questions FOR UPDATE USING (true);

CREATE POLICY "Public read results" ON quiz_results FOR SELECT USING (true);
CREATE POLICY "Public write results" ON quiz_results FOR INSERT WITH CHECK (true);
```

### 3. Create the image storage bucket (for image upload)

1. Go to Supabase → Storage
2. Create a bucket named `quiz-images`
3. Set it to **Public**

### 4. Configure the site

Open the teacher or student app, fill in the Supabase URL and anon key in the config section, and save.

---

## 🛠 Technical Notes

- **Stack**: Pure HTML + CSS + JavaScript (no framework, single-file apps)
- **Cloud storage**: Supabase (PostgreSQL + Storage)
- **Local storage**: localStorage (config, identity, mistake book)
- **Hosting**: GitHub Pages
- **No installation**: works in any browser

## 📁 Project Structure

```
quiz-system/
├── teacher/
│   └── index.html    # Teacher app
├── student/
│   └── index.html    # Student app
└── README.md
```

## License

MIT License
