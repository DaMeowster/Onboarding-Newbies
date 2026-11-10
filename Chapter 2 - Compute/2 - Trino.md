# Introduction to Trino 🐰

## Overview
Today’s session introduces Trino, a distributed SQL query engine designed for high-performance analytics across multiple data sources. Unlike traditional databases, Trino does not store data itself. Instead, it queries data where it already resides, enabling fast interactive analysis across large and diverse datasets.

**The emphasis is on Trino’s architecture, distributed execution model, and how it integrates with modern data platforms.**

## Goals
- Understand what Trino is and where it fits in a modern data architecture.
- Learn how Trino executes distributed queries across workers.
- Explore how Trino connects to multiple data sources and federates queries.
- Improve your ability to research distributed data systems independently.

:warning: **Note:**
- This is a self-study day; independence and time management are crucial.
- If you can’t explain a concept clearly, you probably need to revisit it.
- Ask your mentor if you’re unsure what to research.

### ⏳ Timeline
Estimated Duration: 3 Days

- Day 1: Learn the fundamentals of distributed SQL engines and Trino’s role in the data ecosystem.
- Day 2: Dive deeper into Trino architecture, connectors, and query execution.
- Day 3: Review performance optimization and operational concepts, followed by a Q&A session.

---

## Core Concepts

### Part 1: Distributed SQL Engines (General Concepts)

Answer these questions to understand the broader category of distributed query engines before focusing on Trino specifically.

1. **Role in the Data Architecture:**  
   What is a distributed SQL query engine, and where does it sit in a modern data architecture? How does it differ from storage systems such as data lakes or databases?

2. **Motivation & Use Cases:**  
   Why do organizations use distributed SQL engines? In what scenarios are they preferred (for example: interactive analytics on large datasets, federated queries across multiple systems, or querying data lakes)?

3. **Distributed Query Processing:**  
   How do distributed query engines typically execute queries across multiple nodes? Explain concepts such as parallel processing, data shuffling, and shared-nothing architectures.

---

### Part 2: Trino (Implementation & Operations)

Answer these questions to cover Trino’s major architectural and operational concepts.

1. **Purpose & Position in the Data Stack:**  
   What is Trino, and where does it sit in the data architecture? Explain its role as a distributed MPP SQL engine, how it differs from storage systems, and when it should be used instead of other query engines.

A:

בליבו Trino הוא SQL Query Engine מבוזר מקבילי. Trino שונה מstorage system בכך שהוא לא מחזיק את המידע, הוא כלי שמשומש בשביל להריץ את שאילתות הSQL על המידע. אם המטרה שלי היא בעיקר אנאליטיקות שכל query צריך להיות מהיר ולקחת דקות או שניות או אפילו מהר יותר, אז Trino הכי טוב עבורי. 
2. **Architecture & Query Execution:**  
   How is Trino architected, and how does it execute queries in a distributed environment? Discuss the roles of the Coordinator and Workers, stages and tasks, data exchanges, and the execution model.

A:

מבחינת הארכיטקטורה, יש coordinator וworkers. המשתמש מתחבר לcoordinator והוא והworkers ניגשים למקורות המידע דרך הקטלוגים. העבודה של הworkerים היא להשיג מידע מהconnectors ולהחליף מידע ביניהם, לבסוף הcoordinator אחראי על להשיג מהם את המידע ולהפיק את התוצאה הסופית ללקוח. התקשורת בין הworkers לcoordinators היא עם REST API. כשTrino מריץ שאילתא, הוא מחלק אותה באופן היררכי לעץ של stageים שונים. תמיד יש שורש לעץ שהוא הstage שבו מאגדים את כל המידע מהstageים האחרים. מה שרץ על הworkerים זה לא stage, אלא taskים. כל stage מכיל מספר taskים שרצים באופן מבוזר על workerים שונים בcluster. כל task עובד על splits, שאלו חלוקות של המידע. ברגע נתון task עובד על split אחד אבל אפשר לתת לו כמה שהוא יעבוד עליהם באופן סדרתי. כלומר הכל מתחיל בזה שאני כותב שאילתות SQL שמגיעות לcoordinator, מפוצלות לstageים שמפוצלים לtaskים שרצים באופן מבוזר על workerים שונים, לבסוף המידע נאסף והstageים האחרונים מתאגדים והמידע נאסף על ידי הcoordinator וחוזר ללקוח. 



