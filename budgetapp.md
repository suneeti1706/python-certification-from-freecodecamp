class Category:
    def __init__(self, name):
        self.name=name 
        self.ledger = []

    def deposit(self, amt, desc=""):
        self.ledger.append({'amount': amt, "description" : desc})

    def withdraw(self, amount, description=""):
        if self.check_funds(amount):
            self.ledger.append({"amount": -amount, "description": description})
            return True
        return False


    def get_balance(self):
        total = 0
        for item in self.ledger:
            total += item["amount"]
        return total

    def transfer(self, amount, category):
        if self.check_funds(amount):
            self.ledger.append({
            "amount": -amount,
            "description": f"Transfer to {category.name}"
        })
            category.ledger.append({
            "amount": amount,
            "description": f"Transfer from {self.name}"
        })
            return True
        return False

    def check_funds(self, amt):
        return amt <= self.get_balance()

    def __str__(self):
        title = self.name.center(30, "*") + "\n"
        items = ""
        total = 0

        for item in self.ledger:
            description = item["description"][:23]
            amount = f'{item["amount"]:.2f}'[:7]
            items += f"{description:<23}{amount:>7}\n"
            total += item["amount"]

        return title + items + f"Total: {total:.2f}"


def create_spend_chart(categories):
    withdrawals = []

    for category in categories:
        total = 0
        for item in category.ledger:
            if item["amount"] < 0:
                total += -item["amount"]
        withdrawals.append(total)

    total_withdrawals = sum(withdrawals)
    percentages = []

    for amount in withdrawals:
        percent = int((amount / total_withdrawals) * 10) * 10
        percentages.append(percent)

    chart = "Percentage spent by category\n"

    for i in range(100, -1, -10):
        chart += str(i).rjust(3) + "| "
        for percent in percentages:
            if percent >= i:
                chart += "o  "
            else:
                chart += "   "
        chart += "\n"

    chart += "    -" + "---" * len(categories) + "\n"

    max_len = max(len(category.name) for category in categories)
    for i in range(max_len):
        chart += "     "
        for category in categories:
            if i < len(category.name):
                chart += category.name[i] + "  "
            else:
                chart += "   "
        if i != max_len - 1:
            chart += "\n"

    return chart
