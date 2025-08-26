# E-Commerce CLI in Python
## Description
This is a simple *Command-Line Interface (CLI) E-Commerce system* built in Python for beginners.  
It allows the user to:
- Add new products.
- View all products.
- Search and filter products.
- Add products to a cart.
- View and remove items from cart.
- Checkout and save orders.

The project demonstrates basic Python concepts:
- *List* → store multiple products, cart items, and orders.
- *Dictionary* → represent each product with keys like id, name, price, stock.
- *Tuple* → store fixed values like product priorities (Low, Medium, High).

---

## Project Files
- ecommerce_full.py → main application.
- products.json → stores all products (created automatically if not present).
- orders.json → stores orders (created automatically if not present).

---

## How to Run

### Requirements
- Python 3 installed.

### Steps (Linux / Mac / Windows PowerShell)
bash
git clone https://github.com/AbdelHakimGafer1/e-commerce-_with_python

python ecommerce_full.py
`

### Steps (on Termux - Android)

1. Install Termux from F-Droid or Play Store.
2. Update and install Python:

   bash
   pkg update && pkg upgrade -y
   pkg install python git -y
   
3. Clone the repository:

   bash
   git clone https://github.com/abdelhakimgafer1/e-commerce-_with_python
   
4. Run:

   bash
   python ecommerce_full.py
   

---

## Example Interaction


--- E-Commerce CLI ---
1. Show Products
2. Add Product
3. Search Products
4. Filter Products
5. Add to Cart
6. View Cart
7. Remove from Cart
8. Checkout
9. Exit
Enter your choice: 2
Enter product name: Laptop
Enter product price: 5000
Enter stock quantity: 10
Enter category (e.g. Electronics, Clothing): Electronics
Choose priority: 1.Low  2.Medium  3.High
Enter number: 2
Product 'Laptop' added successfully!


---

## Full Code: ecommerce_full.py

python
import json

# =========================
# تحميل / حفظ البيانات
# =========================
def load_products():
    try:
        with open("products.json", "r") as f:
            return json.load(f)
    except FileNotFoundError:
        return [
            {"id": 1, "name": "Laptop", "price": 1500, "stock": 5, "category": "Electronics", "priority": "High"},
            {"id": 2, "name": "Phone", "price": 800, "stock": 10, "category": "Electronics", "priority": "Medium"},
            {"id": 3, "name": "Headphones", "price": 100, "stock": 15, "category": "Accessories", "priority": "Low"},
        ]

def save_products(products):
    with open("products.json", "w") as f:
        json.dump(products, f, indent=4)

def load_orders():
    try:
        with open("orders.json", "r") as f:
            return json.load(f)
    except FileNotFoundError:
        return []

def save_orders(orders):
    with open("orders.json", "w") as f:
        json.dump(orders, f, indent=4)

# =========================
# البيانات الأساسية
# =========================
products = load_products()
cart = []
PRIORITIES = ("Low", "Medium", "High")

# =========================
# عرض المنتجات
# =========================
def show_products(products_list=None):
    if products_list is None:
        products_list = products
    print("\nAvailable Products:")
    for p in products_list:
        print(f"{p['id']}. {p['name']} - ${p['price']} | Stock: {p['stock']} | Category: {p['category']} | Priority: {p['priority']}")

# =========================
# إضافة منتج جديد
# =========================
def add_product():
    try:
        name = input("Enter product name: ")
        price = float(input("Enter product price: "))
        stock = int(input("Enter stock quantity: "))
        category = input("Enter category (e.g. Electronics, Clothing): ")
        print("Choose priority: 1.Low  2.Medium  3.High")
        p_choice = int(input("Enter number: "))
        priority = PRIORITIES[p_choice - 1] if 1 <= p_choice <= 3 else "Low"
        product_id = max([p['id'] for p in products], default=0) + 1
        product = {"id": product_id, "name": name, "price": price, "stock": stock, "category": category, "priority": priority}
        products.append(product)
        save_products(products)
        print(f"Product '{name}' added successfully!")
    except ValueError:
        print("Invalid input. Please enter correct data types.")

# =========================
# البحث عن المنتجات
# =========================
def search_products():
    keyword = input("Enter product name or category to search: ").lower()
    results = [p for p in products if keyword in p['name'].lower() or keyword in p['category'].lower()]
    if results:
        show_products(results)
    else:
        print("No products found.")

# =========================
# فلترة المنتجات
# =========================
def filter_products():
    print("Filter by: 1.Price  2.Priority")
    choice = input("Enter choice: ")
    if choice == "1":
        max_price = float(input("Enter maximum price: "))
        results = [p for p in products if p['price'] <= max_price]
        show_products(results)
    elif choice == "2":
        print("Priorities: Low, Medium, High")
        prio = input("Enter priority: ").capitalize()
        if prio in PRIORITIES:
            results = [p for p in products if p['priority'] == prio]
            show_products(results)
        else:
            print("Invalid priority.")
    else:
        print("Invalid choice.")

# =========================
# إضافة للسلة
# =========================
def add_to_cart():
    show_products()
    try:
        product_id = int(input("Enter product ID to add to cart: "))
        product = next((p for p in products if p['id'] == product_id), None)
        if not product:
            print("Invalid product ID.")
            return
        qty = int(input(f"Enter quantity (Available: {product['stock']}): "))
        if qty < 1 or qty > product['stock']:
            print("Invalid quantity.")
            return
        existing = next((item for item in cart if item['product_id'] == product_id), None)
        if existing:
            existing['quantity'] += qty
        else:
            cart.append({"product_id": product['id'], "name": product['name'], "price": product['price'], "quantity": qty})
        print(f"{qty} x {product['name']} added to cart!")
    except ValueError:
        print("Please enter a valid number.")

# =========================
# عرض السلة
# =========================
def view_cart():
    if not cart:
        print("\nYour cart is empty.")
        return
    print("\nYour Cart:")
    total = 0
    for i, item in enumerate(cart, start=1):
        subtotal = item['price'] * item['quantity']
        total += subtotal
        print(f"{i}. {item['name']} x {item['quantity']} = ${subtotal}")
    print(f"Total: ${total}")

# =========================
# حذف منتج من السلة
# =========================
def remove_from_cart():
    if not cart:
        print("\nYour cart is empty.")
        return
    view_cart()
    try:
        item_num = int(input("Enter item number to remove: "))
        if 1 <= item_num <= len(cart):
            removed = cart.pop(item_num - 1)
            print(f"{removed['name']} removed from cart!")
        else:
            print("Invalid item number.")
    except ValueError:
        print("Please enter a number.")

# =========================
# إنهاء الطلب
# =========================
def checkout():
    if not cart:
        print("\nYour cart is empty.")
        return
    view_cart()
    confirm = input("Do you want to checkout? (y/n): ").lower()
    if confirm == 'y':
        for item in cart:
            product = next(p for p in products if p['id'] == item['product_id'])
            product['stock'] -= item['quantity']
        save_products(products)
        orders = load_orders()
        orders.append(cart.copy())
        save_orders(orders)
        print("Order placed successfully!")
        cart.clear()
    else:
        print("Checkout cancelled.")

# =========================
# القايمة الرئيسية
# =========================
def main_menu():
    while True:
        print("\n--- E-Commerce CLI ---")
        print("1. Show Products")
        print("2. Add Product")
        print("3. Search Products")
        print("4. Filter Products")
        print("5. Add to Cart")
        print("6. View Cart")
        print("7. Remove from Cart")
        print("8. Checkout")
        print("9. Exit")
        choice = input("Enter your choice: ")

        if choice == "1":
            show_products()
        elif choice == "2":
            add_product()
        elif choice == "3":
            search_products()
        elif choice == "4":
            filter_products()
        elif choice == "5":
            add_to_cart()
        elif choice == "6":
            view_cart()
        elif choice == "7":
            remove_from_cart()
        elif choice == "8":
            checkout()
        elif choice == "9":
            print("Bye!")
            break
        else:
            print("Invalid choice, try again.")

if __name__ == "__main__":
    main_menu()


---
