# Apache Hive 🐝

## Overview

Hive includes several cooperating responsibilities. As you research, distinguish catalog and metadata management, SQL planning, distributed compute, and storage. Identify which Hive or Hadoop component performs each responsibility and how the components interact with one another.

> **Mental model:** HMS answers *“What is the table and where is it?”* The query engine answers *“How will this query run?”* Storage answers *“Where are the bytes?”*

### ⏳ Timeline
Estimated duration: 2 Days 

Complete the research assignments, record the sources you used and the questions that remain open, and review your findings with your mentor.

## Research Assignments

### 1. Hive's Purpose and Place in the Ecosystem

Research why Apache Hive was created and where it fits in the Hadoop ecosystem. Explain the types of workloads it is designed for, the abstractions it adds above distributed storage, and how its approach differs from traditional database systems.

A:
הסיבה שפיתחו את Hive זה לפתור את הבעיה הבאה: נניח אני רוצה להשתמש בMapReduce כדי לבצע פעולות בHDFS, אז אני צריך לכתוב קוד Java מתאים עבור זה, גם עבור דברים פשוטים שבSQL זה יכל להיות ממש פשוט וקל. פייסבוק רצו שהעובדים שלהם שידעו לעבוד טוב עם SQL יוכלו לעבוד עם HDFS, ולכן Hive הומצא כדי לאפשר את הגוף האמצעי הזה. מבחינת הecosystem, זה לא DB, כי Hive שומר אצלו רק metadata המידע עצמו נשמר בHDFS. כשמדברים על Hive זה יותר שייך לdata processing, לעבד את המידע עצמו, לתשאל אותו ולהפיק דברים. מותאם לעבודה עם כמות מאוד גדולה של מידע ומייעל בעצמו את הפעולות שנשלחות. מאפשר ממשק SQLי לעבוד איתו של טבלאות ושפה SQLית. שוב מה שמפריד אותו מDB זה שהמידע עצמו לא נשמר אצלו, אלא בHDFS. 

### 2. Hive Architecture

Draw the architecture of a production-style Hive environment. Include every component required for a client to submit a query, obtain metadata, execute distributed work, and access table data. Explain the responsibility of each component as well as every connection, protocol, deployment boundary, and important failure scenario.

Your explanation should make clear which responsibilities belong to the catalog, query processing, distributed compute, and storage. Explain which component manages metadata, which components plan and coordinate queries, and which components perform the distributed computation.

- Pay particular attention to **decoupling** in Hive's architecture. Explain which responsibilities can operate independently and why separating metadata management from query execution is useful.

A:
נדבר על כל חלק לוגי בארכיטקטורה של Hive. בקצה אחד יש את הלקוח, Hive תומכת בפניה אל Hive מכמעט כל שפה ודרך Thrift מה שמאפשר גמישות רבה. את הפעולות עצמן אנחנו יכולים לעשות בBeeline שהוא shell, שמגיעות לHive Server 2 וכך הן בעצם מגיעות אל תוך הHive. אחרי זה יש את הHive Driver שמקבל את הבקשות של המשתמש ויוצר טיפול חדש עבורן, הוא מוודא שהבקשות יסתיימו ומחזיר לך את המידע בסוף. הוא מורכב מכמה חלקים, יש את הparser שמוודא שהסינטקס הSQLי חוקי, יש את הקומפיילר שממיר את הקוד הSQLי לתוכנית תכנון עבור הmapreduce, הOptimizer ישכתב את התוכנית ויחפש אפשרים לייעל אותה, משם זה מגיע לexecutor, נגיד mapreduce שיעשה את הפעולות שאנחנו רוצים. בנוסף לדרייבר, יש את הmetastore שמכיל סכמות של טבלאות, הגדרות של חלוקות, סוגי עמודות, מיקומים של אחסון בתוך HDFS, סטטיסטיקות ועוד. יכול לרוץ לגמרי בנפרד, המידע מגובה באופן לא נדיף בrelational database. הרעיון של להפריד את הmetadata הוא שימושי כי כך ניתן לגשת לmetadata גם באופן נפרד ללא צורך לעבור בflow המלא. זה בסיוק תומך ברעיון של decoupling, הרכיב שמתרגם את הSQL להנחיות יודע לעבוד בפני עצמו, הרכיב שמייעל הנחיות יודע לעבוד בפני עצמו והmapreduce יודע לבצע את הפעולות בפני עצמו. ביחד הם נותנים לנו את הflow של העבודה. 
### 3. Hive Tables and Storage

Choose one Hive table and investigate its complete lifecycle: definition, schema, storage location, file format, data loading, schema changes, querying, and deletion. Demonstrate how its logical definition relates to the files and directories in storage, and explain the important design choices at each stage.

