import pandas as pd
import csv
from data_entry import get_amount, get_category, get_date, get_description
from datetime import datetime
import matplotlib.pyplot as plt


class CSV:
    # A class to manage CSV operations related to financial data

    # Path to the CSV file storing financial transactions
    CSV_FILE = "finance_data.csv"

    # Column names for the CSV file
    COLUMNS = ["date", "amount", "category", "description"]

    # Date format used throughout the application
    FORMAT = "%d-%m-%Y"

    @classmethod
    def initialize_csv(cls):
        """Initializes the CSV file by creating it if it doesn't exist."""
        try:
            # Try to read the CSV file to ensure it exists
            pd.read_csv(cls.CSV_FILE)
        except FileNotFoundError:
            # If the file doesn't exist, create it with the specified columns
            df = pd.DataFrame(columns=cls.COLUMNS)
            df.to_csv(cls.CSV_FILE, index=False)  # Save the new CSV without the index column

    @classmethod
    def add_entry(cls, date, amount, category, description):
        """Adds a new transaction entry to the CSV file."""
        # Create a dictionary representing the new transaction
        new_entry = {
            "date": date,
            "amount": amount,
            "category": category,
            "description": description
        }

        # Append the new entry to the CSV file
        with open(cls.CSV_FILE, "a", newline="") as csvfile:
            writer = csv.DictWriter(csvfile, fieldnames=cls.COLUMNS)
            writer.writerow(new_entry)
        print("Entry added successfully")

    @classmethod
    def get_transactions(cls, start_date, end_date):
        """
        Retrieves and filters transactions within a specified date range from the CSV file.

        Parameters:
        - start_date: The start date of the range (string in 'DD-MM-YYYY' format).
        - end_date: The end date of the range (string in 'DD-MM-YYYY' format).

        Returns:
        - filtered_df: A DataFrame containing transactions within the date range.
        """
        # Read the entire CSV file into a DataFrame
        # This DataFrame will contain all the transactions stored in the CSV file
        df = pd.read_csv(cls.CSV_FILE)

        # Convert the 'date' column from string format to datetime objects
        # This conversion allows for easier comparison and filtering by dates
        df["date"] = pd.to_datetime(df["date"], format=cls.FORMAT)

        # Convert the provided start_date and end_date strings to datetime objects
        # This ensures the dates can be compared accurately with the 'date' column in the DataFrame
        start_date = datetime.strptime(start_date, cls.FORMAT)
        end_date = datetime.strptime(end_date, cls.FORMAT)

        # Create a boolean mask to filter the DataFrame
        # The mask will be True for rows where the 'date' is within the specified date range
        mask = (df["date"] >= start_date) & (df["date"] <= end_date)

        # Apply the mask to the DataFrame to select only the rows that match the date range
        filtered_df = df.loc[mask]

        # Check if the filtered DataFrame is empty
        # This occurs if there are no transactions in the given date range
        if filtered_df.empty:
            print("No transactions found in the given date range")
        else:
            # If transactions are found, display them
            # The start_date and end_date are formatted back to strings for display purposes
            print(f"Transactions from {start_date.strftime(cls.FORMAT)} to {end_date.strftime(cls.FORMAT)}")

            # Convert the filtered DataFrame to a string for printing, with the 'date' formatted
            # This displays the DataFrame without the index column and ensures the dates are shown in the 'DD-MM-YYYY' format
            print(filtered_df.to_string(index=False, formatters={"date": lambda x: x.strftime(cls.FORMAT)}))

            # Calculate the total income by summing up the 'amount' for transactions categorized as 'Income'
            total_income = filtered_df[filtered_df["category"] == "Income"]["amount"].sum()

            # Calculate the total expense by summing up the 'amount' for transactions categorized as 'Expense'
            total_expense = filtered_df[filtered_df["category"] == "Expense"]["amount"].sum()

            # Print a summary of the financial data
            # This includes the total income, total expense, and net savings (income minus expenses)
            print("\nSummary:")
            print(f"Total Income: ${total_income:.2f}")
            print(f"Total Expense: ${total_expense:.2f}")
            print(f"Net Savings: ${(total_income - total_expense):.2f}")

        # Return the filtered DataFrame so it can be used for further analysis, like plotting graphs
        return filtered_df


def add():
    """
    Gathers user input for a new transaction and adds it to the CSV file.
    """
    # Ensure the CSV file is initialized before adding any entries
    CSV.initialize_csv()

    # Prompt the user for the date of the transaction, with an option to use today's date as default
    date = get_date("Please enter the date of the transaction ('DD-MM-YYYY') or press Enter for today's date: ", allow_default=True)

    # Prompt the user for the amount of the transaction
    amount = get_amount()

    # Prompt the user for the category of the transaction ('Income' or 'Expense')
    category = get_category()

    # Prompt the user for a description of the transaction
    description = get_description()

    # Add the new transaction entry to the CSV file
    CSV.add_entry(date, amount, category, description)



def plot_transactions(df):
    """
    Plots a graph showing Income and Expenses over time using the given DataFrame.

    Parameters:
    - df: A DataFrame containing transactions with a 'date' column as the index.
    """
    # Set the 'date' column as the index of the DataFrame
    # This allows for easier plotting and manipulation of data based on dates
    df.set_index('date', inplace=True)

    # Create a DataFrame for Income transactions, resampled daily
    # 'resample("D").sum()' groups the data by day and sums the amounts for each day
    # 'reindex(df.index, fill_value=0)' ensures the index is aligned with the original DataFrame, filling missing dates with 0
    income_df = df[df["category"] == "Income"].resample("D").sum().reindex(df.index, fill_value=0)

    # Create a DataFrame for Expense transactions, resampled daily
    expense_df = df[df["category"] == "Expense"].resample("D").sum().reindex(df.index, fill_value=0)

    # Set up the plotting area with a specific size
    plt.figure(figsize=(10, 5))

    # Plot the Income data as a green line
    plt.plot(income_df.index, income_df["amount"], label="Income", color="g")

    # Plot the Expense data as a red line
    plt.plot(expense_df.index, expense_df["amount"], label="Expense", color="r")

    # Add labels and title to the plot
    plt.xlabel("Date")  # Label for the x-axis
    plt.ylabel("Amount")  # Label for the y-axis
    plt.title("Income and Expenses Over Time")  # Title of the plot

    # Display the legend to identify the lines
    plt.legend()

    # Show the plot
    plt.show()



def main():
    while True:
        print("\n1. Add a new transaction")
        print("2. View transactions and summary within a date range")
        print("3. Exit")
        choice = int(input("Enter a choice (1 or 2 or 3):"))
        if choice == 1:
            add()
        elif choice == 2:
            start_date = get_date("Enter the start date (DD-MM-YYYY): ")
            end_date = get_date("Enter the end date (DD-MM-YYYY): ")
            df = CSV.get_transactions(start_date,end_date) #having this as a df so that I can plot this on a graph
            #the next line was added after the plot function was added
            if input("Do woy want to see a plot? (y/n): ").lower() == 'y':
                plot_transactions(df)
        elif choice == 3:
            print("Exiting....")
            break
        else:
            print("Invalid choice. You can only enter 1, 2 or 3")


if __name__ == "__main__":  # if we run this file directly this line will execute and will call the main function
    main()                 #or if we import this file this will not run because name is not "main" so we protect this code so
    # that we don't run the main function unless we directly execute this file. If we were to remove this and call the main function if we were to imprort
    # something from this file then this main function would run and we don't want that  \\ good python practice
