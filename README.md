import pandas as pd

def clean_and_organize_csv(input_file, output_file):
    # Load the dataset
    df = pd.read_csv(input_file)

    # Display the first few rows of the dataframe to understand its structure
    print("Initial data preview:")
    print(df.head())

    # 1. Handle missing values: Fill numeric columns with the mean and categorical with the mode
    for column in df.select_dtypes(include=['float64', 'int64']).columns:
        df[column].fillna(df[column].mean(), inplace=True)
    
    for column in df.select_dtypes(include=['object']).columns:
        df[column].fillna(df[column].mode()[0], inplace=True)

    # 2. Remove duplicate rows
    df.drop_duplicates(inplace=True)

    # 3. Standardize column names (convert to lowercase and remove spaces)
    df.columns = [col.lower().replace(' ', '_') for col in df.columns]

    # 4. Handle inconsistent data types: Ensure all columns have appropriate data types
    for column in df.columns:
        if df[column].dtype == 'object':
            try:
                # Attempt to convert object columns to numeric if possible
                df[column] = pd.to_numeric(df[column], errors='ignore')
            except ValueError:
                continue

    # 5. Drop irrelevant columns (you can specify which ones or leave it as it is)
    # Example: df.drop(columns=['irrelevant_column1', 'irrelevant_column2'], inplace=True)

    # 6. Save the cleaned dataset to a new CSV file
    df.to_csv(output_file, index=False)
    print(f"Data cleaned and saved to {output_file}")

# Example usage
input_file = 'path/to/your/input_file.csv'
output_file = 'path/to/your/output_file.csv'
clean_and_organize_csv(input_file, output_file)
