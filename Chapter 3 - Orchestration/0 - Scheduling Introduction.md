# Orchestration Fundamentals:
## Overview
This section will go over the fundamentals of _orchestration_, consisting of _scheduling_ and _resource management_.

**We will focus on general concepts such as scheduling algorithms, preemptive vs non-preemptive scheduling and prioritization.**

## Goals
- Develop a foundational understanding of how scheduling is done.
- Learn the common terminology used by most schedulers.
- Introducing the day-to-day challenges of an infrastructure administrator.
- Practice planning a self-study day and estimating time for learning.

:warning: **Note:**
- This is a self-study day. Independence and time management are essential.
- Many newcomers struggle with self-study; take a moment to plan your day and stick to it.
- Understand the **big picture** of each concept. If you can't explain it, you probably haven't learned it.
- Be prepared to describe how concepts relate to one another and to real-world scenarios.
- When in doubt about what you need to learn, ask your mentor.

### Core Concepts

Think through the following questions; by answering them you’ll touch every major topic listed above:

1. **Scheduling Basics:** Why can’t we just use a simple sleep() command or a basic cron job to manage a modern data workflows? What is the difference between a Time-based trigger and an Event-based trigger? Give a real-world data example for each, In general scheduling, why do we use a DAG to represent a workflow instead of just a simple list of instructions?

A:

תזמנון הוא עניין שצריך להיות גמיש אבל מאוד עדין באותו הזמן. אי אפשר פשוט לעשות sleep למשך זמן כלשהו או להפעיל משהו באופן קבוע בזמן כלשהו, צריך ממש תכנון אחיד ולחשוב על איך תוכנות אחרות עובדות אחת עם השנייה על מנת להבטיח נכונות. דברים חדשים תמיד יכולים לצוץ וצריך שהתזמון שלנו יהיה מותאם למקרה. יש לנו time based trigger שבזמן מסוים קורה תזמון ויש לנו event based trigger שמתחיל תזמון. לדוגמה, אפשר תהליך ירוץ כל יום ב16:00 ותהליך ירוץ כל פעם שאני אלחץ על כפתור. העבודה עם DAGים מאוד מאפטמת לנו את ההצגה של מה שאנחנו צריכים לעשות ומה ניתן. אם יש לנו סתם רשימה של משימות קשה לדעת איך לבצע את זה בצורה הנכונה, אבל המבנה של DAG עם התלויות דרך קשתות מציג לנו בצורה מתמטית את התמונה המלאה. כך נוכל למקבל את מה שצריך ולא לגרום להמתנות מיותרות. 


2. **Scheduling Algorithms & Types:** Compare First-In-First-Out (FIFO), Shortest Job First (SJF) and Round Robin (RR), how do they handle varying workflows? Define Preemptive vs. Non-preemptive scheduling, If a "Critical" job enters the queue while a "Low" priority job is already running, what happens in both scenarios? What is "Starvation" in priority scheduling, and how does the concept of Aging fix it? what are other common algorithms to fix starvation?

A:

ראשית יש FIFO, ממש מתזמנים את המשימות לפי הסדר שלהן. אם כרגע אי אפשר, אז יש המתנה וסדר העדיפויות הוא סדר ההגעה. יש את SJF שלוקחים את העבודה עם זמן הריצה המשוערך הכי נמוך שתרוץ. יש את ROUND ROBIN שמריצים את כל המשימות לפי הסדר כך שלכל אחד מהם יש quantum קבוע, ממשיך באופן מעגלי. לכל אחד יש את החולשות שלו. FIFO הכי הוגן אבל מאוד פגיע כי יכול להיות לנו את הconvoy effect כך שמשימה אחת גדולה תאט אחרות. חיסרון של SJF זה שיכולות להגיע כל הזמן משימות קצרות מאוד שיגרמו להרעבה של משימות ארוכות יותר. יתרון של RR זה פשטות והוא באמת יכול להבטיח לכולם זמן ריצה, אבל מצד שני אי אפשר לתעדף פה משימות והכל נורא בסיסי. בpreemptive scheduling אנחנו יכולים לבצע preemption ולהפסיק ריצה של עבודה ולהחליף בעבודה אחרת. בnon preemption זה לא קורה, אם עבודה מתחילה היא תרוץ עד שהיא תסתיים. (יכול להיות בכוח). בnon preemption אם נכנסת משימה קריטית לתור בזמן שמשימה פחות חשובה רצה, היא לא תופסק. אם יש preemption, אז היא כן תופסק והחשובה יותר תתחיל לרוץ. המונח starvation מתאר מצב שעבודה לא רצה, בקונטקסט של תזמון זה לרוב כי כל הזמן מגיעות עבודות חדשות שלוקחות עדיפות. בזכות הקונספט של aging, אנחנו מוסיפים גיל לעבודות לפי הזמן שהן בהמתנה וזה מחזק להן את התעדוף כך שהם לא יחכו לנצח. דוגמה טובה לאלגוריתם שפותר הרעבה הוא CFS שמבטיח שהתהליכים בסוף ירוצו ויצברו נתח זמן וירטואלי.

3. **Resource Allocation & Scheduling:** Explain Dominant Resource Fairness (DRF). Why is it more "fair" to look at CPU and RAM usage rather than just task count? Compare Static Allocation to Dynamic Allocation. Which one is safer for the system, and which one is more efficient for the cluster? What is Capacity Scheduling, and how does it provide "guaranteed lanes" for different departments?

A:

