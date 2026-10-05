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

4. **Why is Kerberos considered secured? What potential issues could arise with this mechanism?**

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
