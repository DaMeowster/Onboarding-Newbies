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

2. **How does Zookeeper handle consistency and notifications?**  
   Explain:
   - Sequential consistency  
   - Watches  
   - One-time triggers  
   - How clients use watches

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
