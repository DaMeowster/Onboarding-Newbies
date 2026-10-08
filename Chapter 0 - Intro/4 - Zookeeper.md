# Zookeeper Foundations

Zookeeper is a distributed coordination service.

Instead of building coordination mechanisms from scratch, systems rely on Zookeeper.

---

### ⏳ Timeline
Estimated Duration: 0.5 Day

Zookeeper Core Concepts:
- Architecture & Ensemble Roles  
- Znode Data Model  
- Consistency & Watches  
- Sessions & Failure Handling  
- Common Distributed Patterns  
- Failover and Leader Elections

---

### 📚 Resources
- [Apache Zookeeper Documentation](https://zookeeper.apache.org/)
- [Zookeeper Recipes and Solutions](https://zookeeper.apache.org/doc/current/recipes.html)

---

# Zookeeper Core Concepts

### ❓ Guide Questions

1. **What is Zookeeper, and how does its architecture organized?**  
תוכנת zookeeper היא תוכנה לסינכרון בין מערכות מבוזרות. היא מאפשרת fault tolerance ומהווה חלק משמעותי מHbase. הארכיטקטורה מסודרת בצורה הבאה:
יש לנו את האפליקציה שלנו וכל לקוח מתחבר ללקוח zookeeper שמאפשר לו לדבר עם שרתי הzookeeper. אם zookeeper בשרת אחת ולא מבוזר אז יש לנו point of failure מאוד גדול לכן לרוב יש ביזור. האוסף ביחד נקרא zookeeper ensemble. כחלק מהביזור יש גם רפלקציה של המידע בשביל fault tolerance, ואם עכשיו לקוח יבקש לכתוב משהו לznode וירצה fault tolerance של עותק נוסף בשרת אחר, אז הוא יקבל קונפירמציה שהכתיבה נעשתה רק אחרי שבאמת נעשו כל הכתיבות, כולל העותקים הנוספים באופן מבוזר.


2. **How does Zookeeper handle consistency and notifications?**  
   Explain:
   - Sequential consistency  
   - Watches  
   - One-time triggers  
   - How clients use watches
Zookeeper handles consistency and notifications in a smart matter. Regarding notifications, a client can register for notifications on a znode using 'watches'. This saves the client from continuously asking about the znode and checking its state.
Zookeeper promises sequential consistency, meaning, if a client does actions in some order the orders will be applied by that order. 
When using watches in zookeeper in general, they are one time triggers. Meaning you will get notified, but then the watch ends. To get a notification in the future you must apply it again. 

3. **What are Znodes and what types of Znodes exists?**

הznodes הם האיברים בעץ הzookeeper. הם מכילים מידע ומנהלים stat שמכיל מידע תאורטי כגון גרסת המידע מבחינת שינויים עם פרטים על הזמנים. יש שני סוגים של znodes. יש persistent znodes שנשארים שמורים עד שמוחקים אותם באופן מפורש ויש ephemeral znodes שנמחקים אם הלקוח שיצר אותם מאבד חיבור עם הzookeeper.
4. **What are sessions, and how does Zookeeper handle failures and node lifecycle?**  
   Explain:
   - Session lifecycle  
   - Heartbeats  
   - Session expiration  
   - Persistent nodes  
   - Ephemeral sequential nodes
   - Failover
   - Leader elections
   - ZXID


5. **What are the basic operational concerns in Zookeeper?**  
   Describe at a high level:
   - Ensemble deployment  
   - Scaling considerations  
   - Snapshots and transaction logs
   - Common issues 

6. **Which architectural patterns is ZooKeeper commonly used to implement?**

---

### 🔄 Alternatives
Assignment: Compare two coordination approaches:

- Zookeeper vs Alternatives

Deliverable:
- 1–2 sentences comparison  
- Include a simple use case for each  


---

### 🎯 User Story & Scenario

Assignment: Describe a simple real-world coordination scenario.

Deliverable (2 paragraphs)
