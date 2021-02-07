---
title: 数据库gorm
date: 2021-02-05 14:46:57
tags:
    - database
    - sql
categories: 数据库
top: 90
toc: true # 是否启用内容索引
sidebar: true # 是否启用sidebar侧边栏，none：不启用
---
数据库连接框架
封装接口易懂
<!-- more -->
## 框架gorm

```
全功能 ORM
关联 (Has One，Has Many，Belongs To，Many To Many，多态，单表继承)
Create，Save，Update，Delete，Find 中钩子方法
支持 Preload、Joins 的预加载
事务，嵌套事务，Save Point，Rollback To Saved Point
Context，预编译模式，DryRun 模式
批量插入，FindInBatches，Find/Create with Map，使用 SQL 表达式、Context Valuer 进行 CRUD
SQL 构建器，Upsert，数据库锁，Optimizer/Index/Comment Hint，命名参数，子查询
复合主键，索引，约束
Auto Migration
自定义 Logger
灵活的可扩展插件 API：Database Resolver（多数据库，读写分离）、Prometheus…
每个特性都经过了测试的重重考验
开发者友好
```

## 安装导包

```
go get -u gorm.io/gorm
go get -u gorm.io/driver/sqlite
go get -u gorm.io/driver/mysql
import (
  "gorm.io/gorm"
  "gorm.io/driver/sqlite"
)
```

## 库gorm的DB struct

```
// DB GORM DB definition
type DB struct {
   *Config
   Error        error
   RowsAffected int64
   Statement    *Statement
   clone        int
}
```

## 库gorm的Config struct

```
type Config struct {
	// GORM perform single create, update, delete operations in transactions by default to ensure database data integrity
	// You can disable it by setting `SkipDefaultTransaction` to true
	SkipDefaultTransaction bool
	//字段单数(true)还是复数
	NamingStrategy schema.Namer
	// FullSaveAssociations full save associations
	FullSaveAssociations bool
	// Logger
	Logger logger.Interface
	// NowFunc the function to be used when creating a new timestamp
	NowFunc func() time.Time
	// DryRun generate sql without execute
	DryRun bool
	// PrepareStmt executes the given query in cached statement
	PrepareStmt bool
	// DisableAutomaticPing
	DisableAutomaticPing bool
	// DisableForeignKeyConstraintWhenMigrating
	DisableForeignKeyConstraintWhenMigrating bool
	// DisableNestedTransaction disable nested transaction
	DisableNestedTransaction bool
	// AllowGlobalUpdate allow global update
	AllowGlobalUpdate bool
	// QueryFields executes the SQL query with all fields of the table
	QueryFields bool
	// CreateBatchSize default create batch size
	CreateBatchSize int

	// ClauseBuilders clause builder
	ClauseBuilders map[string]clause.ClauseBuilder
	// ConnPool db conn pool
	ConnPool ConnPool
	// Dialector database dialector
	Dialector
	// Plugins registered plugins
	Plugins map[string]Plugin
	callbacks  *callbacks
	cacheStore *sync.Map
}
```

## 库gorm的Session struct

```
// Session session config when create session with Session() method
type Session struct {
	DryRun                   bool
	PrepareStmt              bool
	NewDB                    bool
	SkipHooks                bool
	SkipDefaultTransaction   bool
	DisableNestedTransaction bool
	AllowGlobalUpdate        bool
	FullSaveAssociations     bool
	QueryFields              bool
	Context                  context.Context
	Logger                   logger.Interface
	NowFunc                  func() time.Time
	CreateBatchSize          int
}
```

## 库gorm的Dialector interface

```
// Dialector GORM database dialector
type Dialector interface {
	Name() string
	Initialize(*DB) error
	Migrator(db *DB) Migrator
	DataTypeOf(*schema.Field) string
	DefaultValueOf(*schema.Field) clause.Expression
	BindVarTo(writer clause.Writer, stmt *Statement, v interface{})
	QuoteTo(clause.Writer, string)
	Explain(sql string, vars ...interface{}) string
}
```



