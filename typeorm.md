# Intro
- TypeORM supports both Active Record and Data Mapper patterns

# Datasource
## What is Datasource
- TypeORM's DataSource holds your database connection settings and establishes the initial database connection or connection pool depending on the RDBMS you use by call the **initialize** method of your DataSource instance and disconnecting by **destroy** method

## Creating a new DataSource

```typescript
import { DataSource } from "typeorm"

const AppDataSource = new DataSource({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
})

AppDataSource.initialize()
    .then(() => {
        console.log("Data Source has been initialized!")
    })
    .catch((err) => {
        console.error("Error during Data Source initialization", err)
    })
```

## How to use DataSource

```ts
import { AppDataSource } from "./app-data-source"
import { User } from "../entity/User"

export class UserController {
    @Get("/users")
    getAll() {
        return AppDataSource.manager.find(User)
    }
}
```

## Multiple data sources

### Using multiple data sources

```ts
import { DataSource } from "typeorm"

const db1DataSource = new DataSource({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "root",
    password: "admin",
    database: "db1",
    entities: [__dirname + "/entity/*{.js,.ts}"],
    synchronize: true,
})

const db2DataSource = new DataSource({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "root",
    password: "admin",
    database: "db2",
    entities: [__dirname + "/entity/*{.js,.ts}"],
    synchronize: true,
})
```

### Using multiple databases within a single data source

```ts
import { Entity, PrimaryGeneratedColumn, Column } from "typeorm"

// specific database name 
@Entity({ database: "secondDB" })
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    firstName: string

    @Column()
    lastName: string
}
```
### Using multiple schemas within a single data source

```ts
import { Entity, PrimaryGeneratedColumn, Column } from "typeorm"

// specific schame name
@Entity({ schema: "secondSchema" })
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    firstName: string

    @Column()
    lastName: string
}
```

### Replication
- Replication is supported by the MySQL, PostgreSQL, SQL Server, Cockroach, Oracle, and Spanner connection drivers

```ts
const datasource = new DataSource({
  type: "mysql",
  logging: true,
  replication: {
    // option: set the default destination for read queries as the master instance
    defaultMode: "master",
    master: {
      host: "server1",
      port: 3306,
      username: "test",
      password: "test",
      database: "test"
    },
    slaves: [
      {
        host: "server2",
        port: 3306,
        username: "test",
        password: "test",
        database: "test"
      }, {
        host: "server3",
        port: 3306,
        username: "test",
        password: "test",
        database: "test"
      }
    ]
  }
});
```
- All queries performed by the **find** methods or **SelectQueryBuilder** will use ***a random slave instance***
- All write queries performed by **update, create, InsertQueryBuilder, UpdateQueryBuilder, etc** will use the ***master instance***
- All raw queries performed by calling **.query()** will use the **master instance**
- all schema update operations are performed using the **master instance**

### Explicitly selecting query destinations
- If you want to explicitly use the master for read queries, pass an explicit ReplicationMode when creating your QueryRunner;

```ts
const masterQueryRunner = dataSource.createQueryRunner("master")
try {
    const postsFromMaster = await dataSource
        .createQueryBuilder(Post, "post", masterQueryRunner) // you can either pass QueryRunner as an optional argument with query builder
        .setQueryRunner(masterQueryRunner) // or use setQueryRunner which sets or overrides query builder's QueryRunner
        .getMany()
} finally {
    await masterQueryRunner.release()
}
```

## DataSource API

### Options
- Options used to create this dataSource

```ts
const dataSourceOptions: DataSourceOptions = dataSource.options
```

### isInitialized
- Indicates if DataSource was initialized and initial connection / connection pool with database was established or not.

### driver 
- Underlying database driver used in this dataSource

### manager 
- EntityManager used to work with entities. 

### mongoManager 
- MongoEntityManager used to work with entities for mongodb data source.

### initialize 
- Initializes data source and opens connection pool to the database.

### destroy 
- Destroys the DataSource and closes all database connections. Usually, you call this method when your application is shutting down.

### synchronize 
- Synchronizes database schema. When synchronize: true is set in data source options it calls this method. Usually, you call this method when your application is starting

### dropDatabase 
- Drops the database and all its data. 

### runMigrations 
- Runs all pending migrations

