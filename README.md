# Problem Statement

The goal of the coding exercise is to write a program that reads from the provided CSV file and combines its information with data from the API (http://interview.wpengine.io/v1/docs/) and outputs a new CSV file.

Given a CSV file with the following columns including a header row containing Account ID, Account Name, First Name, and Created On
And a Restful Status API: http://interview.wpengine.io/v1/accounts/{account_id}
that returns information in a JSON format of: {"account_id": 12345, "status": "good", "created_on": "2011-01-12"}
where the "Account ID" in the CSV lines up with the "account_id" in the API and the "created_on" in API represents when the status was set
For every line of data in the CSV, we want to:

-Pull the information from the API for the Account ID
-Merge it with the CSV data to output into a new CSV with columns of Account ID, First Name, Created On, Status, and Status Set On

# Running the application

The program must be invoked from command line as follows:

wpe_merge <input_file> <output_file>

For example:

wpe_merge data/input.csv output.csv

A happy path batch file was created also (wpe_merge_happy_path_hardcoded_files.bat) which contains hardcoded names for two files, input.csv & output.csv. Executing this batch file (double click) directly should lead to a successful run. Success Message out put to user, review contents to see contents are correct in output file.

Please note that the batch file (wpe_merge) targets the generated dll in the debug folder currently -> dotnet bin\Debug\netcoreapp2.1\WPEAccountApp.dll. This can be changed to Release or wherever deemed appropriate.

# WPEAccountApp Description

Program that takes in two arguments: inputFilePath & outputFilePath
1. Reads in Account Details from CSV file
2. Gets Account from WPE API with given Account Id from Account Details
3. Combines Account Details with Account (MergedAccount)
4. Writes out all Merged Accounts, with specified headers, to given outputFilePath

# Requirements

- A batch file (wpe_merge) to be invoked via command line that takes in two string paths to files. First for input, second for output.
- Batch file invokes Program.cs and passes the given paths to this Program.
- Validate given arguments/paths supplied
- Read from input file, map rows of data to object (AccountDetails.cs)
- For each account on input csv (AccountDetails.cs), fetch Account information from Api (Account.cs) with corresponding account Id (http://interview.wpengine.io/v1/accounts/{account_id})
- Merge Account Details on csv (AccountDetails.cs) with Account information from Api (Account.cs). 
- Write all merged accounts out to given output file.
- Documentation, Tests and Instructions on application.
Note: I only merged account if account Id matched Id on Api i.e. if account on csv did not retrieve account from api based on Account Id then I did not ouput this to file in the end. Would ideally like to consult a PO/BA before making a decision like this. 

# API

-Base url: http://interview.wpengine.io/v1
-Endpoint to retrieve an account: accounts/{account_id}
-Absolute url: http://interview.wpengine.io/v1/accounts/{account_id}

# Planning

Needed:
1. Knowledge of data schema on api, input file and output file
2. Interface to alert user to possible errors and result of application.
3. IDE for development and desired programming language.
4. Handle potential File I/O and API issues.
5. Package manager for libraries

# Decided:

1. Consulted API documentation here http://interview.wpengine.io/v1/docs/ and inspected responses
2. Console Application provided means for user to see output.
3. Visual Studio 2017 and C# deemed appropriate given basic requirements. .Net Core (2.1) Application used as requested.
4. CSVHelper is a fantastic library that helps specifically with reading and writing to csv files. It also handles most common issues out of the box. I decided to wrap all file operations, and Api request/response, with a Try/Catch (ErrorHandler.cs Execute()).
5. Nuget Package Manager used as it is quality:

# Dependencies

WPEAccountApp Project
-Microsoft.AspNet.WebApi.Client 5.2.7
-CsvHelper 12.1.1
WPEAccountAppTests Project
-Microsoft.NET.Test.Sdk 15.9.0
-NSubstitute 3.1.0
-Xunit 2.4.0
-Xunit.runner.visualstudio 2.4.0
-WPEAccountApp Project

# Design & Implementation

Clean code and SOLID principles are important to me and hope that my implementation reflects that.
I especially like to focus on SRP as I think this principle is extremely important.
This allows for code to be extensible, maintainnable and allows for segments to be re-usable.
Where possible I also followed a Test Driven Approach to ensure I got a working application.
I commented my code to give reviewer an idea of how it worked and also why it was done.

Divided the Application into:

1. Batch file to be executed from cmd - wpe_merge.bat

2. Program Runner - Program.cs
Decided that a Program class should be responsible for activation and termination of application. 
It would also take in the arguments from the batch file and output information to user.
Since it was the program runner, it invoked all the necessary classes below to handle the required functionality for the application to satisfy the requirements.

3. Controllers - AccountController.cs
The AccountController class was then in charge of getting the accounts from the API.

4. Models - Account.cs, AccountDetails.cs, MergedAccount.cs, AccountDetailsMap.cs

5. Utilities - AccountMerger.cs, Constants.cs, ErrorHandler.cs, FileOperations.cs, InputValidation.cs, HttpClientWrapper.cs

6. Unit Tests - 
Unit tested all the Controllers and Utilities that contained the logic.
Did not unit test Models as there is no logic to test or the Program as it required user input and private methods.
Used XUnit and NSubstitute.

# Notes:
Virtual methods used deemed better for unit testability than interfaces but it is a code smell. With proper Dependency Injection I would always prefer to use interfaces as it leads to greater abstraction and therefore maintainability and extensibility.

# Improvements that could be made:
-HTTPClient could be implemented better for abstraction, separating concerns better with easier unit testability.
-Dependency Injection & abstraction to avoid newing up so many objects and provide greater flexibility in future and testability.
-I could also have tested the File I/O more both manually and with unit tests but I just don't have the time, apologies.
-Document further what was done in ReadMe and why (again apologies, I just have no time)
-Remove HttpClientWrapper.cs instance from AccountMerger.cs and inject through AccountController.cs constructor. AccountMerger should not need to know about this.