## 库gorm的函数Open

```
// Open initialize db session based on dialector
func Open(dialector Dialector, config *Config) (db *DB, err error) {
	if config == nil {
		config = &Config{}
	}

	if config.NamingStrategy == nil {
		config.NamingStrategy = schema.NamingStrategy{}
	}

	if config.Logger == nil {
		config.Logger = logger.Default
	}

	if config.NowFunc == nil {
		config.NowFunc = func() time.Time { return time.Now().Local() }
	}

	if dialector != nil {
		config.Dialector = dialector
	}

	if config.Plugins == nil {
		config.Plugins = map[string]Plugin{}
	}

	if config.cacheStore == nil {
		config.cacheStore = &sync.Map{}
	}

	db = &DB{Config: config, clone: 1}

	db.callbacks = initializeCallbacks(db)

	if config.ClauseBuilders == nil {
		config.ClauseBuilders = map[string]clause.ClauseBuilder{}
	}

	if config.Dialector != nil {
		err = config.Dialector.Initialize(db)
	}

	preparedStmt := &PreparedStmtDB{
		ConnPool:    db.ConnPool,
		Stmts:       map[string]Stmt{},
		Mux:         &sync.RWMutex{},
		PreparedSQL: make([]string, 0, 100),
	}
	db.cacheStore.Store("preparedStmt", preparedStmt)

	if config.PrepareStmt {
		db.ConnPool = preparedStmt
	}

	db.Statement = &Statement{
		DB:       db,
		ConnPool: db.ConnPool,
		Context:  context.Background(),
		Clauses:  map[string]clause.Clause{},
	}

	if err == nil && !config.DisableAutomaticPing {
		if pinger, ok := db.ConnPool.(interface{ Ping() error }); ok {
			err = pinger.Ping()
		}
	}

	if err != nil {
		config.Logger.Error(context.Background(), "failed to initialize database, got error %v", err)
	}

	return
}
```



## 连接MySql

```
package main
import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
)
func main() {
	dsn := "ginblog:admin123@tcp(127.0.0.1:3306)/ginblog?charset=utf8mb4&parseTime=True&loc=Local"
	db, _ := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	db.Config.NamingStrategy = schema.NamingStrategy{SingularTable:true}//字段单数
	fmt.Println(&db)
}
```

## 库sql的连接池设置函数

```
//GORM 使用 database/sql 维护连接池
sqlDB, err := db.DB()
// SetMaxIdleConns 设置空闲连接池中连接的最大数量
sqlDB.SetMaxIdleConns(10)
// SetMaxOpenConns 设置打开数据库连接的最大数量。
sqlDB.SetMaxOpenConns(100)
// SetConnMaxLifetime 设置了连接可复用的最大时间。
sqlDB.SetConnMaxLifetime(time.Hour)
```

## 库gorm的api

