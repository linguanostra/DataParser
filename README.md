# Wasabi DataParser
Wasabi DataParser is a dynamic and extensible SQL query engine for .NET and Mono. It allows to seamlessly query programmatically defined data sources. In other words: **query anything**!

We all love SQL right? This is a new cool way to use it!

Install it

```
PM> Install-Package Wasabi.DataParser
```

Query the file system
```csharp
// Create engine
var engine = new DataParserEngine();

// Start engine
engine.Start();

// Execute query using the file system virtual provider
// Will get file name, MD5 hash and size
var result = engine.ExecuteQuery(@"
    SELECT Name, HASHMD5_FILE(Path) AS Hash, Size FROM FS('C:\\Logs')
    ");   

// Stop engine
engine.Stop();         
```

Use a custom virtual function with a delegate
```csharp
// Define custom multiplication virtual function with input value parameter
var customFunction = new DataParserDelegateVirtualFunction("MakeMeDouble",
    () => new VirtualFunctionsParamsList
    {
        new DataParserQueryParam(1, "Value", DataParserQueryParamType.Number)
    }, (queryParams, context) =>
    {

        // Get value param
        var paramValue = queryParams.GetIntValue("Value");

        // Compute the updated value
        var updatedValue = paramValue * 2;

        // Return result as integer
        context.SetIntResult(updatedValue);

    });

// Install custom function in the engine
customFunction.Install(engine);

// Query engine (with default SQL parser)
var result1 = engine.ExecuteQuery(@"
    SELECT
        MakeMeDouble(5) AS [ResultA],
        MakeMeDouble(10) AS [ResultB],
        MakeMeDouble(MakeMeDouble(20)) AS [ResultC]");

// Query engine (with SQLite parser for more powerful queries)
var result2 = engine.QueryInternal(@"
      SELECT ((ResultA - ResultB) * ResultC) AS [ComputedResult]
      FROM
          (SELECT
              MakeMeDouble(5) AS [ResultA],
              MakeMeDouble(10) AS [ResultB],
              MakeMeDouble(MakeMeDouble(20)) AS [ResultC]
          )
      ");

// (Optional) Uninstall custom function from the engine
customFunction.Uninstall(engine);

// Stop engine
engine.Stop();
```
Compile, run and contemplate the infinite possibilities!

## Features

* Fully managed code with embedded dependencies so you only reference a single assembly
* Query engine built on top of a managed implementation of [SQLite](https://www.sqlite.org/), no need for any interop library
* Custom SQL language parser on top of the SQLite parser
* Create your own virtual functions and tables using plugins that can be loaded from external assemblies
* [Apache Thrift](https://thrift.apache.org/) supported allowing for scalable cross-language services development
* In-memory SQL database storage for faster query performance
* SQL database file supported for data persistence
* Works with .NET Framework 4.5 and up on Windows
* Works with Mono Framework on Windows, Mac OSX and Linux
* And much, much more

## Help out

There are many ways you can contribute to Wasabi DataParser. Like most open-source software projects, contributing code is just one of many outlets where you can help improve. Some of the things that you could help out with are:

* Documentation (both code and features)
* Bug reports
* Bug fixes
* Feature requests
* Feature implementations
* Test coverage
* Code quality
* Sample applications
* Additional plugins

## Acknowledgments

* [C#-SQLite](https://code.google.com/archive/p/csharp-sqlite/) is an independent reimplementation of the SQLite software library by Noah B Hart. Sources of this project are an important part of the core library.
* [Irony](https://irony.codeplex.com/), a .NET language implementation kit.
* And many others

## Copyright

Copyright © 2017 Patrick Cloutier and contributors

## License

Wasabi DataParser is licensed under [MIT](http://www.opensource.org/licenses/mit-license.php "Read more about the MIT license form"). Refer to license.txt for more information.
