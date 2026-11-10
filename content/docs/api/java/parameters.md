---
weight: 30
---

# Parameters

Below are examples of all the parameter types that can be written to and read from the FAIR Data Pipeline TOML parameter file format.

Samples

```Java
samples = ImmutableSamples.builder().addSamples(1, 2, 3).rng(rng).build();
object_component.writeSamples(samples);
```

Distribution (Gamma)

```Java
distribution =
    ImmutableDistribution.builder()
        .internalShape(1)
        .internalScale(2)
        .internalType(DistributionType.gamma)
        .rng(rng)
        .build();
object_component.writeDistribution(distribution);
```

Categorical Distribution

```Java
    MinMax firstMinMax =
        ImmutableMinMax.builder()
            .isLowerInclusive(true)
            .isUpperInclusive(true)
            .lowerBoundary(0)
            .upperBoundary(4)
            .build();

    MinMax secondMinMax =
        ImmutableMinMax.builder()
            .isLowerInclusive(true)
            .isUpperInclusive(true)
            .lowerBoundary(5)
            .upperBoundary(9)
            .build();

    MinMax thirdMinMax =
        ImmutableMinMax.builder()
            .isLowerInclusive(true)
            .isUpperInclusive(true)
            .lowerBoundary(10)
            .upperBoundary(14)
            .build();

    MinMax fourthMinMax =
        ImmutableMinMax.builder()
            .isLowerInclusive(true)
            .isUpperInclusive(true)
            .lowerBoundary(15)
            .upperBoundary(20)
            .build();

    categoricalDistribution =
        ImmutableDistribution.builder()
            .internalType(DistributionType.categorical)
            .bins(List.of(firstMinMax, secondMinMax, thirdMinMax, fourthMinMax))
            .weights(List.of(0.4, 0.1, 0.1, 0.4))
            .rng(rng)
            .build();
object_component.writeDistribution(categoricalDistribution);
```

Estimate

```Java
object_component.writeEstimate(1.234);
```