3. **Connectors & Catalogs:**  
   How does Trino integrate with external systems through connectors and catalogs? Explain what connectors are responsible for, how catalogs are configured, and how Trino can federate queries across multiple data sources.

A:

כפי שאמרנו הConnector הוא הכלי שמחבר את Trino למקור המידע בעזרת Hive או Iceberg. ברעיון הוא כמו דרייבר לDB, הוא מתרגם את השאילתות SQL למשהו שיכול לפעול על השכבה למטה. כל connector של Trino מממש את החלק הרלוונטי של הTrino SPI שמאפשר לנו לעבוד עם אותן יכולות SQL תחת כל data source שעבורו אנחנו מממשים את הconnector. הקטלוג מכיל את הconnector ועוד דברים רלוונטים להתחברות כגון פרטים להתחברות, הוא בעצם מהווה את הגישה למקור המידע. מבחינת קינפוג, צריך ליצור קובץ catalog properties. השם של הקובץ בעצם מהווה את שם הקטלוג. בTrino ניתן בשאילתאת SQL אחת לפנות לכמה טבלאות שונות מקטלוגים שונים באותו הזמן, כל קטלוג מכיל connector וכל connector מפנה לdatasource כפי שאמרנו.  


4. **Governance & Workload Management:**  
   How does Trino handle governance and workload management? Discuss resource groups, concurrency control, memory limits, access control (RBAC), and multi-tenant isolation.

A:

הרעיון של resource group זה להגביל את כמות המשאבים של קבוצת שאילתות. לresource group יכול להיות תת resource group או שהיה תהיה מסוגלת לקבל שאילתות, אבל לא שניהם, כך שתהיה לנו הפרדה ברורה של איזה משאבים אפשר לגשת אליהם ולא יהיה חיתוך. אם בresource group נגמרו המשאבים אז הqueries ששייכים אליה ולא רצים כרגע (נגיד בדיוק התווספו) נכנסים להמתנה. מי שאחראי על הקצאת המשאבים וחוקי החילוק זה הresource group manager, אפשר להוסיף אחד לדוגמה כקובץ ולקנפג אותו. יש אחד שמקבל את הקונפיגורציה שלו לא כקובץ אלא מDB רלציוני. לכל resource group ניתן להגביל את מספר השאילתות שיכולים לרוץ בה. יש גם את העניין של soft limits שזו מגבלה חלשה יותר שכן יעקפו אותה אם כל הresource groups הרלוונטים האחרים גם מעל הsoft limits שלהם. יש גם הגבלה לזיכרון מבוזר שקבוצה יכולה להכנס בו לפני ששאילתות מועברות להמתנה, יש 2 דרכים לתת אותו, או כגודל הזיכרון או כאחוז הזיכרון של הcluster. ניתן להוסיף גם access control מערכתי, מוסיפים קובץ הרשאות שיכול לדבר על כמה דברים. האם מישהו יכול להריץ שאילתות כמשתמש מסוים, האם המשתמש יכול לערוך משתנים מסוימים, וגם יש בדיקות גלובליות שהן לא תלויות בconnector ומתקיימות לפני כן. כלומר יתכן ויש לי הרשאות לconnector מסוים אבל אין לי את ההרשאות הכלל קטלוגי, ולכן לא אקבל גישה. בזכות הרעיון הזה אפשר לממש multi tenant isolation, להפריד את המשאבים של השאילתות שלי משאילתות של מישהו אחר ובכך לא נהיה שכנים רועשים אחד לשני. 

