---
name: code-guidelines
description: This skill should be used whenever writing, modifying, or reviewing C# code in this codebase. It defines the structural patterns all code must follow.
user-invocable: false
---

## Instructions

When writing or modifying C# code, follow all guidelines below. These override typical C# conventions where they differ.

## Two Types of Objects

This codebase follows functional programming guidelines. There are two types of objects: **Functions** and **Data**. Functions are (generally) side effect free. Data is (generally) immutable.

## Functional Object Guidelines

* Use a dependency injection pattern for object creation.
* Must have a primary constructor that takes the parameters and assigns them to fields with no modification.
* Other constructors which perform lightweight manipulation of arguments must call the primary constructor and not do anything else.
* The fields of the object should be immutable and the objects it holds be immutable.
* Should have an interface to facilitate unit testing.
* Only a factory object may create other functional objects.
* Optional dependencies use nullable parameters with null-coalescing defaults in the constructor body:
```csharp
internal ChannelEvaluator(
    IAuctionPlacementContext auctionContext,
    IBucketizer? bucketizer = null,
    IRankEvaluator? rankEvaluator = null)
{
    _bucketizer = bucketizer ?? new Bucketizer();
    _rankEvaluator = rankEvaluator ?? new RankEvaluator(auctionContext);
}
```

### Service Constructor Pattern

Services use a dual-constructor pattern. The internal constructor is the primary constructor — it takes pre-built dependencies directly. The public constructor wraps framework dependencies (e.g. `IConfigAsCodeService`, `ICbsGraphiteHelper`) into domain wrappers (*Cac, *Metrics) and chains to the internal constructor:

```csharp
// Primary: takes domain dependencies directly (used by tests)
internal BiddingService(
    IPredictionService predictionService,
    BiddingCac biddingCac,
    IBiddingMetrics biddingMetrics)
{
    _predictionService = predictionService;
    _biddingCac = biddingCac;
    _biddingMetrics = biddingMetrics;
}

// Secondary: wraps framework deps, chains to primary (used by production DI)
public BiddingService(
    IPredictionService predictionService,
    IConfigAsCodeService configAsCodeService,
    ICbsGraphiteHelper cbsGraphiteHelper)
    : this(predictionService, new BiddingCac(configAsCodeService), new BiddingMetrics(cbsGraphiteHelper))
{
}
```

## Data Object Guidelines

* They are immutable and their properties should be immutable.
* Use `{ get; init; }` for properties set during construction via object initializer. Use `{ get; }` for properties that must be provided to the constructor.
* Default string properties to `string.Empty`, not `null`.
* Provide a `static readonly Default` singleton when a type has a natural default state with specific values:
```csharp
public static readonly QualityScores Default =
    new(new PredictionValue(1.0))
    {
        PredictedCtr = 1.0,
        PredictedNbClicksGivenDisplay = 1.0,
    };
```
* Use `readonly record struct` for small value types that need structural equality.
* Use `IReadOnlyCollection<T>` (not `List<T>`) in public method signatures and interface properties. Use `IList<T>` or `List<T>` only when the caller must mutate the collection.

## Other Guidelines

* Nullability is enabled on the project and nullable violations are errors not warnings.
* Use expression bodies on a method or properties whenever possible.
* Prefer early returns (guard clauses) over nested conditionals.
* Use ternary operators for simple binary decisions.
* Don't use blocks unless necessary.
* Objects have `internal` visibility unless referenced outside the project.
* Unit test projects have visibility to internal objects in the project they are testing.
* Put public objects in the root of the project.
* Metrics should always be wrapped in a <name>Metrics object with an interface and hide the details of how metrics are recorded.
* ConfigAsCode should always be wrapped in a <name>Cac object and hide the details of how config as code works.
* When initializing 8+ init properties, sort the properties alphabetically.
