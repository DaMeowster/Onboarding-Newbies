# Kerberos & LDAP

Kerberos and LDAP are core components for authentication and identity management in distributed systems.

Together, they enable consistent, secure identity handling across large-scale systems.

---

### ⏳ Timeline
Estimated Duration: 1 Day

Authentication & Directory Concepts: 
- Kerberos Authentication Flow  
- Core Kerberos Components  
- LDAP Structure & Operations  
- Basic Integration Concepts  
- Security Fundamentals  

---

### 📚 Resources
- [Kerberos: The Network Authentication Protocol](https://web.mit.edu/kerberos/)
- [LDAP: RFC 4511 Overview](https://datatracker.ietf.org/doc/html/rfc4511)

---

# Kerberos Core Concepts

### ❓ Guide Questions

1. **What is Kerberos, what problem does it solve, and what are its core components?**  

A: 
קרברוס הוא פרוטוקול אות'נטיקציה רשתי שמאפשר אות'נטיקציה בין לקוח לשרת בעזרת הצפנות והאשים. הוא פותר את הבעיה של אות'נטיקציה מעל רשת לא מאובטחת בכך שהוא מבטיח את האבטחתיות בעצמו. קרברוס כולל את הKey Distribution Center (KDC) שמכיל את הAuthentication Server (AS) ואת הTicket Granting Service (TGS). האות'נטיקה עצמה של הלקוח לAS מתרחשת באופן לא תדיר, כשעושים log in בהתחלה או כשפג תוקפו של הTGT. כשהלקוח צריך לתקשר עם שרת שאין לו כרטיס session אקטיבי איתו, הוא מתקשר עם הTGS על מנת להשיג אחד, בעזרת הTGT.

2. **How is Kerberos configured and managed in production? Explain keytab files, service principals, ticket lifetimes, renewal policies, etc.**  
A:

יש הסבר יותר ספציפי על המונחים בשאלות סקילה, אבל בקבצי הkeytab שמורים הkeys שמפיקים מלהפעיל את כל פעולות הhashing על הסיסמה של הprincipals, וכך אפשר לנצל SSO בזה שלא צריך כל פעם להתחבר מחדש כשהTGT נהיה פג תוקף. בנוסף ללהשיג TGT חדש, יש גם את האופציה של לחדש TGT קיים ולהמשיך להשתמש בו. גם קובץ krb5.conf זה חיוני וצריך למלא בו חלק מהפרמטרים שמדובר עליהם למטה בשאלות סקילה.
3. **How does the Kerberos authentication flow work?**  


A:

ראשית המשתמש כותב את השם והסיסמה שלו. הלקוח במחשב של המשתמש שולח לAS את הID של המשתמש ביחד עם הזמן הנוכחי שמוצפן לפי הסיסמה של המשתמש שעברה hashing + salt. הAS מקבל את ההודעה ובודק אם הID קיים אצלו. אם כן, אז הוא מפענח את הזמן המוצפן לפי הסיסמה שעברה האשינג ששמורה אצלו. אם הוא מפענח והזמן תקין, כעת הוא יודע שהמשתמש זה מי שהוא טוען שהוא, אבל התקשורת עדיין תהיה מוצפנת לפי הסיסמה למקרה שהיה זיוף רגעי. הAS שולח ללקוח שני דברים:
1. הTGS session key שמוצפן לפי הסיסמה של המשתמש שעברה האשינג.
2. הTGT שמכיל את הID של המשתמש, הTGS session key, כתובת הרשת של המשתמש, הזמן הנוכחי, תוקף הTGT ועוד כמה דברים. הTGT מוצפן לפי סוד שרק הAG והTGS יודעים.
   הלקוח מקבל את כל זה, את 1 הוא יכול לפענח לפי הסיסמה של המשתמש והוא מקבל את הTGS session key. אין ללקוח דרך לפענח את ההודעה השנייה. כעת הוא שולח לTGS שני דברים:
   1. את הID של המשתמש ביחד עם הזמן הנוכחי, שניהם מוצפנים בעזרת הTGS session key
   2. הTGT המוצפן ממקודם
 הTGS מקבל את שתי ההודעות, הוא יודע את הסוד בשביל לפענח את הTGT. כעת הוא יכול בעזרת הTGT שמכיל את הsession key לפענח את ההודעה הראשונה, והוא מקבל עוד ID של המשתמש. הוא משווה ביניהם, אם הם אכן שווים, אז הוא שולח למשתמש שני דברים:
 1. את הsession key עם השרת שהמשתמש רצה לדבר איתו, מוצפן לפי הTGS session key.
 2. את הClient to server ticket (אותו דבר כמו הTGT רק מכיל את הserver session key במקום) שמוצפן לפי סוד שרק הTGS והשרת הרצוי יודעים
כמו מקודם, הלקוח יכול לפענח רק הserver session key בעזרת הTGS session key. הוא שולח לשרת הרצוי שני דברים:
1. את הID של המשתמש עם הזמן הנוכחי, שניהם מוצפנים בעזרת הserver session key
2. את הClient to server ticket המוצפן ממקודם
השרת מקבל את שניהם, מפענח את השני, בעזרת הserver session key שעכשיו השיג מפענח את הראשון, משווה את הIDים. אם הם שווים, הוא שולח ללקוח את הtimestamp מההודעה שהוא הראשונה שהוא שלח עכשיו מוצפנת בעזרת הserver session key, כדי שהוא יוכל לסמוך עליו שזה אכן השרת. מפה הלקוח והשרת מתקשרים בעזרת הserver session key באופן מוצפן.


4. **Why is Kerberos considered secured? What potential issues could arise with this mechanism?**


A:

קרברוס נחשב מאובטח כי כל שלב בתהליך האות'וריזציה בנוי על כך שהשלב הקודם התבצע באופן מאובטח, עם התחלה יחסית מאובטחת שדורשת לדעת את ההאשינג של הסיסמה. בזכות העובדה שיש האשינג עם salt זה גם מקל על מתקפות brute force קצת כי אי אפשר לקחת כלי תקיפה עם סיסמאות נפוצות לגמרי בפני עצמו, צריך לבצע שינוי בהתאם להרמה של הkerberos. עוד משהו משמעותי באבטחה בקרברוס הוא שהאימות הוא דו כיווני, בסוף תהליך האימות הלקוח מקבל אינדיקציה מהשרת עצמו שהוא יכול לסמוך עליו, תהליך אות'ינטקציה טוב צריך להיות דו צדדי. והדבר הכי חשוב, כל התקשורת בכל תהליך האימות (למעט הID בהתחלה) ולאחר מכן מוצפנת.

בסוף שום פתרון לא מושלם, וגם לקרברוס יש חסרונות. הדבר הכי חשוב כנראה הוא שיש תלות ענקית בזמן, בעיקר בתחילת ובסוף תהליך האימות. לוקחים את הזמנים בשעונים במקום לעבוד עם counter בבדיקות אימות, לכן חשוב שכל השעונים בכל הרכיבים יהיו מתואמים. בגלל שהכל בKDC, אז הוא point of failure ענקי, אם הוא נופל, הכל נופל. לכן חשוב לוודא שתמיד יהיה גיבוי. לבסוף, הקונפיגורציה של קרברוס, בעיקר מחוץ לwindows היא די קשה ומסובכת ביחס לאלגוריתמי אימות אחרים. 

### Skila Questions

1. What are KINIT, KLIST, KDESTROY?

A:

פקודת KINIT הינה פקודה שבהרצתה מחדשת או יוצרת TGT, אם לא מסמנים איזה אחד בדגלים, אז זה נבחר לפי ההגדרות ששמורות בקובץ הקונפיגורציה kdf.conf
פקודת KLIST מציגה את הtickets בcredential cache, ניתן להשתמש בדגלים כדי לראות סוגים ספציפים עם תנאים שבוחרים. 
פקודת KDESTROY מוחקת קובץ credentials cache, גם פה יש דגלים שונים שנותנים לנו למחוק בצורה חכמה ויעילה יותר, נגיד למחוק אוטומטית קבצים שמכילים רק TGTים פגי תוקף. 
2. What is the Kerberos CLI?
בWindows אפשר להריץ בCLI פקודות קרבוס שונות ושימושיות כמו אלו שהרגע דיברנו עליהם ועוד כגון KPASSWD, KSWITCH, KVNO ועוד.

3. Where do the TGTs get stored in the client's computer?

A:
הTGTS נשמרים במחשב של הלקוח בcredentials cache, יש כמה דרכים לעשות את זה אבל הדרך הכי נפוצה היא שכרטיסים נשמרים אחד אחרי השני בקובץ. 

4. What is principal kerberos?
A:
זהות יחודית של אובייקט שמשתמש בקרברוס. איחוד של שם מזהה ביחד עם שם הrealm. אפשר שיהיו לך כמה principals, בין אם זה בגלל הרשאות שונות או כי אתה עובד בכמה realms שונים.

5. What is realm kerberos?
A:
אתר או אוסף אתרים שמאוגדים ביחד שיש להם שרת Kerberos שמכיל מידע על המשתמשים והserviceים של האיגוד.
6. Why do you need keytab, how do you create it?
A:
צריך keytabs בגלל שהם שומרים לנו את הסיסמה שלנו לאחר הhashing מה שמאפשר לנו לגשת לserviceים שונים בלי שנצטרך להתחבר עם שם וסיסמה כל פעם מחדש כמו שחייבים בתחילת התהליך (SSO), גם בגלל שהכל שמור בקבצים אפשר לעשות לגישות האלו אוטומציה. ביצירה צריך לתת לו את הprincipal שרלוונטי לסיסמה, את הסיסמה, ואת אלגוריתם ההאשינג שמפעילים עליו כדי שהוא ידע להפיק את הkey הרלוונטי. 
7. Why do you need krbs.conf, how do you configure it?
A:
צריך קובץ קונפיגורציה כי יש מידע שהספרייה של קרברוס צריכה כמו מה הrealm הדיפולטיבי, הhost name של הKDC, איפה קובץ הkeytab וכו'. מגדירים אותו על ידי כתיבת הפרמטר בסוגריים מרובעות ואז נתינת הערכים, כשמדובר בכמה ערכים בפרמטר ניתן להשתמש בסוגריים מסולסלות כדי להגדיר את זה יחדיו.
8. What is active active and what is active standby
בactive active יש לנו שרתים מבוזרים שמחלקים את העבודה ביניהם ואם שרת אחד נופל אז השרתים האחרים לוקחים על עצמם את העבודה הזאת בצורה חלקה. בactive passive יש שרת שלוקח על עצמו את העבודה ורק אם הוא נופל אז שרת אחר מגיע ולוקח על עצמו את העבודה של השרת שנפל, כלומר השרת השני פועל רק בנפילה של הראשון. בKDC בגלל שגם ככה יש המון תקורה של להרים את הכל וחשוב לנו הכי הרבה שזה לא יפול כדי לפצות על הsingle point of failure, אז לדעתי יותר מתאים להשתמש בactive active. התקורה של הסיבוך הנוסף גם ככה באה אחרי שעבדנו קשה להרים ולעבוד עם KDC. 
# LDAP Core Concept

### ❓ Guide Questions

1. **How is LDAP used for authentication and authorization, and what is the difference between the two?**
A:
הפרוטקול LDAP יכול להיות משומש לauthentication בכך שמשתמש יצטרך להזדהות עם שם משתמש וסיסמה לפני שהוא יוכל לגשת למידע בצורה כלשהי. ניתן להשתמש בLDAP לauthorization בכך שניתן למשתמשים שונים דרגות שונות של הרשאות שנשמור וכל פעם שתהיה גישה של משתמש כלשהי למידע נסתכל על ההרשאות שלו ובאיזה אזור מידע הוא מנסה לגעת והאם הוא רשאי לעשות זאת. 
ההבדל הוא שבauthentication בודקים מי אני, בauthorization בודקים האם אני רשאי לבצע פעולה.
2. **What operations does LDAP support?**  

3. **What is an LDAP schema and why is it important?**  

4. **What is LDAP and how is data structured within it?**

5. **Why is LDAP important for security, and how is it used for authentication and identity management in real-world systems?**

6. **How can Kerberos be integrated with LDAP or other directory services in a real deployment?**  

---

### Skila Questions

 
- Explain how user information is stored and accessed from a central directory  
- Describe how this improves security and organization
