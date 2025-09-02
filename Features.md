<div dir="rtl">

# 🎮 فيتشرات مشروع الألعاب الإلكترونية

## 1) إدارة ملفات المستخدمين (Managing User Profiles)
**المقصود:**  
صفحة شخصية تعرض معلومات اللاعب أو المنظّم (اسم، صورة، إحصائيات، حساب Riot). الهدف تعريف الآخرين بمستوى اللاعب وربط بياناته الرسمية.

**وش يشمل:**  
- عرض البروفايل وتعديله  
- تبويبات: Overview | Stats | Matches | Linked Accounts  
- زر Sync لتحديث البيانات من Riot  

**الجداول:**  
- `users(id, name, email, role, bio, avatar_url, country, city)`  
- `linked_accounts(id, user_id, platform, external_id, verified)`  
- `player_stats(user_id, rank, role_main, winrate, kda, vision_score)`  
- `match_history(id, user_id, match_id, champion, kills, deaths, assists, result)`  

**الفلو:**  
1. يفتح المستخدم البروفايل.  
2. يحرر معلوماته الأساسية.  
3. يربط حساب Riot → تجلب البيانات.  
4. زر Sync يحدّث Player Stats + Match History.  

---

## 2) البحث والفلترة عن لاعبين أو فرق (Filtering & Searching Players/Teams)
**المقصود:**  
تسهيل العثور على لاعبين أو فرق مناسبة حسب الدور والرتبة والمهارات.

**وش يشمل:**  
- فورم فلترة (Role, Rank, Skills, Region)  
- نتائج ببطاقات (Cards)  
- حفظ البحث (Save Filter)  

**الجداول:**  
- `users(id, role, rank, country)`  
- `player_stats(user_id, winrate, kda)`  
- `skills(id, name)`  
- `user_skills(user_id, skill_id, level)`  

**الفلو:**  
1. يفتح المستخدم صفحة البحث.  
2. يختار الفلاتر.  
3. السيرفر يرجّع النتائج.  
4. يقدر يحفظ البحث أو يرسل دعوة.  

---

## 3) إنشاء وإدارة الفرق (Building & Managing Teams)
**المقصود:**  
تمكين اللاعبين من تكوين فرق، إرسال دعوات، وإدارة الأعضاء.

**وش يشمل:**  
- Create Team  
- إرسال دعوات (Invitations)  
- عرض الأعضاء وإدارة الأدوار  
- Owner Dashboard  

**الجداول:**  
- `teams(id, name, owner_id, game, event_id)`  
- `team_members(team_id, user_id, role_in_team)`  
- `team_invitations(id, team_id, to_user_id, status, expires_at)`  

**الفلو:**  
1. القائد ينشئ فريق.  
2. يرسل دعوات.  
3. المدعو يستقبل إشعار → يوافق/يرفض.  
4. عند الموافقة ينضاف لجدول team_members.  

---

## 4) الرسائل داخل التطبيق (Enabling In-App Messaging)
**المقصود:**  
تواصل رسمي بين اللاعبين والفرق داخل المنصة.

**وش يشمل:**  
- محادثات فردية (1-1)  
- محادثة فريق (Team Chat)  
- محادثة بطولة (Tournament Chat)  
- Read receipts + Typing indicators  

**الجداول:**  
- `conversations(id, type)`  
- `conversation_members(conversation_id, user_id)`  
- `messages(id, conversation_id, sender_id, body, created_at, read_at)`  

**الفلو:**  
1. اللاعب يفتح محادثة.  
2. يرسل رسالة → تنحفظ في DB وتبث عبر Socket.  
3. المستلم يقرؤها → يتحدّث read_at.  

---

## 5) تصنيف اللاعبين باستخدام الذكاء الاصطناعي (Classifying Players Using AI)
**المقصود:**  
تحليل أسلوب لعب كل لاعب من بيانات Riot وتصنيفه تلقائيًا.

**وش يشمل:**  
- جمع بيانات المباريات  
- استخراج مؤشرات (KDA, CS/min, Vision…)  
- تشغيل ML (Clustering/Classification)  
- تخزين التصنيف في بروفايل اللاعب  

**الجداول:**  
- `match_history(...)`  
- `player_features(user_id, feature_name, value, window)`  
- `player_ai_labels(user_id, playstyle, tags, last_scored_at)`  

**الفلو:**  
1. تسحب المباريات الأخيرة.  
2. تتولد Features وتخزّن.  
3. يشتغل الموديل ويعطي Playstyle.  
4. النتيجة تنحفظ وتظهر بالبروفايل.  

---

## 6) مركز الفرص (Opportunities Hub)
**المقصود:**  
صفحة تعرض البطولات والتحديات المفتوحة للانضمام.

