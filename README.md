# 📝 Quiz System — 在线答题系统

一个基于 Supabase 的师生互动答题系统。教师上传题目，学生输入代码即可做题。

## 🌐 在线使用

- **教师端**: [thomaszhou22.github.io/quiz-system/teacher](https://thomaszhou22.github.io/quiz-system/teacher/)
- **学生端**: [thomaszhou22.github.io/quiz-system/student](https://thomaszhou22.github.io/quiz-system/student/)

---

## ✨ 功能

### 👩‍🏫 教师端

| 功能 | 说明 |
|------|------|
| 创建题库 | 输入名称，自动生成 6 位分享代码 |
| 章节管理 | 可为题库添加章节分类 |
| 📷 图片上传 | 上传包含题目的图片，逐个标注正确答案（A/B/C/D） |
| 📝 文字输入 | 按格式批量粘贴题目，自动解析 |
| 📊 答题统计 | 查看学生提交记录、平均正确率、每题正确率排行、学生列表 |
| ✏️ 题目编辑 | 查看、编辑、删除单道题目 |
| 多题型支持 | 选择题、图片选择题、判断题、填空题 |
| 分享代码 | 每个题库有唯一 6 位代码，分享给学生 |

**文字输入格式：**

选择题：
```
1. 题目内容
A) 选项A
B) 选项B
C) 选项C
D) 选项D
Answer: B
```

判断题：
```
1. 题目内容
Answer: T
```

填空题：
```
1. 题目内容
Answer: 正确答案
```

### 🎓 学生端

| 功能 | 说明 |
|------|------|
| 👤 学生身份 | 首次使用填写昵称和班级，自动保存 |
| 输入代码 | 输入教师的 6 位题库代码加载题目 |
| 章节选择 | 选择要做哪些章节，设置题目数量 |
| 多题型答题 | 支持选择题、判断题、填空题、图片题 |
| 选项随机 | 选择题选项自动打乱顺序 |
| 即时判分 | 选完立即显示对错，高亮正确答案 |
| 自动跳题 | 1.5 秒后自动进入下一题 |
| 进度条 | 实时显示正确/错误/剩余题数 |
| 图片放大 | 点击题目图片可全屏查看 |
| 错题本 | 自动收录错题，可随时查看 |
| 🔄 重练错题 | 从错题本中取题重新练习 |
| 答题完成 | 显示总题数、正确率统计，自动提交到教师端 |

---

## ⚙️ 部署指南

### 1. 创建 Supabase 项目

1. 前往 [supabase.com](https://supabase.com) 创建项目
2. 记录项目的 **URL** 和 **anon key**

### 2. 创建数据库表

在 Supabase SQL Editor 中执行：

```sql
-- 题库表
CREATE TABLE quiz_banks (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    name TEXT NOT NULL,
    code TEXT UNIQUE NOT NULL,
    chapters JSONB DEFAULT '[]',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 题目表
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

-- 答题结果表
CREATE TABLE quiz_results (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    bank_id UUID REFERENCES quiz_banks(id) ON DELETE CASCADE,
    student_name TEXT NOT NULL DEFAULT '匿名',
    class_name TEXT DEFAULT '',
    total_questions INT NOT NULL,
    correct_count INT NOT NULL,
    wrong_count INT NOT NULL,
    accuracy INT NOT NULL,
    answers JSONB DEFAULT '[]',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 开启 RLS
ALTER TABLE quiz_banks ENABLE ROW LEVEL SECURITY;
ALTER TABLE quiz_questions ENABLE ROW LEVEL SECURITY;
ALTER TABLE quiz_results ENABLE ROW LEVEL SECURITY;

-- 允许匿名读写（开发用，生产环境请根据需求调整）
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

### 3. 创建图片存储桶（图片上传需要）

1. 进入 Supabase → Storage
2. 创建名为 `quiz-images` 的 bucket
3. 设为 **Public**

### 4. 配置网站

打开教师端或学生端，在 Supabase 配置区域填入 URL 和 anon key，点击保存即可。

---

## 🛠 技术信息

- **技术栈**: 纯 HTML + CSS + JavaScript（无框架，单文件应用）
- **云端存储**: Supabase（PostgreSQL + Storage）
- **本地存储**: localStorage（配置、身份信息、错题本）
- **部署**: GitHub Pages
- **无需安装**: 浏览器打开即用

## 📁 项目结构

```
quiz-system/
├── teacher/
│   └── index.html    # 教师端
├── student/
│   └── index.html    # 学生端
└── README.md
```

## 许可

MIT License
