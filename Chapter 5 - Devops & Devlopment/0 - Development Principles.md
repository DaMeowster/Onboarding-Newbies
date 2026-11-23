# Software Development Foundations & Python Basics

Before building data pipelines or services, it is important to understand the
core principles of modern software development.

This module introduces the development practices, conventions, and tools that
are commonly used in professional engineering environments.

The goal is not only to learn *what the tools are*, but also *why they exist*
and how they help create maintainable, scalable, and collaborative systems.

---

### ⏳ Timeline
Estimated Duration: 2 Days

Day 1 – Software Development Principles 
- Development principles and clean architecture
- Development workflows and collaboration
- Testing approaches and design paradigms

Day 2 – Python internals  
- Python ecosystem and development patterns
- REST APIs and Python frameworks
- Testing, mocking, and service design

---

### 📚 Resources
Use the resources below and practice researching additional information online.

- [Clean Python - Sunil Kapil](https://edu.anarcho-copy.org/Programming%20Languages/Python/Clean%20Python.pdf)
- [SOLID Principles Overview](https://www.digitalocean.com/community/conceptual-articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)
- [Python Official Documentation](https://docs.python.org/3/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Pytest Documentation](https://docs.pytest.org/)

---

# Software Development Principles

### ❓ Guide Questions

1. What are **Clean Code principles**, and why are they important in software development?  
   Explain ideas such as readability, maintainability, and the principle of  
   **“Leave the codebase cleaner than you found it.”**

A:

עקרונות קוד נקי אלו עקרונות שעומדים בהם על מנת לשמור את הקוד קריא וניתן לעבודה, הם חשובים מאוד בפיתוח בגלל שכשאנחנו עובדים עם קוד חדש או קוד ישן שלא נגענו בו הרבה זמן, חשוב להיות מסוגל להבין אותו ולהרחיב אותו בקלות. קריאות קוד אומרת שאני מסוגל לקרוא את הקוד ולהבין מה מתבצע יחסית בפשטות, עקרון השימור אומר שקל לי לשמור על נכונות הקוד כשאני מוסיף הרחבות ובפרט קל לי לבצע את ההרחבות האלו בלי לשנות מהותית את הקוד. כשאנחנו מפתחים על קוד אנחנו תמיד רוצים להשאיר את הקוד נקי יותר ממה שהוא היה קודם, כי כל דבר לא נקי שאנחנו תופסים בקוד עלול להשאר ולהחמיר יותר עם הזמן.

2. What are the **SOLID principles**?  
   Describe each principle and explain how they help create maintainable
   object-oriented systems.

עקרון ראשון - SRP
הפרדה לוגית בין רכיבים שעושים דברים שונים. בזכות ההפרדה הזאת אני מסוגל לפתח על רכיב אחד בלי להשפיע על התנהגות של רכיב אחר ובכך לבודד את העבודה שלי תחת עקרון שימור הקוד.
עקרון שני - OCP
להיות מסוגל לבצע הרחבות ושינויים בקלות בלי צורך לשנות הרבה קוד. מתקשר מאוד למה שהרגע אמרנו, אנחנו מוסיפים קוד בלי לשנות קוד קיים ובכך שומרים על שימור הקוד הנוכחי באופן טהור.
עקרון שלישי - LSP
אם אני יורש ממחלקה אז חשוב שאני מסוגל למממש כל דבר שהמחלקה מממשת. זה מתבטא מבחינת קריאות הקוד, אני מצפה שהמחלקות היורשות באמת יהיו אובייקט מאותה מחלקה שיקיים את את המתודות שיש לה.
עקרון רביעי - ISP
חשוב שמחלקה לא תממש ממשקים שהיא לא משתמשת בהם. לכן תהיה לנו חלוקה לממשקים כך שכל מחלקה תוכל לממש רק את הממשקים שהיא צריכה. גם פה זה מבחינת הקריאות וחוסך לנו לכתוב קוד מיותר שיבלבל ויפגע בשימור הקוד.
עקרון חמישי - DIP
אנחנו לא רוצים שרכיבים בHigh level יהיו תלויים ברכיבים בlow level, אלא הקשר שלהם יהיה לפי אבטסרקציה. לכן אם יש לי רכיב שמכיל רכיב לוגי, הוא לא יכיל אותו עם הלוגיקה שלו, אלא הוא יכיל אבסטרקציה של הרכיב ויהיה רכיב אחר שמממש אותו. ככה אנחנו יכולים 'להזריק' אל תוך הרכיב הגדול מופע של הקטן. פה זה מאוד עוזר מבחינת השימור שלא נהיה תלויים ברכיבים כלשהם שאולי נרצה לשנות.

3. Explain the **KISS principle** and its importance in software design.
Why does simple and intuitive software scale well?  
   Why do overly complex systems tend to fail over time?

A:

עקרון הKISS, שמתורגם לKeep it simple stupid אומר שפתרון טוב הוא גם פתרון פשוט ככל האפשר. זה משמעותי מבחינת עיצוב תוכנה בגלל שאנחנו לא רוצים מחלקות ויחסים מסובכים בתוכנה שגם יהיו יותר קשים לקריאה וגם יהיו יותר קשים להרחבה ושימור. בטווח הארוך, זה יגרום לחוסר הבנה ויווצר סרבול. אם הכל מההתחלה פשוט אז נוח לרכב על הפשטות הזו ולשמר אותה.


4. What are the most common **paradigms / programming** (ex. Object Orianted) styles, what are the differences and when should each be used

A:

Imperative programming - 
אני מתעסק באיך אני עושה משהו מאשר מה אני עושה, ממש מבצע את הפעולות שלב שלב ונכנס לעומק ולא משאיר מקום לגמישות. 
Procedural programming - 
לארגן את הקוד שיהיה אוסף של פונקציות שיהיה ניתן להשתמש בהם. תחת Imperative programming כי זה מתרכז בפונקציות שקוראות אחת לשנייה לפי הסדר.
Object oriented programming - 
הכל עוסק סביב אובייקטים. אובייקט זה גוף עם מידע ומתודות. ניתן ליצור מופעים שלו ולרשת ממנו ולהכיל אותו וכו'. 
Functional programming - 
מתרכזים במה לעשות ולא איך, משתמשים בפונקציות כמשתנים באופן חופשי. 
Logic programming - 
מגדירים חוקים שעובדים לפיהם. שולחים שאילתא ומקבלים תשובה של המחשב לפי החוקים. 
Declarative programming - 
מתרכזים במה שאנחנו רוצים מאשר להכנס לעומק, המערכת מטפלת בשאר. לדוגמה, בSQL.

5. What is **Test Driven Development (TDD)**?  
   Explain the development cycle and how it improves code reliability.

A:
הגישה אומרת שלפני שאני כותב את כל הקוד, אני כותב מספיק קוד על מנת שאני אוכל לכתוב טסטים שייכשלו. לאחר מכן, אני אכתוב קוד עד שהטסטים יעברו, אנקה אותו, ואמשיך בלכתוב טסטים חדשים שייכשלו ואת הקוד המתאים שצריך עבורם עד הסוף. הטסטים שנכתבים מראש בצורה לוגית ברורה משפרים את הקריאות כי הם נותנים עוד פרספקטיבה להבין מה קורה. בנוסף לכך, השלב של ניקוי הקוד בסיום כל משימה משפר משמעותית את קריאות הקוד שלנו כיוון שבכל שלב אנחנו מוודאים שהתוספת החדשה שהוספנו שתעבור את הטסטים הספציפיים היא נקייה וכך בטווח הארוך זה ימשיך לנו.

---

# Development Workflows & Architecture Concepts

### ❓ Guide Questions

1. Explain the difference between a **Pull Request (PR)**, **Code Review (CR)**,
   and **Design Review (DR)**.  
   Why are these processes important in team development?

A:

הpull request זו הצעה לאחד קוד מbranch אחד לשני. לדוגמה, אם עכשיו תרמתי קודם ואני רוצה שהוא יופיע בbranch שנקרא dev, אעשה לשם pull request. הcode review זה הבדיקה של הקוד, נכונות שלו ונקיות שלו לפני שמאשרים את הבקשה ומכניסים אותו לקוד הראשי. לעומת זאת, הdesign review נועד על מנת לוודא נכונות של העיצוב עוד לפני שהפיתוח מתחיל, להבין שהעיצוב הזה אכן פותר את הבעיה שלנו כמו שצריך ובא לנו להמשיך איתו. כולם חשובים בפיתוח צוותי, הpr חשוב על מנת שאנשים יוכלו לאחד את הקוד שלהם לbranch הראשי אבל יהיה ניתן לעשות להם cr לפני כן שיוודא נכונות ורצון צוותי של הקוד הזה להיות שם, מעבר לפרספקטיבה של אדם אחד. לבסוף, לפני שמשקיעים משאבים וזמן בפיתוח, חשוב לבצע dr ולוודא שהעיצוב אכן פותר את הבעיה שלנו כמו שאנחנו רוצים.


2. Define the role of a **Pull Request (PR) / Merge Request**.
What is **squshing**? Why is it common practice to squash commits before the final merge?
Find how can you **apply specific fixes** from one branch to another without merging the entire history?
What is the process for **safely undoing** a merged PR using git revert?

A:

המטרה של merge request זה לאחד את הקוד שכתבנו עם קוד בbranch אחר על מנת שכל אחד יוכל לתרום את העבודה שלו למקום משותף בסופו של דבר שכולם עובדים עליו, אבל יהיה ניתן לעבוד לפני כן בסביבה סטרילית אישית. לבסוף, שהמעבר הזה לbranch יהיה תחת בקשה ולא אוטומטי ואנשים יוכלו לעבור עליו. בgit כשמדובר על squashing הכוונה לאחד כמה commitים ביחד. זה יכול לקרות לפני merge סופי אם ההיסטוריית commitים שלנו מבולגנת ואנחנו רוצים לסדר אותה לפני שהקוד והיא תתרם לbranch המרכזי. ככה גם ניתן לאגד commitים ולתת שם יותר אינדקטיבי לאיגוד. אפשר להפעיל שינויים ספציפיים מbranch אחד לשני בעזרת cherry pick. הפעולה היא reverting a pull request, יוצרים pull request חדש שמוריד את השינויים. זה הרבה יותר בטיחותי מאשר למחוק את הpr הקודם בגלל שככה אנחנו לא מאבדים את הקונטקסט של הpr שיכול לבלבל בעתיד וגם זה הרבה יותר קל מבחינת הrebasing ולהעביר דברים שעובדים עליהם לשם. 

3. Explain the difference between **CLI (Command Line Interface)** and
   **UI (User Interface)** applications.  
   What are the benefits of each?

A:

בCLI אנחנו מתקשרים עם האפליקציה בכך שאנחנו כותבים טקסט בתוך חלון ומאוד מוגבלים בפעולות שלנו. לעומת זאת באפליקציות עם UI יש לנו הרבה יותר אפשרויות לתקשר עם האפליקציה בדרכים מגוונות כתלות בUI כגון כפתורים וכו'. היתרון בCLI זה שזה מאוד מתאים ונוח להרצת פקודות קוד ואחיד. היתרון של הUI זה שהוא יכול להיות יותר אלגנטי ונוח למשתמש ולהכיל מגוון דרכי תקשורת.

4. What is the difference between a **compiler** and an **interpreter**?  
   Provide examples of languages that use each approach.

A:

בקומפיילר יש לנו תרגום מלא של שפת הקוד שלנו לשפת מכונה לפני הריצה, תהליך זה נקרא הקמפול. לאחר מכן יש הרצה של קוד המכונה. לעןומת זאת, בinterpreter, הקוד מתרגם לשפת מכונה שורה-שורה בזמן שהוא רץ. בC השפה היא compiled ובpython יש לנו interpreter. 

5. What is **event-driven programming**?  
   Explain how it differs from procedural execution and where it is commonly used.

A:

שיטת תכנות שהflow של התוכנה מוחלט לפי אירועים חיצוניים. לדוגמה, אם דיברנו קודם בUI אז לפי לחיצת כפתור או קבלת הודעה. זה משתנה מprocedural programming ששלבי הפעולות שלי לא ממופים לגמרי אצלי אלא מושפעים גם מדבר חיצוני. זה נפוץ אם האפליקציה שלי קשורה לאירועים חיצוניים וצריכה לפעול לפיהם, לדוגמה כמו אפליקציית התכתבויות, שאני רוצה לראות את ההודעה שלי כשאני מקבל אותה ולא לעשות ריפרש כל זמן קבוע של הודעות.

> ⚠️ **Recommended exercise**  Please organzie your time and consider trying this git exercise [https://learngitbranching.js.org/]()
---

# Python & API Foundations

### ❓ Guide Questions

1. What is **Python**, and what are its main characteristics compared to other
   programming languages (for example c#)?  
   Discuss readability, ecosystem, and runtime behavior.
compare python uniuqe data type like Tuple vs. List.

A:

פייתון היא שפת תכנות high level  שהיא שפת התכנות הפופולארית ביותר עם הקהילה הכי גדולה. לא רק בקרב מפתחים אלא בקרב מתמטיקאים מדענים ועוד. מה שבעיקר מייחד אותו זה הסינטקס הברור והנורא קריא וקצר, האפשרות להשתמש במלא ספריות עם קהילה ענקית שמאוד תורמת לספריות. השפה היא גם interpreted. הlist זה רשימה סופית של איברים שלכל אחד יש אינדקס שמתאים למיקום שלו ברשימה. יש להם סדר מוגר ואפשר לשנות את הרשימה. בtuple זה מאוד דומה אבל אי אפשר לשנות אותו בעתיד. מבחינת הסינטקס, סוגריים מרובעות זה list ועגולות זה tuple. יש גם את הset שמכיל איברים וניתן לעשות עליו פעולות כלשהן כמו לבדוק שייכות איבר לקבוצה, וfrozenset שהוא set שלא ניתן לשינוי. 

2. What is a **REST API**?  
   Explain the core concepts such as resources, HTTP methods, and stateless communication.

A:

ממשק שדרכו שני מכשירים יכולים לתקשר אחד עם השני שחייב לקיים חוקים מסוימים. כל תוכן שאפשר לגשת אליו נקרא resource. מדובר באובייקט עם סוג, מידע, מתודות ויחס לresources אחרים. הresources מחולקים לפי URIים שונים. הלקוח עובד עם הresources בכך שהוא פונה לendpoint שמותאם לresource בעזרת בקשת HTTP. הבקשה מכיל הURI ולפני כן את הפועל הHTTPי שמתבצע. לדוגמה POST, GET, DELETE. בstateless communication אנחנו לא שומרים מצב לפי הבקשות שלנו, כלומר זה לא שפעולות שונות מעבירות אותנו בין מצבים. ואכן, בrest api, אנחנו לא משמרים את הבקשות הקודמות או מושפעים מהן חוץ ממה שהן עשו על המידע שזה כבר לא בתוכנו בתור הAPI. 


3. **What is the Global Interpreter Lock (GIL) in Python?**  
   Explain:
   - What the GIL is and why it exists  
   - How it affects multi-threading and CPU-bound vs I/O-bound tasks  
   - Differences (if any) in how the GIL behaves across Python versions  
   - What Python 3.14 introduces regarding optionally disabling the GIL and why this is significant  
   - Common strategies to work around its limitations (e.g., multiprocessing)

A:

מנעול שמגביל את התוכנת פייתון שלי ככה שכל תהליך יכול להריץ רק thread אחד כל פעם. הסיבה היא שלא יהיו שגיאות מבחינת מספר ההצבעות שיש על אובייקטים מסוימים לאור בעיות תזמון. לכן בעיות CPU-Bound לא יכולות להיות מנוצלות עם multi threading. מבחינת I/O, הנזק פחות חמור בגלל שכשמחכים לI/O אז הthreadים לא מחזיקים את המנעול. בפייתון 3.2 עשו עיצוב מחדש של הGIL כדי למקסם את הביצועים ומקביליות. למרות שזה לא ביטל את ההגבלה ההינהרנטית שלו, זה כן שיפר את הביצועים. ב3.12 הוסיפו את זה שלכל subinterpreter יש GIL משל עצמו, מה שמאפשר מקביליות בין subinterpreters שונים. החל מגרסה 3.13 באופן ניסויי והחל מ3.14 באופן מלא הוסיפו את האופציה לכבות את הGIL. זה משמעותי כי זה מאפשר לנו מקביליות אמיתית בפייתון בthreadים. אחד מהworkarounds הנפוצים היה לעבוד עם Multiprocessing, פשוט שיהיה לנו עוד תהליך פייתון נפרד, שיש לו GIL נפרד, ולכן אפשר מקביליות. במקום threadים נוספים אז תהליכים נוספים. 


4. What is a linter? read and explain about Ruff.

A:

כלי סטטי לאנליזת קוד בלי הרצה שלו, אפשר לבדוק איתו שגיאות וסכמת קוד כמו bad practices וסטייל מסוים. נגיד אם אני רוצה להגדיר שכל שורה תסתיים בנקודה פסיק או שיש לי משתנה לא בשימוש. מאפשר למפתח להתרכז יותר בפיתוח ולא בחיפוש ניואנסים כאלו. גם הרבה יותר קל שהקונבנציות יהיו עקביות ולאנשים חדשים להבין מהן. הוא ממיר את הקוד לAST ואז מריץ את החוקים שלו. יש קובץ קונפיגורציה שמכריע את החוקים הללו. הruff הוא python linter נורא מהיר שכתוב בrust. אפשר להוריד אותו עם pip, הוא מעודכן עם גרסת python 3.15 וכל הזמן עובר עדכונים, יש בו יותר מ900 חוקים דיפולטיביים והוא עובד עם caching כדי לא לבדוק מחדש קבצים שלא השתנו ונבדקו כבר. אפשר להשתמש בו כextension בvscode. אפשר להגדיר אותו לפי הruffl.toml 

5. What are e2e testings? What are **tests** in software development, and why are they important?  
   Explain unit tests, integration tests, and the role of automated testing. What are **mocks**, and why are they used in testing?  
   Compare **pytest** with other Python testing frameworks and explain its advantages.

A:

הend to end testing הם טסטים שבודקים את פעילות התוכנה מההתחלה עד הסוף, את כל הflow באופן מקיף. טסטים בפיתוח זה תהליכים שרצים על התוכנה שלנו או חלק ממנה על מנת לבדוק נכונות התנהגות. טסטים חשובים גם כדי להבין שיש טעות והתנהגות לא צפויה בתוכנה וגם על מנת לבודד אותה ולהבין מה הבעיה המדויקת. הרעיון של unit tests זה לבדוק חלקים נפרדים באופן מבודד בפני עצמם על מנת להבין אם הבעיה בהם או לא. לעומת זאת, בintegration tests אנחנו מריצים כמה רכיבים יחדיו לראות שהם גם פועלים אחד עם השני בצורה הרצויה. הרעיון של automated testing זה להריץ טסטים באופן אוטומטי בלי שבן אדם יצטרך להריץ אותם אחד אחד. זה מאוד תורם כי זה חוסך המון זמן ומאפשר לנו להתמקד יותר בפיתוח עצמו ולעבוד במקביל. הרעיון בmock זה אובייקט שמדמה אובייקט שיהיה לנו בסביבת הרצה אמיתית אבל מפשט אותו. נגיד אם יש לי אובייקט לא דטרמיניסטי, אני יכול להגדיר Mock הרבה יותר פשטני שהחלק הזה יחזיר את הדבר שאני רוצה לבדוק. מקל עלינו משמעותית בטסטים בעיקר במקרי קצה. אפשר להשתמש בpytest כדי ליצור testים בין אם מדובר בunit, integration או functional. מה שמיוחד בpytest זה שהוא מאפשר עבודה עם plugins על מנת להרחיב את היכולות שלו. הסינטקס די פשוט, אפשר להשתמש בmockים באופן built in ואפשר להריץ טסטים במקביל. יש גם את unittest כספרייה לטסטים. בpytest יש את האופציה של פרמטריזציה של להריץ טסטים עם ערכים שונים שהם מקבלים כפרמטרים במקום הצורך לשכפל קוד, דרך דקורטור. גם יש את הfixtures שהם דרך נוחה להגדיר מראש איך אנחנו רוצים שדברים יוגדרו בטסטים ולשנות את זה. גם מבחינת ליצור משאבים ולאחר מכן להרוג אותם, זה מתאפשר בנוחות כי זה יקרה אוטומטית תחת הfixture. 
### Some other stuff

1. What is the context manager?

A:

מנהל תפעול וניקוי של משאבים. לדוגמה לפתוח קובץ עם with, כל הפעולות כמו לסגור את הקובץ ולוודא שהפעולות שאנחנו עושים חוקיות צריכות להתבצע על ידנו כדי שלא תהיה שגיאה רצינית. לעומת זאת, ניתן לתת לcontext manager לבדוק את תקינות ההתנהגות הזאת. 

2. What is venv? 

A:

העתק של הpython executable. ככה כשאני מוריד ספריות אני יכול לקשר אותן רק לvenv שלי ולא לpython executable הראשי שלי. ככה בכל פרויקט אפשר שיהיה לנו רק את הספריות הרלוונטיות שמורדות. לרוב בלינוקס זה לא העתק אלא symlink. יש גם את הקונספט של spack environment שזה לקשר את הספריות למקום משותף עם האשים כדי לחסוך מקום בדיסק. 

3. what is pyc?

A:

למרות שפייתון היא שפה interpreted, היא כן עוברת קמפול לשפת אמצע, והקובץ .py נהיה קובץ .pyc. מכיל את הבייטקוד האמצעי הזה.

### Skila Questions

1. What's the name of the two other folders inside of venv beside script?

יש את include ואת lib/pythonX.Y/site-packages. 

2. Are privates really enforced in python by default?

לא, זה סמנטי.

3. How does else work in try,catch,finally?

קוד שרץ במקרה שלא היה exception.

4. What's the difference between error and exception in python?

הerrors זה דברים רעים שקרו בזמן ריצת קוד או שגיאות בקוד. אין באמת ממש error כאובייקט פייתוני. לעומת זאת, exception זה אובייקט שיורש מהBaseException והוא מחלקה שנותנת הודעת שגיאה.
5. How do you create a module in python?

6. Name of python interpreter in python, is there caching?

7. What is metaclass and dataclass?

8. What's the difference between a callable and a function?

9. Name the staga states

10. Give an example of the factory design

11. What are uv, uvlock?

12. How does the py4j work?

13. What are args and the one with ** and what's the difference?




---

### 🔄 Alternatives
Assignment: Research and briefly compare **two development approaches or tools** mentioned above.

Examples:
- FastAPI vs Flask
- Interpreted languages vs compiled languages

Deliverable:
- A short written comparison (1–2 sentences).
- Include a **real-life use case** for each alternative.

Goal:
Be able to explain **why a specific tool or development approach would be chosen in a real system.**

---

### 🎯 User Story & Scenario
Assignment: Based on your research, describe a small example of a **Python service or tool**.

Deliverable:
Two short paragraphs describing:

- A realistic scenario where a Python service is required.
- How testing (pytest), mocking, and clean code practices would be applied.