5. **Query Optimization & Performance:**  
   How does Trino optimize query performance? Explain cost-based optimization, statistics usage, join strategies, predicate pushdown, partition pruning, spilling, and caching behavior.

A:

בTrino יש יעיול של שאילתות בכמה שיטות. ראשית, יש את cost based optimization. העניין של cost based optimization זה לבצע יעיול של השאילתא לפי סטטיסטקות של טבלאות בשאילתא. הם מונגשים לquery planner על ידי הconnectors. יש סטטיסטיקות של טבלה כמו מספר השורות. פר עמודה יש סטטיסטיקות כמו מספר הערכים השונים, הערך הכי גבוה והכי נמוך בעמודה, אחוז ערכי הnull. סוגי האופימיזצה שנופלים תחת cost based optimization הם join enumeration, שזו הגישה שסדר הjoinים חשוב לנו כי אם נעשה את הjoinים הגדולים בסוף זה יחסוך לנו העברות מידע גדולות נוספות. ההערכה של הגודל של כל join והתקורה מתבצעת לפי הtable statistics. כשיש join, יש צד שtrino יוצר ממנו hash table וצד שני שtrino עובר עליו ומחפש שורות בhash table. יש שתי גישות, גישת partitioned שבה כל node שרלוונטי לשאילתא בונה hash table רק מחלק מהמידע והגישה broadcast שכל node בונה hash table מכל המידע והמידע משוכפל לnodes אחרים. כמובן שיש שיקולים, אם החלק שעושים עליו hash קטן מאוד אז עדיף broadcast כי זה יותר מהיר וחוסך את העברת הטבלאות, אבל הם דורשים שהכל יכנס בזיכרון של הnode במקום שיהיה מבוזר בין השונים. בTrino סוג הjoin distribution נבחר לפי הסטטיסטיקות. באופן דיפולטי זה ככה, אבל ידנית אפשר לקנפג שזה יהיה אחד מהם תמיד. בTrino מתאפשר predicate pushdown שמעביר את התנאי סינון אל מקור המידע דרך הconnector והוא מתבצע שם ברמה הנמוכה. בעזרת partition pruning אנחנו יכולים לקרוא בשאילתא רק את המידע שרלוונטי לחלוקה הרלוונטית במקום לקרוא הכל ואז לזרוק את המידע הלא רלוונטי, נעשה על ידי הקורדינטור שמחליט על מה לדלג. העקרון של spilling מאפשר לנו לעבד שאילתות שצורכות הרבה זיכרון ומקסימום להעביר חלק מהזיכרון כדי לא לחרוג מהמקסימום. ניתן לקנפג לאיפה בדיסק המידע יעבור, להגביל כמות spill, לnode, וspill מקסימלי לquery לnode. בTrino אפשר לעשות cache לקבצים, יש שני סוגים של cache managers. יש את alluxio שעושה cache בדיסק לוקאלי ושם שומרים מידע טבלאי, ויש את הmemory שעושה cache בjava heap ומשומש על ידי הcoordinator כדי לשמור את הmetadata שהוא קורא בזמן שהוא מתכנן את השאילתא. 

6. **High Availability & Multi-Cluster Routing:**  
   How does Trino Gateway enable high availability and workload isolation across multiple Trino clusters? Explain how it provides a single entry point, performs rule-based query routing and load balancing, monitors cluster health, and enables failover and multi-cluster isolation beyond what a single Trino coordinator can support.

A:

הכלי Trino gateway מאפשר לבצע load balancing בין clusterים שונים של Trino. הוא מאפשר 3 דברים מרכזיים: העברת שאילתות לclusterים שונים לפי תנאים, לוודא שclusterים הם בריאים ולא לכלול כאלו שהם לא, לבזר את השאילתות לפי מספר השאילתות בכל cluster. מבחינת הלקוח, יש לו endpoint אחד שהוא שולח לו שאילתות, הכל קורה מאחורי הקלעים. יש גם חלוקה לקבוצות clusterים שונות עם מטרה שונה. בזכות העובדה שיש כמה clusterים בקבוצה ניתן להחליף אחד באופן חלק, אך בכל זמן נתון רק cluster אחד רץ בgroup. רק בזמן הroll שניהם רצים במקביל. השאילתות מגיעות לcluster group לפי המקור, הgateway בודק את הHTTP header שהלקוחות של Trino שלחו. 

