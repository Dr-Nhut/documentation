# Introdution

# Types

## Primitive Types

### boolean

### number

### string

### void

### undefined

### null

## Object Type

### Interface

- Interface đóng vai trò đặt tên cho type.
- Interface đóng vai trò như một hợp đồng giữa các phần của code. Hợp đồng này giúp bảo đảm rằng các object, class, hoặc func sẽ tuân thủ một cấu trúc hoặc hành vi nhất định
- Giúp bảo vệ code của bạn khỏi các lỗi tiềm ẩn và làm cho mã trở nên rõ ràng, dễ đọc và dễ bảo trì hơn
- Interface sẽ báo lỗi nếu truyền dư thừa property. Sử dụng **_type assertion_**, **_extra property_** hoặc gán type cho một biến khác để bỏ qua lỗi này
- Nếu interface được định nghĩa trùng tên, TS sẽ merge chúng nếu property tương thích.

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

// colour là thuộc tính dư thừa nhưng TypeScript vẫn sẽ không báo lỗi
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

#### Optional properties

#### Readonly properties

- Phân biệt Readonly & const

  - Readonly for property
  - Const for variable

#### Index Signatures

- Cho phép xác định type của các property trong object mà tên của chúng chưa được xác định rõ ràng
- Một số type cho phép: string, number, symbol, template string patterns, and union types chứa những kiểu này

```ts
interface StringKeyedObject {
  [key: string]: number;
}
```

#### Extra property

```ts
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: any; // any number of other property
}
```

#### Extending Types

- Allows us to effectively copy members from other named types, and add whatever new members we want
- Có thể extends từ multiple Interface

```ts
interface BasicAddress {
  name?: string;
  street: string;
  city: string;
  country: string;
  postalCode: string;
}

interface AddressWithUnit extends BasicAddress {
  unit: string;
}
```

#### Intersection types

```ts
interface Colorful {
  color: string;
}
interface Circle {
  radius: number;
}

type ColorfulCircle = Colorful & Circle;
```

#### Generic Object Types

```ts
interface Box<Type> {
  contents: Type;
}
```

### Classes

#### Fields

- Fields có thể có khởi tạo, sẽ tự động run khi class được khởi tạo

- **--strictPropertyInitialization**: setting để bắt buộc class fields cần được khởi tạo trong contructors. Có thể sử dụng ! sau tên fields để bỏ qua strict này cho field chỉ định

#### Contructors

- Là một func, nó có thể có params, type annotations, default value and overloads.

##### Super call

#### Methods

#### Getters/ Setters

- If get exists but no set, the property is automatically readonly
- If the type of the setter parameter is not specified, it is inferred from the return type of the getter

```ts
class C {
  _length = 0;
  get length() {
    return this._length;
  }
  set length(value) {
    this._length = value;
  }
}
```

#### Index Signatures

```ts
class MyClass {
  [s: string]: boolean | ((s: string) => boolean);

  check(s: string) {
    return this[s] as boolean;
  }
}
```

#### Class Heritage

##### implements Clauses

- Check xem một class có thỏa mãn interface không?
- Classes may implement multiple interfaces
- Nó chỉ kiểm tra và không thay đổi type của class hoặc methods của class đó.

```ts
interface Pingable {
  ping(): void;
}

class Sonar implements Pingable {
  ping() {
    console.log("ping!");
  }
}
```

##### extends Clauses

- Classes may extend from a base class
- Class kế thừa tất cả properties and methods of base class, và có thể định nghĩa thêm members.

```ts
class Animal {
  move() {
    console.log("Moving along!");
  }
}

class Dog extends Animal {
  woof(times: number) {
    for (let i = 0; i < times; i++) {
      console.log("woof!");
    }
  }
}

const d = new Dog();
// Base class method
d.move();
// Derived class method
d.woof(3);
```

_Overriding Methods_

- Khi một class con kế thừa từ class cha, class con cần phải tuân theo contract của class cha, tức là phải cung cấp các phương thức và thuộc tính mà class cha yêu cầu. Điều này giúp đảm bảo tính tương thích và cho phép bạn sử dụng các đối tượng của class con thông qua tham chiếu của class cha mà không gặp lỗi.

```ts
const b: Base = d;
// No problem
b.greet();
```

_Type-only Field Declarations_

- Giúp obj khai báo lại type của field kế thừa mà không thay đổi hành vi của nó trong runtime
- Sử dụng từ khóa declare

