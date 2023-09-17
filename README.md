# Budget-App
class Category:
    def __init__(self, category):
        self.category = category
        self.ledger = []

    def deposit(self, amount, description=""):
        self.ledger.append({"amount": amount, "description": description})

    def withdraw(self, amount, description=""):
        if self.check_funds(amount):
            self.ledger.append({"amount": -amount, "description": description})
            return True
        return False

    def get_balance(self):
        return sum(item["amount"] for item in self.ledger)

    def transfer(self, amount, budget_category):
        if self.check_funds(amount):
            self.withdraw(amount, f"Transfer to {budget_category.category}")
            budget_category.deposit(amount, f"Transfer from {self.category}")
            return True
        return False

    def check_funds(self, amount):
        return amount <= self.get_balance()

    def __str__(self):
        title = f"{self.category:*^30}\n"
        items = ""
        total = 0
        for item in self.ledger:
            description = item["description"][:23]
            amount = "{:.2f}".format(item["amount"])
            items += f"{description} {amount.rjust(30 - len(description))}\n"
            total += item["amount"]
        total = "{:.2f}".format(total)
        return title + items + f"Total: {total}"

def create_spend_chart(categories):
    chart = "Percentage spent by category\n"
    spendings = [sum(item["amount"] for item in category.ledger if item["amount"] < 0) for category in categories]
    total_spent = sum(spendings)
    
    for i in range(100, -1, -10):
        chart += str(i).rjust(3) + "| "
        for spending in spendings:
            chart += "o " if spending >= i / 100 * total_spent else "  "
        chart += "\n"
    
    chart += "    ----------\n     "
    max_length = max(len(category.category) for category in categories)
    
    for i in range(max_length):
        for category in categories:
            if i < len(category.category):
                chart += category.category[i] + "  "
            else:
                chart += "   "
        if i < max_length - 1:
            chart += "\n     "
    
    return chart




food = Category("Food")
clothing = Category("Clothing")
auto = Category("Auto")

food.deposit(1000, "initial deposit")
food.withdraw(10.15, "groceries")
food.transfer(50, auto)
auto.withdraw(25.55, "fuel")
clothing.withdraw(75.50, "shoes")
clothing.transfer(25, food)

print(food)
print(clothing)
print(auto)

categories = [food, clothing, auto]
print(create_spend_chart(categories))
