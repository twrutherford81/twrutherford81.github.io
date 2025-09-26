---
layout: default
title: Thomas Rutherford
description: State.cs
---

```cs
namespace StatesSearch
{
    /// <summary>
    /// Class that defines a state data object. The primary constructor creates
    /// a new State object with the specified parameters.
    /// 
    /// <para>
    /// author: Thomas Rutherford
    /// version: 1.0
    /// </para>
    /// </summary>
    public class State(string name, string abbreviation, string capital,
        int population, int size, string region, string timeZone)
    {
        /// <summary>The name of the state.</summary>
        public string Name { get; init; } = name;

        /// <summary>The abbreviation of the state name.</summary>
        public string Abbreviation { get; init; } = abbreviation;

        /// <summary>The capital city of the state.</summary>
        public string Capital { get; init; } = capital;

        /// <summary>The population of the state.</summary>
        public int Population { get; init; } = population;

        /// <summary>The state area in square miles.</summary>
        public int Size { get; init; } = size;

        /// <summary>The region of the state.</summary>
        public string Region { get; init; } = region;

        /// <summary>The time zone of the state.</summary>
        public string TimeZone { get; init; } = timeZone;
    }
}
```