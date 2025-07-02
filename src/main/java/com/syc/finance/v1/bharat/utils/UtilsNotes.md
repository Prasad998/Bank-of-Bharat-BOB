In a **Spring Boot** application, the `utils` (or `utility`) folder is typically used to store **general-purpose helper classes** that are not tied to any specific business logic, domain model, or layer (like controller, service, or repository).

These classes contain **reusable methods or constants** that are used throughout the application.

---

### 🔧 Common Contents of the `utils` Folder

| Utility Type             | Description                                                | Example                                      |
| ------------------------ | ---------------------------------------------------------- | -------------------------------------------- |
| **Constants**            | Static variables shared across the app                     | `BankConstants`, `ErrorCodes`, `AppMessages` |
| **Generators**           | Code for generating IDs, passwords, IFSC, OTPs, etc.       | `AccountDetailsGenerators`, `OtpGenerator`   |
| **Date/Time Utils**      | Date formatting, parsing, or difference calculation        | `DateTimeUtil.getCurrentIST()`               |
| **Validation**           | Custom validation logic                                    | `ValidationUtil.isValidEmail(String)`        |
| **Formatters**           | Methods for formatting phone, currency, etc.               | `FormatUtil.formatCurrency(double)`          |
| **Notification Senders** | SMS, Email, Twilio wrappers                                | `NotificationUtil.sendSms()`                 |
| **Encryption/Hashing**   | Methods for hashing passwords, encrypting data             | `PasswordUtil.hashPassword()`                |
| **File Handling**        | File reading/writing utilities                             | `FileUtil.readCsv()`                         |
| **Mapping Helpers**      | Sometimes used when not placed in dedicated mapper classes | `ObjectMapperUtils.copyNonNullProperties()`  |

---

### 📦 Example Structure

```
src/
└── main/
    └── java/
        └── com/
            └── yourapp/
                ├── controller/
                ├── service/
                ├── repository/
                ├── dto/
                ├── entity/
                └── utils/
                    ├── AccountDetailsGenerators.java
                    ├── AccountLimitReached.java
                    ├── NotificationUtil.java
                    ├── DateUtil.java
                    └── BankConstants.java
```

---

### ✅ Best Practices

* ✅ Keep them **stateless** (use static methods or inject stateless beans).
* ✅ Do **not** inject business logic or repository/service code here.
* ✅ Group similar utilities (e.g., `DateUtil`, `StringUtil`) for clarity.
* ✅ Avoid putting too many unrelated utilities in one class like `CommonUtil`.

---

Let me know if you want examples of how your specific project's utils (like `AccountDetailsGenerators` or `AccountLimitReached`) work and when to use them.
