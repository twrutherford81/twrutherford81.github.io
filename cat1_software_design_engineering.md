---
layout: default
title: Software Design and Engineering
description: Enhancements for the software design and engineering category
back_link: ../index.html
---
## Software Design and Engineering

### Description:
<p style="text-indent:3em;">
Several artifacts were created to enhance the original program for the Software Design and Engineering category. The files displayed on this page represent the code used to create the main user interface and the CI/CD solution for the States Search application. These files were created in 2025 as part of my capstone project to transform a command-line Java program into a full-stack C# application.
</p>

### Justification:
<p style="text-indent:3em;">
These artifacts demonstrate my ability to create and release a program for production use. The original program, although simple, was not in a format familiar with most PC users. The enhancement to add a user interface to the program makes the program accessible to larger audience. Additionally, the CI/CD scripts and installer packages provide for a streamlined and consistent manner to release application builds and updates. The build process includes an automatic OWASP dependency check ensuring the project dependencies are reviewed for security vulnerabilities with each release. These enhancements show my ability to work in a wide range of programming languages and development environments.
</p>

### Reflection:
<p style="text-indent:3em;">
This first enhancement included much of the foundational work necessary for creating the full-stack application. The work started with the creation of a new project/solution in Visual Studio. This simple step required some research to determine the best project type to choose from the many options provided. The .NET Windows forms project type was chosen because of is compatibility with Windows and scalability via the .NET framework. Since there was no GUI in the original artifact, the interface had to be designed from the ground up. Visual Studio provides a UI designer that made it easy position the controls and wire them up to their respective listeners. This was a better experience than the work I performed creating UI controls for Java applications in the past.  
</p>

<p style="text-indent:3em;">
For the deployment mechanism, I decided to use the WixToolset because of its advanced customization options. The project uses xml-based commands to control the various screens and actions of the MSI package installer. It was a challenge to set all the right properties to get the application to install successfully. However, the Wix documentation is extensive and helped me determine the right properties to use.  
</p>

<p style="text-indent:3em;">
The greatest challenge for development was the schedule. Creating a new application from the ground up takes a considerable amount of time which competed with the deadlines of the project milestones. However, after the foundation of the project was laid, the next enhancements took less time to complete. 
</p>

### Artifacts:

#### MainForm.cs
The main program class providing the user interface and program logic.
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
