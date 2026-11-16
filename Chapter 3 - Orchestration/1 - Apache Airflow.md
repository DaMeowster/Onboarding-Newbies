# Airflow Fundamentals:
## Overview
This section will go over the fundamentals of _`Apache Airflow`_, consisting of the client side, and the backend.

**We will focus on general concepts of Airflow, the flow of the tasks and how does client code look like.**

## Goals
- Master the fundamental Airflow abstractions and authoring models.
- Understand the core backend architecture, the responsibilities of each component, and how they evolve from Airflow 2 to Airflow 3.
- Trace the end-to-end lifecycle of a `Dag` and `TaskInstance`, from code parsing and `Dag` synchronization (including `DAGBundles`) to `Scheduler` critical loops and `Worker` execution.
- Evaluate the mechanics of data and state passing, distinguishing between metadata sharing, global configuration, and resource-efficient patterns.
- Compare Airflow’s orchestration approach against industry alternatives to justify its usage in production data pipelines.

### :warning: **Note:**
- This is a self-study day. Independence and time management are essential.
- Many newcomers struggle with self-study; take a moment to plan your day and stick to it.
- Understand the **big picture** of each concept. If you can't explain it, you probably haven't learned it.
- Be prepared to describe how concepts relate to one another and to real-world scenarios.
- When in doubt about what you need to learn, ask your mentor.

### Core Concepts

Think through the following questions; by answering them you’ll touch every major topic listed above:


1. **Airflow User API & Concepts:** Explain the difference between a DAG and a DagRun? How do tasks share small metadata versus global configuration? What is Jinja Templating, and why would you use {{ ds }} instead of Python's datetime.now()? Contrast the TaskFlow SDK with Classic Operators. How does the TaskFlow SDK handle XComs differently than the old xcom_pull method? What are Assets? What types of Operators exist? Why is it not recommended to run any time consuming code in top level dag code? How does this affect the DAG Processor's performance? What is a Hook? what is the connection between Hooks, Connections and Operators?

A:

הDagRun הוא בעצם מופע של DAG שנוצר כל פעם שDAG מתוזמן או מותרג. הוא גם מכיל data interval שמגדיר את חלון הריצה שלו. לרוב הוא יתחיל לרוץ דווקא בול אחרי שהdata interval מסתיים. הtasks חולקים metadata בעזרת כך שהוא נשמר בDB מיועד לזה, הכי פופולארי זה Postgres. הקונפיגורציה נשמרת בקובץ airflow.cfg. בפרויקטי פייתון ניתן להשתמש בJinja כtemplate engine. בעקרון ככה אפשר להזריק משתנים אל תוך הbash. יש הבדל בין להשתמש בין ds לdatetime.now, הds מתאר את הזמן הלוגי ולכן בעצם datetime.now יהיה יום אחריו. 


2. **Airflow Backend & Architecture:** What are the different components in the Airflow architecture? Define the roles of each component. Why is the `Executor` considered a mechanism/logic rather than a standalone service? Explain the `Deferrable Operator`. Which component makes these possible, and how do they save money/resources in a Big Data stack? What are `Airflow Providers`? and what are the main changes between Airflow 3 and Airflow 2?

A:

הארכיטקטורה של Airflow מכילה כמה חלקים. יש את המתזמן, שאחראי על לטרגר עבודות ולהגיש taskים לworkerים שיריצו. יש את המעבד DAG שמעבד DAGים מקבצים, הוא מחולק לשני חלקים, חלק שרץ כל הזמן ומכריע איזה קבצים צריכים לעבור עיבוד וחלק שמעבד אותם לDAGים. הוא שומר את הDAGים המסורלזים בmetadata db. יש את הDAG bundles שהם אוספים של קבצי DAGים והקבצים הרלוונטים אליהם כגון קבצי קונפיגורציה, מהם מהprocessor מסרלז. דוגמה פשוטה לDAG bundle זו פשוט תיקייה במחשב שלנו שמכילה קבצי DAG. יש את השרת API שנותן למשתמש ממשק שדרכו הוא יכול לטרגר DAGים, לדבג, לראות מטריקות וכו'. גם הtaskים משתמשים בו לפעמים כדי לדבר ישירות לתקשורת לא דרך הmetadata db. לבסוף, יש את הmetadata DB שמכיל את המצבים של הtaskים, DAGים ועוד משתנים. יש לנו את הworkerים שמריצים taskים שהם מקבלים מהמתזמן. בנוסף יש את הtrigger שמאפשר לנו שtaskים שכרגע מחכים לדברים יוכלו להפסיק לעבוד ויצרו trigger שישלח סיגנל לתזמן אותם מחדש בסוף וכך בזמן הזה הworker יתפנה. הסיבה שהexecutor נחשב לוגי ולא רכיב נפרד הוא שהוא יותר קונפיגורציה אצל המתזמן, מכניזם לוגי. הdeferrable operator זה בדיוק מה שתיארנו, והרכיב שמאפשר הוא הtrigger. החיסכון משאבים נובע מכך שהworker מתפנה כפי שאמרנו. הAirflow Providers


3. **Airflow Security:** How does the airflow security model look like? both arcihtecture-wise and client-wise? how can I integrate airflow with different authentication backends and types? (as if LDAP or SSO), what are the available permissions that exist in airflow (find place in docs)?

4. **Airflow Task Lifecycle:** What is the full flow of a Dag from being written to being run? What happens when the `Dag Processor` encounters your file? How is `Jinja` parsing different in Dag processing than execution time? At which state does the `Scheduler` stop managing the task, and hand it over to the `Executor`? What is the flow when a task gets to a `Worker`? When does it start running?

5. **Airflow Critical Sections:** What is the `'Critical Section'` of the `Scheduler`? Describe the 4 primary `Critical Sections` (`Dag Run` Creation, `Dag Run` Scheduling, `TaskInstance` Creation, `Task` Scheduling).



### Sync Questions

1. What is a python operator? bash operator? kubernetes pod operator?
הpython operator זה פעולה שהיא פשוט כוללת בתוכה פונקציה פייטונית. יש גם bash operator כדי להריץ פקודות bash. אם בא לנו להשתמש בtemplating אז נגדיר את הערך כמשתנה שאליו נקרא בbash כי אין תמיכה בשימוש ישיר בtemplating. הkubernetes pod operator מאפשר ליצור ולהרים podים בkubernetes cluster בעזרת אופרטור. נותנים את הקישור לתמונה וכמה פקודות נוספות, והוא משתמש בלקוח kubernetes פייתוני ליצור בקשת API שמתחילה את הpodים. אפשר לתת קובץ קונפיגורציה וקובץ YAML. 

2. What is a task group? is it physical or logical?

הtaskgroups הם נטו לוגיים בUI. זה מאפשר לנו לאחד taskים בהיררכיה בתצוגה גרפית. ניתן לעשות ככה פעולות במכה על כל הtaskים בקבוצה. הids שלהם מסודרים לפי הid של הקבוצת אם ועוד סיומת, ככה יש ייחודיות. 

3. What type of executors are there?

