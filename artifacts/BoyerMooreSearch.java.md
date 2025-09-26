---
layout: default
title: Thomas Rutherford
description: BoyerMooreSearch.java
---

```java
package com.study.cs201.assignmenttwo;

import java.io.IOException;
import java.util.Scanner;

import com.study.cs201.assignmentone.BinarySearchTree;

/**
 * Java implementation of a Boyer Moore Algorithm used to search
 * text for a given pattern.
 * <p>
 * Study.com CS201 Assignment 2
 *
 * @author Thomas Rutherford
 * @version 1.0 10/28/2024
 */
public class BoyerMooreSearch
{
    /** {@link Scanner} used to get user input from the command line */
    private Scanner scanner;

    /** Flag indicating when the program loop should stop */
    private boolean stop = false;

    /** The number of ASCII characters that could possibly be in the search text */
    private static final int NUM_ASCII_CHARS = 256;

    /** A list of the 50 United States used as the {@link String} to search */
    private static final String searchText = "Alabama Alaska Arizona Arkansas California Colorado "
            + "Connecticut Delaware Florida Georgia Hawaii Idaho Illinois Indiana Iowa Kansas Kentucky "
            + "Louisiana Maine Maryland Massachusetts Michigan Minnesota Mississippi Missouri Montana "
            + "Nebraska Nevada New Hampshire New Jersey New Mexico New York North Carolina North Dakota "
            + "Ohio Oklahoma Oregon Pennsylvania Rhode Island South Carolina South Dakota Tennessee Texas "
            + "Utah Vermont Virginia Washington West Virginia Wisconsin Wyoming";

    /**
     * The main entry point of the program. This will create a new instance of the
     * {@link BinarySearchTree} and start its main loop.
     *
     * @param args program arguments
     */
    public static void main( String[] args )
    {
        BoyerMooreSearch boyerMooreSearch = new BoyerMooreSearch();
        boyerMooreSearch.run();
    }

    /**
     * Creates a new instance of the {@link BoyerMooreSearch} class and initializes
     * it for use.
     */
    private BoyerMooreSearch()
    {
        this.scanner = new Scanner( System.in );
    }

    /**
     * Run the program. This will create a program loop that will continuously run
     * until the user exits the program or an error occurs.
     */
    public void run()
    {
        while ( !stop )
        {
            displayMenu();
            handleMenuChoice();
        }

        // If the loop ended, the program is finished
        scanner.close();
        System.out.println( "Program terminated" );
        System.exit( 0 );
    }

    /**
     * Prints a menu to the screen and prompts for a user choice.
     */
    private void displayMenu()
    {
        System.out.println( "------------------------------------------------------------" );
        System.out.println( "- 1 Display the text" );
        System.out.println( "- 2 Search" );
        System.out.println( "- 3 Exit program" );
        System.out.println( "------------------------------------------------------------" );
        System.out.println();
        System.err.print( "Enter a choice: " );
    }

    /**
     * Uses a {@link Scanner} to get input from the user and handles the user input.
     * If the input is not a valid choice, this method will print an error and loop
     * until a valid choice is entered.
     */
    private void handleMenuChoice()
    {
        // Holds the user choice (-1 for invalid)
        int userInput = -1;

        // Loop until the user enters a valid command or the program exits.
        do
        {
            try
            {
                // Try to get the user input as an int
                userInput = scanner.nextInt();
            }
            catch ( Exception e )
            {
                // Clear the scanner and set userInput to the error value
                scanner.nextLine();
                userInput = -1;
            }

            System.out.println();

            switch ( userInput )
            {
                case 1:
                    // Print the text being searched
                    System.out.println( "Text (50 States):\n" );
                    System.out.println( searchText );
                    System.out.println();

                    break;
                case 2:
                    // search
                    getSearchPattern();
                    break;
                case 3:
                    // Set the flag to break the main loop and return
                    // to prevent the pause in the command line.
                    stop = true;
                    return;
                default:
                    // Print and error and reprompt for a choice
                    System.out.println( "You entered an invalid choice, enter a number between 1 and 3" );
                    displayMenu();
                    userInput = -1;
                    break;
            }
        }
        while ( userInput == -1 );

        // Pause the program to allow the user to view the results
        System.out.print( "\nPress Enter to continue..." );
        try
        {
            System.in.read();
            System.out.println();
        }
        catch ( IOException e )
        {
            System.err.println( "Failure waiting for input: " + e.getMessage() );
            stop = true;
        }
    }

    /**
     * Prompts the user for a pattern to search for in the search text. If the
     * pattern is valid, the text will be search for the pattern.
     */
    private void getSearchPattern()
    {
        // Prompt for the new node value
        System.out.print( "Enter pattern to search for (case sensative): " );

        // Holds the user input, (null for invalid)
        String userInput = null;

        try
        {
            // Try to get the user input
            userInput = scanner.next();
        }
        catch ( Exception e )
        {
            // Clear the scanner and set userInput to the error value
            scanner.nextLine();
            userInput = null;
        }

        if ( userInput == null )
        {
            System.out.println( "Search pattern is invalid, cancelling operation" );
        }

        search( userInput );
    }

    // A utility function to get maximum of two integers
    static int max( int a, int b )
    {
        return ( a > b ) ? a : b;
    }

    // The preprocessing function for Boyer Moore's
    // bad character heuristic
    private void badCharacter( char[] str, int size, int badchar[] )
    {

        // Initialize all occurrences as -1
        for ( int i = 0; i < NUM_ASCII_CHARS; i++ )
            badchar[i] = -1;

        // Fill the actual value of last occurrence
        // of a character (indices of table are ascii and
        // values are index of occurrence)
        for ( int i = 0; i < size; i++ )
            badchar[(int) str[i]] = i;
    }

    /*
     * A pattern searching function that uses Bad Character Heuristic of Boyer Moore
     * Algorithm
     */
    private void search( String pattern )
    {
        char[] pat = pattern.toCharArray();
        char[] txt = searchText.toCharArray();

        int m = pat.length;
        int n = txt.length;

        int badchar[] = new int[NUM_ASCII_CHARS];

        /*
         * Fill the bad character array by calling the preprocessing function
         * badCharHeuristic() for given pattern
         */
        badCharacter( pat, m, badchar );

        // s is shift of the pattern with
        // respect to text
        int index = 0;

        // there are n-m+1 potential alignments
        while ( index <= ( n - m ) )
        {
            int j = m - 1;

            /*
             * Keep reducing index j of pattern while characters of pattern and text are
             * matching at this shift s
             */
            while ( j >= 0 && pat[j] == txt[index + j] )
                j--;

            /*
             * If the pattern is present at current shift, then index j will become -1 after
             * the above loop
             */
            if ( j < 0 )
            {
                System.out.println( "Found pattern at index " + index );

                /*
                 * Shift the pattern so that the next character in text aligns with the last
                 * occurrence of it in pattern. The condition s+m < n is necessary for the case
                 * when pattern occurs at the end of text
                 */
                // txt[s+m] is character after the pattern
                // in text
                index += ( index + m < n ) ? m - badchar[txt[index + m]] : 1;
            }

            else
                /*
                 * Shift the pattern so that the bad character in text aligns with the last
                 * occurrence of it in pattern. The max function is used to make sure that we
                 * get a positive shift. We may get a negative shift if the last occurrence of
                 * bad character in pattern is on the right side of the current character.
                 */
                index += max( 1, j - badchar[txt[index + j]] );
        }
    }
}
```