SSTrolley
==========
[![Build status][shield-build]](#)  
[![Dependencies][shield-dependencies]](#)  
![MyGet tenant](https://img.shields.io/dotnet.myget/dotnet-coreclr/v/Microsoft.DotNet.CoreCLR.svg)

Introduction
------------
This web Application is to identify GPS location and other real time details of the Saugeen Shores Trolley service.
This is the Trolley Controller that deals with Trolley Model.  
Developed using C# and Language Integrated Query (LINQ) to use the same basic query expression patterns to query and transform data in SQL databases, ADO .NET Datasets, XML documents and streams, and .NET collections.  
Donated by [Hatch](https://www.hatch.com/)

 Requirements
-------------
Trolley Controller requires the following software:
* [npm](https://www.npmjs.com/get-npm)
* [.NET Core SDK 2.1 or later](https://www.microsoft.com/net/download/all)
* [linq](https://www.npmjs.com/package/linq)

* [Visual Studio 2017 version 15.7 or later with the ASP.NET and webdevelopment workload](https://www.visualstudio.com/downloads/)  
OR  
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms* vscode.csharp)

You can install the .NET Core by copying and pasting the following command into your Package Manager Console.
It is easiest to use when intalled with npm.
To make the dotnet command available globally.

```bash
npm i -g dotnet-2.1.0      // for running dotnet apps
npm i -g dotnet-sdk-2.1.0  // for developing dotnet apps
```

Build
------
First, install TrolleyController.cs:

```bash
npm install --save-dev TrolleyController
```

Add TrolleyController section to package.json

```bash
+"TrolleyController":[
+	"path":"/SSTrolley/Controllers"
+]
```

The path option:  
For an open source library, specify compiled sources, which will be published to npm (usually the same value as the main field in the package.json);

Create the controller in the Controllers folder where the TrolleyController forces all actions to return JSON-formatted responses and it uses {controller=api}/{action=trolley} as the common route prefix on the controller
Copy trolley to the new project and include route on TrolleyController

Setup
-----
By default the Controller uses namespace SSTrolley.Controllers.

The controller uses the IDisposable objects:
the namespace System, specifically System.Threading.Tasks to simplify the work of writing concurrent and asynchronus code 

the System.Collections.Generic namespace with interfaces and classes which allows users to create strongly typed collections that provide better type safety and performance than non-generic strongly typed collections.

the namespace Microsoft.AspNetCore.Http to encapsulate all HTTP-specific information about the HTTP request.

the namespace Microsoft.AspNetCore.Mvc, with the Model-View-Controller the architectural pattern separates the application into three main groups of components: Models, Views, and Controllers. 

Additionally, it accesses the database Data with the namespace SSTrolley.Data encompassing DbInitializer and TrolleyProcessing and the model Models in the namespace SSTrolley.Models encompassing PassengerPoint, RouteBinding, RoutePoint, Trolley, TrolleyContext, TrolleyLogin and TrolleyPoint.

Methods and Examples
-------------------
The controller uses the .NET Framework namespaces to organize its many classes, controlling the scope. 
The C# application begins with a section of using directives
* For example the component PassengerPoint.cs is defined in the namespace SSTrolley.Models as a model

In the class TrolleyController...

**Constructor:**
TrolleyController(TrolleyContext context)
* Member Declaration:Initalize the DbContext for the TrolleyController from the SSTrolley Model for the Controller for the method 
this is used to qualify the TrolleyController class member context that represents its data and behavior

Accessor Methods:  
GetIds()  
method of type IEnuberable<int>created via HTTPGet for the trolley retrieving the entity of Id's for the Trolleys

GetTrolley(int id)  
method of type IActionResult created via HTTPGet that is an abstract class that tries to represent the Trolleys Id's  in JSON format if possible  

GetALL()  
method of type IEnumberable<Trolley>created via HTTPGet that returns the field for the DbSet of Trolleys with the updated Ids

GetStops(int id) 
method of type IEnuberable<int>created via HTTPGet for the trolley retrieving the entity of Id's for the Trolleys

GetStopsFull(int id)  
method of type IEnuberable<RoutePoint> via HTTPGet for the trolley returning the properly evaluated quiries for context


**Mutator Method:**
Post([FromBody]TrolleyLogin value) 
of type IActionResult created via HTTPGet takes in parameter of class type TrolleyLogin called value and sends values accordingly:
Initally checks for login error   
The DbContextTransaction named transaction is to be disposed once it has been committed or rolled back by applying the using(…) {…} syntax which will automatically call Dispose() when the using block completes.  
* Variable Declaration:Create the local variable trolley of Trolley Models
* Variable Minipulation:
	Inital try statement to match trolley id with the value sent, if invalid throw exception for the error that erases all data modifications made from the start of the transaction or to a savepoint, freeing up resources held by transaction
	If condition to analyze trolley login authentication credentials returning StatusCode(403) if insufficient
	Initalize and define property for Trolley variable LastLongitude that accesses GPS coordinates of trolley and define property Lastlatitude;
	If else statement to convert longitude and latitude values due to hardware limitations that provide zoomed out values
	Initalize and define property as TotalDistance that accesses the Data TrolleyProcessing and calculates the distance inbetween the points with the haversine formula which determines the great-circle distance between two points on a sphere given their longitudes and latitudes
* Object Declaration:Create point of TrolleyPoint Models as an object with the default constructor for the default values
* Object Minipulation:
	In the DbSet add the point to context
	Try statement to call SaveChanges Method() for the feild TrolleyContext, if error during execution call status500InternalServerError
* Object Declaration:In the parameter TrolleyLogin value is called, property Passengers is defined
* Object Minipulation:
	If condition to set Passengers if not null
Try statement again to call SaveChanges Method() for the feild TrolleyContext, if error during execution discard all changes made to database and return error status500InternalServerError
To finish the Post Method return Ok() to create an OkResult (200 OK).