### Skila Questions

1. What is the SQL language of Trino?

סוג שפת הSQL שTrino עובד איתו הוא סטנדרט הANSI.

2. Can you turn Trino into a DB?

לא

3. What's the name of queries you run interactively and of ones you run in the background?

המונחים הם interactive processing וbatch processing.

4. Is Trino fit for ETL?

כביכול אפשר אבל כשמדובר על ETL ועיבוד הרבה מידע עדיף לעשות את העיבוד עצמו עם Spark ולבצע עם Trino את השליפות לאחר מכן. 

5. What clients do I have to connect to Trino?

יש את jdbc,odbchttp, python וc#.

6. What's the difference between a query and a statement?

הstatement זה הקוד הSQLי שהTrino מקבל, השאילתא זה הדבר שהוא מעבד את הstatement אליה. הquery מכיל את הstages, tasks, splits ושאר הרכיבים שרלוונטים על מנת שהשאילתא תתבצע. 


7. What are the subcomponents of the coordinator?
הרכיבים הם parser and analyzer, הplanner והscheduler.

8. What is the meaning of a stage? and of a task? Are tasks divided to things?

הstageים זה הדברים שהqueries מחולקים אליהם. כל stage מסמן שלב לוגי של אגרגציה בין stageים אחרים. כל stage מבחינה עיבודית הוא אוסף של taskים כך שכל task מתאים לsplit כמו שבSpark יש התאמה לpartition. הtask מכיל driverים מקבילים שרצים ומעבדים מידע, הtask מאחד את התוצאות של הdriverים ומעביר אותו לtask אחר. הsplit זה בדיוק מידע שמעובד ומוחזר על ידי worker. 
9. How do coordinators know workers exist?

בעזרת הdiscovery service, הtrino נרשם אצל הdiscovery service ושולח heartbeats. אם הworker לא שולח heartbeats אז הdiscovery service מזהה את זה ולא ישלחו לworkerים taskים. מי שמריץ את הdiscovery server זה הcoordinator וככה הוא תמיד ידע מי קיים ועובד מהworkerים.

10. How does a message in the response of the worker node to the coordinator with the result look like?
בתצורת pages, אוסף של שורות בפורמט עמודתי. 


11. How does the message the coordinator returns to the client look like? What are the two methods and pros and cons?

יש את הגישה של לקרוא מbuffer רציף וכשהוא מתרוקן אז הcoordinator מבקש מהworkerים שיכתבו אליו עוד. היתרון זה שהפרוטוקול פשוט ולא דורש רכיבים נוספים לתפעול. מוגבל לJSON. הפרוטוקול בעיקר טוב לקצת מידע, אבל לא אידיאלי ללהוציא הרבה מידע. מאוד קשה לשנות אותו מבחינת המימוש ולהוסיף עדכונים. זה נקרא direct. הפתרון האחר נקרא spooling, שנותן לך throughput גבוה יותר, קל למימוש ואפשר לעבוד עם כמה פורמטים. הרעיון הוא לשמור את המידע בobject storage שכל הworkerים והcoordinator יכולים לכתוב אליו במקביליות. הcoordinator נותן URLים למידע בobject storage שהclient ניגש אליהם.

12. What's the name of the standard connector that needs to be implemented?

הוא נקרא SPI, שזה Service Providor Interface. 

13. What option do I have to define a catalog except a file?

אפשר בעזרת dynamic catalog management להוסיף קטלוגים עם CREATE CATALOG בהינתן חיבור כלשהו. האם זה ישמר בתור קובץ או לא נקבע האם הcatalog-store הוא על file או memory, דיפולטית כן קובץ.

14. What is a catalog and what is a conenctor and how do I create a catalog?

