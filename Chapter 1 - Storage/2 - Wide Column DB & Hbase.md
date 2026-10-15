# Introduction to HBase :elephant:

> **Note:** this document was renamed earlier to `Wide Column DB & Hbase` to reflect the broader category; the title remains centered on HBase for now.

## Overview
Today’s session dives deeper into column‑oriented databases with a focus on Apache HBase, the Hadoop ecosystem’s wide‑column store. Understanding HBase will help you see how low‑latency random access is provided over massive data sets.

**The emphasis is on HBase’s architecture, core components, and operational model.**

## Goals
- Grasp the columnar database model and why HBase exists.
- Learn the responsibilities of key HBase components (RegionServer, Region, ZooKeeper, HFile, etc.).
- Improve your ability to plan and self‑direct learning.

:warning: **Note:**
- This is a self‑study day; independence and time management are crucial.
- If you can’t explain a concept clearly, you probably need to revaisit it.
- Read the [Exercise](#exercise) before starting so you know what to emphasize.
- Ask your mentor if you’re unsure what to research.

### ⏳ Timeline
Estimated Duration: 3 Days
- Day 1: Learn the concepts of wide column DB and HBASE spesficly; spend the day.
- Day 2-3: Get deep into HBASE spesficly
    - Have a Q&A session at the third day and in between sessions each day

## Core Concepts

## Part 1: Wide Column Databases (General Concepts)

Answer these questions to understand the fundamentals of wide-column databases before focusing on HBase:

1. **Data Model & Structure:**  
   What is a wide-column database, and how does its data model work? Explain the concepts of rows, column families, and flexible schemas. How does this model differ from traditional relational databases and key-value stores?
A:
בwide column database יש לנו טבלאות של שורות ועמודות כך שבכל שורה יכולות להיות לי עמודות שונות בניגוד לrelational databases שזה קבוע באותה שורה. אוסף של עמודות ביחד יכולים להשמר כcolumn family, שנשמרים ביחד פיזית בדיסק. כפי שאמרנו העמודות בכל שורה יכולות להשתנות באותה טבלה ולכן אין סכמה קבועה של איך שורה נראית, מה שמאפשר יותר גמישות בסכמה בניגוד לrelational databases שבכל שורה יש את אותן עמודות בטבלה. 



2. **Use Cases & Motivation:**  
   Why do wide-column databases exist? In what scenarios are they most useful (for example: large-scale datasets, time-series data, sparse data, or systems requiring high write throughput)?
A:
הסיבה שwide column databases קיימים זה מקרים שבהם שמירת המידע בצורה הזו יכולה להיות טובה יותר. לדוגמה, אם יש לנו מידע שכל שורה נותנת מידע על משתמש כלשהו ונוח לשמור את זה ככה, אבל לכל משתמש רלוונטי רק מספר כלשהו של פרמטרים מתוך רשימה ארוכה, אין טעם שנשמור בכל שורה את הפרטים של המשתמש בכל פרמטר אם לא נצטרך אותם, ובמקום להשאיר את האזורים האלו ריקים הרבה יותר אופטימלי שמלכתחילה יהיה משוייך רק את הדברים הרלוונטים. אז דוגמה אמיתית בה זה יכול להיות טוב זה שמירת metadata דינאמית. גם אם יש לי מערכת עם המון כתיבות עדיף לי להשתמש בwide column database כי זה יותר אופטימלי כי בrelational database המבנה ההיררכי של הb trees מגביל אותי. 


3. **Distributed Design:**  
   How do wide-column databases distribute data across clusters? Explain concepts such as partitioning, replication, and horizontal scalability.
A:
חלוקה זה לחלק את המידע לחלקים שונים, תתי חלקים של המידע המקורי. את המידע גם חשוב לשכפל כדי שלא ילך לאיבוד למקרה שנאבד עותק שלו. מעבר לכך, אנחנו מבזרים את המידע על שרתים שונים כך שמבחינת ביצועים העומס מתחלק בין שרתים שונים ואין bottleneck וגם אם שרת אחד נופל אז שרת אחר יכול לקחת את האחריות שלו. זה בדיוק הרעיון של horizontal scalability, להוסיף עוד nodes. 



---

### Part 2: Apache HBase (Implementation & Operations)

Answer these five questions to cover HBase’s major areas:

1. **Architecture & Data Model:**  
   Describe the overall architecture of Apache HBase, including tables, rows keyed by row key, column families, region servers, regions, and the storage format (HFile). How do these elements differ from a traditional relational database, and why is schema design driven by access patterns?

A:

בHbase יש לנו טבלאות שמורכבות מעמודות ושורות. שורות מאונדקסות לפי הrow key, עמודות מאוחדות ביחד כcolumn families. אוסף עמודות שמתארים איברים דומים ושמורים באותו אזור במערכת הקבצים. טבלאות מחולקות לregions, אוסף שורות רציפות מהטבלה. אם טבלה ממש גדולה אז אנחנו לא נרצה שכל הregions ירוצו על אותו שרת ולכן הם מבוזרים ורצים על region servers שונים. HFile הוא פורמט הקובץ שHbase משתמשת בו על מנת לשמור את המידע באחסון. ההבדל הוא שבDB רלציוני רגיל אנחנו עובדים לפי שורות ועמודות בצורה קבועה וכאן יש בחירה של איזה עמודות נמצאים בכל שורה ויכולים להיות שינויים, זה מתאים לדפוסי גישה כי ככה אנחנו אם יש מידע שמעניין אותנו פר שורה אנחנו יכולים לשמור רק אותו ולעבור עליו וגם להתאים את הcolumn families שלנו בהתאם לסוגי הגישות הרלוונטיות שאנחנו יודעים שיהיו לנו. אחרת היינו צריכים לעבור על הכל שורה שורה שהיה לוקח יותר זמן, וגם היינו שומרים מידע שלא לצורך אולי כblank. 

2. **Components & Storage Flow:**  
   Explain the roles of RegionServers, Regions, MemStore, HFiles, block cache, and the Write-Ahead Log (WAL). How does data flow from a client write to durable storage, and how are reads served from memory and disk structures? Understand what is the actual role of each component and its purpose. In addition, understand the formats of these components.
A:
המטרה של region servers היא להיות אחראים על הregions שנתנו להם ולהיות אחראים על פעולות הקריאה והכתיבה לregions. כשיש כתיבות הן בהתחלה מגיעות לWAL ומתווספות לסופו. זה הכל בדיסק, אבל בגלל שהכתיבה היא סדרתית ולא דורסנית זה יחסית מהיר. ככה שאם בשלבים הבאים תהיה קריסה כשהמידע בזיכרון, יהיה ניתן לחלץ אותו מאיפשהו. כעת המידע נשמר בMemstore,
בזיכרון. כשהMemStore מתמלא הוא עובר לדיסק כHfile שאינו ניתן לשינוי. בזכותו ניתן לגשת למידע שרק כרגע נכתב בלי הצורך להתעסק עם הדיסק. ניתן לשמור חלק מהבלוקים שניגשים אליהם הרבה (בלוקים "חמים") בזיכרון, באזור שנקרא הblock cache. מבחינת flow הכתיבה, די תיארנו אותה, רק נותר לציין שברגע שהMemstore מתמלא, גם יכול להיות לפני שהוא עובר לדיסק, נוצר אחד חדש לכתיבות הבאות ויש תהליך compaction שמאחד Hfiles. מבחינת הקריאה, אני מחפש לפי המפתח שלי. אם זה בMemstore אז מצויין. אחרת, אני בודק אם יש Memstores שמחכים לעבור לדיסק שכבר לא ניתנים לשינוי. אם לא מצאתי שם אני מתחיל לבדוק Hfiles מהחדש לישן. כל פעם אני בודק לפי הbloom filter, אם הוא טוען שהמפתח יכול להיות שם, אנחנו נבדוק לפי הsparse index ונעשה חיפוש בינארי עד שנגיע לבלוק אחד שזה יכול להיות בו. אנחנו יודעים שאם נמצא משהו הוא גם באמת יהיה הכי עדכני כי אנחנו בחיפוש שלנו מתחילים מהמידע הכי עדכני ולאט לאט הולכים אחורה! 


3. **Performance & Maintenance:**  
   What are minor and major compactions, MOB storage, Bloom filters, and caching? How do they affect read/write latency, storage efficiency, and amplification? Discuss the importance of row-key design and hotspot avoidance.
A:
כפי שהסברנו יש את הקונספט של compactions. כשמדובר על minor compaction הכוונה לאיחוד כמה Hfiles לHfile. בmajor compaction זה דומה אבל אותם column families נשמרים ביחד בקובץ החדש וכל הcells שסומנו כdeleted נמחקים. ברגע שקובץ הוא מעל 100KB, זה כבר דיפולטית לא מתאים לאפטום הרגיל של hbase. קבצים שגדולים מכך, שנקראים medium sized objects, או MOBS, פוגעים בביצועים. הוסיפו דרך ספציפית אל תוך Hbase 2 שיודעת להתמודד יותר טוב עם MOBs, צריך להשתמש בHFile בגרסה 3, ניתן לקנפג עמודות ספציפיות שיחזיקו מידע MOB. מבחינת המשתמש הוא לא יודע על זה, הכל שקוף עבורו. הרעיון של bloom filters הוא הסתברותי, כל פעם שמוסיפים key אנחנו מדליקים את הביטים שיוצאים מההאשים עליו. זה חוסך לנו משמעותית זמן בדיקה, כי במקום להכנס לכל Hfile בקריאה בחיפוש, אני בודק בעזרת הbloom filter כמה ביטים דלוקים מאלו של הkey על ההאשים. אם חלק לא דלוקים ברור שהוא לא שם, אחרת כן נבדוק. כמו שאמרנו caching מאפשר לנו לשמור בלוקים בזיכרון הנדיף למקרה שניגשים אליהם הרבה, כל הדברים האלו חוסכים משמעותית ומשפרים את הביצועים של קריאה וכתיבה, לוקחים בעיות שנוצרות באופן טבעי מהארכיטקטורה הכללית של Hbase ומוצאים להן פתרונות. הכוונה בhotspot זה שקריאות וכתיבות הן ממוקדות מאוד על region ספציפי, זה כמובן לא דבר טוב כי זה הופך את הregionserver הרלוונטי לbottleneck. יש כמה דרכים לזיהוי, לבדוק מי מקבל הכי הרבה בקשות לדוגמה, ויש דרכים שונות לחלק את הregions על מנת לפתור זאת. 


4. **Fault Tolerance & Coordination:**  
   How does HBase use WAL replay, region reassignment, and coordination via ZooKeeper to handle failures and maintain availability? What happens when a RegionServer crashes?
A:
אם יש לנו קריסה, אז אפשר להשתמש בWAL replay בכדי לראות מה הפעולות שהיו צריכות להתבצע וקרסו ולבצע אותן. אם region server קורס, zookeeper יזהה את כי יפסיק לקבל את הheartbeats שלו. הHMaster יקבל התרעה שהregion server נפל, ויתן את האחריות על הregions שלו לregion servers אחרים. עכשיו יש שאלה, איך נשיג את המידע שהיה בmemstore שעדיין לא היה בדיסק וכביכול נדיף והלך לאיבוד. אז הוא לא הלך לאיבוד, בזכות הWAL. הוא מחלק את הWAL של הregion server שקרס בין הregion servers החדשים בהתאם למה שהם קיבלו, ואז הם יוכלו לעשות לו replay. ואז אתם אומרים, מה אם Hmaster נופל? אז יש עוד כמה ממתינים שיחליפו אותו כי הzookeeper יזהה שהוא נפל, וזה לא הכי בעייתי שיש קצת downtime כי הלקוחות מתקשרים ישירות עם הregion servers.

5. **Scalability & Operations:**  
   Discuss how HBase scales horizontally through region splitting and balancing, how it relies on HDFS for durability, and what administrative actions (snapshots, backups, schema changes, recovery) operators perform in production environments.
A:
בזכות region splitting ואיזון hbase מסוגל לhorizontal scaling, המידע מתפצל לעוד regions שמחולקים לregion servers שונים וככל שיש יותר מידע יש יותר פיצולים ויותר שרתים. בזכות שHDFS נחשב מאוד סולידי לשמירת מידע ומבצע גיבויים רבים hbase מסתמך על שרידות המידע אצלו על מנת לתפקד. ניתן לבצע snapshot על טבלה, שזה תמונת מצב של הטבלה, כולל המידע בה והmetadata. ניתן לחזור לsnapshotים שנוצרו. חשוב להגיד שמבחינת מידע, כשיוצרים snapshot וחוזרים אליו אין צורך להעתיק מידע, כי כל המידע במצב הנוכחי כבר שמור או בתהליך של שמירה בHDFS באופן שלא ניתן לדריסה. לרוב עושים flush לדיסק כדי שלא נאבד את מה שעדיין לא בHDFS ובזיכרון, אבל אפשר גם בלי אם לא דחוף לנו מה שבזיכרון. ניתן לבצע גם backup שבו מכבים את כל הcluster. בנוגע לschema changes, הHmaster אחראי על כך, כשיש שינויים כאלו, אז הם מתבטאים כשיש major compaction מבחינת המידע שנשמר כבר, המידע נבנה מחדש בהתאם לכך.
### 🔄 Alternatives
Assignment: You are required to research and write a comparative analysis between HBase and an industry alternative.
- Deliverable: A written summary (minimum 1 or 2 sentences).
- Focus: Compare performance, architecture, and specific "pain points" this tool solves compared to legacy systems or competitors.
- Goal: You must be able to justify why the department uses this tool for our specific environment.

### 🎯 User Story & Scenario
Assignment: Based on your research and understanding of the department's pipeline, define a concrete Use Case for this technology.
- Deliverable: A written summary example/story (two paragraphs approx.).
- Requirement: Describe a real-world scenario (e.g., a specific client requirement) where this technology is the optimal solution.
- Data Flow: Map out the data flow and explain how this tool integrates with other components in the Data Pipeline.

## Wrapping Up :trophy:
Go over your answers with your mentor and clarify any uncertainties. Relate HBase concepts back to the broader data platform.

## Action Items
- Identify HBase topics you want to delve into further.
- Collect a list of real‑world HBase deployments or related technologies.
- Prepare questions for the next mentor Q&A session.

## Recommended Resources
- [Official HBase Reference Guide](https://hbase.apache.org/book.html) – the definitive documentation.
- *Hadoop: The Definitive Guide* (O'Reilly) – chapters on HBase.
