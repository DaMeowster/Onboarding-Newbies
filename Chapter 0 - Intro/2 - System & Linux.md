MAMAS EMPIRE
# Linux & Infrastructure Foundations 💻💾

Linux is the foundation of modern infrastructure, servers, cloud platforms, containers, and distributed systems.

Understanding Linux fundamentals helps engineers troubleshoot systems, manage resources, automate operations, and understand how modern platforms work internally.

---

### ⏳ Timeline

Estimated Duration: 1 Day

Linux & Infrastructure Core Concepts:

* Linux Architecture & Kernel Basics
* File Systems & Inodes
* Processes, Daemons & Privileges
* System Initialization (systemd)
* cgroups & namespaces
* Root File System & Mounting
* Basic GNU/Linux Utilities
* Vim & Nano Basics

---

### 📚 Resources

* [Linux Journey](https://linuxjourney.com/?utm_source=chatgpt.com)
* [The Linux Documentation Project](https://tldp.org/?utm_source=chatgpt.com)
* [systemd Documentation](https://systemd.io/?utm_source=chatgpt.com)
* [GNU Core Utilities Manual](https://www.gnu.org/software/coreutils/manual/coreutils.html?utm_source=chatgpt.com)
* [Vim Documentation](https://www.vim.org/docs.php?utm_source=chatgpt.com)
* [Intro to OS mini book](https://fantastic-couscous-vjv74gqjj9w2wq74.github.dev/)

---

# Linux & Infrastructure Core Concepts

### ❓ Guide Questions

1. **What are the layers of an operating system, and how do they interact with each other?**

   Explain:
   * The hardware, kernel, system services, and user-space layers
   * How these layers work together to provide a complete operating system
   * What the kernel does and why it is the core of the OS
   * The difference between system services and applications
   * The role of kernel modules and device drivers
A:

בשכבה הכי נמוכה יש לנו את החומרה עצמה. מעליה יש את מערכת ההפעלה שמכילה את הkernerl space ואת הuser space, שבתוך הuser space התהליכים שלנו רצים. 
השכבות עובדות ביחד כל הזמן. נגיד אם אני מריץ קוד בuser space, מקבל פסיקה של חלוקה ב0, עובר למצב גרעין כדי לטפל בה, המעבד בחומרה מטפל בפסיקה לפי שגרת הטיפול. 
המטרה של הקרנל זה לחבר בין החומרה לתהליכים שאנחנו מריצים, הוא אחראי על ניהול הזיכרון, שליטה ותזמון בתהליכים, עבודה עם התקנים חיצוניים, ניהול פעולות I/O, אבטחה וסינכרון. בגלל שהוא מחבר בין הuser space אל החומרה, הוא ממש הלב של מערכת ההפעלה, הוא מאפשר את הקישוריות הזאת בין איפה שהתהליכים שלנו רצים לחומרה. 
בנוגע לsystem services, כלומר daemons, ההבדל ביניהם לאפליקציות זה שאפליקציות היא תוכנה עם מטרה. system service הוא מופע של תכנית שרצה ברקע.
מודולים מאפשרים לנו להוסיף לגרעין לינוקס קטעי קוד חדשים בזמן ריצה. זו בעצם ספריה משותפת שטוענים בזמן ריצה ופועלת בkernel mode. השימוש העיקרי במודולים זה להוסיף תמיכה בהתקני חומרה שונים בעזרת דרייברים. החשיבות של דרייברים עצומה, אם אני רוצה לחבר מקלדת למחשב שלי אני צריך דרייבר כדי שהמחשב ידע לעבוד עם המקלדת. זה שיש לנו מודולים זה יתרון ענק, אפשר להעביר קוד שהיה יושב בגרעין למודולים וכך לחסוך שורות קוד בגרעין ולהקטין את זמן הקומפילציה (שכבר ארוך מדי) ואפשר להוסיף יכולות חדשות לגרעין בלי לקמפל אותו מחדש ובלי לאתחל את המערכת.

2. **How do processes and daemons work in Linux, and how do threads fit into this model?**

   Explain:
   * Processes vs daemons
   * Process lifecycle and basic process attributes such as PID and PPID
   * Privileges, the root user, and why permissions matter
   * Threads and how they differ from processes
   * Signals such as `SIGTERM`, `SIGKILL`, and `SIGHUP`
   * How to inspect processes with `ps`, `top`, `htop`, and `pstree`
   * How services are started and managed with `systemd` and `systemctl`
  
A:
תהליך הוא מופע של תכנית שרצה ועושה משהו. daemon זה תהליך שרץ ברקע. תהליך יכול להיות בכמה מצבים:
ready, running, waiting, zombie
נעבור על כל אחד מהם
בready התהליך עדיין לא רץ אבל הוא מוכן להתחיל לרוץ כשיקראו לו.
בrunning התהליך רץ
בwaiting התהליך כרגע ישן, אם לדוגמה התהליך מחכה למשאב כלשהו שהוא לא יכול להמשיך לרוץ בלעדיו.
בzombie התהליך נגמר, אבל עדיין לא נאסף על ידי הורה/הורה מאמץ.
לכל תהליך יש process id בשם PID שמזהה אותו מיצירתו עד שהוא נאסף כזומבי. PID יכול להיות ממוחזר, כלומר ברגע שתהליך נאסף תהליך חדש יכול להיות עם PID של תהליך קודם. לכל תהליך יש אב, והPPID הוא הPID של אבא שלו. אם אבא שלו נהרג (באסה) אז init מאמץ אותו ולכן הPPID נהיה 1 כי זה הPID של init.

מבחינת הרשאות, על כל קובץ יש הרשאות של יוצר הקובץ, קבוצה מסוימת, וכל השאר. לכל אחת משלושת הקטגוריות מוגדר האם ניתן לקרוא את הקובץ, האם ניתן לערוך את הקובץ והאם ניתן להריץ אותו. חשוב שיהיו הרשאות כדי שלא לכולם תהיה גישה חופשית לעשות מה שהם רוצים, בקריאה אם מדובר בסודיות, עריכה אם מדובר בתכנים חשובים, הרצה יכולה להתבטא גם בבזבוז משאבים כבד. מכל אחת מהסיבות האלו ובגדול ממידור, חשוב שתהיה הפרדה ברורה של מי יכול לעשות מה. root הוא משתמש עם רמת ההרשאות הגבוהה ביותר, אי אפשר להתחבר אליו כמשתמש רגיל (אלא אם כן אתה מגה חנון ועושה passwd וsu) ואפשר לרוץ ברמה של ההרשאות שלו בעזרת sudo. לא אכפת לroot מה ההרשאות של הקבצים, גם אם הוא לא יופיע בהרשאות המפורשות של הקובץ כפי שעכשיו דיברנו כבעל יכולת, הוא עדיין יוכל לעשות את שלושת הפעולות.

אם תהליך הוא מופע של תכנית שרצה ועושה משהו, אז thread (נורא להגיד תהליכון) הוא קטע קוד שרץ כחלק מהתהליך. בתהליך יכולים להיות מספר threads שרצים במקביל.
יש סיגנלים שונים שמגיעים לתהליכים, תהליך יכול להתייחס או להתעלם מהסיגנל, אפשר להוסיף signal handler שיגרום לתפעול שאנחנו רוצים. יש שלושה סיגנלים שאי אפשר להתעלם מהם:
SIGKILL, SIGSTOP, SIGCONT

SIGKILL - הורג את התהליך
SIGSTOP - מרדים את התהליך, מעביר אותו למצב המתנה
SIGCONT - מעיר את התהליך, מעביר אותו למצב מוכן

לSIGCONT כן טכנית אפשר לשים signal handler, אבל התהליך עדיין יעבור לready ורק אז הhandler ישירות יקרה. עוד כמה סיגנלים:
SIGTERM - מבקש מהתהליך להגמר, יותר עדין מקיל בגלל שהתהליך יכול לבצע פעולות לפני כן כדי להעצר בצורה 'נקייה', בגלל שזה לא חלק מה3 הקודמים התהליך יכול להתעלם מהסיגנל לחלוטין
SIGHUP - סיגנל שנשלח לתהליך כשהטרמינל ששולט בו נסגר

יש פקודות לינוקס שבעזרתן אפשר לקבל מידע על תהליכים. לדוגמה, ps מאפשר לראות מידע כללי על תהליכים שונים. סוג המידע וסוג התהליכים תלוי בדג  לים, top מאפשרת לראות מידע על המערכת כמו עומס, כמה תהליכים יש, באיזה מצבים הם. ניתן גם להשתמש בדגלים כדי לראות מידע על threads במערכת. htop דומה מאוד לtop אבל מאפשרת אינטרקטיביות, גם בגלילה באופן אופקי ואנכי וגם בלהציג אותם בפורמטים שונים וסימון חלק מהם לביצוע פעולות. pstree מראה לי את כל התהליכים בהיררכיה עצית של צאצאים. אם אני לא נותן לפקודה pid זה יתחיל מroot, אחרת זה יתחיל כשראש העץ הוא התהליך עם הpid המדובר. אם אני אתן שם של משתמש, זה יראה לי את כל העצים שמתחילים מתהליך שמשוייך למשתמש הזה. 

התהליך של systemd הוא התהליך הראשון שרץ, מתנהג כמו init ומריץ ומנהל את תהליכים בuser space כחלק מהboot process בעזרת קבצי קונפיגורציה קיימים. למזלנו אין שום דיונים וריבים על systemd וכולם שמחים מזה. 
בגלל שsystemd הוא daemon אז כדי לשלוט בו ולעבוד איתו משתמשים בפקודות systemctl.
 
3. **How does Linux isolate workloads and control resources?**

   Explain:
   * cgroups and namespaces
   * CPU, memory, and I/O isolation
   * Why containers rely on these mechanisms
   * Practical examples such as `systemd-run --scope`, `unshare`, and `nsenter`
   * Network namespaces and virtual interfaces
   * Resource monitoring and troubleshooting basics
  
A:
בזכות namespaces, לינוקס מסוגל לחלק משאבי גרעין כך שתהליכים יראו משאבים שנמצאים רק בnamespace שהם משויכים עליו ולא באחרים, מה שיוצר לנו הפרדה. 
המונח Control Group (cgroup) מתאר את האופציה להגביל את המשאבים של קבוצת תהליכים. אני יכול להגביל את המשאבים של תהליך, של תהליך ביחס לקבוצה אחרת. גם אפשר לשנות את מצב הריצה של כל התהליכים בקבוצה בקלות וניתן לראות את תפיסת המשאבים של קבוצה.
כל הדברים האלו נכנסים מאוד לחלוקה ומידור של המשאבים, בזכות הכלים האלו אני מסוגל ליצור הפרדה בחלוקת המשאבים שאני קובע ובכך למנוע התנגשויות בין תהליכים שונים שיכולים לפגוע לנו בנכונות המערכת, ליצור בעיות תזמון וגם מאפשרות פריצות אבטחה. אם כל המשאבים לא היו מופרדים, אז התהליך באתר החשוד שאני נכנס אליו בגוגל היה מסוגל לקרוא את הסודות בתיקיות שלי. קונטיינרים תלויים ברעיונות האלו בגלל שכל המהות של קונטיינר זה שאני לוקח את התוכנה שלי וכל מה שהיא צריכה בשביל לרוץ ומריץ אותה במקום מבודד. בזכות העובדה שיש לה את כל מה שהיא צריכה כדי לרוץ אני יכול להריץ אותה בכל מקום והתכנית שלי הופכת להיות מאוד פורטבילית. אם לא היה לנו את היכולת הזאת של מידור משאבים לא היינו יכולים להבטיח את הבדידות של הקונטיינר והרעיון היה נופל. כי אם אין בדידות, אני צריך להתחיל לחשוב על איך התכנית שלי תתמודד עם קשיים חיצוניים שעלולים לקרות לה כתוצאה מהמרחב המשותף עם תוכניות אחרות, ואז כבר אין לי פורטביליות, זה נהיה תלוי סביבה. 
systemd-run --scope 
מריץ לי את הפקודה שאתן לו בscope unit, מקום מופרד מאיפה שהתהליכים הרגילים, עם היררכיית תהליכים שונה.
unshare 
יוצר לי namespace ומריץ לי שם את התוכנה שאני נותן לו. אם לא נתתי לו תוכנה, הוא יריץ את הshell בnamespace החדש. באופן דיפולטי הnamespace יחזיק עד שכבר לא ירוצו בו תהליכים, אבל אפשר ידנית לגרום לו להיות עמיד.
nsenter 
מריץ תוכנה בnamespaces שמתקבלים בפקודה, אם אין את השם של התוכנה, אז זה יפתח את הshell תחת הnamespaces האלו.

סזכות network namespaces אפשר לבצע את המידור המשאבי כשמדובר במשאבים רשתיים, רכל רכיב רשתי פיזי יכול להיות תחת network namespace יחיד, ואם הnamespace משתחרר אז הnetwork namespace הראשי מאמץ את רכיבי הרשת שהיו תחתיו. 

מה שvirtual interface נותן לנו זה אבסטרקציה לחיבור לרשתות שונות. בנוסף ליכולת שיש לנו ממשק שנותן חיבור לרשת אבסטרקטית עבור התהליכים שלנו, קיימים מימושים שבהם ניתן לעשות חילוק פנימי בממשק כך שהוא מורכב מתת ממשקים כך שיש חלוקת משאבים מופרדת ביניהם, מבחינת מקסימום המידע שניתן להעביר ועוד פרמטרים. בעזרת הממשק אנחנו יכולים להוסיף בעצמנו שכבה של מידור והפרדת משאבים ללא קשר לקרנל.
פקודה שבעזרה ניתן לקבוע את המשאבים בcontrol group היא cgset, מבחינת לראות את המשאבים עצמם ומה קורה בפועל לבקרה ניתן להשתמש בhtop. 

4. **What are the different types of filesystems, and how do they differ? Then focus on Linux filesystems for a deeper understanding.**

   Explain filesystem types across operating systems:
   * Common filesystem types and their use cases
   * How different operating systems organize files and directories
   
   Then dive deep into Linux filesystems:
   * How Linux filesystems work in practice
   * Inodes, directory entries, and why metadata matters
   * Root filesystem (`/`), mount points, and `/etc/fstab`
   * Linux filesystem examples such as `ext4`, `xfs`, `btrfs`, `tmpfs`, and `vfat`
   * Permissions, ownership, and the Linux permission model (`rwx` for user/group/others)
   * Special permissions such as the sticky bit, setuid, and setgid
   * Basic commands such as `mount`, `df`, `stat`, `chmod`, and `chown`
A:
ניתן כמה סוגי Filesystems
FAT - 
יש טבלת FAT אחת עבור כל הקבצים. כל איבר בטבלה מסמן בלוק ומשם ניתן לראות את האינדקס של הבלוק הבא של הקובץ. אם זה הבלוק האחרון של הקובץ, יהיה כתוב שהבא באינדקס -1. יש גם את הdirectory file שמראה איפה הבלוק ההתחלתי עבור כל קובץ כדי שנדע להתחיל את הגישה. הטבלת FAT שמורה בדיסק אבל בנוסף לכך היא גם cached בזיכרון. בזכות העבודה עם הפוינטרים האלו, אין פרגמנטציה חיצונית בFAT. אפשר להמשיך להרחיב את הקבצים כל עוד יש מקום, לא חייבים להקצות מקום מראש והמימוש של FAT יחסית פשוט. מצד שני, כן יש את החסרונות שמאבדים את הגישה הסדרתית וצריך לעבור כל פעם בטבלה ולהמשיך לפי הפוינטר הבא. 
NTFS -
טכנולוגיה של microsoft. תאורטית כמעט ואין הגבלה בגודל הקובץ, יכול להגיע למימדים עצומים. עובד עם journaling, שזה אומר שלפני פעולות כתיבה NTFS יכתוב בקובץ את הפעולות שהוא מתכנן לעשות וכל פעם שהוא יעשה פעולה הוא יסמן אותה באופן סדרתי. ככה אם יש לי נפילה באמצע הפעולות, אז לאחר מכן אפשר לקרוא את הקובץ ולהבין מאיפה צריך להמשיך. חיסרון של NTFS זה שהוא close sourced תחת microsoft ומוגבל רק לקריאות ולא כתיבות מחוץ לWindows.
 
לכן בנוסף ליתרונות של כל אחד בפני עצמו מהמימוש, יש גם את העניין של עם אילו מערכות הפעלה ניתן לעבוד. 

בWindows יש לנו את הDrives שמכילים Directories שיכולים להכיל עוד Directories או קבצים. אפשר לראות את הכל ויזואלית כסוג של עץ באופן טורי, כשהשורש זה THIS PC. יש גם symlinks, שזה קובץ שמקשר לקובץ או תיקייה אחרת על ידי כך שהוא שומר אצלו את המסלול אליו. 
בLinux יש לנו גם Directories, כשבראש העץ יש לנו את הroot directory שמסומן על ידי "/". כל directory מכיל directories או קבצים. לכל תהליך יש working directory שהוא עובד בו, יש מסלולים יחסיים ביחס לתיקייה כלשהי ויש מסלולים מלאים שמתחילים מהroot. סידור ההיררכיה הוא לא בדיוק גרף כי בנוסף לsymlinks יש לנו גם hard links, שזה ממש אותו קובץ ומכיל את אותם התכנים, אולי עם פשוט שם אחר. הקובץ קיים כל עוד יש לו מופע כלשהו, אם נמחקו לו כל המופעים הוא נאבד (אלא אם כן הוא במקרה פתוח).

לכל קובץ יש inode יחיד שהוא המזהה של הקובץ. הInode מכיל את כל הmetadata של הקובץ חוץ מהשם שלו, כגון הבעלים, הרשאות, זמנים רלוונטים, שינוי אחרון וכו'. על מנת להגיע לקובץ הנכון, צריך להשתמש בdirectory entry. בגלל שאנחנו מחפשים קבצים לפי שמות ואין בInode את השמות של הקובץ, יש את הdirectory file שמכיל במבנה של dirent כל directory entry ואומר את השם של הקובץ יחד עם הinode. יש חשיבות גדולה לmetadata, היא גם חוסכת לנו לקרוא קבצים שלמים כדי להבין פרטים פשוטים כגון גודל הקובץ, וגם נותנת לנו מידע חיוני שאנחנו צריכים כדי לתפעל את המערכת שלנו. לדוגמה, זמן גישה אחרונה, כשאנחנו מבצעים PFRA כדי לנקות את מטמון (Say that again?) הדפים, חשוב לנו לדעת לאיזה קבצים ניגשו בזמן האחרון ואילו לא. 


5. **What are the essential Linux commands for everyday system management and basic navigation?**

   Explain and demonstrate basic usage of:
   * Navigation and file handling: `ls`, `cd`, `pwd`, `mkdir`, `cp`, `mv`, `rm`, `touch`, `cat`
   * Viewing and searching files: `head`, `tail`, `grep`, `sed`, `awk`, `sort`, `uniq`
   * System administration basics: `systemctl`, `journalctl`, `ps`, `top`, `df`, `du`
   * File transfer and remote access: `ssh`, `scp`, `rsync`, `curl`
   * Package management basics with `apt`, `yum`, or `dnf`
   * Basic shell concepts such as piping, redirection, environment variables, and simple scripting
   * Text editing basics: compare `vim` and `nano`, including when to use each one

6. **Bonus Question:** Choose a Linux topic from this chapter that interests you most, research it deeply, and explain how an application request becomes a kernel action and translates back to an application response.

   Possible topics to explore:
   * Filesystems and I/O operations
   * Process management and scheduling
   * Memory management and virtual memory
   * Networking and network stacks
   * Security and permission models
   * cgroups and resource isolation
   
   For your chosen topic, explain:
   * The layers involved from application to kernel and back
   * How system calls bridge user-space and kernel-space
   * The role of interrupts, context switches, and scheduling
   * Real examples using tools like `strace`, `ltrace`, or `perf` to trace the flow
   * Why this interaction pattern matters for system performance and reliability

2. **How do processes and daemons work in Linux, and how do threads fit into this model?**

   Explain:
   * Processes vs daemons
   * Process lifecycle and basic process attributes such as PID and PPID
   * Privileges, the root user, and why permissions matter
   * Threads and how they differ from processes
   * Signals such as `SIGTERM`, `SIGKILL`, and `SIGHUP`
   * How to inspect processes with `ps`, `top`, `htop`, and `pstree`
   * How services are started and managed with `systemd` and `systemctl`
     
---
> ⚠️ The lab should be done after answering the Guide Questions

### Free Hands-On Lab 🧪

https://overthewire.org/wargames/bandit/bandit0.html
---
### 🔄 Alternatives

Assignment: Describe a real-world Linux troubleshooting scenario:

* Investigate a slow or unresponsive server
* Diagnose a service that fails to start
* Explain how you would inspect logs, processes, and resource usage

Deliverable:

* 1 paragraph describing the issue
* 1 paragraph explaining your step-by-step troubleshooting approach

---

### 🎯 User Story & Scenario

Assignment: Describe a simple real-world Linux administration scenario.

Possible examples:

* Investigating disk usage on a server
* Restarting a failed service
* Troubleshooting a full filesystem
* Managing permissions for an application

Deliverable:

* 2 paragraphs describing the issue and solution approach

