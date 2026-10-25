# Apache Iceberg

Now that you are familiar with the concepts of catalogs and the metastore,
and understand the critical separation between how data is stored versus how it is logically organized,
it is time to move from theory to the practice of the modern data world.

Meet Apache Iceberg – the table format we use as the storage layer in our lakehouse.
designed to solve the consistency and performance "pains" of legacy directory-based systems.

###⏳ Timeline
Estimated Duration: 2 Days
- Day 1: Independent research and deep dive into the foundations of Iceberg.
- Day 2:
  - (Morning): First Q&A.
  - (End of Day): Question Answering & Final Q&A.

### 📚 Resources
Use the resources listed below and practice searching the internet for questions not answered by the provided documentation.
- [Apache Iceberg Official Docs](https://iceberg.apache.org/docs/latest/#documentation)
- [Apache Iceberg Definitive Guide](https://www.dremio.com/wp-content/uploads/2023/02/apache-iceberg-TDG_ER1.pdf)
- [Meap Book - Apache Iceberg](./asstes/Architecting_an_Apache_Iceberg_Lakehouse_v3_MEAP.pdf)

### Guide Questions❓
Please use these questions as a guide for your research, dive in, and deepen your understanding of all concepts.

1. What is Apache Iceberg? 
   Explain the problems it solves compared to Hive tables (schema evolution, partitioning, consistency, performance).

כשמדברים על Apache Iceberg מדובר על table format. הכוונה בtable format זה סוג של מתרגם אמצעי בין הcompute לstorage, שמאפשר לנו למפות איזה קבצים שמורים בטבלה, איזה חלוקות יש ואיזה קבצים יש בחלוקה ועוד. בזכות הקונספט הזה של table format שגם הcompute וגם הstorage יודעים להבין, נוצר לנו אי-תלות בין הcompute לstorage וניתן להחליף אותם באופן חופשי. יש כמה דברים שIceberg פותר ביחס לHive. בIceberg, הכל ACID. אם בHive היו לנו המון תנאים ספציפים שתחתיהם ניתן לקבל אסידיות, פה זה תמיד מתקיים. בנוסף לכך, בIceberg מתאפשר schema evolution שהוא גם אוטומטי בניגוד לHive שצריך פקודות ידניות ומעבר לכך הוא יכול להתבצע בלי לשנות את המבנה של האחסון בשכבה הנמוכה, מה שהיה יכול להתרחש בHive. גם הביצועים יותר טובים, לאור העניין ששמות הקבצים שמורים בmetadata ולא רק הdirectories כמו בHive, זה חוסך לנו פעולות ls רבות בחיפוש הקובץ שיכולות להיות כבדות מאוד אם יש לנו המון קבצים בתיקייה. עוד עניין ביצועי, זה שכתיבות לא חוסמות קריאות. לאור העובדה שבכתיבה אנחנו מעתיקים מידע ומשנים את המידע המועתק, אז ניתן לבצע קריאות כתיבות במקביל. עד שהמידע מסתיים לכתוב הפוינטר של המידע הוא למידע המקורי וברגע שהכתיבה נגמרת הפוינטר משתנה להיות על המידע המעודכן. ניתן גם לשנות את סוג הpartitioning באופן דינאמי ללא הצורך להכין את הטבלה מחדש.

2. Describe the Apache Iceberg table architecture. 
   Explain metadata files, manifest files, data files, and snapshots and how they relate to each other.


3. What is an Iceberg catalog, and what is its role? Explain what a catalog manages (table namespace, metadata pointers, commits), why it’s required, mention common catalog implementations, and explain how it differs from the Hive Metastore implementation. Describe how catalogs and formats enable multiple compute engines to work on the same data.


4. How does Iceberg handle concurrent reads and writes? 
  Explain snapshot isolation, atomic commits, optimistic concurrency control, and conflict detection.


5. What maintenance operations does Iceberg require, and why? 
   Discuss compaction, snapshot expiration, orphan file cleanup, and metadata cleanup.

### 🔄 Alternatives
Assignment: You are required to research and write a comparative analysis between Iceberg and an industry alternative.
- Deliverable: A written summary (minimum 1 or 2 sentences).
- Add real life usecase 
- Focus: Compare performance, architecture, and specific "pain points" this tool solves compared to legacy systems or competitors.
- Goal: You must be able to justify why the department uses this tool for our specific environment.

### 🎯 User Story & Scenario
Assignment: Based on your research and understanding of the department's pipeline, define a concrete Use Case for this technology.
- Deliverable: A written summary example/story (two paragraphs approx.).
- Requirement: Describe a real-world scenario (e.g., a specific client requirement) where this technology is the optimal solution.
- Data Flow: Map out the data flow and explain how this tool integrates with other components in the Data Pipeline.
