

```markdown
# DESIGN NOTES

AI Agent Lab 2

## **The Problem**

The Office of Student Services identified that students face a significant navigation barrier when trying to find basic information about campus resources, as the details are currently scattered across the college website and difficult to locate. This friction often causes students to give up or waste time searching. To resolve this, the core purpose of the prototype Campus Resource Assistant is to allow students to ask plain-language questions and receive accurate, consolidated answers from a single access point without needing to navigate multiple web pages.

## **LLM Knowledge Gap**

Large language models like Gemini are trained on broad public data but lack access to private, localized, or frequently changing information, such as a specific campus directory. If an AI is asked about specific campus office hours without being provided the appropriate tools, it will likely hallucinate and maybe even invent a plausible but entirely incorrect answer, or pull outdated information from external web searches.

## **Architecture Choice**

Storing campus data in a separate JSON file rather than hardcoding it into the Python logic provides three specific architectural benefits:

- **Independent Review & Non-Programmer Access:** Administrative staff or non-programmers can easily review, edit, and update the data directly in the file without needing to understand or touch the underlying Python code, which could be overwhelming for them to understand or navigate.

- **Separation of Logic and Data:** It keeps the data separate from the code, meaning updates to office hours or locations can be made safely without risking breaking the agent's functionality.

- **Future Scalability:** It prepares the system for growth; if the team later decides to use this data in another tool or replace the JSON file with a live database or API, they can do so without needing to rewrite or edit too much of the code.

## **System Limitations**

1. **The agent has no long-term memory of past conversations:** To fix this in a future version, we could use a cloud-based database (such as Firebase, Supabase, or AWS DynamoDB) to store session logs. To manage storage costs and maintain privacy, we could enforce a data retention policy that automatically deletes logs older than one month.
2. **The data can become stale:** Phone numbers, office hours etc change all the time. We can mitigate this by implementing a scheduled human verification step where staff periodically cross-reference the JSON file against the official college website to ensure things do not break. In future iterations, this could be improved by connecting the tool to a live feed.
3. **The model may occasionally fail to call the tool:** To improve reliability, the team could implement a validation step in the workflow that checks whether a tool has been called for campus-specific queries. If the tool was skipped, the system could systematically force the call before presenting a final answer to the user.

## **Data Responsibility**

Providing inaccurate data carries significant real-world consequences. 
  
For example; 

1. Displaying the wrong room number or hours for the library or academic advising could cause delays and make a student late for critical research or appointments. 
2. Providing incorrect information regarding the financial aid office could result in a student missing a scholarship deadline, turning in tuition late, losing access to a class, or incurring administrative fines. 

The responsibility for verifying this information falls on the project team's human data curators, using the verified_by and last_verified fields to maintain auditability and trust. Having said that, factors outside of the curators’ and programmers’ control such as the tools being used being offline or the AI model being used causing hallucination or things like AI drift must be kept in mind and there should be a disclaimer / warning being displayed to the user when using this agent.

## **Data Table:** 

Below is the verified information that should be used by the agent.   

| Resource | Location | Hours | Phone | Email | Website | Verified By | Date Verified |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Hultquist Library | Hultquist Library (HULT), 260 Curtis Street, Jamestown, NY | Vary by day and semester. Check the library website for current hours. | 716-338-1008 | Not confirmed | https://www.sunyjcc.edu/support/learning-commons/library | Zaid Abdulrahim | 2026-10-09 |
| Learning Center (Tutoring) | Jamestown Campus: HULT Floor 1, Learning Commons. Cattaraugus County Campus: LLAC Floor 1, Learning Commons | Contact office for hours | 716.338.1008 (Jamestown), 716.376.7517 (Cattaraugus County) | Not listed | https://www.sunyjcc.edu/support/learning-commons/learning-center | Jerrica Sosa | 2026-10-01 |
| Success Center (Advising and Counseling) | Jamestown Campus: COCE Floor 1. Cattaraugus County Campus: COLL Floor 1 | Contact office for hours | 716.338.1065 (Jamestown/Online), 716.376.7508 (Cattaraugus County) | successcenter@mail.sunyjcc.edu | https://sunyjcc.edu/support/counseling/staf | Jerrica Sosa | 2026-10-01 |
| Financial Aid Office | Jamestown Campus: COCE Floor 1. Cattaraugus County Campus: COLL Floor 1 | Contact office for hours | 716.338.1009 | FinancialAid@sunyjcc.edu | https://www.sunyjcc.edu/support/financial-aid-billing/scholarships | Maryam Fatima | 2026-09-30 |
| Career Center | Jamestown Campus and Cattaraugus County Campus (Olean) | Contact office for appointment hours | 716.338.1061 | Not listed | https://www.sunyjcc.edu/support/career-services/interest | Maryam Fatima | 2026-09-30 |
| Student Food Pantry | Jamestown Campus, next to the Campus Bookstore, Hamilton Collegiate Center, first floor; Cattaraugus County Campus, Success Center across from the bookstore, College Center, first floor | None listed | Jamestown Campus - 716.338.1004; Cattaraugus County Campus - 716.376.7508 | None listed | https://www.sunyjcc.edu/support/get-help/food-pantry | Jerrica Sosa | 2026-09-22 |
| JCC IT Help Desk | Jamestown Community College; physical walk-in location not confirmed | Monday–Friday, 8:30 AM–4:30 PM | 716-338-1122 | JCCHelpDesk@sunyjcc.edu | https://www.sunyjcc.edu/support/technology/myjcc | Zaid Abdulrahim | 2026-10-09 |
| Campus Bookstore | JCC Campus Store. 525 Falconer Street. Jamestown. NY 14701 | Mon-Thu: 9:00 AM - 4:30 PM. Fri: 9:00 AM - 2:00 PM. Sat-Sun: Closed | (877) 420-1734 | sm8369@bncollege.com | https://sunyjcc.bncollege.com/ | Maryam Fatima | 2026-09-30 |
| Campus Safety | Jamestown Campus (Hamilton Collegiate Center) | Contact office or campus safety cell for active hours | 716.338.1015 | Not listed | https://cm.maxient.com/reportingform.php?SUNYJamestownCC&layout_id=5 | Maryam Fatima | 2026-09-30 |
| Health Center | Jamestown Campus \| COCE 122; Cattaraugus County Campus \| COLL 139 | None Listed | 716.338.1013 - Charlene Johnson (General); 716.338.1014 - Shelly Wells, RN (Nurse) | HealthCenter@sunyjcc.edu | https://www.sunyjcc.edu/support/health-center | Jerrica Sosa | 2026-09-22 |

```