קטלוג הוא אוסף של קונפיגורציות שאני משתמש בהן כדי לגשת לdata source, זה מכיל את הconnector שהוא הדרך תקשורת עצמה ועוד דברים כמו הURL ופרטי התחברות. אפשר ליצור קטלוג בשני דרכים, או על ידי קובץ properties או עם CREATE CATALOG. 

15. What's the term for running something again once it fails? What methods exist in Trino and how are they chosen?

המונח הוא fault tolerant execution. באופן דיפולטי זה לא קורה בTrino. יש שני סוגים של retry policies, אחד זה QUERY ואחד זה TASK, נבחרים לפי הערך של retry-policy. הQUERY אומר לנסות את כל הquery מחדש והtask לנסות רק את הtask מחדש. זה מאוד משתלם אם יש לנו queries מאוד גדולים ואז יותר ישתלם לנו להריץ task קטן מחדש מהכל. מצד שני, הוא כן יכול ליצור יותר latency מretry כללי אם מדובר בqueries קטנים, ואז כבר נרצה QUERY. 

16. What are the usecases of view? Out of the two types, which fit Trino?

יש שני סוגים של views
יש view וmaterialized view. בSQL המונח view מתאר לנו שאילתא שאפשר לעשות לה רפרנס כאילו היא טבלה, אם נעשה לה select זה יריץ את הquery ויתן לנו את התוצאה. בmaterialized view השאילתא רצה פעם אחת ובפעמים הבאות שנפעל על הview נקבל את התוצאה שבאמת נשמרה. החיסרון הוא שצריך לרפרש כל פעם שיש עדכון במידע כי המידע ששמור הוא מה שקיבלנו בתוצאה ויכול להיות שזה השתנה. התוצאה נשמרת בדיסק. בTrino יש אפשרות לליצור ולעבוד עם views וmaterialized views. 

17. What is the name of the concept of giving flags to Trino for queries and what can you do with them?

יש configuration properties שאפשר להגדיר על cluster ויש session properties שהם ברמת הsession עם הcluster ויכולים להיות על כל הcluster או ברמת קטלוג. דוגמאות לsession properties שרלוונטים לquery זה max-writer-task-count, max-execution-time, max-length, max-planning-time, max-run-time, max-write-physical-size.

18. Who is responsible for acting on the predicate pushdown in the data source? In Hive for example

מי שניגש זה עדיין הquery engine. אם מדובר בHive נגיד ששומר קבצי Parquet אז אפשר לפי הmetadata לדעת האם צריך לקרוא את הקובץ או לא לפי סטטיסטיקות של עמודים. 

19. What type of pushdowns are there?
יש 
predicate pushdown, projection pushdown, dereference pushdown, aggregation pushdown, join pushdown, limit pushdown, top n pushdown

בprojection pushdown זה אותו הרעיון אבל עם סינון על קבוצת עמודות במקום קבוצת שורות. בdereference pushdown אנחנו אפילו לא קוראים את כל העמודה לאחר מכן אלא רק את החלק הרלוונטי ממנה. יש גם aggregation pushdown, join pushdown, limit pushdown top n pushdown שאינדיקטיביים מהשם.

20. In the trino gateway is there something more sophisticated for load balancing besides number of queries in a cluster?

כן, יש את הclusterstats. כמה queries רצים, כמה בהמתנה, כמה workerים יש, והסטטוס. 

21. Can a query potentially reach several groups? Is there load balancing between them?

לא, הוא נבחר לפי חוק ששולח אותו. יש עדיפות לחוקים ולכן החוק עם המשקל עדיפות הנמוך יותר יקרה קודם.

22. According to what rules do we decide where the query runs besides load balancing and source. What is the name for these rules and how are they defined?

לפי הrouting roles. אפשר להגדיר חוקים לדוגמה בקובץ או לפי אובייקט חיצוני. אם הגדרת לפי קובץ אז זה בקובץ yaml שלכל חוק יש שם, תיאור, תנאי והפעולה של לאיזה קבוצה זה שולח. בכללי אפשר לחפש דברים בheaderים ולהחליט לפיהם. 



