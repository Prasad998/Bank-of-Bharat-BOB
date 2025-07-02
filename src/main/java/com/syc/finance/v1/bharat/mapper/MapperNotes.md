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

## Manual UserMapper (most common and transparent)
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
        // Password is not in DTO; you can set default or handle elsewhere
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
