---
layout: default
title: Course Outcomes
description: Detailed mapping of work performed to meet course outcomes
back_link: index.html
---

# Course Outcomes

This page details how my work meets the course outcomes for the SNHU BS in Computer Science capstone. See below for mapping and explanations.

### Employ strategies for building collaborative environments that enable diverse audiences to support organizational decision making in the field of computer science 

Before performing any enhancements, I communicated and documented my decision-making process and outlined the plan for enhancements using an informal code review. Next, I transformed a program with only command line interface into a program with a fully functional GUI. This enhancement makes the program accessible to more users and expands the functionality.  Additionally, all the development progress has been tracked using git and GitHub to allow potential stakeholders to view and review the code.

<details>
  <summary style="color: #87CEEB; cursor: pointer;">View User Interface Screenshots</summary>
  <div>
    <img src="/assets/images/ProgramScreenshot.png" alt="Main Form" style="display: block; margin-bottom: 10px;">
    <img src="/assets/images/DeploymentScreenshot.png" alt="MSI Installer" style="display: block;">
  </div>
</details>

### Design, develop, and deliver professional-quality oral, written, and visual communications that are coherent, technically sound, and appropriately adapted to specific audiences and contexts

The enhanced artifacts include detailed code documentation using multi-line comments on all classes and functions, as well as inline comments where appropriate. Additionally, the project repo on GitHub includes README that provides a detailed project overview and outlines the process to build and install the software. This documentation was all written in a professional manner consistent with the target audience. In addition to the enhancements, a video code review was conducted that demonstrates my ability to communicate with stakeholders using visual and oral communication skills.

### Design and evaluate computing solutions that solve a given problem using algorithmic principles and computer science practices and standards appropriate to its solution, while managing the trade-offs involved in design choices

The Boyer Moore algorithm was implemented which optimizes the speed of searching at runtime. The “bad character” and “good suffix” heuristics aid in the speed by using string matching theory to reduce unnecessary comparison. Each search is conducted with a worst-case time-complexity of O(m*n) and a space complexity of Θ(m + k). The trade-off of the Boyer Moore Search is space verses time. The algorithm uses preprocessed tables which can take up more space than other approaches. However, the impact is negligible because the small amount of program data used and the large amount of space available on typical Windows systems.

### Demonstrate an ability to use well-founded and innovative techniques, skills, and tools in computing practices for the purpose of implementing computer solutions that deliver value and accomplish industry-specific goals

I demonstrated this ability by creating and implementing a Firebase Firestore database in the enhanced project. This enhancement transitioned the artifact from using hard-coded, static data to dynamically loaded data from a cloud-based database. This transformation demonstrates the data abstraction, separation of concerns, and scalability principles for database development.

### Develop a security mindset that anticipates adversarial exploits in software architecture and designs to expose potential vulnerabilities, mitigate design flaws, and ensure privacy and enhanced security of data and resources

I have demonstrated this by writing code that adheres to security best practices and implementing version control, static testing, and database access rules. Security was considered in every aspect of the design. For example, authentication rules were added to the Firestore database to ensure controlled access. Additionally, an OWASP dependency check was integrated into the build process to ensure the dependencies are checked with every release. See below for the dependency check report for version 1.0.0.0.

<details>
  <summary style="color: #87CEEB; cursor: pointer;">View Dependency Check Report</summary>
  <div>
    <!-- Style the report for viewing since it is designed for light mode -->
    <style>
        iframe {
            background-color: #ffffff;
            color: #000000;
        }
    </style>
    <iframe src="/artifacts/dependency-check-report.html" style="width: 100%; height: 90vh; border: none;"></iframe>
  </div>
</details>