**وش يشمل:**  
- بطاقات فرص مع فلاتر  
- صفحة تفاصيل الفرصة  
- زر Apply للتقديم  

**الجداول:**  
- `opportunities(id, type, title, organizer_id, game, region, start_at, end_at, requirements, status)`  
- `applications(id, opportunity_id, user_id, team_id, status)`  

**الفلو:**  
1. اللاعب يفتح مركز الفرص.  
2. يفلتر ويستعرض البطاقات.  
3. يضغط Apply → ينشأ طلب.  
4. المنظم يستقبل إشعار.  

---

## 7) إضافة وتعديل البطولات (Adding & Modifying Tournaments)
**المقصود:**  
تمكين المنظمين من إدارة البطولات داخل التطبيق.

**وش يشمل:**  
- فورم 3 خطوات: تفاصيل → قواعد → نشر  
- إدارة الحالة (Draft, Open, Closed)  
- (اختياري) إدارة Bracket  

**الجداول:**  
- `opportunities(type='TOURNAMENT')`  
- `brackets(id, tournament_id, type, rules_url)`  
- `tournament_slots(id, tournament_id, slot_no, team_id)`  

**الفلو:**  
1. المنظم ينشئ بطولة بحالة Draft.  
2. يضيف القواعد والتنسيق.  
3. يراجع وينشر.  

---

## 8) توصية البطولات المناسبة (Recommending Relevant Tournaments)
**المقصود:**  
اقتراح بطولات ملائمة لكل لاعب حسب بياناته.

**وش يشمل:**  
- نظام توصية يعرض فرص مخصصة  
- Badge "Recommended"  
- تحديث دوري للتوصيات  

**الجداول:**  
- `player_ai_labels`  
- `player_stats`  
- `opportunities`  
- `recommendation_logs(id, user_id, recommended_ids, generated_at)`  

**الفلو:**  
1. السيرفر يحسب Score لكل فرصة.  
2. يرجع قائمة مرتبة للمستخدم.  
3. تظهر في الواجهة بعلامة "Recommended".  

---

## 9) التقارير الأسبوعية للأداء (Weekly Performance Reports)
**المقصود:**  
ملخص أسبوعي يوضح أداء اللاعب ونصائح للتحسين.

**وش يشمل:**  
- أرقام الأداء (Winrate, KDA…)  
- Highlights لأفضل/أسوأ مباراة  
- نصائح تلقائية  
- تصدير PDF  

**الجداول:**  
- `weekly_reports(id, user_id, week_start, week_end, kda, winrate, highlights, suggestions, diff)`  

**الفلو:**  
1. Job أسبوعي يجمع بيانات.  
2. يحفظ تقرير جديد.  
3. اللاعب يتلقى إشعار ويستعرض التقرير.  

---

## 10) الخط الزمني للأداء (Visual Performance Timelines)
**المقصود:**  
إظهار التغيّرات في أداء اللاعب بمرور الوقت.

**وش يشمل:**  
- Line/Area charts لمقاييس متعددة  
- Range selector (30/60/90 يوم)  
- Tooltips وتنزيل الشارت  

**الجداول:**  
- `player_aggregates(user_id, period_start, metric, value)`  
- أو استخدام `weekly_reports`  

**الفلو:**  
1. اللاعب يختار المقياس والمدّة.  
2. السيرفر يرجّع سلسلة زمنية.  
3. تظهر الشارتات مع إمكانية التكبير/التصغير.  

---

## 11) الأوسمة والتحفيز (Badges & Motivation System)
**المقصود:**  
نظام يكرّم اللاعبين بوسام عند تحقيق إنجازات.

**وش يشمل:**  
- كتالوج أوسمة (Bronze/Silver/Gold)  
- منح تلقائي أسبوعي/شهري  
- عرض الأوسمة بالبروفايل  

**الجداول:**  
- `badges(id, code, title, tier, criteria, description)`  
- `user_badges(user_id, badge_id, awarded_at, reason)`  

**الفلو:**  
1. Job يفحص استحقاق الأوسمة.  
2. يمنح الجديدة ويحفظ السبب.  
3. إشعار + عرض بالبروفايل.  

---

## 12) إبراز أفضل المواهب السعودية (Top Saudi Talents)
**المقصود:**  
لوحة شرف شهرية تعرض أفضل اللاعبين السعوديين.

**وش يشمل:**  
- ترتيب شهري لأفضل اللاعبين  
- بطاقات شخصية لأعلى 10–20 لاعب  
- روابط للبروفايلات  

**الجداول:**  
- `leaderboards_monthly(month, user_id, score, rank, highlights)`  

**الفلو:**  
1. Job شهري يحسب الأداء للسعوديين.  
2. يحفظ النتائج في leaderboards.  
3. تعرض صفحة Top SA Talents الترتيب.  

</div>
