﻿# Optional homework

These exercises are **optional**. You can earn **extra points** that are added to your exam score. In the exercises and the evaluation results, you will see a text “iMsc”; please ignore that (it is for the Hungarian students). All exercises are available for extra points on this course. Here you find the exercise descriptions; the submission of the solutions is expected via GitHub Classroom.

!!! important "Working code"
    You are expected to write code that actually works! Your code will be executed, and it is required to fulfill the specified task.

## The exercises

1. [MSSQL server-side programming](mssql/index.md)
1. [ADO.NET data access](adonet/index.md)
1. [Entity Framework](ef/index.md)
1. [MongoDB](mongodb/index.md)
1. [REST API and Web API](rest/index.md)

## Submission

Each homework must be submitted in a personal git repository. Please refer to the detailed [guideline here](GitHub.md). You must carefully study these guidelines!

!!! danger "IMPORTANT"
    The submissions of the homework **must** follow these guidelines. Submissions not adhering to the expected format are not considered.

    Workflow errors, i.e., not following the guidelines (e.g., not assigning the right person, or not assigning at all) are penalized.

## Screenshots

Some of the exercises require you to create a screenshot. This screenshot is proof of the completion of the exercise. **The expected content of these screenshots is detailed in the exercise description.** The screenshot may include the entire desktop or just the required portion of the screen.

!!! info ""
    The screenshots must be submitted as part of the solution code, uploaded to the git repository. The repositories are private; only you and the instructions can access them. If there is any content on the screenshot that is not relevant to the exercise and you would like to remove, you can obscure these parts.

## Required tools

- Windows, Linux, or macOS: All tools are platform-independent, or a platform-independent alternative is available.
- GitHub account and a git client.
- For homework using the MSSQL platform:
    - Microsoft SQL Server. The free _Express_ version is sufficient, or you may also use _localdb_ installed with Visual Studio. A [Linux version](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup) is also available. On macOS, you can use Docker.
    - [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms), or you may also use the platform-independent [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/download) is
    - Database initialization script: [mssql.sql](https://raw.githubusercontent.com/bmeviauac01/adatvezerelt/master/docs/db/mssql.sql)
- For homework using a MongoDB database:
    - [MongoDB Community Server](https://www.mongodb.com/download-center/community)
    - [Robo 3T](https://robomongo.org/download)
    - Sample database initialization script: [mongo.js](https://raw.githubusercontent.com/bmeviauac01/adatvezerelt/master/docs/db/mongo.js)
- For the REST API homework: [Postman](https://www.getpostman.com/)
- For writing C# code (most homework, except the first one):
    - Microsoft Visual Studio 2019/2022 [with the settings here](VisualStudio.md)
        - When using Linux or macOS, you can use Visual Studio Code, the .NET Core SDK, and [dotnet CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/).
    - [.NET Core **3.1** SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)

        !!! warning ".NET Core 3.1"
            Mind the version! You need .NET Core SDK version **3.1** to solve these exercises.

            On Windows, it might already be installed along with Visual Studio (see [here](VisualStudio.md#check-and-install-net-core-sdk) how to check it); if not, use the link above to install (the SDK and _not_ the runtime). You need to install it manually when using Linux or macOS.

## Submission evaluation

The evaluation of the exercises is **semi-automatic**. Your code will be executed; therefore, it is vital to follow the exercise descriptions precisely (e.g., use the provided code skeleton, change only the allowed parts of the code, etc.).

You will receive a preliminary result about your submission in GitHub; see in the guideline [here](GitHub.md)). If there are some issues you need to diagnose, the entire log of the execution is available for you on the _GitHub Actions_ web page. A short introduction is provided [here](GitHub-Actions.md).

!!! danger "Verification"
    In some of the exercises, where the technology permits, you will find unit tests. These tests **help** you verify your work, but these are **no substitution for your validation**. When you upload your work, more exhaustive testing will evaluate your submission.
