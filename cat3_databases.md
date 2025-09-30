---
layout: default
title: Databases
description: Enhancements for the databases category
back_link: ../index.html
---
## Databases

### Description:
<p style="text-indent:3em;">
Several artifacts were created to enhance the original program for the Database category. The files displayed on this page represent the code used to create the database connection and the initial data load for the States Search application. These files were created in 2025 as part of my capstone project to transform a command-line Java program into a full-stack C# application.
</p>

### Justification:
<p style="text-indent:3em;">
These artifacts demonstrate my ability to integrate a cloud-based database solution into a full-stack program. The original artifact relied on hard-coded static data which is not a good practice because the data can become stale. The enhancements made allow for dynamic updates of the data without recompiling the program. This is more efficient and better for the user experience. The <code>FirebaseLoader.cs</code> class handles the cloud communication and data loading. The <code>State.cs</code> class is the data model which defines the data structure. The <code>FirebaseUploadUtil.py</code> script is a utility created to upload a CSV file to a Firestore collection. These enhancements showcase my ability to implement a maintainable, scalable database solution.
</p> 

### Reflection:
<p style="text-indent:3em;">
This final enhancement helped me to become more familiar with cloud-based database solutions. I have worked with other databases such as MySQL and MongoDB through the coursework at SNHU. This effort added another database architecture to my resume` of database experience. I have learned the differences between structured and nonstructured (NoSQL) databases throughout my coursework at SNHU and have gained an understanding of the strengths and weaknesses of each.
</p>

<p style="text-indent:3em;">
The greatest challenge with integrating the Firestore database was learning the nuances of Firebase. It took some time and research to become familiar with the Firebase console and the integration architecture. However, this type of challenge has become familiar throughout my software engineering career as the technology is ever evolving.
</p>

### Artifacts:

#### FirebaseLoader.cs
{% include_relative /artifacts/FirebaseLoader.cs.md %}

#### State.cs
{% include_relative /artifacts/State.cs.md %}

#### FirebaseUploadUtil.py
{% include_relative /artifacts/FirebaseUploadUtil.py.md %}