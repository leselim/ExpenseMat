import json
from datetime import datetime
from collections import defaultdict

# Define constants for default email sender and support emails
DEFAULT_SENDER_EMAIL = "no-reply@expensemat.com"
SUPPORT_EMAIL = "support@expensemat.com"

class ExpenseMatApp:
    """Main class for the ExpenseMat Application."""

    def __init__(self):
        self.users = {}  # Store user details
        self.transactions = defaultdict(list)  # Store transactions by user ID
        self.budgets = {}  # Store budget limits by user ID
        self.savings_goals = {}  # Store savings goals by user ID

    def register_user(self, user_id, email, name):
        """Register a new user with email and name."""
        if user_id in self.users:
            raise ValueError("User already exists.")
        self.users[user_id] = {
            "email": email,
            "name": name,
            "over_budget_notifications": [],
            "reward_points": 0,
        }
        print(f"User {name} registered successfully.")

    def add_transaction(self, user_id, amount, category):
        """Add a transaction for a user."""
        if user_id not in self.users:
            raise ValueError("User not found.")
        transaction = {
            "amount": amount,
            "category": category,
            "timestamp": datetime.now().isoformat(),
        }
        self.transactions[user_id].append(transaction)
        print(f"Transaction added for user {self.users[user_id]['name']}: {transaction}")

    def set_budget(self, user_id, budget_limit):
        """Set a budget limit for a user."""
        if user_id not in self.users:
            raise ValueError("User not found.")
        self.budgets[user_id] = budget_limit
        print(f"Budget set for user {self.users[user_id]['name']}: {budget_limit}")

    def check_budget(self, user_id):
        """Check if the user is over budget and send notification."""
        if user_id not in self.users:
            raise ValueError("User not found.")
        total_spent = sum(t["amount"] for t in self.transactions[user_id])
        budget_limit = self.budgets.get(user_id, 0)
        if total_spent > budget_limit:
            self.send_notification(
                user_id,
                f"You have exceeded your budget of {budget_limit}. Total spent: {total_spent}",
            )

    def set_savings_goal(self, user_id, goal_amount):
        """Set a savings goal for a user."""
        if user_id not in self.users:
            raise ValueError("User not found.")
        self.savings_goals[user_id] = goal_amount
        print(f"Savings goal set for user {self.users[user_id]['name']}: {goal_amount}")

    def check_savings_goal(self, user_id):
        """Check if the user has reached their savings goal."""
        if user_id not in self.users:
            raise ValueError("User not found.")
        total_spent = sum(t["amount"] for t in self.transactions[user_id])
        savings_goal = self.savings_goals.get(user_id, 0)
        if total_spent <= savings_goal:
            self.users[user_id]["reward_points"] += 100  # Add reward points
            self.send_notification(
                user_id,
                "Congratulations! You've reached your savings goal.",
            )

    def send_notification(self, user_id, message):
        """Send an email notification to the user."""
        if user_id not in self.users:
            raise ValueError("User not found.")
        email = self.users[user_id]["email"]
        print(f"Sending email to {email}...")
        print(f"From: {DEFAULT_SENDER_EMAIL}")
        print(f"Message: {message}")

    def get_budget_friendly_shops(self):
        """Return a list of budget-friendly shops."""
        return [
            "Shop Smart",
            "Budget Bazaar",
            "Thrifty Mart",
            "SaveMore Superstore",
        ]

    def list_transactions(self, user_id):
        """List all transactions for a user."""
        if user_id not in self.transactions:
            raise ValueError("User not found or no transactions available.")
        return self.transactions[user_id]

    def generate_report(self, user_id):
        """Generate a financial report for the user."""
        if user_id not in self.users:
            raise ValueError("User not found.")
        transactions = self.transactions[user_id]
        total_spent = sum(t["amount"] for t in transactions)
        budget = self.budgets.get(user_id, "Not set")
        savings_goal = self.savings_goals.get(user_id, "Not set")
        report = {
            "total_spent": total_spent,
            "budget": budget,
            "savings_goal": savings_goal,
            "transactions": transactions,
        }
        return report

if __name__ == "__main__":
    app = ExpenseMatApp()

    # Example usage
    app.register_user("user123", "user123@example.com", "Alice")
    app.set_budget("user123", 500)
    app.set_savings_goal("user123", 1000)
    app.add_transaction("user123", 50, "Groceries")
    app.add_transaction("user123", 60, "Transport")
    app.check_budget("user123")
    app.check_savings_goal("user123")
    print("Budget-friendly shops:", app.get_budget_friendly_shops())
    print("Transaction list:", app.list_transactions("user123"))
    print("Financial report:", json.dumps(app.generate_report("user123"), indent=2))