### undoLastMigration 
- Reverts last executed migration.

### hasMetadata 
- Checks if metadata for a given entity is registered. 

### getMetadata 
- Gets EntityMetadata of the given entity. You can also specify a table name and if entity metadata with such table name is found it will be returned. 

### getRepository 
- Gets Repository of the given entity. You can also specify a table name and if repository for given table is found it will be returned. 

### getTreeRepository 
- Gets TreeRepository of the given entity. You can also specify a table name and if repository for given table is found it will be returned.

### getMongoRepository 
- Gets MongoRepository of the given entity. This repository is used for entities in MongoDB dataSource.

### transaction 
- Provides a single transaction where multiple database requests will be executed in a single database transaction. 

### query 
- Executes a raw SQL query

### createQueryBuilder 
- Creates a query builder, which can be used to build queries

### createQueryRunner 
- Creates a query runner used to manage and work with a single real database dataSource

# Entity

## What is Entity?
- Entity is a class that maps to a database table (or collection when using MongoDB). 

## Entity columns

### Primary columns
- @PrimaryColumn(), @PrimaryGeneratedColumn()
- You can have composite primary columns as well:
```ts
import { Entity, PrimaryColumn } from "typeorm"

@Entity()
export class User {
    @PrimaryColumn()
    firstName: string

    @PrimaryColumn()
    lastName: string
}
```
## Special columns: 
    - @CreateDateColumn: automatically set to the entity's insertion date.
    - @UpdateDateColumn: automatically set to the entity's update time each time you call **save**.
    - @DeleteDateColumn: automatically set to the entity's delete time each time you call soft-delete.
    - @VersionColumn: automatically set to the version of the entity (incremental number) each time you call **save**

## Spatial columns
    - là các cột trong cơ sở dữ liệu được thiết kế để lưu trữ dữ liệu không gian, chẳng hạn như tọa độ địa lý, hình học hoặc hình học không gian.
    - MS SQL, MySQL, MariaDB, PostgreSQL and CockroachDB all support spatial columns

## Entity inheritance

### Single Table Inheritance
- Single table inheritance is a pattern when you have multiple classes with their own properties, but in the database they are stored in the same table.

```ts
Entity()
@TableInheritance({ column: { type: "varchar", name: "type" } })
export class Content {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    title: string

    @Column()
    description: string
}


@ChildEntity()
export class Photo extends Content {
    @Column()
    size: string
}

@ChildEntity()
export class Question extends Content {
    @Column()
    answersCount: number
}
```

## Tree entities
- TypeORM supports the Adjacency list and Closure table patterns of storing tree structures.
### Adjacency list

- Adjacency list is a simple model with self-referencing
- Benefit of this approach is simplicity, drawback is you can't load a big tree at once because of join limitations

```ts
@Entity()
export class Category {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    @Column()
    description: string

    @ManyToOne((type) => Category, (category) => category.children)
    parent: Category

    @OneToMany((type) => Category, (category) => category.parent)
    children: Category[]
}
```

### Closure table
- A closure table stores relations between parent and child in a separate table in a special way. 
- Its efficient in both reads and writes. 
```ts
@Entity()
@Tree("closure-table")
export class Category {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    @Column()
    description: string

    @TreeChildren()
    children: Category[]

    @TreeParent()
    parent: Category

    @TreeLevelColumn()
    level: number
}
```

### Nested set
- Nested set is pattern of storing tree structures in the database. It is very efficient for reads, but bad for writes. You cannot have multiple roots in the nested set.

```ts
import {
    Entity,
    Tree,
    Column,
    PrimaryGeneratedColumn,
    TreeChildren,
    TreeParent,
    TreeLevelColumn,
} from "typeorm"

@Entity()
@Tree("nested-set")
export class Category {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    @TreeChildren()
    children: Category[]

    @TreeParent()
    parent: Category
}
```

### Materialized Path (aka Path Enumeration)

- Materialized Path (also called Path Enumeration) is another pattern of storing tree structures in the database. It is simple and effective.
```ts
import {
    Entity,
    Tree,
    Column,
    PrimaryGeneratedColumn,
    TreeChildren,
    TreeParent,
    TreeLevelColumn,
} from "typeorm"

@Entity()
@Tree("materialized-path")
export class Category {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    @TreeChildren()
    children: Category[]

    @TreeParent()
    parent: Category
}
```

