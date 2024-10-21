**Description of the Data System**  
**User functionality of the database participants**  
In this database system, I model and track the theoretical operation of a fast-food restaurant. Through this, I attempt to demonstrate its functionality. The operations are as follows: there are employees (cashier, kitchen staff, shift leader, manager), each with different roles and responsibilities. The cashier takes the order, which may consist of products. For menus, a drink can be selected, which the customer can choose. In the background, the database has separate menus for different drinks, so if two identical menus have different drinks, they are considered two different menus. The customer may inquire about available menus and which products contain allergenic ingredients. After this, the order is assigned a start time, and we know that the order is still pending. Before payment, the customer may apply any discounts. The order is considered closed when it has been paid for, and the cashier has delivered the order, meaning the transaction has ended, and the order is finalized. The product is the smallest orderable unit, for example, a small hamburger. A product may consist of several ingredients, such as the small hamburger, which consists of a small bun, meat, tomato, and cheese. To offer a wider selection, the restaurant provides products in different sizes. There are three options: small, medium, and large. In the kitchen, the employees (kitchen staff) can see the order and prepare the products from the specified ingredients. Menus can be assembled from specific products, depending on the order placed. A product is discarded if an employee in the kitchen makes a mistake or if a complaint is received about a particular product. At the end of the day, the shift leader and manager together query the orders and discards, and from these, they calculate what needs to be ordered for the next restocking. During inventory, the quantity of the ingredients (the building blocks of the products, such as small buns) is checked by the shift leader or manager. The attendance of employees is also stored.  
We are not dealing with permissions or whether there are multiple restaurants in this context.

# **Database design with the creation of necessary entities:**

**INGREDIENT:** these are the components that make up the products.  
**INGREDIENT {ID, name, allergen, quantity, unit of measurement}**

**PRODUCT:** the specific product that can be purchased, which may also be part of a menu. This is what is prepared and sold.  
**PRODUCT {productID, name, isMenuItem, price}**

**CASH REGISTER:** a specific register that will be part of the cashier system. Its status can be active or inactive.  
**CASH REGISTER {ID, status, store}**

**CASHIER:** determines which employee is currently the cashier  
{cashRegisterID, cashOpen, cashClose, employeeID}

**ORDER:** at which cash register and when the order took place  
{receiptNumber, cashRegister, cashOpen, start, end, discountID}

**ORDER_ITEM:** the items of the order with the purchased quantities  
{receiptNumber, productID, quantity}

**DISCARD:** discarding a product due to spoilage, dropping, or other issues, categorized by ingredient  
{ID, ingredientID, date, reason, quantity}

**DISCOUNT:** the discount applied to a given order, valid for a specific time period, usable at the end of any order, and deducted from the total amount  
**DISCOUNT {ID, percentage, name, description, validityStart, validityEnd}**

**COMPLAINT:** a complaint after the order if the product is not suitable for consumption due to some issue  
**COMPLAINT {ID, receiptNumber, productID, reason, date}**

**PRODUCT_COMPOSITION:** the ingredients used to prepare a given product  
{ingredientID, productID}

**MENU_COMPOSITION:** the products that make up a given menu  
{productID, menuItem}

**EMPLOYEES:** records of employee data  
{ID, name, birthPlace, birthDate, taxNumber, positionID}

**ATTENDANCE:** when and on which day an employee clocked in and clocked out  
{employeeID, checkIn, checkOut}

**POSITION:** the position of a given employee in the restaurant  
{employeeID, title, hourlyWage}

**Data-level Description of Functions**

**Employee Management:** In this table, a new row is added whenever a new employee is hired at the restaurant.

- **EmployeeID** is assigned when a new employee is hired, data entry
- **Name** is entered
- **Birthplace** is entered
- **Birthdate** is entered
- **TaxNumber** is entered
- **Position** is selected from the POSITION table, ensuring that the selected position hasn’t already been fully assigned! The employee cannot receive a position that has already been fully assigned.
- **Position opens.**

**Assigning employees to the appropriate position (job role)**

- **PositionID** is assigned when a new position is created, data entry
- **Title** is the name of the position and is entered
- **HourlyWage** is entered based on the difficulty of the position

**Recording Attendance**

- **EmployeeID** is selected from the EMPLOYEE table; only those employees who checked in that day will be modified.
- **Check-in** is recorded when the employee logs in at the beginning of the day, data entry
- **Check-out** is updated when the employee logs out at the end of the day

**Assigning Cashier to a Cash Register** An employee who can be assigned to the cash register must be working in the cashier position and must have checked in that day.

- **CashRegisterID** is selected from the CASH_REGISTER, data entry
- **CashOpen** is recorded
- **CashClose** is recorded
- **EmployeeID** is selected from the EMPLOYEE table for the employee who has checked in today, and the **PositionID** from the POSITION table points to the cashier role

**Recording an Order Item**