המטרה של Dominant Resource Fairness זה לחלק משאבים בצורה הוגנת. הוא עובד כך שהוא מנסה למצוא את האחוז הכי גבוה שהוא יכול שכל התהליכים יוכלו להשיג את המשאב שהם הכי צריכים באחוז הזה. יש יותר הוגנות בלהסתכל על CPU וRAM מאשר סתם מספר הtaskים כי יכולות להיות לנו המון משימות לא דורשניות אבל בסופו של דבר מה שמכריע מה אפשרי זה המשאבים עצמם. בstatic allocation אנחנו מקצים את הזיכרון בהתחלה ובdynamic allocation אנחנו יכולים תוך כדי. מבחינת מה הכי בטיחותי, להקצות באופן סטטי זה הכי בטיחותי כי הכל נשאר קבוע ודטרמיניסטי ונכשל או מצליח על ההתחלה. להקצות באופן דינאמי יכול להיות יותר יעיל כי אפשר להקצות כל פעם רק את הזיכרון שצריך, אבל זה יוצר מהר פרגמנטציות והקצאות עלולות להכשל בזמן ריצה מה שיכול להיות קטלני. בcapacity scheduling יש לנו אפשרות לתת למשתמשים שונים משאבים שונים בתוך cluster משותף. זה מתבצע בעזרת היררכיה בצורת עץ שלכל חלק יש משאבים משלו ובמקרה הצורך ניתן להשאיל ממקומות אחרים. 

4. **Cluster Utilization:** What are idle resources in a cluster, and why are they considered a problem from a utilization perspective? What are our options when we want to improve cluster resource usage on job based or non job based clusters (at least provide 3)? what are the advantages and disadvantages of each option (at least 2)?

A:

בcluster כשמדובר על idle resources אלו משאבים לא מנוצלים. זה נחשב בעיה כי אנחנו רוצים לנצל כל משאבים שיש לנו ושלא יהיו לנו משאבים שיושבים בצד. אם אנחנו רוצים לשפר את השימוש את ניצול המשאבים יש לנו כמה דרכים. דבר ראשון, אפשר לנסות להוסיף מאורעות של פעולות אל תוך הcluster כדי לדחוף את ניצול המשאבים לקצה. יתרון זה שזה יחסית פשוט להוסיף עוד threadים לפעולות, מצד שני אפטום משימות ספציפיות אם יש לנו הרבה שתלויות אחת בשנייה יכול להיות מסובך לפעמים כדי לוודא שאנחנו עושים את זה בצורה שהזמן המתנה ביניהם יהיה מינימלי ושבאמת נרוויח מכך. יש את הפתרון של להוסיף threadים באופן דינאמי, שזה טוב כי אנחנו תמיד יכולים להיות על הגבול של ניצול המשאבים ושזה יתעדכן בהתאם, מצד שני יש תקורה לשינויים האלו כל פעם שיש שינוי באיך שאר המשאבים מנוצלים בכל עת. לבסוף, אפשר פשוט לגרוט משאבים. יתרון שאנחנו עדיין נבצע את מה שרצינו וגם חסכנו כסף, חיסרון שזה באמת מאפשר פחות גמישות בניגוד לפתרון של threadים דינמיים למקרה שהעומס כן יעלה קצת על המצופה. 

5. **Users Perspective:** Why is Idempotency the most important concept for a developer to understand when writing scheduled jobs? what is an Exponential Backoff when jobs fail? What is Backfilling, and how does it relate to the "Optimistic" approach of keeping resources busy?

A:

הקונספט של אידמפוטנטיות הוא מאוד חשוב למפתחים כשהם כותבים פונקציות שיתוזמנו כי הם צריכים לחשוב על החשיבות של נכונות ביצוע פונקציה במקרים שקריאה מספר פעמים יכולה לגרום לכשל לוגי גדול אם אין בקרה. לדוגמה במשיכת כסף בבנק, חשוב לי שהפעולה תתבצע פעם אחת ולא יהיה מקרה שזה יקרה יותר פעמיים. ככה אנחנו מבטיחים נכונות במקרה שהפעולות שלנו ירוצו כמה פעמים בין אם הם תוזמנו ומסיבה מסוימת היו צריכים לרוץ מחדש או תוזמנו מספר פעמים לאור סיבוך כלשהו שקרה בתזמון. כשתהליכים נכשלים אז exponential backoff אומר שזמן ההמתנה שלהם יגדל אקספוננציאלית כל פעם כדי שלא יהיו המון ניסיונות סתם. לדוגמה, בדיסקורד אם אין לך אינטרנט! העקרון של backfilling בתזמון זה שאם יש לי תהליך שרץ ותהליך שעתיד לרוץ אחריו ואני יכול לדחוף משימה עתידית ביניהם ככה שהתהליך השני לא ידחה ולא תהיה פגיעה בהרצת התהליך הנוכחי שלי, אדז אני אעשה את זה. זה מתקשר לגישה האופטימית של לשמור את המשאבים עסוקים בכך שאני מנצל את המשאבים שיש לי גם בין המשימות על ידי כך שאני מסתכל על הטווח הרחוק.
### Real-World Context
Rather than focusing on one technology, think about how these ideas show up in schedulers, such as the linux scheduler or the trino task scheduler.

## Wrapping Up :trophy:
Discuss your answers and any areas of confusion with your mentor. Reflect on how these general concepts will help when you later when using scheduled jobs.

## Additional Topics from Review
- A deep dive into sheduling algorithms.
- What are the different obsolescence algorithms widely used? where could they also be implemented? what are some scheduling algorithms without preemption and aging that solve starvation?

## Action Items
- Review your notes and identify topics you want to explore deeper.
- Collect a list of real-world schedulers and their algorithms.
- Prepare questions for the upcoming mentor Q&A session.

## Recommemded Resources
- [Priority Scheduling Slurm](https://slurm.schedmd.com/priority_multifactor.html)
- [Operating Systems Scheduling Stanford University](https://web.stanford.edu/~ouster/cgi-bin/cs140-spring14/lecture.php?topic=scheduling)