### Working with tree entities
- To bind tree entities to each other, it is required to set the parent in the child entity and then save them. 
```ts
const a1 = new Category()
a1.name = "a1"
await dataSource.manager.save(a1)

const a11 = new Category()
a11.name = "a11"
a11.parent = a1
await dataSource.manager.save(a11)

const a12 = new Category()
a12.name = "a12"
a12.parent = a1
await dataSource.manager.save(a12)

const a111 = new Category()
a111.name = "a111"
a111.parent = a11
await dataSource.manager.save(a111)

const a112 = new Category()
a112.name = "a112"
a112.parent = a11
await dataSource.manager.save(a112)
```

- To load such a tree use TreeRepository
```ts
const trees = await dataSource.manager.getTreeRepository(Category).findTrees()
```
## Embedded Entities

- Embedded column is a column which accepts a class with its own columns and merges those columns into the current entity's database table
- This way code duplication in the entity classes is reduced

## View Entities

## Separating Entity Definition

# Relations
## What ar relations?

- one-to-one using @OneToOne
```ts
import {
    Entity,
    PrimaryGeneratedColumn,
    Column,
    OneToOne,
    JoinColumn,
} from "typeorm"
import { Profile } from "./Profile"

@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    @OneToOne(() => Profile)
    @JoinColumn() // which is required and must be set only on one side of the relation. The side you set @JoinColumn on, that side's table will contain a "relation id" and foreign keys to the target entity table
    profile: Profile
}
```
To load user with profile inside you must specify relation in FindOptions
```ts
const users = await dataSource.getRepository(User).find({
    relations: {
        profile: true,
    },
})
```
Or using QueryBuilder you can join them:
```ts
const users = await dataSource
    .getRepository(User)
    .createQueryBuilder("user")
    .leftJoinAndSelect("user.profile", "profile")
    .getMany()
```
Relations can be uni-directional and bi-directional. Bi-directional relations allow you to join relations from both sides using QueryBuilder
- many-to-one using @ManyToOne, one-to-many using @OneToMany
    - You can omit @JoinColumn in a @ManyToOne / @OneToMany relation. 
    - @OneToMany cannot exist without @ManyToOne.However, the inverse is not required: If you only care about the @ManyToOne relationship, you can define it without having @OneToMany on the related entity. 
    - Where you set @ManyToOne - its related entity will have "relation id" and foreign key.
- many-to-many using @ManyToMany
    - @JoinTable() is required for @ManyToMany relations. You must put @JoinTable on one (owning) side of relation
    - Bi-directional relations allow you to join relations from both sides using QueryBuilder. Note that the inverse relation does not have a @JoinTable. @JoinTable must be only on one side of the relation.
    - Many-to-many relations with custom properties
    ```ts
    import { Entity, Column, ManyToOne, PrimaryGeneratedColumn } from "typeorm"
    import { Question } from "./question"
    import { Category } from "./category"

    @Entity()
    export class QuestionToCategory {
        @PrimaryGeneratedColumn()
        public questionToCategoryId: number

        @Column()
        public questionId: number

        @Column()
        public categoryId: number

        @Column()
        public order: number

        @ManyToOne(() => Question, (question) => question.questionToCategories)
        public question: Question

        @ManyToOne(() => Category, (category) => category.questionToCategories)
        public category: Category
    }
    ```

## Relation options
- **eager**: boolean (default: false) - If set to true, the relation will always be loaded with the main entity when using find* methods or QueryBuilder on this entity
- **cascade**: boolean | ("insert" | "update")[] (default: false) - If set to true, the related object will be inserted and updated in the database. You can also specify an array of cascade options.
- **onDelete**: "RESTRICT"|"CASCADE"|"SET NULL" (default: RESTRICT) - specifies how foreign key should behave when referenced object is deleted
- **nullable**: boolean (default: true) - Indicates whether this relation's column is nullable or not. By default it is nullable
- **orphanedRowAction**: "nullify" | "delete" | "soft-delete" | "disable" (default: disable) - When a parent is saved (cascading enabled) without a child/children that still exists in database, this will control what shall happen to them.
    - delete will remove these children from database.
    - soft-delete will mark children as soft-deleted.
    - nullify will remove the relation key.
    - disable will keep the relation intact. To delete, one has to use their own repository
