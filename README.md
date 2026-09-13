# تصحيح: إزالة نظام الدخول (GETY/WGOM) + إصلاح تلف حقيقي في المشروع

## ما وجدته في الملف المرفوع
عند تطبيق تعديلات GETY وWGOM، تم نسخ ملفات التطبيقين المنفصلين (GETY وWGOM) مباشرة
داخل نفس وحدة `app/` الخاصة بـ RinStudio بدل الإبقاء عليها كمشروعين منفصلين. بما أن
الأسماء متطابقة بين المشاريع الثلاثة (`AndroidManifest.xml`، `build.gradle`،
`activity_main.xml`، `activity_login.xml`، `colors.xml`، `strings.xml`، `themes.xml`،
أيقونات `mipmap`، `proguard-rules.pro`، `google-services.json`...)، **استبدلت ملفات
GETY/WGOM ملفات RinStudio الأصلية فعلياً**، وليس فقط ملفات نظام الدخول. تحديداً:

- `app/build.gradle` و`AndroidManifest.xml` أصبحا ملفّي GETY حرفياً (الـ`namespace`
  تحوّل إلى `com.dlof.gety`، والثيم إلى `Theme.Gety`، واختفت كل أذونات/نشاطات RinStudio).
- `colors.xml` (268 سطراً من نظام ألوان RinStudio الكامل بما فيه صيغة تلوين الكود،
  الطرفية، شاشة Pipeline، المعاينة الحية...) استُبدل بـ 5 ألوان خاصة بـGETY فقط.
- `themes.xml` فقد `Theme.RinLang` بالكامل (الثيم الذي يعتمد عليه التطبيق بأكمله).
- `strings.xml` الافتراضي (317 سطراً) استُبدل بـ19 سطراً من نصوص GETY فقط، حتى اسم
  التطبيق نفسه أصبح "Gety" بدل RinLang.
- `activity_main.xml` (شاشة IDE الرئيسية) استُبدلت بشاشة مسح QR الخاصة بـGETY.
- `activity_login.xml` استُبدلت بتصميم شاشة تسجيل الدخول الخاصة بـWGOM.
- ظهرت ملفات لا تخص RinStudio إطلاقاً داخل نفس الوحدة: `activity_account.xml`
  و`strings_wgom.xml` و`bg_button_outline.xml` (من WGOM)، وأيقونات GETY/WGOM حلّت محل
  أيقونة RinStudio في كل مجلدات `mipmap-*`.
- `proguard-rules.pro` و`google-services.json` استُبدلا أيضاً (فقدان قواعد JNI
  الخاصة بـ`RinEngine`، ودمج تسجيل تطبيق GETY داخل ملف Firebase الخاص بـRinStudio).

هذا التلف **أوسع من مجرد نظام الدخول** — كان سيمنع المشروع من البناء أصلاً (مراجع ألوان
غير موجودة تُستخدم في عشرات الملفات الأخرى غير المرتبطة بالدخول إطلاقاً: تلوين الصيغة
النحوية، الطرفية، Pipeline Runner، المعاينة الحية...).

## ما تم إصلاحه في هذا الأرشيف
1. **إزالة نظام الدخول بالكامل** كما طُلب: حذف `PairingRepository.kt` و
   `DevicePairingActivity.kt` و`activity_device_pairing.xml` و`bg_pairing_qr_frame.xml`،
   وإعادة `LoginActivity.kt`/`VerifyCodeActivity.kt` لسلوكهما الأصلي (إنهاء الشاشة مباشرة
   بعد نجاح الدخول/التحقق، بلا فتح شاشة إقران)، وحذف نشاط `DevicePairingActivity` من
   `AndroidManifest.xml`، وحذف عقدة `pairing_codes` من `firebase/database.rules.json`.
2. **استعادة كل الملفات التي استبدلتها GETY/WGOM خطأً** إلى نسخة RinStudio الأصلية:
   `build.gradle`، `AndroidManifest.xml` (نفس النسخة الأصلية + بلا إضافة الإقران)،
   `proguard-rules.pro`، `google-services.json`، `colors.xml`، `themes.xml`،
   `activity_main.xml`، `activity_login.xml`، `bg_input_field.xml`، وأيقونات `mipmap-*`
   بكل الكثافات.
3. **حذف الملفات التي لا تخص RinStudio إطلاقاً**: `activity_account.xml`،
   `strings_wgom.xml`، `bg_button_outline.xml` (هذه تخص تطبيق WGOM المنفصل فقط).
4. **الإبقاء على `strings.xml` الافتراضي مع دمج تصحيح حقيقي كان ناقصاً**: أعدت المحتوى
   الأصلي الكامل (317 سطراً)، وأضفت إليه 3 نصوص "طيّ الكود" (`menu_view_fold_current`
   وأخواتها) التي كانت موجودة فقط في `values-ar/en/es` بلا نسخة افتراضية — إضافة حقيقية
   ومفيدة من عملكم على ميزة "code folding"، أبقيتها كما هي.
5. **الإبقاء على تصحيح حقيقي آخر وجدته في `strings_auth.xml` الافتراضي**: نصوص شاشة
   "انقطاع الاتصال" (`no_connection_title`/`no_connection_message`/`action_retry`) كانت
   مفقودة من النسخة الافتراضية رغم وجودها في `ar/en/es` — أبقيت هذه الإضافة وحذفت فقط
   نصوص `pairing_*` منها.
6. **لم أمسّ** أي من الملفات التالية لأنها تعديلات مستقلة واضحة لا علاقة لها بنظام
   الدخول أو بالتلف: `MainActivity.kt`، `RinCodeEditorController.kt`،
   `RinCodeEditorView.kt`، `RinContainerTags.kt`، وملفات `docs/` و`README.md` وworkflows
   الـGitHub. أي تعديلات أخرى قمتم بها يدوياً على `colors.xml`/`themes.xml`/
   `AndroidManifest.xml`/`build.gradle`/`google-services.json`/`proguard-rules.pro`
   بين النسخة الأصلية وهذا التلف — لن أستطيع اكتشافها لأن هذه الملفات استُبدلت بالكامل؛
   ستحتاجون لإعادة تطبيقها يدوياً إن كانت موجودة.

## محتوى هذا الأرشيف
نسخة **كاملة** من مجلدَي `app/` و`firebase/` بعد التصحيح (وليس فقط الفروقات)، لأن حجم
التلف كان يطال ملفات أساسية يصعب تطبيقها كـ"باتش" جزئي بأمان. انسخهما فوق نفس المسارين
في مستودعكم بالكامل (استبدال تام)، ثم راجعوا Git diff قبل الدفع للتأكد من عدم فقدان أي
تعديل يدوي لم أكن أعرف عنه في هذه الملفات تحديداً.

## تذكير مهم
- إن كنتم قد نشرتم قواعد `pairing_codes` سابقاً على Firebase Console، احذفوها يدوياً
  من هناك أيضاً (أو انشروا `firebase/database.rules.json` الجديد كاملاً فينظّفها).
- تطبيقا GETY وWGOM المنفصلان (اللذان أرسلتهما في الرسالة السابقة) لم يُمسّا؛ ما زالا
  صالحين إن قررتم استخدامهما لاحقاً كتطبيقين مستقلين فعلاً بدل دمجهما داخل RinStudio.
