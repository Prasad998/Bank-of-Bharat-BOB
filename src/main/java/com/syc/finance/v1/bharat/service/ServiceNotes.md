## 🤔 What's up with Services ?

* **Controllers** delegate requests to **Services** — they contain the **main business logic**.
* Services call:
  * Repositories (to talk to DB),
  * Mappers (to convert data),
  * Other utility classes.
> 📌 The Service layer is the glue between incoming HTTP requests (Controller) and data processing/saving (Repository + Mapper).
---
* Annotated with `@Service` → tells Spring this is a service class.
* Implements the `AccountService` interface.
* `@Slf4j` → enables `log.info`, `log.error`, etc. for logging.

---

### 🔗 Dependencies Injected

```java
@Autowired
private AccountDetailsRepositories accountDetailsRepositories;
@Autowired
private TransactionHistoryRepository transactionHistoryRepository;
@Autowired
private UPIDetailsRepositories upiDetailsRepositories;
@Autowired
private TransactionService transactionService;
@Autowired
private AccountlLimitReached accountlLimitReached;
@Autowired
private NotificationsUtility notificationsUtility;
```

These are various repositories and helper services:

* **Repositories**: Talk to the database.
* **Services**: Perform operations like transactions and sending notifications.
* **LimitValidator**: Checks for daily transaction limits.
* **Twilio**: Initialized statically for SMS notifications.

---

### 🔧 `createAccount(UserRequest userRequest)`

Creates a new user account:

* Validates if email/phone already exists.
* Generates unique identifiers (IFSC, PIN, password).
* Saves a new `AccountInformation` object in DB.
* Sends account creation notifications.

---

###✏️ `updateAccountDetails(AccountUpdatingDetailsRequest req)`

Updates user info based on the account number:

* Loads account by account number.
* Creates new `AccountInformation` with updated fields.
* Saves it and sends notification.
  ⚠️ **Flaw**: This may overwrite some unchanged fields because a new object is being saved instead of modifying the existing one.

---

### 🗑️ `deleteAccount(AccountDeleteAccountDetailsRequest req)`

Deletes a user’s account:

* Validates using `accountNumber + email + password`.
* Deletes from DB and sends a deletion notification.

---

### 🔍 `getYourAccountDetails(String accountNumber, String IFSCCode, String password)`

Returns a DTO with full account details:

* Fetches from DB using credentials.
* Includes dummy/incomplete values for UPI and NetBanking info (currently not pulled from DB).

---

### 💰 `creditYourMoney(CreditCredential credential)`

Credits money to the user’s account:

* Validates account + transaction limits.
* Adds balance and records the transaction.
* Sends credit notification.

---

### 💸 `debitYourMoney(DebitCredential credential)`

Debits money from account:

* Validates account, limit, and balance.
* Handles high-value transactions differently (>= 10,000).
* Updates balance, records transaction, and sends debit notification.

---

### 📞 `balanceEnquiry(BalanceEnquireyRequest req)`

Returns current account balance:

* Validates account credentials.
* Sends balance notification and returns a `BalanceEnquiryResponse`.

---

### 🔁 `payUsingUpi(AddMoneyFromAccountToUPIRequest req)`

Transfers money from main account to UPI:

* Validates UPI + account.
* Deducts from account and adds to UPI balance.
* Returns success response.

---

### 🔁 `addingMoneyFromAccountNumberToUpi(AddMoneyToUPIFromAccountRequest req)`

Reverse of above:

* Adds UPI balance back to main account.
* Used for cases like refunds, manual adjustments.

---

### 🛠️ `updateAmountInPerson(UpdateAmountManually req)`

Used for manually updating the account balance (e.g., ATM, bank staff):

* Directly sets the balance for the account number.

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