## Cascades

## @JoinColumn options
- @JoinColumn not only defines which side of the relation contains the join column with a foreign key, but also allows you to customize join column name and referenced column name.

```ts
@JoinColumn({ name: "cat_id" })
```

## @JoinTable options
- @JoinTable is used for many-to-many relations and describes join columns of the "junction" table. You can change column names inside junction tables and their referenced columns with @JoinColumn: You can also change the name of the generated "junction" table.
```ts
@ManyToMany(type => Category)
@JoinTable({
    name: "question_categories", // table name for the junction table of this relation
    joinColumn: {
        name: "question",
        referencedColumnName: "id"
    },
    inverseJoinColumn: {
        name: "category",
        referencedColumnName: "id"
    }
})
categories: Category[];
```

## Eager and Lazy Relations
### Eager relations
- Eager relations are loaded automatically each time you load entities from the database
- Eager relations only work when you use **find*** methods. If you use QueryBuilder eager relations are disabled and have to use leftJoinAndSelect to load the relation. Eager relations can only be used on one side of the relationship, using eager: true on both sides of relationship is disallowed.

### Lazy relations
- Entities in lazy relations are loaded once you access them. Such relations must have Promise as type - you store your value in a promise, and when you load them a promise is returned as well.
```ts
import { Entity, PrimaryGeneratedColumn, Column, ManyToMany } from "typeorm"
import { Question } from "./Question"

@Entity()
export class Category {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    @ManyToMany((type) => Question, (question) => question.categories)
    questions: Promise<Question[]>
}
```

## FAQ
### How to create self referencing relation

```ts
import {
    Entity,
    PrimaryGeneratedColumn,
    Column,
    ManyToOne,
    OneToMany,
} from "typeorm"

@Entity()
export class Category {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    title: string

    @Column()
    text: string

    @ManyToOne((type) => Category, (category) => category.childCategories)
    parentCategory: Category

    @OneToMany((type) => Category, (category) => category.parentCategory)
    childCategories: Category[]
}
```

### How to use relation id without joining relation
- To do this you just need to add another property to your entity with @Column named exactly as the column created by your relation.
```ts
@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    //use relation id without joining relation
    @Column({ nullable: true })
    profileId: number

    @OneToOne((type) => Profile)
    @JoinColumn()
    profile: Profile
}
```

### Avoid foreign key constraint creation
```ts
import { Entity, PrimaryColumn, Column, ManyToOne } from "typeorm"
import { Person } from "./Person"

@Entity()
export class ActionLog {
    @PrimaryColumn()
    id: number

    @Column()
    date: Date

    @Column()
    action: string

    @ManyToOne((type) => Person, {
        createForeignKeyConstraints: false,
    })
    person: Person
}
```

# EntityManager ans Responsibility
## What is EntityManager
- Using EntityManager you can manage (insert, update, delete, load, etc.) any entity. EntityManager is just like a collection of all entity repositories in a single place. You can access the entity manager via DataSource

```ts
import { DataSource } from "typeorm"
import { User } from "./entity/User"

const myDataSource = new DataSource(/*...*/)
const user = await myDataSource.manager.findOneBy(User, {
    id: 1,
})
user.name = "Umed"
await myDataSource.manager.save(user)
```

## What is Repository
- Repository is just like EntityManager but its operations are limited to a concrete entity. You can access the repository via EntityManager.
```ts
import { User } from "./entity/User"

const userRepository = dataSource.getRepository(User)
const user = await userRepository.findOneBy({
    id: 1,
})
user.name = "Umed"
await userRepository.save(user)
```
- There are 3 types of repositories:
    - Repository - Regular repository for any entity.
    - TreeRepository - Repository, extensions of Repository used for tree-entities (like entities marked with @Tree decorator). Has special methods to work with tree structures.
    - MongoRepository - Repository with special functions used only with MongoDB.