```ts
interface Animal {
  dateOfBirth: any;
}

interface Dog extends Animal {
  breed: any;
}

class AnimalHouse {
  resident: Animal;
  constructor(animal: Animal) {
    this.resident = animal;
  }
}

class DogHouse extends AnimalHouse {
  // Does not emit JavaScript code,
  // only ensures the types are correct
  declare resident: Dog;
  constructor(dog: Dog) {
    super(dog);
  }
}
```

_Initialization Order_

```ts
//for example
class Base {
  name = "base";
  constructor() {
    console.log("My name is " + this.name);
  }
}

class Derived extends Base {
  name = "derived";
}

// Prints "base", not "derived"
const d = new Derived();
```

1. The base class fields are initialized
2. The base class constructor runs
3. The derived class fields are initialized
4. The derived class constructor runs

_Inheriting Built-in Types_

#### Member Visibility

- _public_ (default): a public member can be accessed anywhere
- _protected_: protected members are only visible to subclasses of the class they’re declared in

  - Exposure of protected members: Các class kế thừa cần follow theo base class của nó, nhưng có thể chọn để expose một subtype của baseclass với nhiều khả năng hơn, bao gồm chuyển _protected_ thành _public_

  - Cross-hierarchy protected access: không thể truy cập _protected_ member với các class anh/chị

