# Constants for the application
TAX_RATE = 0.13 # 13% HST
STUDENT_DISCOUNT_RATE = 0.10 # 10% student discount
DELIVERY_CHARGE = 5.00 # Fixed delivery charge
MIN_DELIVERY_FREE = 30.00 # Free delivery if subtotal is above this amount

# Menu with 6 items
menu = {
1: {"name": "Spaghetti Bolognese", "price": 10.85},
2: {"name": "Chicken Parmesan", "price": 12.50},
3: {"name": "Vegetable Stir Fry", "price": 9.99},
4: {"name": "Grilled Salmon", "price": 15.50},
5: {"name": "Cheeseburger", "price": 8.75},
6: {"name": "Chicken Curry", "price": 15.85},
}

# Function to collect customer information with input validation
def get_customer_info():
"""Collects and validates customer information."""
while True:
first_name = input("Enter your first name: ").strip()
if not first_name:
print("First name cannot be empty.")
continue
last_name = input("Enter your last name: ").strip()
if not last_name:
print("Last name cannot be empty.")
continue
address = input("Enter your full delivery address (Street, Unit #, etc.): ").strip()
if not address:
print("Address cannot be empty.")
continue
city = input("Enter your city: ").strip()
if not city:
print("City cannot be empty.")
continue
province = input("Enter your province: ").strip()
if not province:
print("Province cannot be empty.")
continue
postal_code = input("Enter your postal code (7 characters): ").strip()
if len(postal_code) != 7:
print("Postal code must be 7 characters long.")
continue
phone_number = input("Enter your phone number: ").strip()
if not phone_number:
print("Phone number cannot be empty.")
continue
return {
"first_name": first_name,
"last_name": last_name,
"address": address,
"city": city,
"province": province,
"postal_code": postal_code,
"phone_number": phone_number,
}

# Function to display the menu
def display_menu():
"""Displays the menu with 6 items."""
print("\nMenu:")
for key, item in menu.items():
print(f"{key}) {item['name']} - ${item['price']:.2f}")

# Function to get meal choice with validation
def get_meal_choice():
"""Prompts the user to choose a meal and validates the input."""
while True:
try:
choice = int(input("\nEnter the number of the meal you wish to order (1-6): "))
if 1 <= choice <= 6:
return choice
else:
print("Invalid choice. Please select a number between 1 and 6.")
except ValueError:
print("Invalid input. Please enter a number between 1 and 6.")

# Function to get quantity of the chosen meal
def get_quantity():
"""Asks the user for the quantity of the selected meal."""
while True:
try:
quantity = int(input("How many servings would you like? "))
if quantity > 0:
return quantity
else:
print("Quantity must be a positive number.")
except ValueError:
print("Please enter a valid number.")

# Function to get the delivery choice with validation
def get_delivery_option():
"""Prompts the user for delivery or pickup."""
while True:
delivery_choice = input("Do you need delivery? (y/n): ").lower()
if delivery_choice in ["y", "n"]:
return delivery_choice
else:
print("Invalid input. Please enter 'y' for yes or 'n' for no.")

# Function to get tip choice with validation
def get_tip():
"""Asks the user for the tip percentage (only for delivery)."""
while True:
tip_choice = input("Choose a tip (1 = 10%, 2 = 15%, 3 = 20%): ")
if tip_choice in ["1", "2", "3"]:
return int(tip_choice)
else:
print("Invalid choice. Please select 1, 2, or 3.")

# Function to calculate all costs (item total, discount, tax, tip, etc.)
def calculate_totals(choice, quantity, is_student, delivery_choice, tip_choice):
"""Calculates the item totals, discount, tax, delivery charge, and tip."""
item_price = menu[choice]["price"]
item_total = item_price * quantity

# Apply student discount if applicable
discount = item_total * STUDENT_DISCOUNT_RATE if is_student else 0
subtotal = item_total - discount

# Delivery charge
if delivery_choice == "y":
delivery_charge = DELIVERY_CHARGE if subtotal < MIN_DELIVERY_FREE else 0
else:
delivery_charge = 0