## Find Options
### Basic options
- select
- relations
- where
- order
- withDeleted: include entities which have been soft deleted with softDelete or softRemove, e.g. have their @DeleteDateColumn column set. By default, soft deleted entities are not included.
- skip
- take
- cache
- lock: Enables locking mechanism for query. Can be used only in findOne and findOneBy methods. 
### built-in operators
- Not
- LessThan
- LessThanOrEqual
- MoreThan
- MoreThanOrEqual
- Equal
- Like
- ILike
- Between
- In
- Any
- IsNull
- ArrayContains
- ArrayContainedBy
- ArrayOverlap
- Raw: In the simplest case, a raw query is inserted immediately after the equal symbol. But you can also completely rewrite the comparison logic using the function.
```ts
import { Raw } from "typeorm"

//SELECT * FROM "post" WHERE "currentDate" > '2020-10-06'
const loadedPosts = await dataSource.getRepository(Post).findBy({
    currentDate: Raw((alias) => `${alias} > :date`, { date: "2020-10-06" }),
})
```

## Custom repositories
- Extend repository
```ts
// user.repository.ts
export const UserRepository = dataSource.getRepository(User).extend({
    findByName(firstName: string, lastName: string) {
        return this.createQueryBuilder("user")
            .where("user.firstName = :firstName", { firstName })
            .andWhere("user.lastName = :lastName", { lastName })
            .getMany()
    },
})

// user.controller.ts
export class UserController {
    users() {
        return UserRepository.findByName("Timber", "Saw")
    }
}
```
- Using custom repositories in transactions
```ts
await connection.transaction(async (manager) => {
    // in transactions you MUST use manager instance provided by a transaction,
    // you cannot use global entity managers or repositories,
    // because this manager is exclusive and transactional

    const userRepository = manager.withRepository(UserRepository)
    await userRepository.createAndSave("Timber", "Saw")
    const timber = await userRepository.findByName("Timber", "Saw")
})
```
## EntityManager API
- dataSource
- queryRunner: The query runner used by EntityManager. Used only in transactional instances of EntityManager
- transaction: Provides a transaction where multiple database requests will be executed in a single database transaction
- query: Executes a raw SQL query
- createQueryBuilder: Creates a query builder use to build SQL queries
- hasId: Checks if given entity has its primary column property defined
- getId - Gets given entity's primary column property value. If the entity has composite primary keys then the returned value will be an object with names and values of primary columns
- create
- merge: Merges multiple entities into a single entity
```ts
const user = new User()
manager.merge(User, user, { firstName: "Timber" }, { lastName: "Saw" }) // same as user.firstName = "Timber"; user.lastName = "Saw";
```
- preload: Creates a new entity from the given plain javascript object. If the entity already exist in the database, then it loads it (and everything related to it), replaces all values with the new ones from the given object, and returns the new entity. The new entity is actually loaded from the database entity with all properties replaced from the new object.
- save: Saves a given entity or array of entities. If the entity already exists in the database, then it's updated. If the entity does not exist in the database yet, it's inserted. It saves all given entities in a single transaction (in the case of entity manager is not transactional). Also supports partial updating since all undefined properties are skipped. In order to make a value NULL, you must manually set the property to equal null.
- remove - Removes a given entity or array of entities. It removes all given entities in a single transaction (in the case of entity, manager is not transactional).
- insert 
- update
- upsert: Inserts a new entity or array of entities unless they already exist in which case they are updated instead. Supported by AuroraDataApi, Cockroach, Mysql, Postgres, and Sqlite database drivers
- delete
- increment
- decrement
- exists
- existsBy
- count
- countBy
- find
- findBy
- findAndCount
- findAndCountBy
- findOne
- findOneBy
- findOneOrFail: Finds the first entity that matches some id or find options. Rejects the returned promise if nothing matches
- findOneByOrFail
- clear: Clears all the data from the given table (truncates/drops it).
- getRepository
- getTreeRepository
- getMongoRepository
- withRepository: Gets custom repository instance used in a transaction.
- release: Releases query runner of an entity manager. Used only when query runner was created and managed manually
## Repository API
- manager: The EntityManager used by this repository
- metadata
- queryRunner: The query runner used by EntityManager. Used only in transactional instances of EntityManager
- target: The target entity class managed by this repository. Used only in transactional instances of EntityManager
- createQueryBuilder
- hasId
- getId
- merge 
- preload
- save
- remove
- insert
- update
- upsert
```ts
await repository.upsert(
    [
        { externalId: "abc123", firstName: "Rizzrak", dateAdded: "2020-01-01" },
        { externalId: "bca321", firstName: "Karzzir", dateAdded: "2022-01-01" },
    ],
    {
        conflictPaths: ["externalId"],
        skipUpdateIfNoValuesChanged: true, // supported by postgres, skips update if it would not change row values
        indexPredicate: "dateAdded > 2020-01-01", // supported by postgres, allows for partial indexes
    },
)
/** executes
 *  INSERT INTO user
 *  VALUES
 *      (externalId = abc123, firstName = Rizzrak, dateAdded = 2020-01-01),
 *      (externalId = cba321, firstName = Karzzir, dateAdded = 2022-01-01),
 *  ON CONFLICT (externalId) WHERE ( dateAdded > 2021-01-01 ) DO UPDATE
 *  SET firstName = EXCLUDED.firstName,
 *  SET dateAdded = EXCLUDED.dateAdded,
 *  WHERE user.firstName IS DISTINCT FROM EXCLUDED.firstName OR user.dateAdded IS DISTINCT FROM EXCLUDED.dateAdded
 **/
```
- delete
- softDelete and restore
- softRemove and recover: This is alternative to softDelete and restore.
- increment
- decrement
- exists
- existsBy
- count
- countBy
- sum
- average
- minimum
- maximum
- find
- findBy
- findAndCount
- findAndCountBy
- findOne
- findOneBy
- findOneOrFail
- findOneByOrFail
- query
```ts
const rawData = await repository.query(`SELECT * FROM USERS`)

// You can also use parameters to avoid SQL injection
// The syntax differs between the drivers

// aurora-mysql, better-sqlite3, capacitor, cordova, 
// expo, mariadb, mysql, nativescript, react-native, 
// sap, sqlite, sqljs
const rawData = await repository.query(
    'SELECT * FROM USERS WHERE name = ? and age = ?',
    [ 'John', 24 ]
)

// aurora-postgres, cockroachdb, postgres
const rawData = await repository.query(
    'SELECT * FROM USERS WHERE name = $1 and age = $2',
    ['John', 24]
)

// oracle
const rawData = await repository.query(
    'SELECT * FROM USERS WHERE name = :1 and age = :2',
    ['John', 24]
)

// spanner
const rawData = await repository.query(
    'SELECT * FROM USERS WHERE name = @param0 and age = @param1',
    [ 'John', 24 ]
)

// mssql
const rawData = await repository.query(
    'SELECT * FROM USERS WHERE name = @0 and age = @1',
    [ 'John', 24 ]
)
```
- clear

