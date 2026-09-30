# SE Lab 3: Component Modelling & Architectural Pattern Selection


| | |
|---|---|
| **Name** | Raksha |
| **SRN** | PES1UG24CS363 |
| **Class** | F |
| **University** | PES University |

---

## 📌 Objective

Evaluate different architectural styles, select the most appropriate one for the assigned scenario, and create a UML Component Diagram showing modules, interfaces and dependencies.

---

## ☕ Scenario: Self-Service Coffee Kiosk System

A self-service kiosk in a busy café that lets customers order coffee without staff assistance.

**Functional requirements**
- Select from 3 coffee types: Espresso, Americano, Latte
- Choose from 2 sizes: Small, Large
- Pay using a credit card only
- Print a receipt with order details

**Technical constraints**
- Handle touch screen interactions
- Connect to receipt printer hardware
- Store menu data and pricing information

---

## 🏗️ Architectural Style Analysis

| Style | Pros | Cons | Fit for the Kiosk |
|---|---|---|---|
| **Layered** | Clear separation of concerns, simple to build and test, runs on one device | Some overhead between layers, cannot scale layers separately | ✅ **Best fit**: small, fixed scope on a single machine |
| **Microservices** | Independent scaling and deployment, fault isolation | High operational complexity, network latency, data consistency issues | ❌ Overkill for one kiosk serving one customer at a time |
| **Client-Server** | Centralised control, easy data consistency | Single point of failure, depends on the network | ❌ Kiosk would stop working if the server or network goes down |

### ✔️ Selected: Layered Architecture

> *"We chose Layered Architecture for the Self-Service Coffee Kiosk System."*

---

## 🧩 Components

| # | Component | Layer | Responsibility |
|---|---|---|---|
| 1 | **User Interface Component** | Presentation | Touch screens for menu, size selection and payment |
| 2 | **Order Manager Component** | Business | Core order logic, pricing and workflow control |
| 3 | **Payment Service Component** | Business | Processes credit card payments through the card reader |
| 4 | **Receipt Printer Component** | Business | Formats and prints receipts on the thermal printer |
| 5 | **Database Component** | Data | Stores menu items, prices and order records |

---

## 🔌 Interfaces

| Interface | Provided By | Required By | Technology | Operations |
|---|---|---|---|---|
| **IOrder** | Order Manager | User Interface | In-process API calls | `getMenu()`, `placeOrder(type, size)` |
| **IPayment** | Payment Service | Order Manager | Payment API call | `charge(amount)` |
| **IReceipt** | Receipt Printer | Order Manager | Hardware interface (ESC/POS over USB) | `printReceipt(order)` |
| **IMenuData** | Database | Order Manager | Database queries (SQLite) | `getPrice()`, `saveOrder()` |

External hardware (Touch Screen, Card Reader, Thermal Printer) is connected through `«use»` dependencies.

---

## 📊 Component Diagram

![Component Diagram](Lab3_Component_Diagram.png)

---

## 🔄 Order Data Flow

1. Customer taps a coffee type and size on the touch screen.
2. The UI calls `IOrder.placeOrder(type, size)` on the Order Manager.
3. The Order Manager gets the price through `IMenuData.getPrice()`.
4. The Order Manager calls `IPayment.charge(amount)`, and the Payment Service contacts the card reader.
5. If payment is approved, the order is saved through `IMenuData.saveOrder()`.
6. `IReceipt.printReceipt(order)` sends the order details to the thermal printer.
7. A confirmation is sent back up to the UI.

---

## 📝 Justification Summary

**Reasons for choosing Layered Architecture**
1. **Small, fixed scope on a single device:** the kiosk has only 6 menu combinations and one payment method, so the extra complexity of microservices is not needed.
2. **Hardware and menu changes stay isolated:** a new printer or card reader only affects one component, and price changes only affect the Data Layer.

**🔒 Security advantage:** The UI can only access the system through `IOrder`. It never reads the database or handles raw card data. Card details stay inside the Payment Service, and prices are read from the database instead of being trusted from the UI.

**⚡ Performance benefit:** All layers run in one process on the kiosk, so calls between them are fast local method calls. The menu is cached in memory, so screens respond instantly to touch. The only external call per order is card authorisation.

📄 The full justification is in [`Lab3_Justification.pdf`](Lab3_Justification.pdf).

---

## 📁 Repository Structure

```
SE-LAB-3/
├── Lab3_Component_Diagram.png   # UML component diagram
├── Lab3_Justification.docx      # Written justification (Word)
├── Lab3_Justification.pdf       # Written justification (PDF)
└── README.md                    # Project overview
```

---

## 🛠️ Tools Used

- UML Component Diagram notation (ball-and-socket interfaces, ports, «use» dependencies)
- Microsoft Word for the justification document