### Skila 2 Questions

1. How is ANSI SQL different from other SQLs?

בזכות העובדה שANSI הוא סטנדרט אז אם לומדים אותו ניתן לעבוד עם הרבה מערכות במקום ללמוד כל פעם שפה שונה. בנוסף לכך, הוא גם מנוהל בצורה טובה, ככה שכל פעם שיוצאים פיצ'רים עדכניים ומהותיים בSQL הם בסוף יגיעו גם לANSI. עוד עניין הוא שאם אנחנו מחליפים מערכת המעבר יכול להיות אוטומטי. 

2. Why is spark preferable in high batch processing?

זה לא באמת נכון, זו סטיגמה שהייתה נכונה לפני project tardigrade כשלtrino לא הייתה שרידות גבוהה ולא היה task retries וqueries לא היו מצליחים אם ברגע לא היה מספיק זיכרון לבצע אותם. היום Trino מתאים ומסוגל לעבוד עם ETL. 

3. What is the difference between odbc and c# clients?

הc# client מאפשר תמיכה בחיבור עם תוכנות על ממשק .NET. 
הODBC Driver מאפשר תמיכה בחיבור עם אפליקציות שתומכות בODBC. 

4. What UI SQL programs allow connection to Trino?

יש כמה כמו Superset וTrino Query UI שמאפשרים עבודה עם מטריקות גרפים ועוד. 

5. What is an operator?

פעולה שמקבלת מידע, עושה עליו פעולה ומחזירה מידע. כמו table scan, פילטור ועוד. 
6. What defines a stage? 

חלק מהquery plan שיכול לרוץ ביחד. לרוב מה שיגדיר לי את הגבולות שלstage זה shuffleים שצריכים לקרות. כלומר אגרגציות, JOINים, ORDER BY, ועוד. 

7. What makes a task retry have more latency?

כשיש spooling ע"י הexchange manager יש את התקורה של הspooling לS3 לדוגמה.

8. How does Trino run a view in Hive if it can't read HiveSQL?

אין אופציה ליזור Materialized hive views בTrino. יש תרגום של הHIVESQL לANSI בעזרת Coral. הוא מתרגם את הHIVEQL לייצור פנימי עם Apache calcite ומשם לTrino SQL. 

---

### 🔄 Alternatives
Assignment: You are required to research and write a comparative analysis between Trino and an industry alternative.

- Deliverable: A written summary (minimum 1–2 paragraphs).
- Add a real-life use case.
- Focus: Compare performance, architecture, and specific "pain points" this tool solves compared to legacy systems or competing technologies.
- Goal: You must be able to justify why the department uses this tool for our specific environment.

---
### 🎯 User Story & Scenario
Assignment: Based on your research and understanding of the department's pipeline, define a concrete Use Case for this technology.
- Deliverable: A written summary example/story (two paragraphs approx.).
- Requirement: Describe a real-world scenario (e.g., a specific client requirement) where this technology is the optimal solution.
- Data Flow: Map out the data flow and explain how this tool integrates with other components in the Data Pipeline.
---


## Wrapping Up :trophy:
Go over your answers with your mentor and clarify any uncertainties. Relate Trino concepts back to the broader data platform and how distributed query engines interact with storage systems and processing frameworks.

---

## Action Items
- Identify Trino topics you want to explore further.
- Search examples of industray usage of Trino.
- **Bonus** - what is the rabbit's name?
- Prepare questions for the next mentor Q&A session.

---

## Recommended Resources
- [Official Trino Documentation](https://trino.io/docs/current/) – the primary reference guide.
- [Trino: The Definitive Guide (O'Reilly)](https://dokumen.pub/trino-the-definitive-guide-sql-at-any-scale-on-any-storage-in-any-environment-2nbsped-109813723x-9781098137236.html)
- [Official Trino Gateway Documentation](https://trinodb.github.io/trino-gateway/)