# Query Builder
## Select using Query Builder
### What is QueryBuilder?
- QueryBuilder allows you to build SQL queries using elegant and convenient syntax, execute them and get automatically transformed entities.
### Important note when using the QueryBuilder
- When using the QueryBuilder, you need to provide unique parameters in your WHERE expressions

### 5 different QueryBuilder types available
- SelectQueryBuilder
- InsertQueryBuilder

```ts
await dataSource
    .createQueryBuilder()
    .insert()
    .into(User)
    .values([
        { firstName: "Timber", lastName: "Saw" },
        { firstName: "Phantom", lastName: "Lancer" },
    ])
    .execute()

// Update values ON CONFLICT
await dataSource
    .createQueryBuilder()
    .insert()
    .into(User)
    .values({
        firstName: "Timber",
        lastName: "Saw",
        externalId: "abc123",
    })
    .orUpdate(
        ["firstName", "lastName"],
        ["externalId"],
    )
    .execute()
//IGNORE error (MySQL)
await dataSource
    .createQueryBuilder()
    .insert()
    .into(User)
    .values({
        firstName: "Timber",
        lastName: "Saw",
        externalId: "abc123",
    })
    .orIgnore()
    .execute()
```
- UpdateQueryBuilder
```ts
await dataSource
    .createQueryBuilder()
    .update(User)
    .set({ firstName: "Timber", lastName: "Saw" })
    .where("id = :id", { id: 1 })
    .execute()
```
- DeleteQueryBuilder
```ts
await dataSource
    .createQueryBuilder()
    .delete()
    .from(User)
    .where("id = :id", { id: 1 })
    .execute()
```
- RelationQueryBuilder
```ts
await dataSource
    .createQueryBuilder()
    .relation(User,"photos")
    .of(id)
    .loadMany();
//another way for add a category with id = 3 into post with id = 1 | more efficient
await dataSource.createQueryBuilder().relation(Post, "categories").of(1).add(3) // or using set in many-to-many and one-to-many relations 
```
- You can switch between different types of query builder within any of them, once you do, you will get a new instance of query builder (unlike all other methods).

