# Neo4J go mapper

Data mapper library for [Neo4J go driver](https://github.com/neo4j/neo4j-go-driver)
Refer [test](./test) for usage examples.

## How it works
Package mapper makes heavy use of `reflect` to construct values out of specified types (an empty, initialized value of a type).
This makes it easy to read arbitrary values from neo4j client. Examples [here](./test/reader_test.go). Supports `slice` and `struct` as return types.
```
Usage:
- pass empty, initialized type(s) as the last argument(s) of `ReadSingleRow` and `ReadSingleRow`
- get back values and cast them back into the required types
```

## Interface

```go
// This is the interface implemented by Client
type Mapper interface {
	// Ensure Neo4J connection
	Ping() error

	// Closes bolt driver
	Close() error

	// Execute a cypher statement
	Exec(cypher string, params map[string]interface{}) error

	// Query all results/rows from Neo4J
	Query(
		cypher string,
		params map[string]interface{},
		transform func(record neo4j.Record) interface{},
	) ([]interface{}, error)

	// Query a single Row from Neo4J, for example when the result is a `count`
	QuerySingle(
		cypher string,
		params map[string]interface{},
		transform func(record neo4j.Record) interface{},
	) (interface{}, error)

	// The following 2 functions are Reader utilities for convenience.
	// Pass in initiated empty values in the ordering that corresponds to result elements, cast it back such as `val.(MyType)`
	ReadSingleRow(cypher string, params map[string]interface{}, blankTypes ...interface{}) ([]interface{}, error)
	ReadRows(cypher string, params map[string]interface{}, blankTypes ...interface{}) ([][]interface{}, error)

	// Use this to run `CREATE INDEX/CONSTRAINTS`
	Bootstrap(cypherStmts []string) error
}
```

## Installation
```
go get github.com/sagittaros/neo4j-go-mapper/mapper
```
