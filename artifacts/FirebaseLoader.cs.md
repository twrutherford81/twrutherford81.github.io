---
layout: default
title: Thomas Rutherford
description: FirebaseLoader.cs
back_link: ../index.html
---

```cs
using Google.Cloud.Firestore;

namespace StatesSearch
{
    /// <summary>
    /// Class to load data from Google Firebase and store it for use by the StatesSearch application.
    /// If Firebase cannot be reached or an error occurs, the embedded data will be used.
    /// 
    /// <para>
    /// author: Thomas Rutherford
    /// version: 1.0
    /// </para>
    /// </summary>
    public class FirebaseLoader
    {
        /// <summary>A reference to the Firebase Firestore database</summary>
        private readonly FirestoreDb? firestoreDb;

        /// <summary>The file name of the Firestore key</summary>
        private static readonly string FIREBASE_KEY_FILENAME = "cs-499-snhu-capstone-5c43ff995aac.json";

        /// <summary>
        /// Path to the json config file stored on the local PC used to authenticate with Firebase.
        /// This file is temporarily stored on the client's PC so that an environment variable can
        /// be set against it. Once Firebase is authenticated, the file is deleted.
        /// </summary>
        private readonly string LOCAL_FIREBASE_CONFIG_FILE = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, FIREBASE_KEY_FILENAME);

        /// <summary>
        /// Name of the environment variable needed for Firebase authentication.  The variable must be set to the
        /// path of the json key file created on the Firebase console.
        /// </summary>
        private const string FIREBASE_AUTH_ENV_VAR = "GOOGLE_APPLICATION_CREDENTIALS";

        /// <summary>Project ID of the CS-499 SNHU Capstone project needed to access the database</summary>
        public const string PROJECT_ID = "cs-499-snhu-capstone";


        /// <summary>
        /// Creates a new instance of the <c>FirebaseLoader</c> and initializes Firebase.
        /// Firebase is authenticated via the <c>GOOGLE_APPLICATION_CREDENTIALS</c>
        /// environment variable.  The variable must be set to the path of the json file
        /// containing the key.
        /// </summary>
        public FirebaseLoader()
        {
            try
            {
                // Set an ENV variable for the Firebase key file
                Environment.SetEnvironmentVariable(FIREBASE_AUTH_ENV_VAR, LOCAL_FIREBASE_CONFIG_FILE);

                // Get the firebase instance and authorize it
                firestoreDb = FirestoreDb.Create(PROJECT_ID);
            }
            catch (Exception ex)
            {
                firestoreDb = null;
                MessageBox.Show("Failed to establish connection with the database." + Environment.NewLine + Environment.NewLine + ex.Message,
                    "Connection Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }

        /// <summary>
        /// <c>Async</c> method to load the states data into a list for program use. If an error
        /// occurs while retrieving the data, a static list of states will be returned instead.
        /// </summary>
        /// <returns>The list of <see cref="State"/> objects</returns>
        public async System.Threading.Tasks.Task<List<State>> LoadStatesData()
        {
            var states = new List<State>();
            try
            {
                if (firestoreDb == null)
                    throw new InvalidOperationException("Firestore database is not initialized.");

                var snapshot = await firestoreDb
                  .Collection("states")
                  .GetSnapshotAsync();

                states = snapshot.Documents
                    .Select(stateDoc => new State(
                        stateDoc.Id,
                        stateDoc.GetValue<string>("abbreviation"),
                        stateDoc.GetValue<string>("capital"),
                        stateDoc.GetValue<int>("population"),
                        stateDoc.GetValue<int>("size"),
                        stateDoc.GetValue<string>("region"),
                        stateDoc.GetValue<string>("timezone")
                    ))
                    .ToList();
            }
            catch (Exception ex)
            {
                MessageBox.Show("An error occurred while retrieving data from the database. The default static data will be used instead." +
                    Environment.NewLine + Environment.NewLine + ex.Message,
                    "Data Retrieval Error", MessageBoxButtons.OK, MessageBoxIcon.Warning);
                states = GetDefaultStateList();
            }

            return states;
        }

        /// <summary>
        /// Retrieves a default list of U.S. states with their associated details.
        /// </summary>
        /// <returns>A static <see cref="List{T}"/> of <see cref="State"/> objects</returns>
        private List<State> GetDefaultStateList()
        {
            var list = new List<State>
            {
                new State("Alabama", "AL", "Montgomery", 5024279, 50645, "Southeast", "Central"),
                new State("Alaska", "AK", "Juneau", 733391, 570641, "West", "Alaska"),
                new State("Arizona", "AZ", "Phoenix", 7151502, 113594, "West", "Mountain"),
                new State("Arkansas", "AR", "Little Rock", 3011524, 52035, "Southeast", "Central"),
                new State("California", "CA", "Sacramento", 39538223, 155779, "West", "Pacific"),
                new State("Colorado", "CO", "Denver", 5773714, 103642, "West", "Mountain"),
                new State("Connecticut", "CT", "Hartford", 3605944, 4842, "Northeast", "Eastern"),
                new State("Delaware", "DE", "Dover", 989948, 1949, "Northeast", "Eastern"),
                new State("Florida", "FL", "Tallahassee", 21538187, 53625, "Southeast", "Eastern"),
                new State("Georgia", "GA", "Atlanta", 10711908, 57513, "Southeast", "Eastern"),
                new State("Hawaii", "HI", "Honolulu", 1455271, 6423, "West", "Hawaii-Aleutian"),
                new State("Idaho", "ID", "Boise", 1839106, 82643, "West", "Mountain"),
                new State("Illinois", "IL", "Springfield", 12812508, 55519, "Midwest", "Central"),
                new State("Indiana", "IN", "Indianapolis", 6785528, 35826, "Midwest", "Eastern"),
                new State("Iowa", "IA", "Des Moines", 3190369, 55857, "Midwest", "Central"),
                new State("Kansas", "KS", "Topeka", 2937880, 81759, "Midwest", "Central"),
                new State("Kentucky", "KY", "Frankfort", 4505836, 39486, "Southeast", "Eastern"),
                new State("Louisiana", "LA", "Baton Rouge", 4657757, 43204, "Southeast", "Central"),
                new State("Maine", "ME", "Augusta", 1362359, 30843, "Northeast", "Eastern"),
                new State("Maryland", "MD", "Annapolis", 6177224, 9707, "Northeast", "Eastern"),
                new State("Massachusetts", "MA", "Boston", 7029917, 10554, "Northeast", "Eastern"),
                new State("Michigan", "MI", "Lansing", 10077331, 57914, "Midwest", "Eastern"),
                new State("Minnesota", "MN", "Saint Paul", 5706494, 86936, "Midwest", "Central"),
                new State("Mississippi", "MS", "Jackson", 2961279, 48432, "Southeast", "Central"),
                new State("Missouri", "MO", "Jefferson City", 6154913, 69707, "Midwest", "Central"),
                new State("Montana", "MT", "Helena", 1084225, 147040, "West", "Mountain"),
                new State("Nebraska", "NE", "Lincoln", 1961504, 76824, "Midwest", "Central"),
                new State("Nevada", "NV", "Carson City", 3104614, 109781, "West", "Pacific"),
                new State("New Hampshire", "NH", "Concord", 1377529, 8953, "Northeast", "Eastern"),
                new State("New Jersey", "NJ", "Trenton", 9288994, 7354, "Northeast", "Eastern"),
                new State("New Mexico", "NM", "Santa Fe", 2117522, 121590, "West", "Mountain"),
                new State("New York", "NY", "Albany", 20201249, 47214, "Northeast", "Eastern"),
                new State("North Carolina", "NC", "Raleigh", 10439388, 53819, "Southeast", "Eastern"),
                new State("North Dakota", "ND", "Bismarck", 779094, 69001, "Midwest", "Central"),
                new State("Ohio", "OH", "Columbus", 11799448, 40861, "Midwest", "Eastern"),
                new State("Oklahoma", "OK", "Oklahoma City", 3959353, 69899, "Southwest", "Central"),
                new State("Oregon", "OR", "Salem", 4237256, 98379, "West", "Pacific"),
                new State("Pennsylvania", "PA", "Harrisburg", 13002700, 44817, "Northeast", "Eastern"),
                new State("Rhode Island", "RI", "Providence", 1097379, 1034, "Northeast", "Eastern"),
                new State("South Carolina", "SC", "Columbia", 5118425, 32020, "Southeast", "Eastern"),
                new State("South Dakota", "SD", "Pierre", 886667, 75811, "Midwest", "Central"),
                new State("Tennessee", "TN", "Nashville", 6910840, 42144, "Southeast", "Central"),
                new State("Texas", "TX", "Austin", 29145505, 261232, "Southwest", "Central"),
                new State("Utah", "UT", "Salt Lake City", 3271616, 82170, "West", "Mountain"),
                new State("Vermont", "VT", "Montpelier", 643077, 9250, "Northeast", "Eastern"),
                new State("Virginia", "VA", "Richmond", 8631393, 39490, "Southeast", "Eastern"),
                new State("Washington", "WA", "Olympia", 7693612, 71298, "West", "Pacific"),
                new State("West Virginia", "WV", "Charleston", 1793716, 24038, "Southeast", "Eastern"),
                new State("Wisconsin", "WI", "Madison", 5893718, 54310, "Midwest", "Central"),
                new State("Wyoming", "WY", "Cheyenne", 576851, 97093, "West", "Mountain")
            };
            return list;
        }
    }
}
```