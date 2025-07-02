## 🤔 What's up with Services ?

* **Controllers** delegate requests to **Services** — they contain the **main business logic**.
* Services call:
  * Repositories (to talk to DB),
  * Mappers (to convert data),
  * Other utility classes.
> 📌 The Service layer is the glue between incoming HTTP requests (Controller) and data processing/saving (Repository + Mapper).

---
**Follow the full request lifecycle `CSMDR`**:
`Controller ➝ Service ➝ Mapper/Repository ➝ Response`   






































---
## ⚠️ Scope Of Improvements

| Area | Comment                                                                                                                     |
| ---- | --------------------------------------------------------------------------------------------------------------------------- |
| ✅    | Very complete with full CRUD, transaction, UPI, notifications.                                                              |
| ⚠️   | In `updateAccountDetails`, it creates a new object instead of updating the fetched one — potential loss of unchanged data.  |
| ⚠️   | UPI and NetBanking details in `getYourAccountDetails` are hardcoded / default constructed — should fetch actual DB objects. |
| 🧪   | Unit and integration tests would help ensure the integrity of these operations.                                             |
| 🔐   | Repeated account validation could be centralized into a helper method to avoid DRY violations.                              |
| 🔒   | Passwords are stored and compared as plain text — consider hashing and salting.                                             |