- A new row is inserted into the **Order_Item** table from the **Order** table (INSERT)
- A new row is inserted into the **Order_Item** table from the **Product** table (UPDATE)
- **Order_Item.Quantity** is continuously updated by adding the number of **Order_Item.ProductID** rows within a single **Order_Item.ReceiptNumber** (UPDATE)

**Discarding**

- A new row is inserted into the **Discard** table
- The kitchen discard is manually recorded
- A new row is inserted into the **Complaint** table
- Based on **Order_Item**, it is manually recorded

**Recording/Adding a Complaint: COMPLAINT**  
New row:

- **Reason** is entered into the **Reason** column
- **ComplaintID:** automatically increments with the number of complaints (first complaint is 1)
- **Date:** system date is recorded
- **ReceiptNumber:** from the **ORDER** table's **ReceiptNumber**
- **ProductID:** from the **PRODUCT** table's **ProductID**

**Adding a Product - New Record:**

- **ProductID** is a unique identifier, key
- **Name** is entered, does not need to be unique
- **IsMenu** is entered, true if the product is part of a menu
- **Price** is entered, must be greater than zero

**Adding Menu Composition - New Record:**

- The product is entered, if it is a menu item, the **ProductID** is recorded in the **MenuComposition**. This **ProductID** matches the **ProductID** in the **Product** table.
- In **MenuComposition** , a new **MenuItemProduct** is recorded, which can be any **Product** that is not a menu item.

**Adding Product Composition - New Record:**

- The product is entered, and the **ProductID** is recorded in **ProductComposition**.
- Ingredients are entered for the given **ProductID**.

**Concepts**

- Prices are amounts, payable values, all representing net values.
- The quantity of ingredients always refers to the amount needed for a single product, according to the unit of measure for that material.
- The ingredient identifier identifies ingredients, while the product identifier identifies products.
- By "discard," we mean the ingredients that are discarded.
- Complaints can be made about ordered products within a certain period.
- A "menu item" refers to products organized into a menu.
- The start of the order indicates the beginning of the transaction, while the end marks its completion, closing the transaction.

# Queries to implement:

- daily/weekly/monthly/annual revenue report
- who worked that day, and the wages to be transferred to them
- what products were purchased and how much raw material was consumed, for restocking the warehouse
- quantity of waste
- complaints
- the cashier's daily turnover, to see how efficient they are
- how many cheeseburgers did the restaurant sell between two dates?
- which employees have worked in the cashier position as well?
- which product had the most items discarded?
- Which employee was at the cash register during the highest percentage discount?
- How many menus include the cheeseburger?
- menu products with product names
- with the ingredients of a given product named
- which products do not contain allergens
- how many products incorporate a given raw material
- how many menus include a given raw material
- which product in the offer is available in only one size
- the faulty cash register (where the employee is not eligible)
- the faulty cash register (where the employee is not present)
- the faulty attendances (where the employee's presence is not conflict-free)
- the faulty order (where the cash register was not active at the time)
- the faulty order (where any potential discount was not valid at the time)
- calculation of the total amount for a given order
- the faulty complaints (where the product could not have been on the receipt)
- the faulty complaints (where the date differs from the receipt date or the time is not later)
- the faulty complaints (where the employee was not working at that time)
- the faulty discards (which were not recorded by a present employee)
- the faulty cash register closures (where there was an ongoing order at the time)
- the quantity of products sold on a given day + the quantity of products provided through complaint handling
- the quantity of materials consumed today + the quantity of waste
- which menu sold the most between two dates
- which product is more successful on its own than in a menu
- list of actual products in a given open order
- what and how much of each ingredient is required for a given menu item

# Restrictions

- uniqueness (see keys)
- references (see foreign keys)
- prices, discount percentages: non-negative numbers (see field restrictions)
- quantities: positive numbers (see field restrictions)
- we do not modify the structure of the product, instead, we add a new product
- the cash register can only be in active status if it has been opened in the CASHIER system and an employee has logged in
- menus can be compiled only with the allowed product types
- the cashier’s employee must be eligible and present
- the employee's attendance must be conflict-free
- at the time of the order, the current cash register must be active, and any applicable discount must be valid
- order items can be added as long as the order is not completed
- the total of the order: calculated field (function)
- receipt details cannot be modified or deleted after submission
- only items of an open receipt can be modified or deleted
- the complained product can only be from the actual non-menu items of the order from the same day, up to the ordered quantity, and with a present eligible employee (note: the customer receives a fresh product or a refund)
- defect recording can only be done on the same day by a present eligible employee
- discount details can only be modified before their validity
- cash register closure can only be recorded on a register with no ongoing orders
- attendance end can only be recorded for an employee who is not currently a cashier
- function: daily inventory (total quantities of sold products, complained products, consumed materials, and discarded materials)
- function: who is working in which role at a given date and time
- function: list of actual products in a given open order
- function: what and how much of each ingredient is required for a given menu item