- _private_: private giống như protected, nhưng không cho phép truy cập vào thành viên ngay cả từ các lớp con

  - Cross-instance private access: TypeScript cho phép các đối tượng của cùng một class truy cập private members của nhau, điều này tương tự như Java hay C#.
  - Caveats: protected và private chỉ áp dụng trong quá trình type checking. private cũng cho phép truy cập qua **s["secretKey"]**. Nếu cần private cứng có thể sử dụng _closures_, _WeakMaps_, _private field (#)_

#### Static member

- Là các member thuộc về class, không phải instance
- Có thể use public, protected, private
- Có thể được kế thừa

##### Special Static Names

- Function properties like _name_, _length_, and _call_ aren’t valid to define as static members

##### static Blocks in Classes

- Cho phép bạn thực hiện các phép toán hoặc khởi tạo phức tạp trong class mà không cần phải sử dụng static method

```ts
class MyClass {
  static data: string;

  static {
    // Static block
    console.log("Static block is executed");
    MyClass.data = "Hello from static block";
  }
}
```

- Chỉ chạy 1 lần: Static block chỉ được thực thi khi lớp được nạp vào, và nó sẽ chỉ chạy một lần duy nhất.
- Không có đối tượng lớp: Static block không cần đến bất kỳ đối tượng nào của class để thực thi. Nó chỉ liên quan đến class và các thuộc tính static của class.
- Thực thi trước constructor: Static block sẽ được thực thi trước bất kỳ constructor nào của class.

##### Generic Classes

```ts
class Box<Type> {
  contents: Type;
  constructor(value: Type) {
    this.contents = value;
  }
}

const b = new Box("hello!");
```

- Static members không thể tham chiếu đến Generic Type (vì generic type phụ thuộc vào instance còn static member thuộc về class)

##### this at Runtime in class

- Từ khóa this trong JavaScript đôi khi khó hiểu và dẫn đến lỗi không mong muốn

```ts
class MyClass {
  name = "MyClass";
  getName() {
    return this.name;
  }
}
const c = new MyClass();
const obj = {
  name: "obj",
  getName: c.getName,
};

// Prints "obj", not "MyClass"
console.log(obj.getName());
```

- Một số phương pháp giải quyết điều này
  - Arrow functions: bởi vì arrow function không có this, nên sử dụng nó giúp code rõ ràng dễ hiểu hơn. Nhược điểm: tốn bộ nhớ, không thể sử dụng function đó trong class con
  ```ts
  class MyClass {
    name = "MyClass";
    getName = () => {
      return this.name;
    };
  }
  const c = new MyClass();
  const g = c.getName;
  // Prints "MyClass" instead of crashing
  console.log(g());
  ```
  - this parameters:
  ```ts
  // TypeScript input with 'this' parameter
  function fn(this: SomeType, x: number) {
    /* ... */
  }
  ```
- this types

  - Nó sẽ tham chiếu đến type của class hiện tại
  - Đảm bảo đối số là một đối tượng thuộc cùng class hoặc class con

- this-based type guards

  - Có thể sử dụng _this is Type_ khi return cho method trong class và interfaces
  - **this is** dùng để kiểm tra xem đối tượng hiện tại có phải là instance của class hay không

  ```ts
  class FileSystemObject {
    //Cú pháp [this is FileRep] giúp TypeScript hiểu rằng nếu phương thức này trả về true, thì kiểu của this là FileRep.

    isFile(): this is FileRep {
      return this instanceof FileRep;
    }
    isDirectory(): this is Directory {
      return this instanceof Directory;
    }
    isNetworked(): this is Networked & this {
      return this.networked;
    }
    constructor(public path: string, private networked: boolean) {}
  }
  ```

##### Parameter Properties

- Giúp bạn khai báo và khởi tạo các thuộc tính trong constructor của lớp một cách ngắn gọn và rõ ràng, giúp mã của bạn trở nên dễ đọc và bảo trì hơn.

```ts
class Params {
  constructor(
    public readonly x: number,
    protected y: number,
    private z: number
  ) {
    // No body necessary
  }
}
const a = new Params(1, 2, 3);
console.log(a.x);

(property) Params.x: number
console.log(a.z);
```

##### Class Expressions

```ts
const someClass = class<Type> {
  content: Type;
  constructor(value: Type) {
    this.content = value;
  }
};
```

##### Constructor Signatures

- The InstanceType utility type models this operation.

```ts
class Point {
  createdAt: number;
  x: number;
  y: number;
  constructor(x: number, y: number) {
    this.createdAt = Date.now();
    this.x = x;
    this.y = y;
  }
}
type PointInstance = InstanceType<typeof Point>;
```

##### abstract classes and Members

- Class, method và field đều có thể gán abstract
- Một abstract method hoặc abstract field là phương thức chưa được cung cấp triển khai. Các member này phải tồn tại bên trong một lớp trừu tượng, không thể khởi tạo trực tiếp.

```ts
abstract class Base {
  abstract getName(): string;

  printName() {
    console.log("Hello, " + this.getName());
  }
}
//Cannot create an instance of an abstract class
const b = new Base();
```

##### Abstract Construct Signatures

- Là một cách để định nghĩa chữ ký của constructor trong abstract class mà không cần cung cấp phần triển khai, cho phép các lớp con kế thừa và thực hiện lại constructor đó. Điều này giúp kiểm soát cách thức khởi tạo các đối tượng trong hệ thống của bạn, đảm bảo tính nhất quán và linh hoạt trong thiết kế phần mềm.

```ts
abstract class Shape {
  abstract color: string;

  // Định nghĩa abstract constructor signature
  abstract new(color: string): Shape;
}
```

##### Relationships Between Classes

- Các class trong TypeScript được so sánh về mặt cấu trúc, giống như các kiểu khác

```ts
// hai lớp này có thể được sử dụng thay thế cho nhau vì chúng giống hệt nhau
class Point1 {
  x = 0;
  y = 0;
}

class Point2 {
  x = 0;
  y = 0;
}

// OK
const p: Point1 = new Point2();
```

### Enum

- Enum có thể truy vấn giá trị key từ value và ngược lại.

#### Numeric Enums

- Giá trị của các member sẽ tự động tăng (+1 so với giá trị member trước)

#### String Enums

#### Heterogeneous enums

#### Ambient enums

- Dùng để khai báo một Enum mà không cần phải định nghĩa giá trị của nó trong file hiện tại

```ts
//declare cho TypeScript biết rằng Enum Direction sẽ được định nghĩa ở đâu đó bên ngoài code hiện tại
declare enum Direction {
  Up,
  Down,
  Left,
  Right,
}
```

- Ambient Enums chỉ cần thiết khi bạn làm việc với các mã ngoài hoặc thư viện không có sẵn khai báo TypeScript.
- Bạn vẫn có thể truy cập Enum như bình thường, nhưng nó không được định nghĩa trực tiếp trong dự án TypeScript của bạn

#### Enum và Object

- Có thể thay thế Enum bằng objects với **as const** trong nhiều trường hợp mà không làm mất type safety. Đây là một cách tiếp cận tối ưu hơn nếu bạn muốn giữ cho mã nguồn nhẹ nhàng và dễ duy trì, đặc biệt trong các ứng dụng không yêu cầu ánh xạ ngược hoặc quá nhiều tính năng phức tạp của Enum

```ts
const enum EDirection {
  Up,
  Down,
  Left,
  Right,
}

const ODirection = {
  Up: 0,
  Down: 1,
  Left: 2,
  Right: 3,
} as const;
```

### Array

### Tuple

### Object

## Top type

- They represent the most general types that can hold any kind of value

### any

### unknown

- Tương tự như _any_, bất cứ type nào cũng có thể được gán cho unknown, nhưng unknown không thể gán cho bất cứ type nào ngoại trừ any

## Bottom type

### never

## Assertions

### as [type]

- Chỉ định kiểu cụ thể hơn cho một giá trị
- Có thể sử dụng angle-bracket syntax

```ts
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;

//angle-bracket
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```

- Có thể tận dụng để loại bỏ các ràng buộc type quá nghiêm ngặt trong TypeScript

```ts
const a = expr as any as T;
```

### as any

### as const

- Được sử dụng để tạo ra các literal types không thể thay đổi từ một giá trị hoặc đối tượng. Nó giúp "khóa" giá trị của một biến, khiến nó trở thành một kiểu không thay đổi (literal type), thay vì để TypeScript tự động suy luận type như string, number, v.v.
- Nó giúp đảm bảo tính bất biến và cung cấp kiểu dữ liệu chính xác hơn cho các giá trị.

```ts
let arr = [10, 20, 30]; // Kiểu: number[]
let arrConst = [10, 20, 30] as const; // Kiểu: [10, 20, 30]

let obj = { a: 10, b: 20 }; // Kiểu: { a: number, b: number }
let objConst = { a: 10, b: 20 } as const; // Kiểu: { a: 10, b: 20 }
```

### Non-null assertion operator (!)

- Khẳng định value của type sẽ không bao gồm null | undefined

### Satisfies

- Giúp đảm bảo rằng một giá trị tuân thủ theo một kiểu xác định mà không thay đổi kiểu dữ liệu của giá trị đó

```ts
//someValue sẽ phải thỏa mãn (satisfy) kiểu AnotherType. Tuy nhiên, satisfies không thay đổi kiểu của someValue và chỉ kiểm tra tính tương thích.
const value: Type = someValue satisfies AnotherType;
```

- Mục đích
  - Kiểm tra tính tương thích type
  - Giảm bớt việc ép kiểu (type assertion)
  - Giúp mã dễ bảo trì

# Type Inference

# Type Compatibility

- Type Compatibility dựa trên structural typing
- Structural typing là cách liên hệ các type dựa trên members của chúng

```ts
interface Pet {
  name: string;
}
class Dog {
  name: string;
}
let pet: Pet;
// OK, because of structural typing
pet = new Dog();
```

- x được xem là compatible với y nếu y ít nhất là có cùng members như x.

```ts
interface Pet {
  name: string;
}
let dog = { name: "Lassie", owner: "Rudd Weatherwax" };
function greet(pet: Pet) {
  console.log("Hello, " + pet.name);
}
greet(dog); // OK
```

## Comparing two function

- So sánh hai func chỉ khác nhau ở tham số:
  - Kiểm tra mỗi param trong x (type) có tồn tại trong y không. Nếu có tham số nào trong x không tồn tại trong y, phép gán sẽ báo lỗi.
  ```ts
  let x = (a: number) => 0;
  let y = (b: number, s: string) => 0;
  y = x; // OK
  x = y; // Error
  ```
- So sánh return type: Return type của source phải là return type con của destination function
  ```ts
  let x = () => ({ name: "Alice" });
  let y = () => ({ name: "Alice", location: "Seattle" });
  x = y; // OK
  y = x; // Error, because x() lacks a location property
  ```

## Function Parameter Bivariance

- Function Parameter Bivariance cho phép các func param có tính bivariance trong một số trường hợp nhất định, mặc dù TypeScript sử dụng kiểm tra kiểu dựa trên contravariance (Một kiểu cha có thể thay thế cho kiểu con) cho func param.
- Function Parameter Bivariance cho phép gán theo cả covariant (Một kiểu con (subtype) có thể thay thế cho kiểu cha (supertype)) lẫn contravariant trong một số trường hợp.

```ts
enum EventType {
  Mouse,
  Keyboard,
}
interface Event {
  timestamp: number;
}
interface MyMouseEvent extends Event {
  x: number;
  y: number;
}
interface MyKeyEvent extends Event {
  keyCode: number;
}
function listenEvent(eventType: EventType, handler: (n: Event) => void) {
  /* ... */
}
// Unsound, but useful and common
listenEvent(EventType.Mouse, (e: MyMouseEvent) => console.log(e.x + "," + e.y));
// Undesirable alternatives in presence of soundness
listenEvent(EventType.Mouse, (e: Event) =>
  console.log((e as MyMouseEvent).x + "," + (e as MyMouseEvent).y)
);
listenEvent(EventType.Mouse, ((e: MyMouseEvent) =>
  console.log(e.x + "," + e.y)) as (e: Event) => void);
// Still disallowed (clear error). Type safety enforced for wholly incompatible types
listenEvent(EventType.Mouse, (e: number) => console.log(e));
```

- You can have TypeScript raise errors when this happens via the compiler flag **_strictFunctionTypes_**.

## Optional Parameters and Rest Parameters

```ts
function invokeLater(args: any[], callback: (...args: any[]) => void) {
  /* ... Invoke callback with 'args' ... */
}
// Unsound - invokeLater "might" provide any number of arguments
invokeLater([1, 2], (x, y) => console.log(x + ", " + y));
// Confusing (x and y are actually required) and undiscoverable
invokeLater([1, 2], (x?, y?) => console.log(x + ", " + y));
```

## Functions with overloads

- Mỗi overload trong target type nên được khớp với một signature trên source type. Điều này chắc chắn rằng source func có thể được call trong tất cả các case giống như target type

## Class

- Khi compare two object của class type, chỉ members của instance được compare mà không quan tâm đến static members và constructors
- Nếu một class có private hoặc protected, thì chỉ các class cùng một hệ kế thừa mới có thể gán cho nhau. 📌 Điều này giúp giữ vững tính đóng gói và an toàn kiểu dữ liệu trong TypeScript .

```ts
class Base {
  protected x = 10;
}

class Derived extends Base {}

class Other {
  protected x = 10;
}

let base: Base = new Derived(); // ✅ Hợp lệ: Derived kế thừa từ Base
let base2: Base = new Other(); // ❌ Lỗi: Other không kế thừa từ Base
```

## Advanced Topics

- Check document typescript

# Combining Types

## Union Types

```ts
function getFirstThree(x: number[] | string) {
  // slice có ở cả array and string
  return x.slice(0, 3);
}
```

- TypeScript sẽ chỉ cho phép 1 operation nếu nếu nó valid cho mỗi member trong Union

## Intersection type

- Tạo một type mới kết hợp multiple type

```ts
interface Identity {
  id: number;
  name: string;
}

interface Contact {
  email: string;
  phone: string;
}

type Employee = Identity & Contact;
```

## keyof type operator

✅ keyof giúp lấy danh sách key của một type dưới dạng union type.

✅ Hữu ích khi kết hợp với Generic, typeof, và Mapped Types.

```ts
//Generic
function getValue<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "Alice", age: 25 };
const name = getValue(person, "name"); // ✅ Alice
const age = getValue(person, "age"); // ✅ 25
// const invalid = getValue(person, "address"); // ❌ Lỗi: "address" không tồn tại

// typeof
const person = { name: "Alice", age: 25 };
type PersonKeys = keyof typeof person; // "name" | "age"

// Mapped Types
type Person = {
  name: string;
  age: number;
};

type OptionalPerson = {
  [K in keyof Person]?: Person[K];
};
// Tương đương với:
type OptionalPerson2 = {
  name?: string;
  age?: number;
};
```

✅ Giúp TypeScript kiểm tra kiểu dữ liệu an toàn hơn khi truy cập object

# TypeGuards / Narrowing

## instanceof narrowing

- Dùng để kiểm tra xem một object có phải là instance của một class cụ thể hay không.
- TypeScript không hỗ trợ instanceof với interface (và type alias) vì interface không tồn tại ở runtime
- Không dùng được với object từ JSON hoặc API (cần tạo instance mới)

```ts
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toUTCString());
  } else {
    console.log(x.toUpperCase());
  }
}
```

## typeof

## Truthiness narrowing

### Equality narrowing

- TypeScript also uses switch statements and equality checks like ===, !==, ==, and != to narrow types

### _in_ operator

- Kiểm tra xem một object có chứa một thuộc tính cụ thể hay không.
- Hoạt động với Class, Object, và Optional Properties: in kiểm tra cả thuộc tính của object lẫn các thuộc tính kế thừa từ prototype, trong khi Object.hasOwnProperty() chỉ kiểm tra thuộc tính trực tiếp.

```ts
class Person {
  name = "Alice";
}

const p = new Person();

console.log("name" in p); // ✅ true
console.log("toString" in p); // ✅ true (kế thừa từ Object.prototype)
console.log(p.hasOwnProperty("toString")); // ❌ false (không có trong instance)
```

### Type Predicates

- Giúp TypeScript thu hẹp kiểu dữ liệu một cách an toàn
- Không thay đổi giá trị, chỉ giúp TypeScript hiểu kiểu dữ liệu tốt hơn

```ts
// ***value is Type*** là type predicate, giúp TypeScript biết rằng nếu hàm trả về true, thì value chắc chắn là ***Type***.
function isType(value: unknown): value is Type {
  return condition;
}
```

# Function

## Typing Function

### Call Signatures

```ts
type DescribableFunction = {
  description: string;
  // Sử dụng ":" thay vì "=>"
  (someArg: number): boolean;
};
function doSomething(fn: DescribableFunction) {
  console.log(fn.description + " returned " + fn(6));
}

function myFunc(someArg: number) {
  return someArg > 3;
}
myFunc.description = "default description";

doSomething(myFunc);
```

### Construct Signatures

- Thêm new keyword trước **Call Signatures** để tạo thành **Construct Signatures**

```ts
type SomeConstructor = {
  new (s: string): SomeObject;
};
function fn(ctor: SomeConstructor) {
  return new ctor("hello");
}
```

### Generic Functions

```ts
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```

- Constraints: dùng để giới hạn loại của type mà type param có thể accept (sử dụng _extends_ Type )

```ts
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
```

- Specifying Type Arguments: Chúng ta có thể chỉ định type arguments cho Generic type khi TypeScript không thể infer chúng trong 1 số trường hợp

```ts
function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
  return arr1.concat(arr2);
}

//Type 'string' is not assignable to type 'number'.
const arr = combine([1, 2, 3], ["hello"]);

//Specifying Type Arguments
const arr = combine<string | number>([1, 2, 3], ["hello"]);
```

#### Guidelines for Writing Good Generic Functions

- Push Type Parameters Down: Khi có thể, sử dụng type parameter của chính nó thay vì constraining nó

```ts
//better
function firstElement1<Type>(arr: Type[]) {
  return arr[0];
}

// less better
function firstElement2<Type extends any[]>(arr: Type) {
  return arr[0];
}
```

#### Use Fewer Type Parameters

- Luôn sử dụng càng ít tham số càng tốt

```ts
//better
function filter1<Type>(arr: Type[], func: (arg: Type) => boolean): Type[] {
  return arr.filter(func);
}

function filter2<Type, Func extends (arg: Type) => boolean>(
  arr: Type[],
  func: Func
): Type[] {
  return arr.filter(func);
}
```

#### Type Parameters Should Appear Twice

- Kiểm tra xem có cần thiết sử dụng Generic không (số lần xuất hiện của tham số, tính chất và use case của func)

## Function Overloads

- Cho phép chỉ định cách func có thể được gọi
- Note: Luôn ưu tiên params với union type thay vì overloads khi có thể

```ts
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
```

# Generics

## Variance Annotations

# Mixins design pattern

# Decorator

- Decorator cung cấp cách để thêm cả annotations và meta-programming syntax cho khai báo class và member
- A Decorator là một loại khai báo đặc biệt có thể được đính kèm đến khai báo class, method, accessor, property, or parameter.
- Sử dụng form **_@expression_**, nơi expression là một func sẽ được call tại runtime với thông tin về decorated declaration.

## Decorator Factories

- Là một func returns expression sẽ được call bởi decorators tại runtime

```ts
function color(value: string) {
  // this is the decorator factory, it sets up
  // the returned decorator function
  return function (target) {
    // this is the decorator
    // do something with 'target' and 'value'...
  };
}
```

## Decorator Composition

- Khi 1 khai báo đính kèm multi decorator, các decorator sẽ lần lượt thực hiện như sau:
  1. Các tính toán trong decorator sẽ thực hiện tuần tự từ trên xuống.
  2. Kết quả của function return sẽ thực hiện từ dưới lên

```ts
function first() {
  console.log("first(): factory evaluated");
  return function (
    target: any,
    propertyKey: string,
    descriptor: PropertyDescriptor
  ) {
    console.log("first(): called");
  };
}

function second() {
  console.log("second(): factory evaluated");
  return function (
    target: any,
    propertyKey: string,
    descriptor: PropertyDescriptor
  ) {
    console.log("second(): called");
  };
}

class ExampleClass {
  @first()
  @second()
  method() {}
}

// first(): factory evaluated
// second(): factory evaluated
// second(): called
// first(): called
```

## Decorator Evaluation

- Thứ tự cách decorator được apply đến nhiều khai báo bên trong class:
  - Parameter Decorators, followed by Method, Accessor, or Property Decorators được apply cho mỗi instance member.
  - Parameter Decorators, followed by Method, Accessor, or Property Decorators được apply cho mỗi static member
  - Parameter Decorators được apply cho mỗi constructor.
  - Class Decorators được apply cho class

## Class Decorators

- Được apply đến contructor của class và có thể được sử dụng để observe, modify, hoặc replace một khai báo class.
- Không thể sử dụng class decorator trong tệp .d.ts vì đây chỉ là tệp chứa kiểu dữ liệu, không chứa logic thực thi
- Không thể sử dụng class decorator trên declare class vì lớp này không có định nghĩa thực tế trong TypeScript.
- Expression for class decorator sẽ được call như một func tại runtime, với constructor của class là đối số duy nhất
- Nếu class decorator trả về một giá trị, nó sẽ thay thế lớp ban đầu bằng giá trị được trả về, có thể dùng để extend hoặc replace class

### Method Decorators

- Nếu method decorator return một value, nó sẽ được sử dụng như Property Descriptor cho method

```ts
function enumerable(value: boolean) {
  return function (
    target: any, // Either the constructor function of the class for a static member, or the prototype of the class for an instance member
    propertyKey: string, //The name of the member
    descriptor: PropertyDescriptor //The Property Descriptor for the member
  ) {
    descriptor.enumerable = value;
  };
}

class Greeter {
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }

  @enumerable(false)
  greet() {
    return "Hello, " + this.greeting;
  }
}
```

### Accessor Decorators

### Property Decorators

```ts
// target: nếu property là static property: constructor func của class, nếu property là instance property: prototype của class
// propertyKey: tên của thuộc tính
function PropertyDecorator(target: any, propertyKey: string) {
  console.log(`PropertyDecorator applied to ${propertyKey}`);
  console.log('Target:', target);
}

class Example {
  @PropertyDecorator
  instanceProperty: string = 'Hello';

  @PropertyDecorator
  static staticProperty: string = 'World';
}

//result
PropertyDecorator applied to instanceProperty
Target: { constructor: ƒ Example() }

PropertyDecorator applied to staticProperty
Target: class Example { }
```

### Parameter Decorators

- Cho phép bạn quan sát hoặc ghi metadata cho tham số đó nhưng không thể thay đổi giá trị của tham số tại runtime.

```ts
function ParameterDecorator(
  target: Object, // Nếu method là static, target là constructor của class. Nếu method là instance method, target là prototype của class.
  methodName: string | symbol, // Tên method chứa tham số
  parameterIndex: number // Vị trí của tham số trong danh sách tham số của phương thức (bắt đầu từ 0).
) {
  console.log(
    `ParameterDecorator applied to parameter at index ${parameterIndex} of method ${String(
      methodName
    )}`
  );
}
```

- Không thể thay đổi giá trị của tham số tại runtime, chỉ có thể ghi metadata hoặc ghi log.
- Không có Property Descriptor nên không thể sửa đổi trực tiếp method hoặc hành vi của nó như method decorator.
- Cần kết hợp với method decorator nếu muốn thay đổi logic của phương thức

# Utility Types

## Partial

- Cho phép biến tất cả property của một type thành optional

```ts
interface User {
  name: string;
  age: number;
  email: string;
}

function createUser(user: Partial<User>): User {
  return {
    name: "John Doe",
    age: 30,
    email: "john.doe@example.com",
    ...user,
  };
}

const newUser = createUser({ name: "Jane Doe" });
```

## Pick

- Tạo một type bằng cách pick một tập các key từ type khác

```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};
```

## Readonly

- Tạo một type từ type khác mà tất cả các property trở thành readonly

```ts
interface Todo {
  title: string;
}

const todo: Readonly<Todo> = {
  title: "Delete inactive users",
};

// Cannot assign to 'title' because it is a read-only property.
todo.title = "Hello";
```

## Omit

- Tạo một type không chứa các key được pick từ 1 type khác

```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
  createdAt: number;
}

type TodoPreview = Omit<Todo, "description">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
  createdAt: 1615544252770,
};

type TodoInfo = Omit<Todo, "completed" | "createdAt">;

const todoInfo: TodoInfo = {
  title: "Pick up kids",
  description: "Kindergarten closes at 5pm",
};
```

## Exclude

- Tạo một type bằng cách excluding từ UnionType tất cả union members có thể gán cho ExcludedMember

```ts
type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
type T1 = Exclude<"a" | "b" | "c", "a" | "b">; // "c"
type T2 = Exclude<string | number | (() => void), Function>; // string | number
```

## Record

- Tạo một object type với property keys là Keys và property values là Type.

```ts
interface CatInfo {
  age: number;
  breed: string;
}

type CatName = "miffy" | "boris" | "mordred";

const cats: Record<CatName, CatInfo> = {
  miffy: { age: 10, breed: "Persian" },
  boris: { age: 5, breed: "Maine Coon" },
  mordred: { age: 16, breed: "British Shorthair" },
};
```

## Awaited

- Tạo ra type sau khi đệ quy để upwrap Promises

```ts
type A = Awaited<Promise<string>>;
// type A = string

type B = Awaited<Promise<Promise<number>>>;
// type B = number

type C = Awaited<boolean | Promise<number>>;
// type C = number | boolean
```

## Extract

- Tạo ra type bằng cách extracting từ Type tất cả union member được gán cho Union

```ts
type T0 = Extract<"a" | "b" | "c", "a" | "f">;
//    ^ = type T0 = "a"
```

## Parameters

- Tạo một tuple type từ type được sử dụng trong params của func type Type

```ts
type T0 = Parameters<() => string>;
// type T0 = []

type T1 = Parameters<(s: string) => void>;
// type T1 = [s: string]

type T2 = Parameters<<T>(arg: T) => T>;
// type T2 = [arg: unknown]

declare function f1(arg: { a: number; b: string }): void;
type T3 = Parameters<typeof f1>;
// type T3 = [arg: {
//     a: number;
//     b: string;
// }]

type T4 = Parameters<any>;
// type T4 = unknown[]

type T5 = Parameters<never>;
// type T5 = never

type T6 = Parameters<string>;
// ^ Type 'string' does not satisfy the constraint '(...args: any) => any'.

type T7 = Parameters<Function>;
// ^ Type 'Function' does not satisfy the constraint '(...args: any) => any'.
```

### Non Nullable

- Tạo một type bằng cách excluding null và undefined từ Type

```ts
type T0 = NonNullable<string | number | undefined>;
// type T0 = string | number

type T1 = NonNullable<string[] | null | undefined>;
// type T1 = string[]
```

### Return Type

- Tạo một type chứa return type của func type

```ts
type T0 = ReturnType<() => string>;
// type T0 = string

type T1 = ReturnType<(s: string) => void>;
// type T1 = void

type T2 = ReturnType<<T>() => T>;
// type T2 = unknown

type T3 = ReturnType<<T extends U, U extends number[]>() => T>;
// type T3 = number[]

declare function f1(): { a: number; b: string };
type T4 = ReturnType<typeof f1>;
// type T4 = {
//     a: number;
//     b: string;
// }

type T5 = ReturnType<any>;
// type T5 = any

type T6 = ReturnType<never>;
// type T6 = never

type T7 = ReturnType<string>;
// ^ Type 'string' does not satisfy the constraint '(...args: any) => any'.

type T8 = ReturnType<Function>;
// ^ Type 'Function' does not satisfy the constraint '(...args: any) => any'.
```

## Instance Type

- Tạo 1 type chứa instance type của một contructor function trong Type

```ts
class C {
  x = 0;
  y = 0;
}

type T0 = InstanceType<typeof C>;
// type T0 = C

type T1 = InstanceType<any>;
// type T1 = any

type T2 = InstanceType<never>;
// type T2 = never

type T3 = InstanceType<string>;
// ^ Type 'string' does not satisfy the constraint 'abstract new (...args: any) => any'.

type T4 = InstanceType<Function>;
// ^ Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'.
```

## Mapped Types

- Tạo một type dựa trên type đã tồn tại nơi mỗi property của type đã tồn tại được transformed bằng cách nào đó.

```ts
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

let obj = { x: 10, y: 20 };
let readonlyObj: Readonly<typeof obj> = obj;
```

- Mapping Modifiers: có thể add hoặc remove readonly và ? bằng prefix: + (default) và -
- Key Remapping thông qua as:

```ts
type Getters<Type> = {
  [Property in keyof Type as `get${Capitalize<
    string & Property
  >}`]: () => Type[Property];
};

interface Person {
  name: string;
  age: number;
  location: string;
}

type LazyPerson = Getters<Person>;
```

## Conditional Types

```ts
interface Animal {
  live(): void;
}
interface Dog extends Animal {
  woof(): void;
}

//type Example1 = number
type Example1 = Dog extends Animal ? number : string;

//type Example2 = string
type Example2 = RegExp extends Animal ? number : string;
```

## Literal Types

## Template Literal Types

```ts
type Name = `Mr. ${string}`;

let name: Name = `Mr. Smith`; // ok
let name: Name = `Mrs. Smith`; // error
```

## Recursive Types

- Định nghĩa 1 type tham chiếu đến chính nó
- Được sử dụng để định nghĩa các complex data structures: trees, linked lists

```ts
type LinkedList<T> = {
  value: T;
  next: LinkedList<T> | null;
};

let list: LinkedList<number> = {
  value: 1,
  next: { value: 2, next: { value: 3, next: null } },
};
```

# TypeScript Modules
