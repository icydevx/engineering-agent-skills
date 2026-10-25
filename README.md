
# Engineering Agent Skills

**A two-skill workflow for evidence-based codebase assessment and safer AI-assisted software changes.**

[English](#english) · [فارسی](#فارسی)

---

## English

### Overview

Engineering Agent Skills is a reusable collection of instructions for AI coding agents. It separates two responsibilities that are often mixed together:

1. Establish a reliable understanding of a repository and its engineering constraints.
2. Apply those constraints consistently during everyday software development.

The repository contains two complementary skills:

- [`project-foundation`](skills/project-foundation/SKILL.md) — repository discovery, evidence-based assessment, an approval gate before cleanup, and creation or refresh of project-specific engineering rules.
- [`engineering-guard`](skills/engineering-guard/SKILL.md) — a continuous workflow for scoped implementation, risk assessment, verification, conflict handling, and honest reporting.

These skills provide instructions to an AI agent. They are not a standalone application, a technical security boundary, or a guarantee that an agent will follow every instruction.

### The Skills

#### 1. Project Foundation

Use this skill when starting in an unfamiliar repository, inheriting an existing codebase, planning a substantial refactor, or reviewing missing, outdated, or conflicting engineering rules.

Its workflow is designed to:

- Record the repository's initial state and identify existing user changes.
- Build a coverage ledger showing which areas were examined and which remain unexamined.
- Trace real execution paths and compare documentation with code.
- Assess architecture, maintainability, reliability, security, testing, and other quality dimensions using evidence.
- Separate confirmed findings from risks, assumptions, and open questions.
- Produce a prioritized report before making changes.
- Default to audit-only mode when permission to edit is unclear.
- Permit only explicitly authorized, low-risk cleanup within a defined scope.
- Require specific approval for high-risk changes.
- Create or refresh a project-specific `ENGINEERING_RULES.md` through the appropriate review and approval process.

The goal is not to rewrite a codebase for the sake of style. It is to understand the project, preserve what works, and make proposed changes reviewable.

#### 2. Engineering Guard

Use this skill for coding tasks such as implementing features, fixing bugs, refactoring, writing tests, reviewing changes, or modifying APIs, schemas, dependencies, configuration, or architecture.

Its workflow is designed to:

- Read relevant project rules and inspect the actual code path.
- Check Git status and establish a relevant test baseline.
- Classify the task by risk and prepare a proportionate plan.
- Keep changes within the authorized scope.
- Preserve uncommitted and untracked user work.
- Protect secrets and avoid unapproved destructive operations.
- Request explicit approval for high-risk or contract-changing work.
- Run relevant tests and quality checks, inspect actual output and exit codes, and report checks that were not run or could not be verified.
- Review the final changes independently.
- Report changed files, verification results, conflicts, remaining risks, and follow-up items.

The goal is disciplined implementation without unnecessary abstraction, unrelated cleanup, or unsupported claims that a change is complete.

### How the Skills Work Together

```text
New or unfamiliar repository
          |
          v
   project-foundation
          |
          +--> Repository assessment and coverage report
          |
          +--> Proposed changes and approval gate
          |
          +--> Project-specific ENGINEERING_RULES.md
                              |
                              v
                    engineering-guard
                              |
                              +--> Plan and scoped implementation
                              +--> Tests and verification
                              +--> Independent review and report
```

`project-foundation` is intended for deep, occasional repository work. `engineering-guard` is intended for ongoing development. The second skill can use the project-specific rules established by the first.

### Repository Structure

```text
engineering-agent-skills/
├── README.md
├── LICENSE
├── .gitignore
└── skills/
    ├── project-foundation/
    │   └── SKILL.md
    └── engineering-guard/
        └── SKILL.md
```

### Installation and Usage

These are Agent Skills-style Markdown instructions, not executable packages. Installation paths and skill-discovery behavior depend on the coding agent you use.

1. Open the skill you want to use:
   - [`project-foundation/SKILL.md`](skills/project-foundation/SKILL.md)
   - [`engineering-guard/SKILL.md`](skills/engineering-guard/SKILL.md)
2. Follow the installation instructions for your specific agent, if it supports the Agent Skills format.
3. If your tool does not automatically discover skills from this repository, place or copy the skill folder into the tool's documented skills directory.
4. Confirm that the tool can discover and load the skill before relying on it.
5. If your project uses `ENGINEERING_RULES.md`, review the generated rules and confirm that they reflect the project's actual architecture and approved decisions.

Do not assume that every coding agent supports the same frontmatter fields, discovery paths, activation behavior, or tools. Consult your agent's documentation and test installation in a non-critical repository first.

### Safety Model and Limitations

These skills encourage safer workflows, but they cannot independently enforce safety outside the capabilities and permissions of the AI agent and its execution environment.

- **Agent compliance is not guaranteed.** An agent may ignore, misinterpret, or incompletely follow instructions.
- **Approval requests are not technical access controls.** Restrict destructive permissions and credentials at the environment level as well.
- **Verification must be real.** A test should only be reported as passed when it was actually run and its result inspected. A delegated or summarized result is not independent verification.
- **Repository context matters.** Rules and recommendations must be checked against the actual code, configuration, tests, and deployment environment.
- **Compatibility must be tested.** A valid `SKILL.md` does not guarantee that a particular coding agent will discover or activate it.
- **No blanket security claim is made.** These skills are not a substitute for code review, automated security tools, backups, or sound access controls.

### Compatibility Status

Compatibility should be documented only after testing. A tool that has not been tested should be listed as **not yet verified**, not as supported.

When reporting compatibility, record:

- Tool and version
- Installation path
- Test scenario
- Expected behavior
- Observed result
- Known limitations

Contributions that add reproducible compatibility tests are welcome.

### Contributing

Contributions, issue reports, and suggestions are welcome.

When proposing changes:

- Explain the problem the change solves.
- Keep each change focused and reviewable.
- Avoid weakening safety rules without a concrete rationale and discussion.
- Include reproducible examples or test scenarios when possible.
- Do not claim compatibility or improved results without evidence.

Before opening an issue, check whether a similar issue already exists.

### License

This project is distributed under the MIT License. See [`LICENSE`](LICENSE) for the full license text.

---

## فارسی

### معرفی

**Engineering Agent Skills** مجموعه‌ای از دستورالعمل‌های قابل استفادهٔ مجدد برای Agentهای برنامه‌نویسی هوش مصنوعی است. هدف پروژه جداکردن دو مسئولیت است که معمولاً با یکدیگر ترکیب می‌شوند:

۱. شناخت قابل اتکا از مخزن کد، ساختار و محدودیت‌های مهندسی آن.

۲. رعایت مستمر همان محدودیت‌ها در جریان توسعهٔ روزمرهٔ نرم‌افزار.

این مخزن دو Skill مکمل دارد:

- [`project-foundation`](skills/project-foundation/SKILL.md) — شناخت مخزن، ارزیابی مبتنی بر شواهد، دریافت تأیید پیش از پاک‌سازی و ایجاد یا به‌روزرسانی قواعد مهندسی مخصوص پروژه.
- [`engineering-guard`](skills/engineering-guard/SKILL.md) — فرایندی مستمر برای پیاده‌سازی محدود به محدودهٔ مجاز، ارزیابی ریسک، راستی‌آزمایی، مدیریت تعارض‌ها و گزارش صادقانه.

این Skillها دستورالعمل‌هایی برای Agent هوش مصنوعی هستند؛ برنامهٔ مستقل، مرز امنیتی فنی یا تضمینی برای پیروی کامل Agent از دستورها نیستند.

### معرفی Skillها

#### ۱. Project Foundation

زمانی از این Skill استفاده کنید که با مخزنی ناآشنا روبه‌رو هستید، نگهداری یک کدبیس موجود را تحویل می‌گیرید، قصد بازآرایی بزرگ دارید یا قواعد مهندسی پروژه ناقص، قدیمی یا متناقض‌اند.

فرایند آن برای این کارها طراحی شده است:

- ثبت وضعیت اولیهٔ مخزن و شناسایی تغییرات موجود کاربر.
- تهیهٔ جدول پوشش برای مشخص‌کردن بخش‌های بررسی‌شده و بررسی‌نشده.
- دنبال‌کردن مسیر واقعی اجرای برنامه و مقایسهٔ مستندات با کد.
- ارزیابی معماری، نگهداشت‌پذیری، پایداری، امنیت، آزمایش‌ها و سایر جنبه‌های کیفیت بر اساس شواهد.
- تفکیک ایرادهای تأییدشده از ریسک‌ها، فرض‌ها و پرسش‌های باز.
- تهیهٔ گزارش اولویت‌بندی‌شده پیش از هرگونه تغییر.
- انتخاب حالت فقط‌بررسی در صورت نامشخص‌بودن اجازهٔ ویرایش.
- انجام پاک‌سازی کم‌ریسک فقط در محدوده‌ای که صریحاً تأیید شده است.
- درخواست تأیید مشخص برای تغییرات پرریسک.
- ایجاد یا به‌روزرسانی `ENGINEERING_RULES.md` مخصوص پروژه از طریق فرایند مناسب بررسی و تأیید.

هدف، بازنویسی کدبیس صرفاً به‌خاطر ظاهر یا سلیقه نیست؛ هدف شناخت پروژه، حفظ بخش‌های سالم و قابل بررسی‌کردن تغییرات پیشنهادی است.

#### ۲. Engineering Guard

برای کارهایی مانند پیاده‌سازی قابلیت، رفع باگ، بازآرایی، نوشتن تست، بازبینی تغییرات یا تغییر API، ساختار پایگاه داده، وابستگی‌ها، تنظیمات و معماری از این Skill استفاده کنید.

فرایند آن برای این کارها طراحی شده است:

- خواندن قواعد مرتبط پروژه و بررسی مسیر واقعی کد.
- بررسی وضعیت Git و ثبت وضعیت اولیهٔ آزمایش‌های مرتبط.
- تعیین سطح ریسک و تهیهٔ برنامه‌ای متناسب با آن.
- محدودکردن تغییرات به محدودهٔ مجاز.
- حفظ تغییرات ذخیره‌نشده و فایل‌های جدید کاربر.
- محافظت از اطلاعات محرمانه و اجتناب از عملیات مخرب تأییدنشده.
- درخواست تأیید صریح برای کارهای پرریسک یا تغییر قراردادهای موجود.
- اجرای تست‌ها و بررسی‌های کیفیت، مشاهدهٔ خروجی و کد خروج واقعی و گزارش بررسی‌هایی که اجرا یا تأیید نشده‌اند.
- بازبینی مستقل تغییرات نهایی.
- گزارش فایل‌های تغییرکرده، نتایج بررسی، تعارض‌ها، ریسک‌های باقی‌مانده و کارهای پیشنهادی بعدی.

هدف، پیاده‌سازی منظم و قابل بررسی است؛ بدون پیچیدگی غیرضروری، پاک‌سازی نامرتبط یا ادعای تأییدنشده دربارهٔ کامل‌شدن کار.

### ارتباط دو Skill با یکدیگر

```text
پروژهٔ جدید یا ناآشنا
          |
          v
   project-foundation
          |
          +--> ارزیابی پروژه و گزارش پوشش
          |
          +--> تغییرات پیشنهادی و مرحلهٔ تأیید
          |
          +--> ENGINEERING_RULES.md مخصوص پروژه
                              |
                              v
                    engineering-guard
                              |
                              +--> برنامه‌ریزی و پیاده‌سازی محدود
                              +--> تست و راستی‌آزمایی
                              +--> بازبینی مستقل و گزارش نهایی
```

`project-foundation` برای بررسی عمیق و گهگاهی مخزن طراحی شده است. `engineering-guard` برای توسعهٔ مستمر به کار می‌رود و می‌تواند قواعد مهندسی ایجادشده توسط Skill اول را رعایت کند.

### ساختار مخزن

```text
engineering-agent-skills/
├── README.md
├── LICENSE
├── .gitignore
└── skills/
    ├── project-foundation/
    │   └── SKILL.md
    └── engineering-guard/
        └── SKILL.md
```

### نصب و استفاده

این فایل‌ها دستورالعمل‌های Markdown در سبک Agent Skills هستند، نه بسته‌های اجرایی مستقل. مسیر نصب و روش کشف Skill به ابزار برنامه‌نویسی مورد استفاده بستگی دارد.

۱. فایل Skill موردنظر را باز کنید:

- [`project-foundation/SKILL.md`](skills/project-foundation/SKILL.md)
- [`engineering-guard/SKILL.md`](skills/engineering-guard/SKILL.md)

۲. اگر ابزار شما از قالب Agent Skills پشتیبانی می‌کند، طبق مستندات همان ابزار آن را نصب کنید.

۳. اگر ابزار به‌طور خودکار Skillهای این مخزن را پیدا نمی‌کند، پوشهٔ Skill را در مسیر رسمی و مستندشدهٔ ابزار قرار دهید.

۴. پیش از اتکا به Skill، مطمئن شوید ابزار آن را پیدا و بارگذاری می‌کند.

۵. اگر پروژه از `ENGINEERING_RULES.md` استفاده می‌کند، قواعد تولیدشده را بررسی کنید تا با معماری واقعی و تصمیم‌های تأییدشدهٔ پروژه مطابقت داشته باشند.

فرض نکنید همهٔ Agentها از فیلدهای فرانت‌متر، مسیرهای کشف، رفتار فعال‌سازی یا ابزارهای یکسانی پشتیبانی می‌کنند. مستندات ابزار خود را بررسی و نصب را ابتدا در یک پروژهٔ غیرحیاتی آزمایش کنید.

### مدل ایمنی و محدودیت‌ها

این Skillها برای تشویق فرایندهای ایمن‌تر طراحی شده‌اند، اما مستقل از قابلیت‌ها و مجوزهای Agent و محیط اجرای آن، ایمنی را از نظر فنی تضمین نمی‌کنند.

- **رعایت دستورها تضمین‌شده نیست.** Agent ممکن است دستورالعملی را نادیده بگیرد، اشتباه تفسیر کند یا ناقص اجرا کند.
- **درخواست تأیید، کنترل دسترسی فنی نیست.** مجوزهای عملیات مخرب و اطلاعات محرمانه را در سطح محیط اجرا نیز محدود کنید.
- **راستی‌آزمایی باید واقعی باشد.** تست فقط وقتی موفق گزارش شود که واقعاً اجرا شده و نتیجه‌اش بررسی شده باشد. گزارش Agent دیگر یا خلاصهٔ آن، راستی‌آزمایی مستقل محسوب نمی‌شود.
- **زمینهٔ پروژه اهمیت دارد.** قواعد و پیشنهادها باید با کد، تنظیمات، تست‌ها و محیط استقرار واقعی تطبیق داده شوند.
- **سازگاری باید آزمایش شود.** معتبر بودن `SKILL.md` تضمین نمی‌کند که یک ابزار مشخص آن را پیدا یا فعال کند.
- **ادعای امنیت مطلق مطرح نمی‌شود.** این Skillها جایگزین بازبینی کد، ابزارهای خودکار امنیتی، پشتیبان‌گیری یا کنترل دسترسی مناسب نیستند.

### وضعیت سازگاری

سازگاری ابزارها را فقط پس از آزمایش مستند کنید. ابزاری که آزمایش نشده باید «هنوز راستی‌آزمایی نشده» معرفی شود، نه «پشتیبانی‌شده».

برای گزارش سازگاری، این موارد را ثبت کنید:

- نام و نسخهٔ ابزار
- مسیر نصب
- سناریوی آزمایش
- رفتار مورد انتظار
- نتیجهٔ مشاهده‌شده
- محدودیت‌های شناخته‌شده

مشارکت‌هایی که آزمایش‌های قابل تکرار برای بررسی سازگاری اضافه کنند، ارزشمندند.

### مشارکت

گزارش ایراد، پیشنهاد و مشارکت در بهبود پروژه مورد استقبال است.

هنگام پیشنهاد تغییر:

- توضیح دهید تغییر چه مشکلی را حل می‌کند.
- تغییرات را کوچک، مشخص و قابل بررسی نگه دارید.
- قواعد ایمنی را بدون دلیل روشن و بررسی مناسب تضعیف نکنید.
- در صورت امکان، مثال یا سناریوی آزمایش قابل تکرار ارائه دهید.
- بدون شواهد ادعای سازگاری یا بهبود نتیجه نکنید.

پیش از ایجاد Issue، بررسی کنید که موضوع مشابهی قبلاً ثبت نشده باشد.

### مجوز

این پروژه تحت مجوز MIT منتشر می‌شود. متن کامل مجوز در فایل [`LICENSE`](LICENSE) قرار دارد.
