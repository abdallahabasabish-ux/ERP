# دليل رفع المشروع إلى GitHub والنشر التلقائي

هذا الدليل يشرح خطوة بخطوة كيف ترفع نظام ERP إلى GitHub، وكيف يصبح GitHub
مصدر النشر التلقائي إلى Render (الخادم) و Vercel (لوحة التحكم).

> **ملاحظة:** المستودع محضّر مسبقاً — يوجد `git init` و commit أولي،
> وملف `.gitignore` يمنع رفع `.env` و`service-account.json` والأسرار.

---

## المتطلبات

| المتطلب | الرابط | ملاحظات |
|---|---|---|
| حساب GitHub | https://github.com/signup | مجاني |
| Git | https://git-scm.com/downloads | اختر نسختك (Windows/Mac/Linux) |

تحقّق من التثبيت:

```bash
git --version      # يجب أن يظهر إصدار 2.x
```

---

## الخطوة 1 — أنشئ مستودعاً جديداً على GitHub

1. سجّل الدخول إلى GitHub واضغط **+** أعلى اليمين ← **New repository**.
2. املأ البيانات:
   - **Repository name:** `erp-accounting-system` (أو أي اسم تريده).
   - **Visibility:** `Private` (موصى به لنظام محاسبي — لا تختر Public إلا إذا علمت ما تفعل).
   - **لا تختر** "Add a README" ولا "Add .gitignore" ولا "Choose a license"
     (لأن المشروع يحتويها بالفعل — اختيارها يسبب تعارضاً عند الرفع الأول).
3. اضغط **Create repository**.
4. انسخ رابط المستودع الذي سيظهر، إحدى الصيغتين:
   - HTTPS: `https://github.com/USERNAME/erp-accounting-system.git`
   - SSH: `git@github.com:USERNAME/erp-accounting-system.git`

---

## الخطوة 2 — عرّف نفسك في Git (مرة واحدة فقط على جهازك)

```bash
git config --global user.name  "اسمك الحقيقي"
git config --global user.email "بريدك@المرتبط-بـGitHub.com"
```

> استخدم نفس البريد المسجّل في حساب GitHub حتى تُنسب Commits إلى حسابك
> وتظهر صورتك بجانبها. (الـ commit الأولي المُحضّر داخل المشروع يحمل
> هوية محايدة — يمكنك تجاهلها أو دمجها بهويتك بأمر واحد، انظر الملحق ج).

---

## الخطوة 3 — اربط المشروع بالمستودع وارفعه

افتح الطرفية (Terminal / PowerShell / Git Bash) داخل مجلد `erp-system` ونفّذ:

```bash
cd erp-system

# اربط بمستودعك (استبدل USERNAME باسم مستخدمك)
git remote add origin https://github.com/USERNAME/erp-accounting-system.git

# ارفع الفرع main
git push -u origin main
```

سيطلب منك تسجيل الدخول — **GitHub لم يقبل كلمة المرور العادية منذ 2021**،
استخدم أحد الحلّين في الخطوة 4.

بعد نجاح الأمر: حدّث صفحة GitHub وسترى كل الكود في المستودع. ✅

---

## الخطوة 4 — المصادقة على GitHub (اختر طريقة واحدة)

### الطريقة أ — Personal Access Token (الأسهل مع HTTPS)

1. GitHub ← صورة حسابك ← **Settings** ← **Developer settings**
   (آخر عنصر في القائمة الجانبية) ← **Personal access tokens** ← **Tokens (classic)**.
2. **Generate new token (classic)**:
   - **Note:** `erp-push`
   - **Expiration:** 90 يوماً (أو No expiration).
   - **Scopes:** علّم ✅ `repo` فقط.
3. اضغط **Generate token** وانسخ الرمز فوراً (يظهر مرة واحدة).
4. عند طلب GitHub للدخول أثناء `git push`:
   - **Username:** اسم مستخدمك
   - **Password:** الصق **الرمز** (وليس كلمة المرور)

   وسيتذكره Git للمرات القادمة (على ويندوز عبر Git Credential Manager تلقائياً).

### الطريقة ب — مفتاح SSH (إعداد واحد يعمل للأبد)

```bash
# 1) أنشئ المفتاح (اضغط Enter لكل الأسئلة)
ssh-keygen -t ed25519 -C "بريدك@example.com"

# 2) انسخ المفتاح العام
cat ~/.ssh/id_ed25519.pub        # لينكس/ماك
type %USERPROFILE%\.ssh\id_ed25519.pub   # ويندوز (CMD)
```

3. GitHub ← **Settings** ← **SSH and GPG keys** ← **New SSH key** ← الصق المفتاح.
4. اختبر الاتصال ثم غيّر رابط المستودع إلى صيغة SSH:

```bash
ssh -T git@github.com            # يجب أن يظهر: Hi USERNAME!
git remote set-url origin git@github.com:USERNAME/erp-accounting-system.git
```

### الطريقة ج — بدون أوامر إطلاقاً (GitHub Desktop / VS Code)

