## 🔄 Example Flow:

1. **Controller** receives a request.
2. Calls a **Service method** with input DTO.
3. Service:

   * Validates input
   * Calls **Mapper** to convert DTO → Entity
   * Calls **Repository** to save/fetch data
   * Calls **Mapper** again to convert Entity → Response DTO
4. Returns response to controller
---
## 🔁 After Services:

Once you understand the flow in the Service layer, **then** revisit:

* `Mapper` classes to see the conversion logic in isolation
* `DTOs` and `Entities` to understand data structure
* `Repositories` to know what queries are being made

---

### 🧭 Final Suggestion:

**Follow the full request lifecycle**:
`Controller ➝ Service ➝ Mapper/Repository ➝ Response`

---
`UserDTO`: Used for data transfer (e.g. API requests/responses).  

`User`: The Entity, used for database persistence.  

---
**UserDTOtoUser()**  
Converts incoming request data to a database entity.  

🔸 Used When:  
You receive a UserDTO from the frontend (e.g., registration form).  
You want to save it to the database, so you convert it to User.  
```java
public User dtoToEntity(UserDTO dto) {
    return User.builder()
        .name(dto.getName())
        .email(dto.getEmail())
        .password(encode(dto.getPassword()))
        .build();
}
```
**UserToUserDTO()**  
Converts internal data (entity) to something safe to return via API.  

🔸 Used When:  
You fetch a User from the DB, but don’t want to expose all internal fields.  
You want to return only public/safe fields (e.g., no password, no internal IDs).

```java
public UserDTO entityToDTO(User user) {
    return UserDTO.builder()
        .name(user.getName())
        .email(user.getEmail())
        .build();
}
```

| Direction      | Analogy                            | Purpose                     |
| -------------- | ---------------------------------- | --------------------------- |
| `DTO ➜ Entity` | Filling a form into a database row | Accept user input and store |
| `Entity ➜ DTO` | Showing a profile page from DB     | Send safe data to frontend  |

---
**Bonus**: Use a Mapper Class  
It’s common to centralize these in a UserMapper class or use tools like MapStruct.
```java
@Component
public class UserMapper {
    public User toEntity(UserDTO dto) { ... }
    public UserDTO toDTO(User user) { ... }
}
```
---

## 1. Manual UserMapper (most common and transparent)
📁 Structure:
- `User.java` (Entity)
- `UserDTO.java` (DTO)
- `UserMapper.java` (Converter utility)  
  
🔸 `User.java` (Entity)
```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
    private String password;
}
```
🔸 `UserDTO.java` (DTO)
```java
public class UserDTO {
    private String name;
    private String email;
}
```
🔸 `UserMapper.java` (Converter utility)  
```java
@Component
public class UserMapper {

    public User toEntity(UserDTO dto) {
        if (dto == null) return null;

        User user = new User();
        user.setName(dto.getName());
        user.setEmail(dto.getEmail());
        // ❌ Never expose password in DTO
        return user;
    }

    public UserDTO toDTO(User user) {
        if (user == null) return null;

        UserDTO dto = new UserDTO();
        dto.setName(user.getName());
        dto.setEmail(user.getEmail());
        return dto;
    }
}
```
## ✅ 2. With MapStruct (recommended for large projects)  
🔸** Step 1: Add MapStruct Dependency**  
```
<details> <summary>Maven</summary>
```
🔸 **Step 2: Create the Mapper Interface**
```java
@Mapper(componentModel = "spring")
public interface UserMapper {

    User toEntity(UserDTO dto);

    UserDTO toDTO(User user);
}
//✅ MapStruct will generate an implementation at build time and inject it via Spring (@Component-like behavior).
```
**🔸 Step 3: Use it in your Service**
```java
@Service
public class UserService {

    private final UserMapper userMapper;

    public UserService(UserMapper userMapper) {
        this.userMapper = userMapper;
    }

    public UserDTO getUserDtoFromDb(Long id) {
        User user = userRepository.findById(id).orElseThrow();
        return userMapper.toDTO(user);
    }

    public void createUser(UserDTO dto) {
        User user = userMapper.toEntity(dto);
        userRepository.save(user);
    }
}
```

```css
src/
├── dto/
│   └── UserDTO.java
├── entity/
│   └── User.java
├── mapper/
│   └── UserMapper.java  ← Manual or MapStruct
├── service/
│   └── UserService.java

```

| Method       | Manual Mapper                         | MapStruct                        |
| ------------ | ------------------------------------- | -------------------------------- |
| Easy Setup   | ✅ Yes                                 | ⚠️ Needs dependency + annotation |
| Verbosity    | 🟡 Verbose                            | ✅ Minimal                        |
| Custom Logic | ✅ Easy to add (e.g., encode password) | 🟡 Needs expression syntax       |
| Performance  | ✅ Fast                                | ✅ Fast (compile-time gen)        |

---

## Doubt   
```
❓ You're asking:
If password isn't set in UserMapper.toEntity(...), how does it get saved in the database?
🔍 Short Answer:
You must include password in the UserDTO when it's needed — like during registration — and handle it properly, usually by encoding it before saving.  
Add password to UserDTO only in relevant use cases, like UserRegistrationDTO (not in general DTO shown to frontend).  
In toEntity(...), use passwordEncoder.encode(...) before saving.
```
**📦 Summary**
| Use Case      | DTO Type              | Password Present? | Notes                     |
| ------------- | --------------------- | ----------------- | ------------------------- |
| Register user | `UserRegistrationDTO` | ✅ Yes             | Required, encode it       |
| Login         | `UserLoginDTO`        | ✅ Yes             | Used to check credentials |
| View user     | `UserDTO`             | ❌ No              | Never expose password     |