# Tip is calculated on subtotal before delivery and HST
tips = 0
if delivery_choice == "y":
tip_rate = {1: 0.10, 2: 0.15, 3: 0.20}[tip_choice]
tips = subtotal * tip_rate

# HST calculation
tax = subtotal * TAX_RATE

# Total cost
total = subtotal + delivery_charge + tips + tax
return subtotal, discount, tax, delivery_charge, tips, total

# Function to print the receipt in the required format
def print_receipt(customer_info, choice, quantity, subtotal, discount, tax, delivery_charge, tips, total):
"""Prints the formatted receipt."""
print("\n----------------------------Receipt-----------------------------")
print(f"{customer_info['first_name']} {customer_info['last_name']}")
if delivery_charge > 0:
print(f"{customer_info['address']}")
print(f"{customer_info['city']}, {customer_info['province']}, {customer_info['postal_code']}")
print("This is my special instruction") # Placeholder for special instructions
print(f"\n{'Order Item':<20} {'Amt Item Price':<15} {'Total':<10}")
print("-" * 30)
print(f"{menu[choice]['name']:<20} {quantity:<5} ${menu[choice]['price']:<10.2f} ${subtotal:<10.2f}")
if discount > 0:
print(f"10% student savings: -${discount:.2f}")
print(f"Sub Total: ${subtotal:.2f}")
if delivery_charge > 0:
print(f"Delivery: ${delivery_charge:.2f}")
else:
print(f"Delivery (Waived) $0.00")
print(f"Tips ({int(tips / subtotal * 100)}%): ${tips:.2f}")
print(f"Tax (13%): ${tax:.2f}")
print("-" * 30)
print(f"TOTAL: ${total:.2f}")

# Main function to execute the program
def main():
"""Main function to execute the program."""
customer_info = get_customer_info()
display_menu()

order = []
while True:
meal_choice = get_meal_choice()
quantity = get_quantity()
is_student = input("Are you a student? (y/n): ").lower() == "y"
delivery_choice = get_delivery_option()
if delivery_choice == "y":
tip_choice = get_tip()
else:
tip_choice = 0

# Calculate totals and display the receipt
subtotal, discount, tax, delivery_charge, tips, total = calculate_totals(
meal_choice, quantity, is_student, delivery_choice, tip_choice
)
print_receipt(
customer_info, meal_choice, quantity, subtotal, discount, tax, delivery_charge, tips, total
)

add_more = input("Do you want to add more? (y/n): ").lower()
if add_more == 'n':
break

# Save the receipt to a file
with open(f"receipt_{customer_info['first_name']}_{customer_info['last_name']}.txt", "w") as f:
f.write("----------------------------Receipt-----------------------------\n")
f.write(f"{customer_info['first_name']} {customer_info['last_name']}\n")
f.write(f"{customer_info['address']}\n")
f.write(f"{customer_info['city']}, {customer_info['province']}, {customer_info['postal_code']}\n")
f.write("This is my special instruction\n")
f.write(f"\n{'Order Item':<20} {'Amt Item Price':<15} {'Total':<10}\n")
f.write("-" * 30 + "\n")
f.write(f"{menu[meal_choice]['name']:<20} {quantity:<5} ${menu[meal_choice]['price']:<10.2f} ${subtotal:<10.2f}\n")
if discount > 0:
f.write(f"10% student savings: -${discount:.2f}\n")
f.write(f"Sub Total: ${subtotal:.2f}\n")
if delivery_charge > 0:
f.write(f"Delivery: ${delivery_charge:.2f}\n")
else:
f.write(f"Delivery (Waived) $0.00\n")
f.write(f"Tips ({int(tips / subtotal * 100)}%): ${tips:.2f}\n")
f.write(f"Tax (13%): ${tax:.2f}\n")
f.write("-" * 30 + "\n")
f.write(f"TOTAL: ${total:.2f}\n")

# Execute the main function if __name__ == "__main__":
if __name__ == "__main__":
main()
