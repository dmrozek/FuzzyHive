# FuzzyHive
Fuzzy extensions for Apache Hive

FuzzyHive library extends the Apache Hive framework with fuzzy sets-based techniques for querying, analyzing, and reporting on Big data warehouses. The fuzzy techniques, like including fuzzy filtering on dimensional attributes, projection with fuzzy transformation, fuzzy grouping, and fuzzy joining, can be used while operating on Hive-based data warehouses. These operations are embedded in Hive Query Language (HiveQL). Such extensions make Big Data warehousing more flexible and contribute to the portfolio of tools used by the community of people working with fuzzy sets and data analysis. The FuzzyHive library complements the spectrum of available solutions for fuzzy data processing and querying in large data sets.

Fuzzy querying in HiveQL requires data to be loaded in Hive and stored in the Hadoop Distributed File System (HDFS). Hive relies on the concept of tables as organizational units for data (like in relational databases). However, the data are stored in files that may have different, sometimes complex and not row-oriented format. In simpler cases, these can be comma-separated or tab-delimited files and it is quite easy to map the structure of a created table to a row-oriented file, as each row of the file is extracted and seen as a tuple of the table.

Fuzzy querying in Hive Big Data warehouse environment requires extending the HiveQL: (1) fuzzy selection, (2) projection with fuzzy transformation, (3) fuzzy grouping, (4) fuzzy join, (5) building complex fuzzy filtering criteria, (6) defining linguistic variables and linguistic values.

FuzzyHive is a general-purpose library that allows for fuzzy querying in the Hive environment through extensions introduced to Hive Query Language (HiveQL). Additional definitions of domain-specific elements, like definitions of linguistic variables and values, are provided by a user in separate files. This section gives quick information on (A) how to register the FuzzyHive library in Hive, and (B) which modules and functions were exposed for fuzzy querying in HiveQL.

A. Registering Library in Hive

Before the FuzzyHive library can be used, it should be registered in the Hive environment. FuzzyHive library is delivered in the form of library source code that must be compiled to the .jar file. This allows to add custom, specialized and dedicated definitions of linguistic variables and their linguistic values. These definitions are stored in JSON files, like the one presented below. The compilation step can be skipped, if no custom definitions are added. 

```JSON
{
  "linguisticVariables" :[
    {
      "name": "BMI",
      "values": [
        {
          "name" : "Low",
          "type": "TrapezoidalMembershipFunction",
          "params": [
            0,
            0,
            17.5,
            19.5
          ]
        },
        {
          "name" : "Normal",
          "type": "TrapezoidalMembershipFunction",
          "params": [
            17.5,
            19.5,
            24.5,
            26.0
          ]
        },
        ...
  ]
}
```
After compilation, the library should be registered in the Hive environment together with appropriate call specifications for all functions that will be invoked from the HiveQL queries. This step is shown below.

```SQL
add jar (*@\textit{absolute path to the file fuzzyhive-1.0.jar}@*);
create function fuzzyOr as "functions.numeric.FuzzyOr";
create function fuzzyAnd as "functions.numeric.FuzzyAnd";
create function Around as "functions.numeric.Around";
create function fequals as "functions.numeric.FuzzyEquals";
create function membership as "fuzzy_model.GenericMembershipFunction";
create function BMI_Low as "fuzzy_hive.bmi.Low";
create function BMI_Normal as "fuzzy_hive.bmi.Normal";
...
```

The FuzzyHive library contains various component modules exposing different classes, functions, and data types for fuzzy data processing. The most important are as follows:
- TrapezoidalMembershipFunction -- a class that allows creating trapezoidal and triangular membership functions to represent particular fuzzy sets or fuzzy values; it is especially useful when defining linguistic values for linguistic variables in JSON files,
- Around -- a function which assesses how much a given numeric value of the dimensional attribute matches to a fuzzy value represented by a trapezoidal membership function,
- membership -- a function that allows calculating membership degree of a given value of a dimensional attribute to a fuzzy set,
- fuzzyAnd -- a function that implements a fuzzy variant of the conjunction (AND) operator, used to check how much the complex filtering criteria on dimensional attributes are satisfied according to minimum t-norm,
- fuzzyOr -- a function that implements a fuzzy variant of the disjunction (OR) operator, used to check how much the complex filtering criteria on dimensional attributes are satisfied according to maximum t-conorm,
- fequals (in particular FuzzyEquals) -- a function that determines the degree of equality of two fuzzy sets; it is especially useful while performing a fuzzy join on dimensional attributes,
- other functions that are dynamically created by code generator for the declared and defined linguistic values (e.g., BMI_Low} and BMI_Normal for the sample JSON file presented before.    