### Getting values using QueryBuilder
- getOne(), getOneOrFail(), getMany(), getRawOne(), getRawMany(), getCount()

### What are aliases for?
- used **createQueryBuilder("user")**.
### Using parameters to escape data

```ts
.where("user.name = :name", { name: "Timber" })
//is a shortcut for:
.where("user.name = :name")
.setParameter("name", "Timber")
//array
.where("user.name IN (:...names)", { names: [ "Timber", "Crystal", "Lina" ] })
```

### Adding WHERE expression
- andWhere, orWhere, using Brackets, NotBrackets:
```ts
createQueryBuilder("user")
    .where("user.registered = :registered", { registered: true })
    .andWhere(
        new Brackets((qb) => {
            qb.where("user.firstName = :firstName", {
                firstName: "Timber",
            }).orWhere("user.lastName = :lastName", { lastName: "Saw" })
        }),
    )

createQueryBuilder("user")
    .where("user.registered = :registered", { registered: true })
    .andWhere(
        new NotBrackets((qb) => {
            qb.where("user.firstName = :firstName", {
                firstName: "Timber",
            }).orWhere("user.lastName = :lastName", { lastName: "Saw" })
        }),
    )
//SELECT ... FROM users user WHERE user.registered = true AND NOT((user.firstName = 'Timber' OR user.lastName = 'Saw'))
```

### Adding DISTINCT ON expression (Postgres only)
### Getting the generated query
- getSql(), printSql()

### Streaming result data
- stream() returns you raw data, and you must handle entity transformation manually
## Set locking
- QueryBuilder supports both optimistic and pessimistic locking

## Use custom index
```ts
//Only mysql
const users = await dataSource
    .getRepository(User)
    .createQueryBuilder("user")
    .useIndex("my_index") // name of index
    .getMany()
```

### Max execution time
```ts
const users = await dataSource
    .getRepository(User)
    .createQueryBuilder("user")
    .maxExecutionTime(1000) // milliseconds.
    .getMany()
```

### Partial selection
```ts
const users = await dataSource
    .getRepository(User)
    .createQueryBuilder("user")
    .select(["user.id", "user.name"])
    .getMany()
```

### Using subqueries

```ts
const posts = await dataSource
    .getRepository(Post)
    .createQueryBuilder("post")
    .where((qb) => {
        const subQuery = qb
            .subQuery()
            .select("user.name")
            .from(User, "user")
            .where("user.registered = :registered")
            .getQuery()
        return "post.title IN " + subQuery
    })
    .setParameter("registered", true)
    .getMany()

//another way
const userQb = await dataSource
    .getRepository(User)
    .createQueryBuilder("user")
    .select("user.name")
    .where("user.registered = :registered", { registered: true })

const posts = await dataSource
    .getRepository(Post)
    .createQueryBuilder("post")
    .where("post.title IN (" + userQb.getQuery() + ")")
    .setParameters(userQb.getParameters())
    .getMany()
```
### Hidden Columns
- use the addSelect function in order to retrieve the information from the column

### Common table expressions
```ts
const users = await connection.getRepository(User)
    .createQueryBuilder('user')
    .select("user.id", 'id')
    .addCommonTableExpression(`
      SELECT "userId" FROM "post"
    `, 'post_users_ids')
    .where(`user.id IN (SELECT "userId" FROM 'post_users_ids')`)
    .getMany();
```

# Migrations
## Creating a new migration

```ts
import { DataSource } from "typeorm"

export default new DataSource({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
    entities: [
        /*...*/
    ],
    migrations: [
        /*...*/
    ],
    migrationsTableName: "custom_migration_table",
})
```

# Transactions

## Creating and using transactions

