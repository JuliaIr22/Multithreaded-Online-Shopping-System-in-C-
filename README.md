# Online Shopping System (C++)

#### Video Demo: <URL here>

#### Description:

This project is a simple console-based online shopping system implemented in C++. It simulates a small e-commerce platform where users can browse products, add items to their shopping cart, and complete purchases through a basic checkout flow. The system also demonstrates multithreading to model multiple users interacting with the store at the same time, while maintaining data integrity using mutexes.

## What the project does

The application starts by creating a product catalog with a few sample items (such as a laptop, headphones, and a mouse). Each product has a name, description, price, and stock quantity. Users can view the catalog and see the current stock levels.

The core of the system is the interaction between users and the store:

- Users have their own shopping carts.
- Each user can add products to their cart with specific quantities.
- When a user checks out, the system calculates the total cost, applies a discount based on the user type, and asks for confirmation.
- If the user confirms the purchase, the product stock is updated and an order summary is printed.

To simulate real-world concurrency, the program creates multiple user objects and runs their shopping sessions in separate threads. These threads attempt to add products and perform checkout operations concurrently. A mutex protects shared product data so that stock quantities remain consistent even when multiple threads are running.

## Files in the project

- **main.cpp**  
  Contains the entire implementation of the system, including class definitions and the `main()` function. In a more advanced version, this could be split into multiple header and source files:
  - `Product.h` / `Product.cpp`
  - `ShoppingCart.h` / `ShoppingCart.cpp`
  - `User.h` / `User.cpp`
  - `Store.h` / `Store.cpp`

For this assignment, keeping everything in one file makes it easier to compile and review.

## Key classes and design decisions

- **Product**  
  The `Product` class encapsulates product data: ID, name, description, price, and stock quantity. Methods like `reduceStock()` and `increaseStock()` manage inventory changes. Using an integer ID makes it easy to reference products from other classes.

- **ShoppingCart**  
  The `ShoppingCart` class stores items as a `std::map<int, int>`, mapping product IDs to quantities. This design keeps the cart independent from the actual product objects, which remain in the store’s catalog. The cart provides methods to add, remove, and update quantities, as well as to clear the cart after checkout.

- **User hierarchy**  
  The base `User` class holds common information (name, email, address) and a `ShoppingCart`. It defines a virtual method `getDiscountRate()` and a pure virtual method `getType()`.  
  Derived classes:
  - `RegularUser` (no discount)
  - `PremiumUser` (10% discount)
  - `VipUser` (20% discount)

  This inheritance structure cleanly models different user privileges without duplicating code. If new user types are needed in the future, they can be added by extending `User`.

- **Store**  
  The `Store` class manages the product catalog and checkout logic. It uses a `std::vector<Product>` to store products and a `std::mutex` to protect shared access. The `checkout()` method validates stock, calculates totals and discounts, prompts the user to confirm, and then updates stock quantities. It also prints a detailed order summary.

  The `simulateUserSession()` method is used by threads to represent a user’s interaction with the store. It adds items to the user’s cart and calls `checkout()`.

## Multithreading and concurrency

The program uses `std::thread` to run multiple user sessions at the same time. Each thread calls `Store::simulateUserSession()` with a different user and purchase scenario. Because all threads share the same `Store` instance and product catalog, a `std::mutex` is used to guard operations that read or modify product stock.

This design demonstrates how to handle concurrent access to shared resources in C++ while keeping data consistent. Without the mutex, two users could oversell a product by reading outdated stock values.

## Error handling and validation

The code includes basic validation, such as:

- Checking if a product exists before adding it to a cart.
- Ensuring requested quantities do not exceed available stock.
- Allowing the user to cancel a purchase at checkout.

You can extend this with more robust input validation (e.g., handling invalid IDs or non-numeric input) depending on assignment requirements.