יש אפשרות להריץ לוקאלית תחת התהליך של הscheduler, זה מאוד קל לשימוש ומהיר אבל פוגע בביצועים של הscheduler. יש גם executors מרוחקים והם מחולקים ל2 קטגוריות: ראשית, יש queue executors. הtaskים נשלחים לqueue שמשם workerים מושכים אותם ומבצעים אותם, לרוב יכול כמה במקביל. היתרון זה שיש לנו הפרדה בין הscheduler לtaskים, הם יכולים להתמודד עם הרבה taskים במקביל. חיסרון זה חלוקת משאבים נכונה בין workerים ולוודא שהworkerים שהם יקרים באמת מנוצלים טוב ואין לנו בזבוז משאבים. יכול להיות הרבה רעש ביניהם. דוגמה קלאסית זה CeleryExecutor, יש גם batch וedge. בCelery משתמשים נגיד בRABBITMQ או REDIS. מבחינת חסרונות צריך לנהל queue וdependency manager חיצוני. גם צריך לוודא שהworkerים יהיו מסונכרנים. מבחינת יתרונות, יש לנו scalability מאוד טוב כי אפשר להמשיך לגדול באופן רוחבי. מצד שני, יש גם containerized executors. שם הtaskים בpodים וכל task מבודד. היתרון זה בגלל שכל task מבודד לגמרי בcontainer משלו אין רעש בין השכנים ואפשר להתאים את הסביבה אליו, גם הworker קיים רק בזמן הריצה של הtask. החיסרון זה שיש את התקורה של לעשות deploy שלוקח זמן, נהיה גרוע יותר אם המשימות קצרות והמהירות חשובה. דוגמה זה kubernetes cluster. החל מairflow 2 אפשר לעבוד עם כמה executors במקביל ולחלק את המשימות אליהם לפי מה חכם. הexecutor הראשון ברשימה יהיה הדיפולטי. אפשר ליצור executor בעצמך. 

4. How does airflow connect to the DB?

החיבור כולל את הhost שצריך להתחבר אליו, שם הDB וקישור לסוד עם פרטי משתמש וסיסמה. 

5. What are DB encryptions?

באופן דיפולטי Airflow שומר את הסיסמאות לחיבור בDB. אפשר להצפין אותם, לדוגמה עם fernet key. צריך לעדכן את ההצפנות כשמוסיפים key חדש כדי שהם יהיו ניתנים לפענוח. 


### Skila 1 Questions

1. What is an operator? What is the relation between an operator and a task?

הTask הוא יחידת ריצה עם task instance שפיזית רץ. לעומת זאת, אופרטור הוא סוג של טמפלייט שעוטף task, הוא מאוד נוח ומוכן ורק נותנים לו פרמטרים ומריצים. 

2. What is celery?

אחד מהדרכים להריץ taskים. יש לנו queue של taskים והworkerים אוסף taskים ומבצע אותם. יכול להיות שworkerים מופרדים למכשירים שונים ויכול להיות שלא. 

3. Where is the task run stored in the metadata db? What is stored with it except the logical and start,end times?

בtask state store. שומר dag id, run id, task id, region id וmap index. 


4. How does the worker get the task instance it runs?

דרך הmetadata DB.

5. How is the DAG stored in the DB?

מסורלז בתוך Dagbag. הdagbag מכיל את הdagים, מתי הdagbag שונה לאחרונה וwarnings וerrors. הprocessor לרוב יקרא את הDAGים מהקבצים כדי לוודא שהdagbag מסונכרן אבל הscheduler והwebserver יקראו מהdagbag למען האחידות.

6. Where are logs stored such that the web server can see them?

הFileTaskHandler כותב logים של taskים לקבצים באחסון חיצוני. אפשר לבחור בקובץ config איפה לשמור את קבצי הlog. באופן דיפולטי הם נשמרים בתיקייה AIRFLOW_HOME.
7. What is the job table in the DB?

8. What is logical date?

הזמן שהdag run רץ עבורו, נגיד אם אני מריץ DAG ב16.11 עבור מידע שנאסף לאורך ה15.11, הlogical date הוא ה15.11.
9. What is data interval start and data interval end?

מסמנים את ההתחלה של הריצה בlogical date והסוף שלה לאחר הוספת אינטרוול הריצה.
10. Why doesn't a dag run at the start date?

בגלל שהריצה אמורה להיות על המידע שנאסף על אותו הטווח, היא תתחיל ישר אחרי כדי לוודא שכל המידע הרלוונטי קיים. 

11. What is catch up?

העקרון אומר שאם יש לי data intervals שפספסתי מהזמן האחרון של הinterval שרץ (או שהרגע הגדרתי עם start_date שהיה) אז הוא ישלים ריצות עבור הintervals האלו. דלוק דיפולטית, אפשר לכבות. 