```ts
await myDataSource.transaction(async (transactionalEntityManager) => {
    // execute queries using transactionalEntityManager
})
//or 
await myDataSource.manager.transaction(async (transactionalEntityManager) => {
    // execute queries using transactionalEntityManager
})
```

## Specifying Isolation Levels

```ts
await myDataSource.manager.transaction(
    "SERIALIZABLE",
    (transactionalEntityManager) => {},
)
```

Isolation level implementations are not agnostic across all databases.

## Using QueryRunner to create and control state of single database connection

- QueryRunner provides a single database connection. Transactions are organized using query runners. Single transactions can only be established on a single query runner. You can manually create a query runner instance and use it to manually control transaction state. 

```ts
// create a new query runner
const queryRunner = dataSource.createQueryRunner()

// establish real database connection using our new query runner
await queryRunner.connect()

// now we can execute any queries on a query runner, for example:
await queryRunner.query("SELECT * FROM users")

// we can also access entity manager that works with connection created by a query runner:
const users = await queryRunner.manager.find(User)

// lets now open a new transaction:
await queryRunner.startTransaction()

try {
    // execute some operations on this transaction:
    await queryRunner.manager.save(user1)
    await queryRunner.manager.save(user2)
    await queryRunner.manager.save(photos)

    // commit transaction now:
    await queryRunner.commitTransaction()
} catch (err) {
    // since we have errors let's rollback changes we made
    await queryRunner.rollbackTransaction()
} finally {
    // you need to release query runner which is manually created:
    await queryRunner.release()
}
```

# Indices
## Column indices
- @Index("name1-idx")
## Unique indices
- @Index({ unique: true })
## Indices with multiple columns
```ts
@Entity()
@Index(["firstName", "lastName"])
@Index(["firstName", "middleName", "lastName"], { unique: true })
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    firstName: string

    @Column()
    middleName: string

    @Column()
    lastName: string
}
```

## Spatial Indices
- @Index({ spatial: true })

## Disabling synchronization
- @Index("POST_NAME_INDEX", { synchronize: false })

# Entity Listeners and Subscribers
## What is an Entity Listener
- Any of your entities can have methods with custom logic that listen to specific entity events. You must mark those methods with special decorators depending on what event you want to listen to.
## @AfterLoad
- You can define a method with any name in entity and mark it with @AfterLoad and TypeORM will call it each time the entity is loaded using QueryBuilder or repository/manager find methods

```ts
@Entity()
export class Post {
    @AfterLoad()
    updateCounters() {
        if (this.likesCount === undefined) this.likesCount = 0
    }
}
```

## @BeforeInsert
## @AfterInsert
## @BeforeUpdate
## @AfterUpdate
## @BeforeRemove
## @AfterRemove
## @BeforeSoftRemove
## @AfterSoftRemove
## @BeforeRecover
## @AfterRecover

## What is a Subscriber
- Marks a class as an event subscriber which can listen to specific entity events or any entity events. Events are firing using QueryBuilder and repository/manager methods.
```ts
@EventSubscriber()
export class PostSubscriber implements EntitySubscriberInterface<Post> {
    /**
     * Indicates that this subscriber only listen to Post events.
     */
    listenTo() {
        return Post
    }

    /**
     * Called before post insertion.
     */
    beforeInsert(event: InsertEvent<Post>) {
        console.log(`BEFORE POST INSERTED: `, event.entity)
    }
}
```

## Event Object

- Excluding listenTo, all EntitySubscriberInterface methods are passed an event object that has the following base properties:
    - dataSource: DataSource - DataSource used in the event.
    - queryRunner: QueryRunner - QueryRunner used in the event transaction.
    - manager: EntityManager - EntityManager used in the event transaction
- Note that event.entity may not necessarily contain primary key(s) when Repository.update() is used. Only the values provided as the entity partial will be available. In order to make primary keys available in the subscribers, you can explicitly pass primary key value(s) in the partial entity object literal or use Repository.save(), which performs re-fetching.
```ts
await postRepository.update(post.id, { description: "Bacon ipsum dolor amet cow" })

// post.subscriber.ts
afterUpdate(event: UpdateEvent<Post>) {
  console.log(event.entity) // outputs { description: 'Bacon ipsum dolor amet cow' }
}
```

Note: All database operations in the subscribed event listeners should be performed using the event object's queryRunner or manager instance.