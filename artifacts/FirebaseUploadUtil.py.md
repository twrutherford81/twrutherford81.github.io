```python
"""
This script allows the users to upload data from a CSV file to
a Firestore database. The Firestore service account JSON file,
the CSV file, and the collection name are optional parameters.

:author:  Thomas Rutherford
:version: 1.0.0
"""
import csv
import argparse
import firebase_admin
from firebase_admin import credentials, firestore

# Script defaults
DEFAULT_FIREBASE_SERVICE_ACCOUNT = "../StatesSearch/cs-499-snhu-capstone-5c43ff995aac.json"
DEFAULT_CSV_FILE = "states_data.csv"
DEFAULT_COLLECTION = "states"


def initialize_firebase(svc_account = DEFAULT_FIREBASE_SERVICE_ACCOUNT):
    """
    Initialize the Firebase Firestore client
    :param svc_account: Firebase Firestore service account
    :return: the Firebase Firestore client
    """
    # Initialize Firestore
    cred = credentials.Certificate(svc_account)
    firebase_admin.initialize_app(cred)
    return firestore.client()


def parse_args():
    """
    Attempts to parse the command line arguments. If the arguments are not
    provided, default values will be used.
    The valid arguments are:
        --svc_account: Firebase service account to use (*.json)
        --csv_file: the CSV file to parse (*.csv)
        --collection: the collection to upload to
    :return: the resolved command line arguments
    """
    parser = argparse.ArgumentParser(description="Upload CSV data to Firestore")
    parser.add_argument(
        "--svc_account",
        type=str,
        default=DEFAULT_FIREBASE_SERVICE_ACCOUNT,
        help=f"Path to the Firebase service account JSON file (default: {DEFAULT_FIREBASE_SERVICE_ACCOUNT})"
    )
    parser.add_argument(
        "--csv_file",
        type=str,
        default=DEFAULT_CSV_FILE,
        help=f"Path to the CSV file (default: {DEFAULT_CSV_FILE})"
    )
    parser.add_argument(
        "--collection",
        type=str,
        default=DEFAULT_COLLECTION,
        help=f"Firestore collection name (default: {DEFAULT_COLLECTION})"
    )

    return parser.parse_args()

def convert_types(row):
    """
    Converts numeric string values in the row to int or float types.
    :param row: Dictionary representing a CSV row
    :return: Dictionary with converted types
    """
    converted = {}
    for key, value in row.items():
        if value is None:
            converted[key] = value
            continue
        # Try to convert to int
        try:
            converted[key] = int(value)
            continue
        except ValueError:
            pass
        # Try to convert to float
        try:
            converted[key] = float(value)
            continue
        except ValueError:
            pass
        # Leave as string if not numeric
        converted[key] = value
    return converted


def upload_csv_data(csv_file, collection_name):
    """
    Uploads the data from the given CSV file to given Firestore collection.
    NB: the CSV file must have the following format:
    - Field names in the top row
    - First column defines the document ID (must be unique)

    :param csv_file: the (*.csv) file to parse
    :param collection_name: the collection to upload to
    """
    print(f"Attempting to upload data from '{csv_file}' to the '{collection_name}' collection ")

    with open(csv_file, newline='', encoding='utf-8') as csvfile:
        reader = csv.DictReader(csvfile)
        for row in reader:
            doc_id = row.pop(reader.fieldnames[0])  # First column is the document ID
            row = convert_types(row)
            db.collection(collection_name).document(doc_id).set(row)

    print("Uploaded complete!")


# Usage
if __name__ == "__main__":
    """
    This is the main entry point for the script.
    Run this script from the command line to upload data from a CSV file.
    Usage example:
    python FirebaseUploadUtil.py --svc_account path/to/serviceAccount.json \
                                 --csv_file path/to/data.csv \
                                 --collection my_collection
    """
    args = parse_args()
    db = initialize_firebase(args.svc_account)
    upload_csv_data(args.csv_file, args.collection)
```