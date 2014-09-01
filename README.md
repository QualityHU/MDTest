# SortedSplitList 

## Introduction
This solution contains performance tests for various sorted data structures.
The goal of this library is to measure and compare performance of these implementations
The name comes from the original name of the first data structure under test.

Some implementations may reside (copied) here with source, others just referred as NuGet package or are part of the standard .NET Framework

## Method

Currently the main focus on the Add/Remove/Search operations.
As the implementations under tests does not implement any common interface a simplistic test interface is defined to make test reusable.
The access to the original functionality happens via a lightweight wrapper.

For performance and visualizing results testing this project uses NunitBenchmarker (https://github.com/Orcomp/NUnitBenchmarker). Start with PerformanceTest classes, run the tests just as you would run any ordinary unit test.


## Implementations under test

- **SortedSplitList** - This code was taken from article: "SortedSplitList - An Indexing Algorithm in C#" by Aurelien Boudoux. See code for more details
- **SortedList** - Taken from Orcomp other internal project 'Stockpile' (planned)
- **SortedList** - .NET Framework (planned)
- **C5.???** - C5's sorted data structures (planned)


## Planned

- More operations to test not just Add/Remove/Search


