# Kerberos & LDAP

Kerberos and LDAP are core components for authentication and identity management in distributed systems.

Together, they enable consistent, secure identity handling across large-scale systems.

---

### ⏳ Timeline
Estimated Duration: 1 Day

Authentication & Directory Concepts: 
- Kerberos Authentication Flow  
- Core Kerberos Components  
- LDAP Structure & Operations  
- Basic Integration Concepts  
- Security Fundamentals  

---

### 📚 Resources
- [Kerberos: The Network Authentication Protocol](https://web.mit.edu/kerberos/)
- [LDAP: RFC 4511 Overview](https://datatracker.ietf.org/doc/html/rfc4511)

---

# Kerberos Core Concepts

### ❓ Guide Questions

1. **What is Kerberos, what problem does it solve, and what are its core components?**  

A: 
קרברוס הוא פרוטוקול אות'נטיקציה רשתי שמאפשר אות'נטיקציה בין לקוח לשרת בעזרת הצפנות והאשים. הוא פותר את הבעיה של אות'נטיקציה מעל רשת לא מאובטחת בכך שהוא מבטיח את האבטחתיות בעצמו. קרברוס כולל את הKey Distribution Center (KDC) שמכיל את הAuthentication Server (AS) ואת הTicket Granting Service (TGS). האות'נטיקה עצמה של הלקוח לAS מתרחשת באופן לא תדיר, כשעושים log in בהתחלה או כשפג תוקפו של הTGT. כשהלקוח צריך לתקשר עם שרת שאין לו כרטיס session אקטיבי איתו, הוא מתקשר עם הTGS על מנת להשיג אחד, בעזרת הTGT.

2. **How is Kerberos configured and managed in production? Explain keytab files, service principals, ticket lifetimes, renewal policies, etc.**  

3. **How does the Kerberos authentication flow work?**  


A:

ראשית המשתמש כותב את השם והסיסמה שלו. הלקוח במחשב של המשתמש שולח לAS את הID של המשתמש ביחד עם הזמן הנוכחי שמוצפן לפי הסיסמה של המשתמש שעברה hashing + salt. הAS מקבל את ההודעה ובודק אם הID קיים אצלו. אם כן, אז הוא מפענח את הזמן המוצפן לפי הסיסמה שעברה האשינג ששמורה אצלו. אם הוא מפענח והזמן תקין, כעת הוא יודע שהמשתמש זה מי שהוא טוען שהוא, אבל התקשורת עדיין תהיה מוצפנת לפי הסיסמה למקרה שהיה זיוף רגעי. הAS שולח ללקוח שני דברים:
1. הTGS session key שמוצפן לפי הסיסמה של המשתמש שעברה האשינג.
2. הTGT שמכיל את הID של המשתמש, הTGS session key, כתובת הרשת של המשתמש, הזמן הנוכחי, תוקף הTGT ועוד כמה דברים. הTGT מוצפן לפי סוד שרק הAG והTGS יודעים.
   הלקוח מקבל את כל זה, את 1 הוא יכול לפענח לפי הסיסמה של המשתמש והוא מקבל את הTGS session key. אין ללקוח דרך לפענח את ההודעה השנייה. כעת הוא שולח לTGS שני דברים:
   1. את הID של המשתמש ביחד עם הזמן הנוכחי, שניהם מוצפנים בעזרת הTGS session key
   2. הTGT המוצפן ממקודם
 הTGS מקבל את שתי ההודעות, הוא יודע את הסוד בשביל לפענח את הTGT. כעת הוא יכול בעזרת הTGT שמכיל את הsession key לפענח את ההודעה הראשונה, והוא מקבל עוד ID של המשתמש. הוא משווה ביניהם, אם הם אכן שווים, אז הוא שולח למשתמש שני דברים:
 1. את הsession key עם השרת שהמשתמש רצה לדבר איתו, מוצפן לפי הTGS session key.
 2. את הClient to server ticket (אותו דבר כמו הTGT רק מכיל את הserver session key במקום) שמוצפן לפי סוד שרק הTGS והשרת הרצוי יודעים
כמו מקודם, הלקוח יכול לפענח רק הserver session key בעזרת הTGS session key. הוא שולח לשרת הרצוי שני דברים:
1. את הID של המשתמש עם הזמן הנוכחי, שניהם מוצפנים בעזרת הserver session key
2. את הClient to server ticket המוצפן ממקודם
השרת מקבל את שניהם, מפענח את השני, בעזרת הserver session key שעכשיו השיג מפענח את הראשון, משווה את הIDים. אם הם שווים, הוא שולח ללקוח את הtimestamp מההודעה שהוא הראשונה שהוא שלח עכשיו מוצפנת בעזרת הserver session key, כדי שהוא יוכל לסמוך עליו שזה אכן השרת. מפה הלקוח והשרת מתקשרים בעזרת הserver session key באופן מוצפן.


5. **Why is Kerberos considered secured? What potential issues could arise with this mechanism?**

---

# LDAP Core Concepts

### ❓ Guide Questions

1. **How is LDAP used for authentication and authorization, and what is the difference between the two?**

2. **What operations does LDAP support?**  

3. **What is an LDAP schema and why is it important?**  

4. **What is LDAP and how is data structured within it?**

5. **Why is LDAP important for security, and how is it used for authentication and identity management in real-world systems?**

6. **How can Kerberos be integrated with LDAP or other directory services in a real deployment?**  

---

### Skila Questions
 
- Explain how user information is stored and accessed from a central directory  
- Describe how this improves security and organization
