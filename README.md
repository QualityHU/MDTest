MDTest
======

MDTest

[License](LICENSE)

[Spec](docs/Specifications.md)

qweqwe qweqwe qweqweqweqwe
adasdaasdasd asdasdasdasd

p2 asdasd asdasdasdasd

```csharp
[Test, TestCaseSource(typeof(ListPerformanceTestFactory<int>), "TestCases")]
[MaxTime(10000)]
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

