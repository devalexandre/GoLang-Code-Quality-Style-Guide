# GoLang-Code-Quality-Style-Guide

## TODO

- [x] clear architecture
- [x] ddd architecture
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
    - Infrastructure
    - Helpers


# DDD Architecture
## Folder Structure

```bash
├── cmd
│   └── main.go
├── internal
│   ├── domain
│   │   ├── user
│   │   │   ├── repository.go
│   │   │   ├── service.go
│   │   │   ├── factory.go
│   │   │   ├── contracts.go
│   │   │   ├── repository_test.go
│   │   │   ├── service_test.go
│   │   │   ├── factory_test.go
│   │   │   └── mock.go
│   ├── infra
│   │   ├── database
│   │   │   ├── mysql
│   │   │   │   ├── connect.go
│   │   │   │   ├── contracts.go
│   │   │   │   └── mock.go
│   │   ├── openapi
│   │   │   ├── openapi.go
│   │   │   ├── openapi_test.go
│   │   │   ├── contracts.go
│   │   │   └── mock.go
│   ├── helpers
│   │   ├── errors
│   │   │   ├── errors.go
│   │   │   └── errors_test.go
│   │   ├── logger
│   │   │   ├── logger.go
│   │   │   └── logger_test.go
│   │   ├── config
│   │   │   ├── environment.go
│   │   │   └── environment_test.go


```

## Domain

The domain is the core of DDD in this folder you can find the business rules of your application.
for example, if you have a user entity you can create a folder called user and inside this folder you can create the contracts, repository, service and factory.

### Factory

the factory is a simple function that return a struct for use in tests, when you need to create a user you can use the factory to create a user.

### Repository

Repositories are the parts of our code that contain the logic necessary to access data sources, such as databases or web services. In DDD, we use repositories to abstract the data access layer.

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

### Service

the service is a interface that define the methods that you need to implement in your service, and this file you include the business rules of your application.

### Contracts

the contracts is a file that define the contracts that you need to use in your application, for example, if you need  use a struct to create a user you can create a file called contracts and define the struct that you need to use.


example:
```go
type User struct {
    Name string
    Address string
    Birthday time.Time
}


type IUserRepository interface {
    FindAll() ([]User, error)
    FindById(id int) (User, error)
    Save(user User) error
    Update(user User) error
    Delete(user User) error
}
```

### Mock
the mock is a file that you can use to create a mock of your repository or service, this file is used in tests.

## Infra

The infra is a folder that you can use to create the connection with your database or external API, you can create a folder called database and inside this folder you can create a folder called mysql and inside this folder you can create the connection with your database.