A:
נתרכז בexternal tables. הרעיון בexternal tables זה שניהול המידע עצמו מתרחש בHDFS והHive אחראי רק על ניהול הmetadata, לא ניהול המידע עצמו. אם אני מפיל את הטבלה, אז רק הmetadata נופל, המידע הHDFSי עצמו נשאר שם. מבחינת המיקום, נותנים אותו בעת יצירת הטבלה תחת המשתנה LOCATION. הסכמה מתקבלת גם בעת יצירה. בניגוד לmanaged tables, המידע לא שמור בתיקייה של הwarehouse, אלא במקום חיצוני אחר. הלוגיקה פה מאוד מתאימה לאיך שהעניין מאוחסן, הרי אנחנו רוצים הפרדה בין המידע עצמו לHive, לכן המידע עצמו נשמר אם הטבלה נמחקת בHive והמידע לא נשמר תחת תיקייה של Hive, כך שמספר תוכנות יכולות לעבוד על המידע בלי לפגוע בתפקוד של Hive. אנחנו יוצרים הפרדה בין הcompute לstorage כך שלא יהיה לנו coupling. אפשר להריץ MSCK REPAIR TABLE כדי לעדכן לhive את הmetadata במקרה שתוכנה אחרת שינתה את המבנה של הטבלה. 



### 4. Hive Partitioning

Research partitioning in Hive and how Hive implements it. Explain how a partitioned table is defined, how partitions affect the filesystem directory structure, and how partitioning can improve or harm query performance. Include the trade-offs involved in choosing partition columns.

A:
הרעיון הוא לחלק את הטבלה לכמה חלקים לפי פרמטרים. מבחינת המימוש, בHDFS יווצרו תיקיות לכל חלק בחלוקה. כשנריץ אחרי זה WHERE שרלוונטי לדברים שחילקנו, אז Hive יקרא את המידע רק בתיקייה הרלוונטית, ובכך אנחנו חוסכים חיפוש ארוך במערכת הקבצים. יש שני סוגים של חלוקה, סטטית ודינמית. בסטטית אנחנו אומרים במפורש את החלוקה ובדינמית Hive מבין לבד לפי המידע. גם מבחינת ניהול המידע זה נותן לנו את היתרון שמעבר למהירות המידע מסודר יותר בHDFS עם היררכיה מתאימה. בכל זאת יש תקורה שערכים שונים על הפרמטר נשלחים לתיקיות שונות, מה שיוכיל להוביל להמון קבצים מעבר מה שצריך שאת זה אנחנו מאוד לא אוהבים בHDFS, לכן לרוב נרצה לעשות את זה כשמספר הערכים האפשריים עבור הפרמטר הוא לא גדול מדי. 

### 5. Dedicated MapReduce Exercise: WordCount

Explain how the classic MapReduce WordCount program works from input to final output. Describe the mapper input and output, shuffle and sort, reducer input and output, and which operations run in parallel. Use concrete key/value pairs in your explanation. Then explain why executing a multi-stage Hive query with MapReduce can be expensive and how Tez differs.

A:

בתור קלט אנחנו מקבלים המון קבצים מבוזרים שמכילים מילים שונות, אנחנו רוצים בסופו של דבר להכריע מה מספר המופעים של כל מילה. כל פונקציית מיפוי על אוסף של קבצים בשרת מבוזר סופרת את המופעים של כל המילים שם ופולטת key value pair של המילה ומספר המופעים שלה. התוצאות של כל המיפויים עוברים ערבוב כך שהזוגות שחולקים את אותו המפתח ביחד. פונקציות הreduce עוברות על כל אוסף כזה וסוכמות את מספר המופעים עבור כל אחד בכך שהן סוכמות את המספרים עבור כל key ספציפי. לבסוף כל התוצאות האלו ממופות לקובץ אחד. פעולות הmapper ופעולות הreducer מתרחשות באופן מקבילי מכיוון שהן עובדות על קבצים שונים בשרתים שונים. בTez עובדים עם DAGים כאשר המשימות הם קודקודים שמחוברים בקשתות. ככה ניתן לייצג את הפעולות שצריך לבצע בצורה יותר יעילה וניתן להסתכל בפרספקטיבה יותר מורחבת שמאפשרת אופטימיזציות באיחוד פעולות. 
### 6. Fault Tolerance

