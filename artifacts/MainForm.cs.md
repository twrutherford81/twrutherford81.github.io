```cs
using Google.Protobuf.WellKnownTypes;
using System.ComponentModel;

namespace StatesSearch
{
    /// <summary>
    /// Class that defines the main user interface form for the application. This form is responsible for
    /// handling all user interactions and displaying relevant information.
    /// 
    /// <para>
    /// author: Thomas Rutherford
    /// version: 1.0
    /// </para>
    /// </summary>
    public partial class MainForm : Form
    {
        /// <summary>Error message displayed when no search criteria is provided.</summary>
        private const string ERROR_NO_SEARCH_CRITERIA = "Please enter search criteria.";

        /// <summary>Error message displayed when search yielded no results.</summary>
        private const string ERROR_NO_SEARCH_RESULTS = "Pattern not found.";

        /// <summary>Binding list to hold the states loaded from Firebase for display in the DataGridView.</summary>
        private readonly List<State> states = [];

        /// <summary>Binding list to hold the results for display in the DataGridView.</summary>
        private readonly BindingList<State> results = [];

        /// <summary>
        /// Creates a new instance of the MainForm class and initializes the UI.
        /// </summary>
        public MainForm()
        {
            InitializeComponent();
        }

        /// <summary>
        /// Method called when this dialog form loads. This will initialize the database connection
        /// and the search algorithm.
        /// </summary>
        /// <param name="sender">Object invoking this method</param>
        /// <param name="e">The event arguments</param>
        private void MainForm_Load(object sender, EventArgs e)
        {
            // Display the application version in the UI, this is set in the project properties
            // and will be updated automatically by the build script.
            this.VersionLabel.Text = "Version: " + Application.ProductVersion;

            // Set up the DataGridView to use the results binding list as its data source
            this.resultsDataGrid.AutoGenerateColumns = true;
            this.resultsDataGrid.DataSource = results;

            LoadFirebaseData();
        }

        /// <summary>
        /// <c>Async</c> method to load the states data into a list for program use.
        /// </summary>
        private async void LoadFirebaseData()
        {
            FirebaseLoader loader = new();
            var data = await loader.LoadStatesData();

            if (data == null)
            {
                MessageBox.Show("Failed to load states data, please try again.", "Program Error",
                                MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            states.Clear();
            states.AddRange(data);
            ShowStates(states); // Show all states on load
        }

        /// <summary>
        /// Called when a key is pressed while the search text box has focus.
        /// This allows the user to press Enter to initiate a search.
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void SearchTextBox_KeyDown(object sender, KeyEventArgs e)
        {
            if (e.KeyCode == Keys.Enter)
            {
                // Simulate search button click
                OnClickHandler(searchButton, EventArgs.Empty);
                e.Handled = true;
                e.SuppressKeyPress = true; // Prevent ding sound
            }
        }

        /// <summary>
        /// Handles the onClick event of the clickable UI buttons.
        /// </summary>
        /// <param name="sender">Object invoking this method</param>
        /// <param name="e">The event arguments</param>
        private void OnClickHandler(object sender, EventArgs e)
        {
            if (sender == null || sender is not Button)
            {
                return;
            }

            Button button = (Button)sender;
            Console.WriteLine("Button clicked: " + button.Name);

            // Clear previous search results and error messages
            results.Clear();
            ClearError();

            switch (button)
            {
                case var _ when button == searchButton:

                    var query = searchTextBox.Text.Trim();
                    if (query.Length == 0)
                    {
                        ShowError(ERROR_NO_SEARCH_CRITERIA);
                        return;
                    }

                    // Initialize the Boyer-Moore search algorithm with the query
                    // (lowercase for case-insensitive search)
                    var boyerMooreSearch = new BoyerMooreSearch(query.ToLowerInvariant());

                    // Perform the search on both state names and capitals
                    var bmResults = states
                        .Where(state =>
                            boyerMooreSearch.Search(state.Name.ToLowerInvariant()) >= 0 ||
                            boyerMooreSearch.Search(state.Capital.ToLowerInvariant()) >= 0)
                        .ToList();

                    if (bmResults.Count == 0)
                    {
                        ShowError(ERROR_NO_SEARCH_RESULTS);
                        return;
                    }
            
                    ShowStates(bmResults);
                    
                    break;
                case var _ when button == showAllButton:

                    // Clear the search box when showing all records
                    searchTextBox.Clear();

                    ShowStates(states);

                    break;
                default:
                    Console.WriteLine("Unknown button clicked: " + button.Name);
                    break;
            }
        }

        /// <summary>
        /// Updates the results collection with the provided list of states. Since this
        /// is a binding list, the grid will update automatically.
        /// </summary>
        /// <param name="statesList">A list of <see cref="State"/>s to be displayed.</param>
        private void ShowStates(List<State> statesList)
        {
            if (statesList == null)
                return;
            
            results.Clear();
            foreach (var state in statesList)
            {
                results.Add(state);
            }
        }

        /// <summary>
        /// Shows an error message on the screen.
        /// </summary>
        /// <param name="message">The error message to display</param>
        private void ShowError(string message)
        {
            this.errorLabel.Text = message;
            this.errorLabel.Visible = true;
        }

        /// <summary>
        /// Hides the error message from the screen.
        /// </summary>
        private void ClearError()
        {
            this.errorLabel.Text = string.Empty;
            this.errorLabel.Visible = false;
        }
    }
}
```