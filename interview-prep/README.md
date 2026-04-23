# Interview Preparation Suite

A comprehensive, role-based interview preparation library. Each subfolder targets a specific technical role with its own JD reference, question sets, and study materials.

---

## 📂 Available Roles

| Role | Format | Questions | Status |
|---|---|---|---|
| [Python Staff Engineer](./python-staff-engineer/) | `.ipynb` (runnable) | 27 notebooks | ✅ Complete |
| [Senior AWS DevOps Engineer](./aws-devops-engineer/) | `.md` (reference) | 89 questions / 10 files | ✅ Complete |
| [.NET Chief Architect](./dotnet-chief-architect/) | `.md` (reference) | 52 questions / 8 files | ✅ Complete |

---

## 📖 How This Repo is Organized

```
interview-prep/
├── README.md                       ← You are here
├── python-staff-engineer/          ← Runnable Jupyter notebooks
│   ├── README.md
│   └── 01..27-*.ipynb
├── aws-devops-engineer/            ← Markdown Q&A files
│   ├── README.md
│   ├── JD.md
│   └── 01..10-*.md
├── dotnet-chief-architect/         ← Markdown Q&A files
│   ├── README.md
│   ├── JD.md
│   └── 01..08-*.md
└── <future-role>/                  ← Add more roles here
    ├── README.md
    ├── JD.md
    └── *.md / *.ipynb
```

### File Format Guidelines

| Format | Use When |
|---|---|
| `.ipynb` | Content has **runnable code** (Python tutorials, coding exercises) |
| `.md` | Content is **Q&A, scenarios, reference** (interview questions, architecture deep-dives) |

---

## 🚀 Adding a New Role

1. Create a subfolder: `interview-prep/<role-name>/`
2. Add an anonymized `JD.md` with the job description
3. Create topic files (`.md` for Q&A, `.ipynb` for coding)
4. Add a `README.md` with the topic index
5. Update this master README table

---

*Created by Antigravity AI for akmalkhaniub - 2026*
