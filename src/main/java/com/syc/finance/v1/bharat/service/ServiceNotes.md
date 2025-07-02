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
