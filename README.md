# 🎪 Comic-Con India Parking System ERD

## 📌 Overview

This project models a **multi-zone parking management system** designed for large-scale events like Comic-Con India. The system handles high-volume vehicle traffic across multiple days, supporting structured parking allocation, reserved access, and payment tracking.

The design follows **crow’s foot notation** and focuses on scalability, flexibility, and real-world constraints such as multiple entries, reserved parking, and dynamic availability.

---

## 🚀 Features

* Track **vehicles entering and exiting** the venue
* Support multiple **vehicle categories** (bike, car, SUV, EV, etc.)
* Manage **multi-zone, multi-level parking structure**
* Allocate parking spots based on:

  * vehicle type
  * access category (VIP, staff, exhibitor, etc.)
* Handle **reserved parking spots**
* Record **parking sessions** with entry and exit timestamps
* Generate **parking tickets**
* Track **payments and transaction status**
* Support **multiple visits** by the same vehicle
* Ensure **parking spot reuse over time**

---

## 🧱 System Design

### 🔹 Core Entities

| Entity             | Description                            |
| ------------------ | -------------------------------------- |
| `vehicle`          | Stores vehicle details                 |
| `vehicle_category` | Defines vehicle types                  |
| `parking_zone`     | High-level parking area                |
| `parking_level`    | Subdivision of zones                   |
| `parking_spot`     | Individual parking spaces              |
| `spot_category`    | Defines spot type (EV, reserved, etc.) |
| `parking_session`  | Tracks entry and exit of vehicles      |
| `parking_ticket`   | Issued at entry                        |
| `payment`          | Stores payment details                 |

---

### 🔹 Supporting Entities

| Entity                    | Description                              |
| ------------------------- | ---------------------------------------- |
| `access_category`         | Defines roles like VIP, staff, exhibitor |
| `vehicle_access`          | Maps vehicles to access privileges       |
| `spot_access_restriction` | Restricts spot usage by access type      |

---

## 🔄 Workflow

### 🚗 Entry Process

1. Vehicle arrives at entry gate
2. System identifies:

   * vehicle category
   * access privileges
3. Parking ticket is generated
4. Suitable parking spot is assigned
5. Parking session begins (entry_time recorded)

---

### 🅿️ During Parking

* Vehicle occupies assigned spot
* Spot becomes unavailable (logically via active session)

---

### 🚪 Exit Process

1. Vehicle exits parking
2. Exit time is recorded
3. Duration is calculated
4. Parking fee is computed
5. Payment is processed
6. Session is closed

---

## 🧠 Key Design Decisions

### ✅ 1. Parking Session as Core Entity

* Represents a single visit
* Handles entry, exit, and duration
* Enables:

  * multiple visits per vehicle
  * reuse of parking spots

---

### ✅ 2. Separation of Concerns

| Concept        | Entity             |
| -------------- | ------------------ |
| Vehicle Type   | `vehicle_category` |
| Spot Type      | `spot_category`    |
| Access Control | `access_category`  |

---

### ✅ 3. Flexible Access Control

* Vehicles can have multiple access roles via `vehicle_access`
* Spots can restrict access via `spot_access_restriction`

---

### ✅ 4. Availability Handling

* No direct dependency on static availability
* Availability can be derived from active sessions:

  * A spot is occupied if a session exists with `exit_time = NULL`

---

### ✅ 5. Multi-Level Parking Structure

* `parking_zone` → `parking_level` → `parking_spot`
* Enables hierarchical organization

---

## 📊 Example Queries

### 🔍 Vehicles Currently Parked

```sql
SELECT *
FROM parking_session
WHERE exit_time IS NULL;
```

---

### 🅿️ Available Parking Spots

```sql
SELECT *
FROM parking_spot ps
WHERE NOT EXISTS (
  SELECT 1
  FROM parking_session s
  WHERE s.spot_id = ps.spot_id
  AND s.exit_time IS NULL
);
```

---

### 💰 Payments for a Session

```sql
SELECT *
FROM payment
WHERE session_id = 'SESSION_ID';
```

---

## 🛠️ Tech Considerations

* Designed for **high concurrency environments**
* Supports **real-time parking allocation**
* Easily extendable for:

  * online booking
  * dynamic pricing
  * analytics dashboards

---

## 📷 ER Diagram

> Add your ER diagram image here

```md
![ER Diagram](./diagram.png)
```

---

## 🎯 Conclusion

This system provides a scalable and structured approach to managing parking for large-scale events. By separating entities and modeling real-world relationships effectively, it ensures flexibility, maintainability, and accuracy in tracking parking operations.

---

## 🙌 Author

Built as part of system design practice for real-world event scenarios.
