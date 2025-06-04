# GoLang-Code-Quality-Style-Guide

Go (Golang) is a language created to be easy to learn and simple for developers. When starting a project in Go, you need to think about how to make it easy for a junior developer to understand.

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

The domain is the core of DDD. In this folder, you can find the business rules of your application.
For example, if you have a user entity, you can create a folder called `user` and inside this folder you can create the contracts, repository, service, and factory.

### Factory

The factory is a simple function that returns a struct for use in tests. When you need to create a user, you can use the factory to create a user.

### Repository

Repositories are the parts of our code that contain the logic necessary to access data sources, such as databases or web services. In DDD, we use repositories to abstract the data access layer.

Example:
```go
type UserRepository struct {
    db database.Database
}

func (r *UserRepository) FindById(id int) (*User, error) {
    user := &User{}
    err := r.db.QueryOne(ctx, user, "SELECT * FROM users WHERE id = $1", id)
    if err != nil {
        return nil, err
    }
    return user, nil
}
```

### Service

The service is an interface that defines the methods you need to implement in your service. In this file, you include the business rules of your application.

### Contracts

The contracts file defines the contracts that you need to use in your application. For example, if you need to use a struct to create a user, you can create a file called `contracts.go` and define the struct that you need to use.

Example:
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

You might think that Infra is where the adapters used in your application are located.
We look forward to you creating many adapters, and most importantly, that every adapter can be reused in other projects.

### Example

In this example, I'll show a SQLite adapter.

`sqlite.go`
```go
package sqlite

import (
	"context"
	"devalexandre/golang-ddd-template/internal/infra/database"

	"github.com/vingarcia/ksql"
	"github.com/vingarcia/ksql/adapters/ksqlite3"
)

type Database struct {
	DB database.IDB
}

func NewDatabase(ctx context.Context, pathFile string, config ksql.Config) (database.IDatabase, error) {
	conn, errDb := ksqlite3.New(ctx, pathFile, config)

	if errDb != nil {
		return nil, errDb
	}

	return &Database{
		DB: conn,
	}, nil
}

func (c *Database) Select(ctx context.Context, record interface{}, query string, params ...interface{}) error {
	return c.DB.QueryOne(ctx, record, query, params...)
}

func (c *Database) Insert(ctx context.Context, table string, data interface{}) error {
	tb := ksql.NewTable(table)
	return c.DB.Insert(ctx, tb, data)
}

func (c *Database) Update(ctx context.Context, table string, record interface{}) error {
	tb := ksql.NewTable(table)
	return c.DB.Patch(ctx, tb, record)
}

func (c *Database) Delete(ctx context.Context, table string, record interface{}) error {
	tb := ksql.NewTable(table)
	return c.DB.Delete(ctx, tb, record)
}

func (c *Database) Query(ctx context.Context, record interface{}, query string, params ...interface{}) error {
	return c.DB.Query(ctx, record, query, params...)
}

func (c *Database) Close() error {
	return c.DB.Close()
}
```

Remember, to build a consistent application, you need to use interfaces.
When you use them in your application, you can write tests faster and more easily.

`contracts.go` every interfaces I write in contracts.go file

```go
package database

import (
	"context"

	"github.com/vingarcia/ksql"
)

type IDatabase interface {
	Select(ctx context.Context, record interface{}, query string, params ...interface{}) error
	Insert(ctx context.Context, table string, data interface{}) error
	Update(ctx context.Context, table string, record interface{}) error
	Delete(ctx context.Context, table string, record interface{}) error
	Query(ctx context.Context, record interface{}, query string, params ...interface{}) error
	Close() error
}

type IDB interface {
	Query(ctx context.Context, record interface{}, query string, params ...interface{}) error
	QueryOne(ctx context.Context, record interface{}, query string, params ...interface{}) error
	Insert(ctx context.Context, table ksql.Table, record interface{}) error
	Patch(ctx context.Context, table ksql.Table, record interface{}) error
	Delete(ctx context.Context, table ksql.Table, idOrRecord interface{}) error
	Close() error
}
```

# Variables and Functions