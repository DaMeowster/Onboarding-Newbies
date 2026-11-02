# Spark Fundamentals

## Overview
This is a self-study day on the fundamentals of **Apache Spark** — the compute layer of our datalake platform.

**You'll focus on general concepts: Spark's architecture and execution model, planning & optimization, shuffle & joins, memory management, and how Spark is configured and tuned in our environment (PySpark on Kubernetes).**

The goal is not to memorize definitions. For each concept, aim to explain the **big picture in your own words** and connect it to the others — in the oral exam you'll be asked to reason, not recite.

## Goals
- Build a foundational mental model of how Spark **plans and executes** a job.
- Learn the core terminology (Driver, Executor, Job/Stage/Task, DAG, Shuffle, Catalyst, partition…) well enough to use it fluently.
- Understand how Spark **manages memory** and how it's **configured** — especially in a multi-tenant, PySpark-on-Kubernetes setup.
- Practice planning a self-study day and estimating your own learning time.

:warning: **Note:**
- This is a self-study day. Independence and time management are essential.
- Many newcomers struggle with self-study; take a moment to plan your day and stick to it.
- Understand the **big picture** of each concept. If you can't explain it, you probably haven't learned it.
- Be prepared to describe how concepts relate to one another and to real-world scenarios.
- Skim the [assignments](#-alternatives) and the exercise in Core Concept 1 before diving in, so you know what to focus on.
- When in doubt about what you need to learn, ask your mentor.

### Core Concepts

Think through the following. Treat them as a **rough map of the territory**, not a checklist — they point you at the areas to research, and in the oral exam the examiner will drill deeper into each. If you've truly done the research you'll be able to follow those deeper questions; if you only skimmed the outline, it will show.

1. **Spark Architecture & Execution:** What are the main components of Spark, and what is the role of each? Where can the Driver and Executors run, and what does `spark-submit` do? What is the difference between a transformation and an action? What is lazy execution in Spark? What happens between calling an action and work running on the executors — how does the work break down, and what causes those boundaries? What is a DAG, and how does Spark use it, both to schedule work and to achieve fault tolerance? What is the difference between `SparkContext` and `SparkSession`? Go over [this file](assets/where_do_i_run.py) and, for each line, comment on where it runs.

A:

הארכיטקטורה של Spark מכילה בעיקר את הרכיבים הבאים. יש לנו את הדרייבר, שהוא הלב של Spark. הוא תהליך שמתזמן את כל התהליכים, יוצר את הSparkSession, בונה את הDAGים ומחלק עבודה למשימות ומתזמן ונותן אותן לexecutors. לבסוף הוא אוסף את התוצאה הסופית שלהן. כל אחד מהexecutors האלו הוא תהליך שמריץ את המשימה עצמה על worker nodes בcluster, כפי שאמרנו הם מחזירים את התוצאה לDriver. יש לנו גם את הResource Manager שמוודא ניצול נכון של משאבים לאורך כל הnodes, לרוב משתמשים בYARN. הDriver יכול לרוץ גם אצלי וגם בתוך הCluster עצמו, מה שנקרא Client mode וCluster mode. הפקודה spark-submit בעצם מגישה אפליקציית Spark לcluster. חשוב לעשות את ההפרדה שtransformation זה פעולות שמתארות מה צריך להתבצע למידע, הן לא חייבות להתבצע באותו הרגע על מנת שתהיה לנו נכונות. לעומת זאת, action זו פעולה שמתרחשת כאן ועכשיו ומחזירות תוצאה. ההפרדה הזאת מאפשרת לנו לבצע דברים בצורה lazy, כלומר לקבל פעולות ולבצע אותן באמת רק כשצריך. בין קריאת הפעולות עד הפעולה הרלוונטית שגורמת לכך שהפעולות אכן חייבות להתבצע (נגיד, תציג לי תוצאות) מתבצע בנייה של DAG של הפעולות המתוכננות. כשמגיעה הפעולה הרלוונטית, הפעולות מתחילות להתבצע. כשמדברים על DAG מדובר בגרף מכוון אציקלי שמכיל פעולות. Spark משתמש בו על מנת לשמור את הפעולות שצריכות להתבצע לפי הסדר ולבצע איפטומים. אם אנחנו מאבדים RDD אנחנו יכולים להחזיר אותו מאביו בעזרת הDAG. הSparkContext מייצג את החיבור לSpark cluster, יוצר את הRDDs ומבצע את הפעולות. לעומת זאת, הSparkSession חדש יותר, מהווה נקודת חיבור כללית לתקשורת עם Spark ומאפשר לי יותר פונקציונליות כמו עבודה עם streaming וספריית ML. יש גם הרבה אבסטרקציות כך שאני יכול לראות את הכל יותר בhigh level. היום לרוב מומלץ לעבוד עם SparkSession. 

2. **Spark Planning & Optimization:** Logical vs. Physical Planning — walk through the transition from Logical Plan to Physical Plan. What is the fundamental difference between Rule-Based (RBO) and Cost-Based (CBO) optimization, and what are the common kinds of optimizations used? What is AQE? Why is running `ANALYZE TABLE` recommended for performant CBO? And what is whole-stage code generation?

A:
הLogical plan זה תכנית הפעולות שצריכות להתבצע. הPhysical plan זה ממש איך לבצע את הפעולות האלו, נגיד באיזה אלגוריתמים. כשיש לנו את הLogical plan, מפעילים את הCatalyst optimizer שמאפטם את תכנית הפעולות. לדוגמה עם predicate pushdown (דוגמה זה bloom filter) וcolumn pruning. לאחר מכן עוברים לPhysical plan. הגישה של Cost based optimization אומרת להשתמש בסטטיסטיקות של טבלאות כדי לתת הערכה לתקורות של תוכניות שונות ולבסוף לבחור את האחת עם התקורה הכי קטנה. הגישה של Rule based optimization זה לעבוד לפי חוקים מוגדרים מראש כמו הדוגמאות שדיברנו עליהן קודם במעבר לphysical. הAdaptive query execution, או AQE, זו גישה של לאפטם את התוכנית מחדש בזמן ריצה כדי לא להיות מקובע לדוגמה אם קיבלנו סטטיסטיקות לא תואמות. על מנת שזה יעבוד צריך שהתוכנית עבודה לא תהיה בstreaming ושיהיה לפחות exchange או subquery אחד. הפקודה ANALYZE TABLE מעדכנת את הסטטיסטיקות של הטבלה, שזה עדיף לביצועים של CBO כי זה גם מוריד את זמן איסוף הסטטיסטיקות העדכנית מהCBO עצמו כל פעם וגם כנראה יתן לי ייעול יותר נכון מעדכניות. הרעיון של whole stage code generation זה לאחד כמה פונקציות (נגיד query tree) אל תוך פונקציה אחת בקוד. 


3. **Spark Shuffle & Joins:** Compare the different kinds of joins — when will Spark use each, and how can we tell Spark to prefer one over another? What is join reordering? Why is "broadcasting" considered a high-risk, high-reward optimization? What is a *Narrow* transformation and a *Wide* transformation? Why do some operations require a shuffle? What exactly is written in a shuffle?


A:

יש כמה סוגים של Joinים בSpark. יש Inner joins, outer joins, semi joins, anti joins וcross joins. הinner join זה כנראה האחד הכי אינטואיטיבי, פשוט לקחת את השורות משתי הטבלאות שעומדות בקריטריון ולשמור בטבלה החדשה. בouter join גם אם יש שורה בטבלה המקורית שלא מתאימה לתנאי האיחוד, היא תהיה בטבלה החדשה פשוט בלי ערך שם. בsemi join לוקחים את כל השורות בטבלה אחת שיש בטבלה השנייה שורה שמתאימה להן מבחינת התנאי, כלומר יש גם פילטר על האם יש התאמה בטבלה השנייה. הanti joins זה בדיוק ההפך מsemi, כל השורות שאין להן שורה בטבלה השנייה שמתאימה לתנאי. לבסוף, cross join נותן לנו ממש את כל הקומבינציות האפשריות בין שתי הטבלאות. הרעיון של join reordering זה להחליף את סדר פעולות הjoin שיש על מנת לאפטם את הביצועים. ניתן לעשות את זה שכל הjoins הם inner, לא מושפעים אחד מהשני וכל הפונקציות דטרמיניסטיות. באופן דיפולטי spark משתמש בinner, אפשר להגיד לו באופן מפורש להחליף על ידי דגלים, נגיד להגדיר את spark.sql.crossJoin.enabled כtrue. לעשות broadcasting נחשב הכי מהיר כי אנחנו מפחיתים את הdata shuffling בזה שאנחנו פשוט משדרים לכולם את המידע הקטן וכל join מתרחש בתוך node עצמו. אם הגודל גדול, אז אפשר להגיע לשגיאה שנגמר המקום בזיכרון למידע, לכן יש סיכון. הכוונה בnarrow transformation זו טרנספורמציה שכל פלט של partition שהטרנספורמציה מביאה מושפע רק מpartition אחד בקלט, ולכן אין צורך בshuffling. לדוגמה, פילטור, אני לא צריך לעבור על partitions אחרים בשביל הפעולה על הpartition הזה. לעומת זאת, בwide partitions אני צריך לעבור על כמה קלטי partitions על מנת להגיע לעבד partition של פלט. לדוגמה, ספירה. הסיבה שצריך shuffle היא שמבחינת איך שהמידע מעובד ומקבל צורה חדשה, אנחנו רוצים שהמקומות שקרובים אחד לשני לוגית יהיו שמורים באותו מקום ולכן הביזור צריך להיות בהתאם. מה שנכתב בshuffle זה המידע שהיה שמור פשוט מבוזר במקומות שונים עכשיו. מבחינת הכתיבה עצמה של המידע הזה, הוא נשמר בshuffle files בדיסק. 

4. **Tungsten, Abstractions & Memory:** What is an RDD? Why did Spark move away from RDDs in favor of DataFrames/Datasets? Explain how Tungsten uses off-heap memory to avoid Garbage-Collection pauses. Why is it a bad idea to give one executor a lot of resources (the "Fat Executor" problem)? What is the difference between Execution/Storage memory and overhead memory, and what happens when a task exceeds its allotted execution memory? In PySpark specifically, where does the Python worker's memory live relative to the JVM heap — and why can heavy Python UDFs cause memory trouble the JVM heap won't show? On Kubernetes, how does all of this relate to `memoryOverhead` and to a pod being `OOMKilled`?


A:

הRDDs הם המבני נתונים הבסיסיים שהיו בSpark. הם בלתי ניתנים לעריכה, אפשר לשחזר אותם במקרה שאחד נופל ואפשר לבצע אותם באופן lazy. הם מחולקים בnodes שונים באופן מבוזר, אפשר לעבד אותם במקביל ואפשר לשמור אותם לטווח הארוך למקרה שנרצה לעבד אותם המון פעמים בזיכרון. העבודה עם Dataframes היא יותר high level, נותן לנו לעבוד עם מידע מסודר ולאכוף סכמה ולאפטם ביצועים. אפשר עדיין להשתמש בRDDs אם בא לנו לעבוד בlow level, אבל הסיבה העיקרית למערב הייתה ביצועים. בזכות העובדה שTungsten שובר את המידע שלו במקומות רציפים בזיכרון שנפרדים מהheap ולא כאובייקטי Java אלא כבינארי, אנחנו חוסכים המון אובייקטי Java קטנים שתופסים מקום בJVM והיו גורמים לנו להרבה עצירות של הgarbage collector מחוסר מקום. הבעיה של fat executor זה שאם יש לנו לא המון משימות או משימות קלות אז יכול להיות שיש לנו משאבים שסתם מתבזבזים. בנוסף לכך הקצאות זיכרון גדולות יכולות לגרום לעצירות בgarbage collector. הexecution memory משומש לפעולות כמו shuffling וjoins. ברגע שהפעולה מסתיימת, הזיכרון משוחרר. הoverhead memory מנוצל לפעולות פנימיות של spark, לרוב לוקח 10% מהזיכרון של הexecutor, ניתן לעריכה. אם משימה עוברת את הallotted memory, אז או שיש השאלה מהstorage memory או שיש disk spillage. הזיכרון של הpython workers חי בmemory overhead ולכן צריך overhead יותר גדול אם יש UDFs כבדים ולכן זה לא יראה את זה בJVM כי זה מחוץ לJVM Heap. לכן באמת נעלה את הmemoryOverhead, חשוב כדי שלא נקבל OOMKilled שזה מה שקורה לPod כשנגמר הזיכרון. 

5. **Spark Skew, Partitioning & Caching:** What is data skew, and how can it be solved? What is the difference between `repartition(n)` and `coalesce(n)`? What are the Spark `StorageLevel`s? What is the difference between `cache` and `persist`? Why are UDFs (especially in Python) bad, and how does Spark address the serde bottleneck with UDFs?

A:

בSpark המונח data skew מתאר את זה שמטלה במשימה לוקחת הרבה יותר זמן מהאחרות וכך מאטה את הכל. אפשר לפתור את זה בכמה דרכים. או לעשות broadcast כשאפשר אם חלק מהjoin הוא קטן כדי לחסוך את הshuffle, או להוסיף עמודות דומה ולפיהן לעשות עוד תת חלוקה. הפונקציה repartition משנה את מספר הpartitions בDataframe, וישר עושה shuffle לאחר מכן. לעומת זאת הפונקציה coalsce משומשת על מנת להקטין את מסר הpartitions בלבד, ולא מבצעת shuffle לאחר הפעולה שלה בכך שהיא מאחדת את הpartitions באופן לוקאלי. בSpark הStorageLevels מאפשרים לנו לקנפג איפה אנחנו רוצים לשמור את הDataFrames שלנו על מנת למחזר אותם. ההבדלים זה שpersist זו פונקציה כללית שאיתה בוחרים את הStorageLevel וcache זו פונקציית קיצור שפשוט בוחרת לשמור בזיכרון ולזלוג לדיסק במקרה הצורך. הבעיה בUDFs זה שמבחינת Spark זו קופסה שחורה, הוא לא יכול לבצע את האפטום שהוא טוב בו לפונקציות האלו, בעיקר בפייתון שזו שפה די איטית. גם הSerde לוקח זמן כל פעם שמפעילים את הפונקציה ושומרים את התוצאה בחזרה. גם הרבה יותר קשה לדבג כי קשה להיות מודע למה שקורה בפנים עם הפונקציות האלו. בSpark 4 מתמודדים עם זה על ידי כך שבמקום שהUDF מקבל שורה שורה הוא מקבל אוסף ביחד ואיתם עובד כArrow table. 

6. **Configuration & Tuning:** What configs would you expect to set for a typical job, and how do they shape its parallelism and memory? What is dynamic allocation, and what does it rely on (keeping in mind there is no external shuffle service on Kubernetes)? When the same setting is defined in more than one place — in the SparkSession, injected by Airflow, and in `spark-defaults` — how does Spark decide which one wins, and which settings can't be changed once the application has started? If a tenant sets a config and it seems to have no effect, how would you investigate — and how do you check what configuration a running application actually has?


A:


הגיוני שיהיו קונפיגים על job שמאפשרים להגביל את כמות המשאבים שהוא משתמש בהם, זה ישפיע על המקביליות כי יהיו מקרים שיהיו התנגשויות בין דרישת המשאבים ולכן לא תהיה מקביליות מלאה, חלק מהדרישות יכולות להיות שימוש בזיכרון. בעזרת dynamic allocation בSpark האפליקציה יכולה לשנות את מספר הexecutors שלה בזמן ריצה. יש את הבעיה שיש להם shuffle data שאולי צריך. בגלל שבKubernetes אין external shuffling, אז מה שקורה זה שהדרייבר מעודכן באיזה executors יש מידע שהוא shuffle data של משימות שכרגע רצות, ויודע לשחרר רק את הexecutors שהם לא כאלו. כשיש הגדרות בכמה מקומות, יש היררכיה שקובעת מי מנצח: הSparkSession נדבק ראשון והכי מכריע, לאחר מכן קובץ הקונפיגורציה ולבסוף הגדרות גלובליות כמו מה שהוזרק על ידי Airflow. אפשר לדעת אם הגדרה ניתנת לעריכה או לא לפי המתודה IsModifiable. אם אני רואה שאין לכך השפעה הייתי בודק אם יש אולי הגדרה בשכבה חשובה יותר שדורסת אותה, ומה ההגדרות הרלוונטיות כרגע, שאת זה אפשר לבדוק עם spark.conf.get וגם בUI תחת הEnvironment. 


## Skila Questions

1. What's the difference between Spark and Tez?

מבחינת פונקציונליות, בSpark מתאפשר למידת מכונה וgraphx שאין בTez. לSpark יש APIים ממש טובים שמפתחים יכולים להשתמש בהם שאין בTez. גם Spark יכול לעבוד עם YARN, Kubernetes ועוד המון cluster managers כשTez רק מעל YARN. בSpark הכל נעשה בתוך הזיכרון כשבTez רק חלק, וגם Tez הוא לא Query engine בפני עצמו. 

2. What is SparkSession made up of and how does it differ from SparkContext?

יש לו את הcatalog שדרכו המשתמש יכול ליצור לשנות או לחפש בטבלאות וכו'. יש את conf שמהווה ממשק הקונפיגורציה בזמן ריצה. יש את הsparkcontext שהוא מכיל, יש את הdataframereader שמאפשר לקרוא את המידע. ההבדל הוא שהSparkSession עוטף את הSparkContext ויש לו עוד אחריות. הAPI הוא לפי Dataframes וDatasets, יותר highlevel. יש לו אופטימיזציות של הCatalyst optimizer שמשתמש בכך שיש לנו סכמה. 

3. Is there a line of code in the session, let's say python, which is spark-submmit?

לא, אבל כן יש ספרייה כזו בפייתון, spark_submit.

4. Is Tungsten on default or do you need to config something?
כן הוא דיפולטי בגרסאות 3 ו4 העדכניות של Spark.

5. What is special about a Dataset that is not in a Dataframe? Why don't we have datasets in python?
בDatasets יש בדיקה של types בזמן קומפילציה וAPI דומה לשל RDDs שעדיין יש לו אופטימיזציות כמו של Dataframe. הסיבה שאין את זה בפייתון זה כי פייתון לא תומכת בבדיקת types בזמן קומפילציה. 

6. What are some transformation operations?

כמה דוגמאות הן map, filter, select.

7. What do I have between a job and a task? What creates it?
יש stage. מה שיוצר stage חדש זה wide transformation שיוצר shuffling. 


8. Which operations will trigger a shuffle?
הפעולות שיגרמו לshuffle הן wide transformations.


9. On what do I have checkpoints? If not only RDDs.
על RDDs וDataFrames. 


10. How do hive table formats allow Spark to do predicate pushdown on Parquet above HDFS? If HDFS isn't aware of the tabular structure of the file.

11. What kind of joins are there in terms of algorithmic difference?

יש את הbroadcast hash join, שבו יוצרים hash table שמכיל את המידע הקטן שרוצים לעשות לו join עם מידע אחר ועושים broadcast לכל הpartitionים שבהם המידע השני נמצא. הjoin קורה לוקאלית בכל node מה שחוסך לנו shuffle גדול. 

12. What's the relation between task and partition? Something numerical.
ממש יחס חח"ע, הtask הוא יחידת חישוב על partition אחד לכן מספר הtasks הוא לפי הpartitions שמטופלים. 

13. Why do I even have persist and cache in Spark?

אם יש לי כמה פעולות transform ואז כמה actions, אני לא רוצה להריץ את הDAG מחדש כל פעם בשביל כל action. אני רוצה להרכיב הכל ואז לבצע את הactions, על פי המידע המורכב ששמרתי לפני הactions.

14. What happens if I run spark-submit then control c?

15. What is Arrow? What does it allow?
הוא data format שמאפשר עבודה עם אותם מבני נתונים בשפות שונות בלי הצורך בסריאליזציה. 

16. How does the work between python and scala work? 

הPy4J מתרגם את הקוד הפייתוני לפקודות JVM. 

17. How does writing the configuration inside the source code fit into the configuration hierarchy?

זה בראש ההיררכיה, אחרי זה מה שבsparks-default ואחרי זה הדבר הדיפולטי בסביבה.

### Real-World Context
Rather than focusing on one technology in isolation, think about how these ideas show up across distributed processing frameworks — how other engines handle the same problems, and what the *core concepts* of distributed processing are.

## :arrows_counterclockwise: Alternatives

Assignment: research and write a short comparative analysis between Spark and an industry alternative.

- **Deliverable:** a written summary (a sentence or two).
- **Focus:** compare performance, architecture, and the specific "pain points" Spark solves. Cover **Trino** specifically — it's the ad-hoc / analytical query engine that sits alongside Spark in our datalake — and, for historical context, how Spark improved on **MapReduce**.
- **Goal:** be able to justify **when we reach for Spark vs. Trino** in our environment — and when you would *not* use Spark at all.

## :dart: User Story & Scenario

Assignment: based on your research and understanding of the department's pipeline, define a concrete Use Case for this technology.

- **Deliverable:** a written example/story (~two sentences).
- **Requirement:** describe a real-world scenario (e.g. a specific client requirement) where Spark is the optimal solution.

## Wrapping Up :trophy:
Discuss your answers and any areas of confusion with your mentor. Reflect on how these general concepts will help when you later write code and support clients.

## Additional Topics from Review
- A deeper dive into Spark internals: what other optimizations does Spark implement? How does Spark's memory allocation work end to end?
- What other well-known processing frameworks exist? Which use cases does Spark fit — and when should you *not* use it?

## Action Items
- Review your notes and identify topics you want to explore deeper.
- Collect a list of real-world use cases for Apache Spark.
- Prepare questions for the upcoming mentor Q&A session.

## Recommended Resources
- [Apache Spark Documentation](https://spark.apache.org/documentation.html)
