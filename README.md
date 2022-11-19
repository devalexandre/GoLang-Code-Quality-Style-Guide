# GoLang-Code-Quality-Style-Guide

## TODO

- [ ] clear architecture
- [ ] variables and functions
- [ ] structs
- [ ] tests
- [ ] interfaces
- [ ] errors

## Clear architecture

When you start a new project, you should think about the architecture of your project. You should think about the following things:

    - How many layers do you need?
    - What is the responsibility of each layer?
    - How do you want to communicate between the layers?
    - How do you want to test the layers?

### Layers
To create a project with clear architecture, you should create the following layers:

    - Domain
    - Application
    - Infrastructure
    - Helpers

#### Domain
In this layer, you should create the following things:

    - Value Objects
    - Entities
    - Interfaces
    - Exceptions

- example:
    - Value Objects
        - Email
        - Password
    - Entities
        - User
        - Post
        - Comment
    - Interfaces
        - UserRepository
        - PostRepository
        - CommentRepository
    - Exceptions
        - UserNotFoundException
        - PostNotFoundException
        - CommentNotFoundException

##### Value Objects
Value Object is a simple pattern on first look but it is very powerful. It is a small object that represents a simple concept. It is immutable and it is used to represent a value. It is used to represent a value that is not an entity. For example, a name, an address, a birthday, and so on. It is used to represent a value that is not an entity. 

example:
```go
type User struct {
    Name string
    Address string
    Birthday time.Time
}
```

##### Entities
Entities are the things that you want to manage in your project. For example, if you want to manage users, you should create a User entity.

KSQL example:
```go
type UserRepository struct {
    db database.Database
}

func (r *UserRepository) FindById(id int) (*User, error) {
    user := &User{}
    err := r.db.QueryOne(ctx, &row, "FROM users  WHERE r.id = $1", user)
    if err != nil {
        return nil, err
    }
    return user, nil
}
```
##### Interfaces
Interfaces are used to define the behavior of the entities. For example, if you want to manage users, you should create a UserRepository interface.

example:
```go
type IUserRepository interface {
    FindAll() ([]User, error)
    FindById(id int) (User, error)
    Save(user User) error
    Update(user User) error
    Delete(user User) error
}
```