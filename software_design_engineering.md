---
layout: default
title: Software Design and Engineering
description: Enhancements for Software Design and Engineering
back_link: ../index.html
---
## Software Design and Engineering Artifacts
### Description:
This file contains the main user interface form for the States Search application. It was created in 2025 as part of my capstone project to transform a command-line Java program into a full-stack C# application.

### Justification:
Justify the inclusion of the artifact in your ePortfolio. Why did you select this item? What specific components of the artifact showcase your skills and abilities in software development? How did the enhancement improve the artifact? What specific skills did you demonstrate in the enhancement?

This file and the other files linked below demonstrate my ability to enhance an existing artifact to include a user interface and additional functionality. The project includes scripts for building and packaging the application and performing a dependency check.
  
### Reflection:
Reflect on the process of enhancing the artifact. What did you learn as you were creating it and improving it? What challenges did you face? How did you incorporate feedback as you made changes to the artifact? How was the artifact improved? Which course outcomes did you partially or fully meet with your enhancements? Which do you feel were not met?
- This first enhancement included much of the foundational work necessary for creating the full-stack application. The work started with the creation of a new project/solution in Visual Studio. This simple step required some research to determine the best project type to choose from the many options provided. The .NET Windows forms project type was chosen because of is compatibility with Windows and scalability via the .NET framework. Since there was no GUI in the original artifact, the interface had to be designed from the ground up. Visual Studio provides a UI designer that made it easy position the controls and wire them up to their respective listeners. This was a better experience than the work I performed creating UI controls for Java applications in the past.
- For the deployment mechanism, I decided to use the WixToolset because of its advanced customization options. The project uses xml-based commands to control the various screens and actions of the MSI package installer. It was a challenge to set all the right properties to get the application to install successfully. However, the Wix documentation is extensive and helped me determine the right properties to use.
- The greatest challenge for development was the schedule. Creating a new application from the ground up takes a considerable amount of time which competed with the deadlines of the project milestones. However, after the foundation of the project was laid, the next enhancements took less time to complete.

### Code:

#### MainForm.cs
{% include_relative /artifacts/MainForm.cs.md %}
##### Screenshot:
<div style="text-align: center;">
  <img src="/assets/images/ProgramScreenshot.png" alt="MainForm Screenshot">
</div>

#### Package.wxs
A WiX Toolset XML file to define the installer package for the application.
{% include_relative /artifacts/Package.wxs.md %}
##### Screenshot:
<div style="text-align: center;">
  <img src="/assets/images/DeploymentScreenshot.png" alt="Deployment Screenshot">
</div>

#### build.ps1
A PowerShell script for build automation, including versioning and packaging.
{% include_relative /artifacts/build.ps1.md %}

#### git_version.ps1
A PowerShell script to retrieve the current git version tag.
{% include_relative /artifacts/git_version.ps1.md %}

#### dependency_check.ps1
A PowerShell script to automate the OWASP Dependency-Check tool.
{% include_relative /artifacts/dependency_check.ps1.md %}