Research fault tolerance in Hive. Consider failures in the services involved in metadata management, query coordination, distributed execution, and storage. Explain which failures a query can recover from, which failures cause it to fail, and what makes recovery possible.
A:
מבחינת אחסון, אנחנו יודעים שהמידע מאוחסן בסופו של דבר בHDFS ובבחינה הזו הfault tolerance של HDFS מגן עלינן. כשמדובר בביצוע בקשות, בHive יש דרך כך שאם בקשה נכשלת אז היא מנוסה מחדש. בנוסף אמרנו כבר קודם שיש שכפול של הmetadata. בזכות העובדה שאנחנו יכולים לעבוד עם Tez ולא Mapreduce, אז אנחנו יכולים לבצע מספר פעולות כjob אחד שלא צריך לעבור רכיבים, מה שחוסך לנו את השמירת מידע באמצע. מצד אחד זה חוסך יותר זמן, אבל מצד שני זה גורם לכך שיש לנו פעולה אחת ארוכה לא מגובה ואז אם היא קורסת באמצע אנחנו חייבים להתחיל אותה מהתחלה מאשר אם היא הייתה מחולקת באופן ברור ל2 כמו בMapreduce שהחלקים בלתי תלויים ואין צורך לחזור על השלב הקודם. כשיש cell שמזוהה כבעייתי, מה שנעשה על ידי cells אחרים, הם צריכים להסכים שהוא בעייתי ולאתחל אותו. עוד עניין חשוב הוא שבזכות העובדה שכל הרכיבים מאוד מופרדים בHive אז נפילה זמנית של רכיב אחד לא בהכרח עוצרת את הכל כי יש רכיבים שמסוגלים לתפקד באופן עצמאי, כך שיכולה להווצר המתנה לרכיב הקריטי אבל זה לא שלא תתבצע עבודה בזמן הזה. 

### Skila questions

1. What is the name of the service that keeps the meta in hive?
זה הHive meta store

2. What is DDL and DML?
  שפת DDL, שהיא Data Definition Language היא שפה שמכילה פקודות SQL שקובעות את המבנה של הDB, לדוגמה ליצור טבלה, ליצור עמודה וכו'. לעומת זאת, שפת DML, שהיא Data Manipulation Language היא שפה שמכילה פקודות SQL שמשפיעה על האיברים בתוך המבנה. כמו לערוך איבר בתוך הטבלה, להוסיף שורה חדשה. 

3. What is required in OLTP and OLAP and what keeps Hive from being OLTP?
בOLTP חשוב שיהיה לנו פעולות real time ותמיכה בכמות גדולה מאוד של פעולות קטנות. לעומת זאת, בOLTP חשוב שתהיה לנו תמיכה בשאילתות מסובכות עם המון מידע. יש בעיה שHive לא יכול לתמוך בOLTP כי הוא לא real time, הוא לא מותאם לקבל כמות גבוהה של כתיבות ולבצע אותן בזמן מהיר. 
4. Does Hive support transactions? Is Hive ACID?

יש בHive אופציה לtransactions, והtransactions בHIVE הן עוקבות אחרי ACID. על מנת לבצע אותן, צריך שהטבלה תהיה bucketed ולהדליק את האופציה לtransactions.
5. Is Thrift implemented upon RPC?

6. What is the difference between Hiveshell and Beeline?

7. Why did Hadoop choose Thrift and not REST?

8. What are the two ways with 4 letter names to connect to Hive?

9. What is the output of the parser, what does it change in its input after it verifies it's correct?

10. What type of optimizations can the optimizer do?

11. What is the redundancy and what are the extra steps in mapreduce hurting it?

12. What is the lifecycle of a job managed by YARN, what is the fault tolerance?

13. What is a table format?

14. What is ERD? What is the ERD of Hive meta store?

15. What is Hive used for today?

16. How do I connect to Hive meta store?



## Optional Hands-On

Complete the [Docker Compose lab](./Hive%20Hand%20On%20(optional)/README.md) to inspect catalog and query services separately, create an external table, run `EXPLAIN`, and observe metadata and data lifecycles.

## Trainee Completion Checklist

- [ ] I can explain why Hive exists and the workloads for which it is designed.
- [ ] I can draw Hive's architecture and distinguish catalog, query processing, compute, and storage.
- [ ] I can explain decoupling between metadata management and query execution.
- [ ] I can trace the lifecycle of a Hive table from its logical definition to its physical data.
- [ ] I can explain how Hive partitioning is represented in the table definition and filesystem.
- [ ] I can explain WordCount, especially shuffle and sort, and compare MapReduce with Tez.
- [ ] I can explain how Hive's components handle failures and where fault tolerance comes from.
- [ ] I recorded the sources I used and my remaining questions for the mentor.

## Recommended Resources

- [MapReduce video](https://youtu.be/cvhKoniK5Uo?si=MGoozk3SU-uOCGEA) — **Recommended viewing while learning about MapReduce.** It provides a visual explanation to reinforce the map, shuffle and sort, and reduce stages.

Review your research and checklist with your mentor. The chapter is complete when you can explain the system in your own words and diagnose a problem by layer instead of only saying that “Hive is broken.”