```
Variables
func Expr(expr string, args ...interface{}) clause.Expr
func Scan(rows *sql.Rows, db *DB, initialized bool)
type Association
func (association *Association) Append(values ...interface{}) error
func (association *Association) Clear() error
func (association *Association) Count() (count int64)
func (association *Association) Delete(values ...interface{}) error
func (association *Association) Find(out interface{}, conds ...interface{}) error
func (association *Association) Replace(values ...interface{}) error
type ColumnType
type Config
type ConnPool
type ConnPoolBeginner
type DB
func Open(dialector Dialector, config *Config) (db *DB, err error)
func (db *DB) AddError(err error) error
func (db *DB) Assign(attrs ...interface{}) (tx *DB)
func (db *DB) Association(column string) *Association
func (db *DB) Attrs(attrs ...interface{}) (tx *DB)
func (db *DB) AutoMigrate(dst ...interface{}) error
func (db *DB) Begin(opts ...*sql.TxOptions) *DB
func (db *DB) Callback() *callbacks
func (db *DB) Clauses(conds ...clause.Expression) (tx *DB)
func (db *DB) Commit() *DB
func (db *DB) Count(count *int64) (tx *DB)
func (db *DB) Create(value interface{}) (tx *DB)
func (db *DB) CreateInBatches(value interface{}, batchSize int) (tx *DB)
func (db *DB) DB() (*sql.DB, error)
func (db *DB) Debug() (tx *DB)
func (db *DB) Delete(value interface{}, conds ...interface{}) (tx *DB)
func (db *DB) Distinct(args ...interface{}) (tx *DB)
func (db *DB) Exec(sql string, values ...interface{}) (tx *DB)
func (db *DB) Find(dest interface{}, conds ...interface{}) (tx *DB)
func (db *DB) FindInBatches(dest interface{}, batchSize int, fc func(tx *DB, batch int) error) *DB
func (db *DB) First(dest interface{}, conds ...interface{}) (tx *DB)
func (db *DB) FirstOrCreate(dest interface{}, conds ...interface{}) (tx *DB)
func (db *DB) FirstOrInit(dest interface{}, conds ...interface{}) (tx *DB)
func (db *DB) Get(key string) (interface{}, bool)
func (db *DB) Group(name string) (tx *DB)
func (db *DB) Having(query interface{}, args ...interface{}) (tx *DB)
func (db *DB) InstanceGet(key string) (interface{}, bool)
func (db *DB) InstanceSet(key string, value interface{}) *DB
func (db *DB) Joins(query string, args ...interface{}) (tx *DB)
func (db *DB) Last(dest interface{}, conds ...interface{}) (tx *DB)
func (db *DB) Limit(limit int) (tx *DB)
func (db *DB) Migrator() Migrator
func (db *DB) Model(value interface{}) (tx *DB)
func (db *DB) Not(query interface{}, args ...interface{}) (tx *DB)
func (db *DB) Offset(offset int) (tx *DB)
func (db *DB) Omit(columns ...string) (tx *DB)
func (db *DB) Or(query interface{}, args ...interface{}) (tx *DB)
func (db *DB) Order(value interface{}) (tx *DB)
func (db *DB) Pluck(column string, dest interface{}) (tx *DB)
func (db *DB) Preload(query string, args ...interface{}) (tx *DB)
func (db *DB) Raw(sql string, values ...interface{}) (tx *DB)
func (db *DB) Rollback() *DB
func (db *DB) RollbackTo(name string) *DB
func (db *DB) Row() *sql.Row
func (db *DB) Rows() (*sql.Rows, error)
func (db *DB) Save(value interface{}) (tx *DB)
func (db *DB) SavePoint(name string) *DB
func (db *DB) Scan(dest interface{}) (tx *DB)
func (db *DB) ScanRows(rows *sql.Rows, dest interface{}) error
func (db *DB) Scopes(funcs ...func(*DB) *DB) *DB
func (db *DB) Select(query interface{}, args ...interface{}) (tx *DB)
func (db *DB) Session(config *Session) *DB
func (db *DB) Set(key string, value interface{}) *DB
func (db *DB) SetupJoinTable(model interface{}, field string, joinTable interface{}) error
func (db *DB) Table(name string, args ...interface{}) (tx *DB)
func (db *DB) Take(dest interface{}, conds ...interface{}) (tx *DB)
func (db *DB) Transaction(fc func(tx *DB) error, opts ...*sql.TxOptions) (err error)
func (db *DB) Unscoped() (tx *DB)
func (db *DB) Update(column string, value interface{}) (tx *DB)
func (db *DB) UpdateColumn(column string, value interface{}) (tx *DB)
func (db *DB) UpdateColumns(values interface{}) (tx *DB)
func (db *DB) Updates(values interface{}) (tx *DB)
func (db *DB) Use(plugin Plugin) error
func (db *DB) Where(query interface{}, args ...interface{}) (tx *DB)
func (db *DB) WithContext(ctx context.Context) *DB
type DeletedAt
func (DeletedAt) DeleteClauses(f *schema.Field) []clause.Interface
func (n DeletedAt) MarshalJSON() ([]byte, error)
func (DeletedAt) QueryClauses(f *schema.Field) []clause.Interface
func (n *DeletedAt) Scan(value interface{}) error
func (n *DeletedAt) UnmarshalJSON(b []byte) error
func (n DeletedAt) Value() (driver.Value, error)
type Dialector
type Migrator
type Model
type Plugin
type PreparedStmtDB
func (db *PreparedStmtDB) BeginTx(ctx context.Context, opt *sql.TxOptions) (ConnPool, error)
func (db *PreparedStmtDB) Close()
func (db *PreparedStmtDB) ExecContext(ctx context.Context, query string, args ...interface{}) (result sql.Result, err error)
func (db *PreparedStmtDB) QueryContext(ctx context.Context, query string, args ...interface{}) (rows *sql.Rows, err error)
func (db *PreparedStmtDB) QueryRowContext(ctx context.Context, query string, args ...interface{}) *sql.Row
type PreparedStmtTX
func (tx *PreparedStmtTX) Commit() error
func (tx *PreparedStmtTX) ExecContext(ctx context.Context, query string, args ...interface{}) (result sql.Result, err error)
func (tx *PreparedStmtTX) QueryContext(ctx context.Context, query string, args ...interface{}) (rows *sql.Rows, err error)
func (tx *PreparedStmtTX) QueryRowContext(ctx context.Context, query string, args ...interface{}) *sql.Row
func (tx *PreparedStmtTX) Rollback() error
type SavePointerDialectorInterface
type Session
type SoftDeleteDeleteClause
func (sd SoftDeleteDeleteClause) Build(clause.Builder)
func (sd SoftDeleteDeleteClause) MergeClause(*clause.Clause)
func (sd SoftDeleteDeleteClause) ModifyStatement(stmt *Statement)
func (sd SoftDeleteDeleteClause) Name() string
type SoftDeleteQueryClause
func (sd SoftDeleteQueryClause) Build(clause.Builder)
func (sd SoftDeleteQueryClause) MergeClause(*clause.Clause)
func (sd SoftDeleteQueryClause) ModifyStatement(stmt *Statement)
func (sd SoftDeleteQueryClause) Name() string
type Statement
func (stmt *Statement) AddClause(v clause.Interface)
func (stmt *Statement) AddClauseIfNotExists(v clause.Interface)
func (stmt *Statement) AddVar(writer clause.Writer, vars ...interface{})
func (stmt *Statement) Build(clauses ...string)
func (stmt *Statement) BuildCondition(query interface{}, args ...interface{}) []clause.Expression
func (stmt *Statement) Changed(fields ...string) bool
func (stmt *Statement) Parse(value interface{}) (err error)
func (stmt *Statement) Quote(field interface{}) string
func (stmt *Statement) QuoteTo(writer clause.Writer, field interface{})
func (stmt *Statement) SelectAndOmitColumns(requireCreate, requireUpdate bool) (map[string]bool, bool)
func (stmt *Statement) SetColumn(name string, value interface{}, fromCallbacks ...bool)
func (stmt *Statement) WriteByte(c byte) error
func (stmt *Statement) WriteQuoted(value interface{})
func (stmt *Statement) WriteString(str string) (int, error)
type StatementModifier
type Stmt
type TxBeginner
type TxCommitter
type Valuer
type ViewOption
```

SQL查询例子

```
SELECT category.name AS "分类", title AS "文章标题",content AS "内容" FROM article
INNER JOIN category
ON article.cid = category.id
WHERE category.id=1
```

