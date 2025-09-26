---
layout: default
title: Thomas Rutherford
description: BoyerMooreSearch.cs
back_link: ../index.html
---

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace StatesSearch
{
    /// <summary>
    /// C# implementation of a Boyer Moore Algorithm used to search
    /// text for a given pattern. This class was adapted from a java
    /// implementation originally created for Study.com CS201 Assignment 2.
    /// 
    /// The original implementation utilized static search text and allowed
    /// the user to specify the pattern to search for. This implementation
    /// initializes the search pattern when the class is created and allows
    /// the user to specify the text to search when the search method is called.
    /// 
    /// <para>
    /// author: Thomas Rutherford
    /// version: 2.0
    /// </para>
    /// </summary>
    internal class BoyerMooreSearch
    {
        private readonly int[] badChar;
        private readonly int[] goodSuffix;
        private readonly string pattern;
        private readonly int patternLength;

        /// <summary>The number of ASCII characters that could possibly be in the search text</summary>
        private static readonly int NUM_ASCII_CHARS = 256;

        /// <summary>
        /// Creates a new instance of the <see cref="BoyerMooreSearch"/> class and initializes it
        /// with the specified search pattern.
        /// </summary>
        /// <param name="pattern">The pattern to search for. This value cannot be <see langword="null"/> or empty.</param>
        public BoyerMooreSearch(string pattern)
        {
            if (string.IsNullOrEmpty(pattern))
                throw new ArgumentException("Pattern cannot be null or empty.", nameof(pattern));

            // Convert the pattern to lower case for case-insensitive search
            pattern = pattern.Trim().ToLower();

            this.pattern = pattern;
            this.patternLength = pattern.Length;
            this.badChar = BuildBadCharTable();
            this.goodSuffix = BuildGoodSuffixTable();
        }

        /// <summary>
        /// Builds the bad character table for the Boyer-Moore string search algorithm.
        /// </summary>
        /// <returns>An int[] array <see cref="NUM_ASCII_CHARS"/> long representing the bad character shift</returns>
        private int[] BuildBadCharTable()
        {
            var table = Enumerable.Repeat(patternLength, NUM_ASCII_CHARS).ToArray();
            for (int i = 0; i < patternLength - 1; i++)
                table[(byte)pattern[i]] = patternLength - 1 - i;
            return table;
        }

        /// <summary>
        /// Builds the good suffix table for the Boyer-Moore string search algorithm.
        /// The good suffix table is used to determine how far the search window can be shifted
        /// when a mismatch occurs during pattern matching based on the good suffix heuristic.
        /// </summary>
        /// <returns>An array of integers representing the good suffix shift values for each position in the pattern. The array
        /// length is equal to the length of the pattern.</returns>
        private int[] BuildGoodSuffixTable()
        {
            // Array to hold the shift values for the good suffix heuristic
            var goodSuffixTable = new int[patternLength];
            // Array to hold the border positions (used to find matching suffixes)
            var borderPositions = new int[patternLength + 1];
            // Start from the end of the pattern
            int patternIndex = patternLength;
            int borderIndex = patternLength + 1;

            // Initialize the last border position
            borderPositions[patternIndex] = borderIndex;

            // Loop to calculates border positions and initial good suffix shift values
            while (patternIndex > 0)
            {
                while (borderIndex <= patternLength && pattern[patternIndex - 1] != pattern[borderIndex - 1])
                {
                    goodSuffixTable[borderIndex - 1] = borderIndex - patternIndex;
                    borderIndex = borderPositions[borderIndex];
                }
                borderPositions[--patternIndex] = --borderIndex;
            }

            // Loop to fill in remaining good suffix shift values using border information
            borderIndex = borderPositions[0];
            for (patternIndex = 0; patternIndex < patternLength; patternIndex++)
            {
                if (goodSuffixTable[patternIndex] == 0)
                    goodSuffixTable[patternIndex] = borderIndex;
                else if (patternIndex == borderIndex)
                    borderIndex = borderPositions[borderIndex];
            }

            // Return the completed good suffix shift table
            return goodSuffixTable;
        }

        /// <summary>
        /// Searches for the first occurrence of the pattern (case-insensitive) in the specified text and
        /// returns its starting index. The pattern is expected to be defined when this class is created.
        /// </summary>
        /// <param name="text">The text in which to search for the pattern. This value cannot be <see langword="null"/> or empty.</param>
        /// <returns>The zero-based index of the first occurrence of the pattern in the text, or -1 if the pattern is not found.</returns>
        public int Search(string text)
        {
            if (string.IsNullOrEmpty(text))
                throw new ArgumentException("Search text cannot be null or empty.", nameof(text));

            // Convert the text to lower case for case-insensitive search
            text = text.Trim().ToLower();

            int textLength = text.Length;
            int textIndex = 0;

            // Loop through the text until the search window exceeds the text length
            while (textIndex <= textLength - patternLength)
            {
                int patternIndex = patternLength - 1;

                // Compare pattern from end to start
                while (patternIndex >= 0 && pattern[patternIndex] == text[textIndex + patternIndex])
                {
                    patternIndex--;
                }

                // If the pattern is found, return the starting index
                if (patternIndex < 0)
                    return textIndex;

                // Shift the search window using the maximum of good suffix and bad character heuristics
                textIndex += Math.Max(goodSuffix[patternIndex], badChar[(byte)text[textIndex + patternIndex]]);
            }

            // Pattern not found
            return -1;
        }
    }
}
```