- **GitHub Desktop** (https://desktop.github.com):
  File ← Add local repository ← اختر مجلد `erp-system` ←
  Repository ← Push (سيطلب تسجيل دخول المتصفح تلقائياً).
- **VS Code:** افتح المجلد ← أيقونة Source Control ← **Publish to GitHub** ←
  اختر Private ← يتم كل شيء من الواجهة.

---

## ما لن يُرفع إلى GitHub (محمي بواسطة .gitignore)

| الملف | السبب |
|---|---|
| `.env` | يحتوي أسراراً (`DEMO_SECRET`، بيانات Firebase) |
| `service-account.json` | مفتاح Firebase الكامل = صلاحيات قاعدة البيانات |
| `node_modules/` | تُعاد بناؤها من `package.json` |
| `.next/` | مخرجات بناء تُعاد في منصة النشر |

⚠️ **تحذير مهم:** لا ترفع `service-account.json` إلى GitHub أبداً حتى لو كان
المستودع Private — أي تسريب له يعني وصولاً كاملاً لقاعدة بياناتك.
على Render/Vercel تُدخل هذه القيم كمتغيرات بيئة (لا كملفات).

---

## الخطوة 5 — النشر التلقائي من GitHub (الهدف الأكبر)

بعد رفع الكود، أي `git push` جديد سيُعيد النشر تلقائياً:

### الخادم (Express) على Render

1. https://dashboard.render.com ← **New** ← **Blueprint**.
2. اربط حساب GitHub واختر مستودع `erp-accounting-system`.
3. Render سيقرأ `server/render.yaml` تلقائياً (rootDir=server، Node 20، أمر البناء والتشغيل).
4. في صفحة الخدمة ← **Environment** أضف المتغيرين المطلوبين:
   - `FIREBASE_PROJECT_ID` = معرف مشروعك
   - `FIREBASE_SERVICE_ACCOUNT_JSON` = محتوى `service-account.json` كاملاً في سطر واحد
5. **Apply** — سينشر الخادم على رابط مثل `https://erp-accounting-api.onrender.com`
   (تحقّق من `/api/health`).

### لوحة التحكم (Next.js) على Vercel

1. https://vercel.com ← **Add New** ← **Project** ← اختر نفس المستودع.
2. **Root Directory:** `admin-dashboard`.
3. متغيرات البيئة:
   - `ERP_API_URL` = رابط خادم Render من الخطوة السابقة.
4. **Deploy** — ستحصل على رابط مثل `https://erp-dashboard.vercel.app`.

> ملاحظة: الخطوة 5 اختيارية الآن — يمكنك الاكتفاء برفع الكود إلى GitHub
> اليوم، والنشر لاحقاً وقت جاهزية بيانات Firestore الحقيقية.

---

## الاستخدام اليومي (بعد أول رفع)

```bash
git add .                       # جهّز كل التغييرات
git commit -m "وصف التعديل"     # ثبّتها بتاريخ ووصف
git push                        # ارفعها (ويعيد النشر تلقائياً)
```

لجلب آخر التعديلات على جهاز آخر أو لزميل:

```bash
git clone https://github.com/USERNAME/erp-accounting-system.git
git pull
```

---

## ملحق أ — مشاكل شائعة وحلولها

| الرسالة | السبب | الحل |
|---|---|---|
| `Authentication failed` | استخدمت كلمة المرور بدل الرمز | أنشئ PAT (الخطوة 4-أ) |
| `remote origin already exists` | أضفت remote قبل ذلك | `git remote set-url origin <الرابط>` |
| `Updates were rejected ... fetch first` | يوجد commit على GitHub غير موجود عندك | `git pull --rebase origin main` ثم `git push` |
| `refusing to merge unrelated histories` | أنشأت README على GitHub عند الإنشاء | `git pull origin main --allow-unrelated-histories` ثم ارفع |
| `fatal: not a git repository` | أنت خارج مجلد المشروع | `cd erp-system` أولاً |
| `push declined - large files` | ملف أكبر من 100MB | تأكد أنه ليس `service-account.json`؛ الأكواد هنا كلها صغيرة |

## ملحق ب — نصح أمنية للمستودع

- اجعل المستودع **Private** — الكود يحتوي منطق أعمال مالية.
- فعّل **Branch protection** لفرع `main` إذا عمل فريق: Settings ← Branches.
- راجع تبويب **Security** ← Dependabot لتحديث الثغرات في التبعيات.
- إذا رفعت سراً بالخطأ: ألغِ فوراً المفتاح في Firebase (وليس حذف commit فقط)،
  ثم أنشئ مفتاحاً جديداً وحدّث متغيرات البيئة على منصة النشر.

## ملحق ج — تطويعات اختيارية

```bash
# نسبة الـ commit الأولي إلى هويتك:
git commit --amend --reset-author --no-edit

# إعادة تسمية الفرع إن لزم:
git branch -M main

# إضافة وصف قصير يظهر أعلى صفحة المستودع:
# على GitHub ← About (أيقونة الترس) ← Description ← "نظام محاسبي مؤسسي مزدوج القيد"
```
