import csv

class Transaction:
  def __init__(self, date, amount, category):
    self.date = date
    self.amount = amount
    self.category = category

def load_transactions(filename):
  transactions = []
  try:
    with open(filename, 'r', newline='') as file:
      reader = csv.reader(file)
      for row in reader:
        date, amount, category = row
        transactions.append(Transaction(date, float(amount), category))
  except FileNotFoundError:
    print(f"File '{filename}' not found. Creating a new one.")
  return transactions

def save_transactions(transactions, filename):
  with open(filename, 'w', newline='') as file:
    writer = csv.writer(file)
    for transaction in transactions:
      writer.writerow([transaction.date, transaction.amount, transaction.category])

def main_menu():
  print("\nBudget Tracker")
  print("1. Add Expense")
  print("2. Add Income")
  print("3. View Transactions")
  print("4. View Budget")
  print("5. Analyze Expenses")
  print("6. Exit")
  choice = input("Enter your choice: ")
  return choice

def add_transaction(transactions, type):
  while True:
    try:
      amount = float(input(f"Enter {type} amount: "))
      break
    except ValueError:
      print("Invalid amount. Please enter a number.")
  category = input(f"Enter {type} category: ")
  date = input("Enter date (optional, leave blank for today): ") or None
  transactions.append(Transaction(date, amount if type == "Income" else -amount, category))
  save_transactions(transactions, "transactions.csv")
  print(f"{type} added successfully!")

def view_transactions(transactions):
  print("\nTransactions:")
  if not transactions:
    print("No transactions found.")
    return
  for transaction in transactions:
    date_str = transaction.date if transaction.date else "N/A"
    print(f"{date_str} - {transaction.amount:.2f} ({transaction.category})")

def calculate_budget(transactions):
  income = sum(t.amount for t in transactions if t.amount > 0)
  expense = sum(t.amount for t in transactions if t.amount < 0)
  remaining = income + expense
  print("\nBudget Summary:")
  print(f"Total Income: {income:.2f}")
  print(f"Total Expense: {expense:.2f}")
  print(f"Remaining Budget: {remaining:.2f}")

def analyze_expenses(transactions):
  categories = {}
  for transaction in transactions:
    amount = transaction.amount
    category = transaction.category
    categories[category] = categories.get(category, 0) + abs(amount)
  print("\nExpense Breakdown by Category:")
  if not categories:
    print("No expenses found.")
    return
  for category, total in categories.items():
    print(f"{category}: {total:.2f}")

if __name__ == "__main__":
  transactions = load_transactions("transactions.csv")
  while True:
    choice = main_menu()
    if choice == '1':
      add_transaction(transactions, "Expense")
    elif choice == '2':
      add_transaction(transactions, "Income")
    elif choice == '3':
      view_transactions(transactions)
    elif choice == '4':
      calculate_budget(transactions)
    elif choice == '5':
      analyze_expenses(transactions)
    elif choice == '6':
      print("Exiting...")
      break
    else:
      print("Invalid choice!")

