Getting Started
==


#### Requirements.
* Visual Studio 2012 or 2013 with NuGet package manager.

#### Download NUnitBenchmarker solutions from GitHub.
You will need these repositories:
* https://github.com/Orcomp/NUnitBenchmarker

(Later NUnitBenchmarker will be available as Nuget package)

#### Building The Solution

You will need Nuget installed on your computer.

The first time you build the solution, Nuget will fetch the required packages. (If it fails to do this you may have to right click on the solution and enable "Nuget Restore".) and then rebuild the solution again. This time it should work.



#### Architecture

In a typical scenario there will be four participants:

* The implementation under performance test: **The Testee**
* The unit test like method what you are about to write now: **The Test**
* The NUnitBenchmarker helper framework which is a class library assembly: **NUnitBenchmarker**
* The GUI which is a WPF executable: **The GUI**

Your **Test** will reference **NUnitBenchmarker** and will use the static Benchmarker class's helper methods.
That's all. Well... not exactly. You will have to implement a standard NUnit TestCaseSource class which is a factory for drive the test cases.

When your test will run in any runner, it will launch and communicate with *The GUI*. This communication is optional cou can run the thest headless and use the output PDFs



#### Writing a performance test

* Create a unit test project and refer to **NUnitBenchmarker**
* Suppose you have two IList implementations implemented in **The Testee**
* Write a unit test method like this: (Note: we are using only standard NUnit attributes)


```csharp
[Test, TestCaseSource(typeof(ListPerformanceTestFactory<int>), "TestCases")]
public void AddTest(ListPerformanceTestCaseConfiguration<int> conf)
{
	var itemsToAdd = ListPerformanceTestHelper<int>.GenerateItemsToAdd(conf).ToArray();
	var target = ListPerformanceTestHelper<int>.CreateListInstance(conf);
			
	var action = new Action(() =>
	{
		foreach (var item in itemsToAdd)
		{
			target.Add(item);
		}
	});

	action.Benchmark(conf, "Add", conf.ToString());
}

* This method above is testing an IList implementation's Add method for different number of items to add.
* The factory class is ListPerformanceTestFactory its factory method is TestCases
* The Benchmarker static class is called the implemented action like Benchmarker.Bencmark(action, ....) using the extension method syntactic sugar.


* You must implement the ListPerformanceTestFactory class and its TestCase method

```csharp
public class ListPerformanceTestFactory<T> 
{
	public IList<Type> Implementations { get; private set; }

	public ListPerformanceTestFactory()
	{
		// Issue in NUnit: This constructor is called _earlier_ than TestFixtureSetup....

		// It would be ideal if this class would be a Singleton. However this class instantiated
		// by the runner by calling its public parameterless constructor so we have no way to prevent
		// multiple instances exist. This will not cause any error, just an optimizaion thing.
		Implementations = Benchmarker.GetImplementations(typeof(IList<>), true).ToList();
	}
		 
	public IEnumerable<ListPerformanceTestCaseConfiguration<T>> TestCases()
	{
		// Issue in NUnit: even this method is called _earlier_ than TestFixtureSetup....
		// so we can not call GetImplementations here, because FindImplementatins was not called yet :-(

		var lastImplementation = Implementations.LastOrDefault();

		foreach (var implementation in Implementations)
		{
			var identifier = string.Format("{0}", implementation.GetFriendlyName());

			yield return new ListPerformanceTestCaseConfiguration<T>()
			{
				Identifier = identifier,
				TargetImplementationType = implementation,
				IsLast = false,
				Size = 100,
				DummyForTesting = 0
			};

			yield return new ListPerformanceTestCaseConfiguration<T>()
			{
				Identifier = identifier,
				TargetImplementationType = implementation,
				IsLast = false,
				Size = 1000,
				DummyForTesting = 0
			};


			yield return new ListPerformanceTestCaseConfiguration<T>()
			{
				Identifier = identifier,
				TargetImplementationType = implementation,
				IsLast = false,
				Size = 10000,
				DummyForTesting = 0
			};

			yield return new ListPerformanceTestCaseConfiguration<T>()
			{
				Identifier = identifier,
				TargetImplementationType = implementation,
				IsLast = implementation == lastImplementation,
				Size = 100000,
				DummyForTesting = 0
			};
		}
	}
}


