# משימה: אריזת הסימולטור לאפליקציית אנדרואיד (Google Play)

מסמך זה מיועד ל־Claude Code שרץ על המחשב של אליה, עם גישה מלאה לרשת.
הסביבה שבה נכתב האתר חסומה להורדת Android SDK, ולכן שלב האריזה מועבר לכאן.

## מה יש כבר
- האתר: https://eliashtai-stack.github.io/molecules/ (ענף `main`, קובץ `index.html` יחיד).
- PWA תקין: `manifest.json` (שם, אייקונים 192/512, `display: standalone`, `start_url: ./`), `sw.js`.
- לאליה יש כבר חשבון מפתח ב־Google Play.

## מה צריך להפיק
1. קובץ `app-release-bundle.aab` חתום, לחבילה `io.github.eliashtai_stack.molecules`.
2. מפתח חתימה (`android.keystore`) + סיסמאות, שמורים **מחוץ למאגר** (למשל בתיקייה `~/molecules-keys/`). אסור לעשות להם commit.
3. קובץ `.well-known/assetlinks.json` במאגר, עם טביעת ה־SHA-256 של המפתח, כדי שה־TWA ייפתח בלי שורת כתובת.

## שלבים
1. ודא שמותקנים Node.js 18+ ו־Java 17 (JDK). אם אין JDK, להתקין (למשל Temurin 17).
2. התקן את bubblewrap: `npm i -g @bubblewrap/cli`.
3. בתיקייה חדשה מחוץ למאגר (למשל `~/molecules-android/`):
   `bubblewrap init --manifest https://eliashtai-stack.github.io/molecules/manifest.json`
   - בהתקנה ראשונה bubblewrap מציע להוריד JDK ו־Android SDK בעצמו; לאשר.
   - תשובות: Package ID `io.github.eliashtai_stack.molecules`; שם האפליקציה "סימולטור מולקולות"; שם קצר "מולקולות"; צבע `#0b7566`; מסך פתיחה `#eef2f1`; display `standalone`; orientation `default`; keystore חדש בנתיב `~/molecules-keys/android.keystore`, alias `molecules`. לבחור סיסמאות חזקות ולשמור אותן ב־`~/molecules-keys/passwords.txt`.
4. `bubblewrap build` → מפיק `app-release-bundle.aab` ו־`app-release-signed.apk`.
5. `bubblewrap fingerprint list` (או `keytool -list -v -keystore ~/molecules-keys/android.keystore`) → להעתיק את SHA-256.
6. במאגר `molecules`: ליצור `.well-known/assetlinks.json`:
   ```json
   [{"relation":["delegate_permission/common.handle_all_urls"],
     "target":{"namespace":"android_app","package_name":"io.github.eliashtai_stack.molecules",
               "sha256_cert_fingerprints":["<SHA-256 כאן>"]}}]
   ```
   לעשות commit ו־push ל־`main`. לוודא ש־GitHub Pages מגיש את הקובץ (לפתוח את הכתובת `/.well-known/assetlinks.json` בדפדפן).
7. להתקין את ה־APK בטלפון של אליה (`adb install` או העברת הקובץ) ולוודא שהאפליקציה נפתחת במסך מלא בלי שורת כתובת, ושחיפוש של חומר שאינו במאגר (למשל `aspirin`) עובד.
8. להנחות את אליה בהעלאה ל־Play Console: אפליקציה חדשה → שם "סימולטור מולקולות" → קטגוריה: חינוך → חינם → העלאת ה־aab למסלול שהחשבון מאפשר (production אם יש גישה, אחרת closed testing: 12 בודקים ל־14 יום).
   - **חשוב:** אם Play Console מציע Play App Signing (ברירת מחדל), Google ייצור מפתח חתימה משלו, וטביעת ה־SHA-256 שצריכה להופיע ב־assetlinks.json היא של **מפתח ה־App Signing של Google** (מופיע ב־Play Console תחת Setup → App signing), לא של המפתח המקומי. במקרה כזה להוסיף גם אותה למערך `sha256_cert_fingerprints` ולדחוף שוב.

## טקסטים לדף החנות
- תיאור קצר: "מקלידים שם של מולקולה, אטום או יון, ורואים מודל תלת־ממדי, מבנה לואיס, אורביטלים ונתונים. בעברית."
- תיאור מלא: הסימולטור מיועד לסטודנטים בקורסי מבוא לכימיה. מאגר מובנה של 104 חומרים ו־118 יסודות, וחיפוש של כל חומר אחר דרך PubChem. מודל תלת־ממדי לסיבוב והגדלה, מבנה לואיס עם זוגות לא קושרים ומטענים פורמליים, מודל בוהר וצורות אורביטלים לאטומים, גאומטריה לפי VSEPR, היברידיזציה, קוטביות, נקודות היתוך ורתיחה. האפליקציה אינה אוספת מידע.
- מדיניות פרטיות: האפליקציה אינה אוספת, שומרת או שולחת מידע אישי. חיפושים של חומרים שאינם במאגר נשלחים ל־PubChem ולוויקיפדיה ללא פרטים מזהים. (לפרסם כדף `privacy.html` במאגר ולהפנות אליו מ־Play Console.)
- צילומי מסך: לצלם מהטלפון מים, סולפט, ברזל (לשונית אורביטלים), קפאין.