12. What is backfill?

אפשר להריץ DAGים לפי תאריכים שכבר היו בעבר, לתת start date וend date וairflow יתזמן את הDAG באינטרוולים לפי הצורך. 

13. What is a timetable?

מאפשר ממש להגדיר את הזמנים שבהם אנחנו רוצים שהDAG ירוץ באופן ספציפי. במקרה שזה ימים מאוד ספציפיים זה מאפשר לנו לתזמן בצורה הזאת בניגוד לcron וdelta שהם עדיין יחסית גנריים מבחינת טווח תאריכים או הפרשי זמן. 
14. What is a hook?

ממשק שמאפשר לנו להתחבר ולבצע פעולות במערכת חיצונית, אבסטרקציה לתקשורת החיצונית. 

15. What is the difference between a hook and an operator?

הoperatorים שצריכים מערכות חיצוניות על מנת לפעול ממומשים מעל hooks. 

16. What is dynamic task mapping?

אפשר לשנות את מספר הtaskים בDAG באופן דינאמי. לפי הפלט של הtask הקודם הscheduler יודע להתאים את הtask הבא. לדוגמה, הכי קלאסי זה פונקציה שמקבלת קובץ ממשתמש ופונקציה שמעבדת קובץ. ניתן להעלות כמה קבצים בפונקציה הראשונה, ואז להתאים את הפונקציה השנייה שתעבד את כולם בהתאם לפי expand.

17. What is jinja templating?

במקום שערכים יהיו hard coded בtask יהיה ניתן להזריק ערכים בזמן ריצה לפי מה שבטמפלייט. 

18. What's a zombie task?

מדובר בtaskinstances שתקועים על מצב running למרות שהתהליכים שלהם לא פעילים. יכול להיות שהworker נהרג בגלל חוסר זיכרון, הזיזו את הworker לnode אחר, הוא קיבל ריסטרט וכו'. לאורך הזמן Airflow ימצא אותם ויהרוג אותם. הקוד ממש מחפש taskים על running שהjob או לא רץ או שלא שלח heartbeat מלא זמן. 

### Real-World Context
Rather than focusing on one technology, think about how data workflows are scheduled, and think about when running and orchestrating data workflows.

## 🔄 Alternatives

Assignment: You are required to research and write a comparative analysis between Airflow and an industry alternative.

    Deliverable: A written summary (minimum 1 or 2 sentences).
    Focus: Compare performance, architecture, and specific "pain points" this tool solves compared to legacy systems or competitors.
    Goal: You must be able to justify why the department uses this tool for our specific environment.

## 🎯 User Story & Scenario

Assignment: Based on your research and understanding of the department's pipeline, define a concrete Use Case for this technology.

    Deliverable: A written summary example/story (two sentences approx.).
    Requirement: Describe a real-world scenario (e.g., a specific client requirement) where this technology is the optimal solution.
    Data Flow: Map out the data flow and explain how this tool integrates with other components in the Data Pipeline.


## Wrapping Up :trophy:
Discuss your answers and any areas of confusion with your mentor. Reflect on how these general concepts will help when you later when using scheduled jobs.

## Additional Topics from Review
- A deep dive into the Airlfow database and the inner workings of Airflow.
- A deep dive into bugs solved and unsolved inside Airflow.

## Action Items
- Review your notes and identify topics you want to explore deeper.
- Collect a list of real-world schedulers and their algorithms.
- Prepare questions for the upcoming mentor Q&A session.

## Completion Checklist

- You understand the airflow architecture and can explain in detail what each component does
- You understand airflow on the applicative side, where you know how to author dags
- You understand how airflow manages it's security
- You understand what happens to the task from start to end
- You understand at each tasks state what happens to it and can tell where it is stuck if it is

## Recommemded Resources
- [Airflow Docs](https://airflow.apache.org/docs/)
