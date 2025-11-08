//1.1
function mergeArrays<T, U>(arr1: T[], arr2: U[]): (T | U)[] {
    return [...arr1, ...arr2];
}
const numArr = [1, 2, 3];
const strArr = ["a", "b", "c"];
console.log(mergeArrays(numArr, strArr));

//1.2
class Car {
  drive() {
    console.log("Araç sürülüyor...");
  }
}
class Truck {
  drive() {
    console.log("Kamyon sürülüyor...");
  }
  loadCargo() {
    console.log("Kargo yükleniyor...");
  }
}
type Vehicle = Car | Truck;
function useVehicle(vehicle: Vehicle) {
  vehicle.drive();  
  if ("loadCargo" in vehicle) {
    vehicle.loadCargo();
  }
}
const car = new Car();
const truck = new Truck();
useVehicle(car);
useVehicle(truck);

//1.3
class Logger {
  private logHistory: string[] = [];
  log(message: string) {
    console.log(message);
    this.logHistory.push(message);
  }
  getHistory() {
    return this.logHistory;
  }
}
const logger = new Logger();
logger.log("İlk log.");
logger.log("İkinci log.");
console.log(logger.getHistory());

//1.4
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
const user = { name: "Ali", age: 30 };
console.log(getProperty(user, "name"));
console.log(getProperty(user, "age"));

//1.5
type User = { id: number; name: string; email: string };

const users: User[] = [
  { id: 1, name: "Ali", email: "ali@mail.com" },
  { id: 2, name: "Ayşe", email: "ayse@mail.com" },
];
function search(id: number): User | undefined;
function search(name: string): User[];
function search(param: number | string): User | User[] | undefined {
  if (typeof param === "number") {
    return users.find(u => u.id === param);
  } else {
    return users.filter(u => u.name === param);
  }
}
console.log(search(1));
console.log(search("Ayşe"));

//1.6
class MemoryCache<K, V> {
  private data = new Map<K, V>();
  set(key: K, value: V): void {
    this.data.set(key, value);
  }
  get(key: K): V | undefined {
    return this.data.get(key);
  }
  clear(): void {
    this.data.clear();
  }
const cache = new MemoryCache<string, number>();
cache.set("user:1_score", 100);
console.log(cache.get("user:1_score"));
cache.clear();
console.log(cache.get("user:1_score"));

//1.7
function updateUser(
  id: number,
  updates: Partial<User>
): Readonly<User> | undefined {
  const user = users.find((u) => u.id === id);
  if (!user) {
    return undefined;
  }
  const updated = { ...user, ...updates };
  return updated as Readonly<User>;
}
console.log("Eski: ", users[0]);
const updatedUser = updateUser(1, { email: "ali.yeni@mail.com" });
console.log("Yeni: ", updatedUser);

//1.8
function sum(...numbers: number[]): number {
  return numbers.reduce((total, n) => total + n, 0);
}
console.log(sum(1, 2, 3));
console.log(sum(10, 20, 30, 40));

//1.9
abstract class Shape {
  abstract getArea(): number; // abstract metot
}
class Circle extends Shape {
  constructor(private radius: number) {
    super();
  }

  getArea(): number {
    return Math.PI * this.radius * this.radius;
  }
}
const circle = new Circle(10);
console.log(circle.getArea());

//1.10
class MathHelper {
  static PI = 3.14159;
  static calculateCircumference(radius: number): number {
    return 2 * MathHelper.PI * radius;
  }
}
console.log(MathHelper.PI);
console.log(MathHelper.calculateCircumference(10));

//2.11
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;
type pNum = Promise<number>;
type regularNum = number;
let val1: UnwrapPromise<pNum> = 10;
let val2: UnwrapPromise<regularNum> = 20;
let val3: UnwrapPromise<pNum> = "string";
console.log("Soru 11 Başarılı!", val1, val2);

//2.12
type GetReturnType<T> = T extends (...args: any[]) => infer R ? R : never;
const fn1 = () => "hello";
const fn2 = (a: number) => a * 2;
let ret1: GetReturnType<typeof fn1> = "world";
let ret2: GetReturnType<typeof fn2> = 100;
let error1: GetReturnType<typeof fn1> = 123;
console.log("Soru 12 Başarılı!", ret1, ret2);

//2.13
type CreateGetters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
};
type UserProps = { id: number; name: string; };
type UserGetters = CreateGetters<UserProps>;
const userGetters: UserGetters = {
  getId: () => 1,
  getName: () => "Ali"
};
const errorGetter: UserGetters = {
  getId: () => 1,
  getName: () => 123
};
console.log("Soru 13 Başarılı!", userGetters.getName());

//2.14
type DeepReadonly<T> =
  T extends Function ? T :
  T extends Array<infer U> ? ReadonlyArray<DeepReadonly<U>> :
  T extends object ? { readonly [K in keyof T]: DeepReadonly<T[K]> } :
  T;
type Config = { api: { url: string; }, features: string[] };
const config: DeepReadonly<Config> = {
  api: { url: "http://..." },
  features: ["A", "B"]
};
config.api.url = "yeni-url";
config.features.push("C");
console.log("Soru 14 Başarılı!", config.api.url);

//2.15
type PickByValueType<T, V> = {
  [K in keyof T as T[K] extends V ? K : never]: T[K]
};
type Sample = { name: string; age: number; active: boolean; };
type StringOrNumberProps = PickByValueType<Sample, string | number>;
const valid: StringOrNumberProps = {
  name: "Ali",
  age: 30
};
const invalid: StringOrNumberProps = {
  name: "Veli",
  age: 40,
  active: true
};
console.log("Soru 15 Başarılı!", valid.name);

//2.16
type Brand<K, T> = K & { __brand: T };
type UserID = Brand<string, "UserID">;
type PostID = Brand<string, "PostID">;
function logID(id: string) { console.log(id); }
let userID = "user-123" as UserID;
let postID = "post-456" as PostID;
logID(userID); 
logID(postID); 
userID = postID;
console.log("Soru 16 Başarılı!");

//2.17
type FilterUnion<T, U> = T extends U ? never : T;
type AllStatus = 'pending' | 'active' | 'inactive' | 'deleted';
type ActiveStatus = 'active' | 'pending';
type InactiveStatus = FilterUnion<AllStatus, ActiveStatus>;
let status1: InactiveStatus = 'inactive';
let status2: InactiveStatus = 'deleted';
let errorStatus: InactiveStatus = 'pending';
console.log("Soru 17 Başarılı!", status1, status2);

//2.18
type LastParameter<T> =
  T extends (...args: infer P) => any
    ? P extends [...infer _, infer L] ? L : never
    : never;
const fn1 = (a: string, b: number, c: boolean) => {};
const fn2 = (a: Date) => {};
let p1: LastParameter<typeof fn1> = true;
let p2: LastParameter<typeof fn2> = new Date();
let errorP: LastParameter<typeof fn1> = "string";
console.log("Soru 18 Başarılı!", p1);

//2.19
type Flatten<T> = T extends (infer U)[] ? U : T;
type NumArray = number[];
type Str = string;
let el1: Flatten<NumArray> = 123;
let el2: Flatten<Str> = "hello";
let errorEl: Flatten<NumArray> = "string";
console.log("Soru 19 Başarılı!", el1, el2);

//2.20
type ParseRouteParams<T extends string> =
  T extends `${string}:${infer Param}/${infer Rest}`
    ? { [K in Param | keyof ParseRouteParams<`/${Rest}`>]: string }
    : T extends `${string}:${infer Param}`
      ? { [K in Param]: string }
      : {};
type Route1 = "/users/:id";
type Route2 = "/posts/:postId/comments/:commentId";
type Params1 = ParseRouteParams<Route1>;
type Params2 = ParseRouteParams<Route2>;
const p1: Params1 = { id: "123" };
const p2: Params2 = { postId: "abc", commentId: "xyz" };
const errorP1: Params1 = { userId: "123" };
const errorP2: Params2 = { postId: "abc" };
console.log("Soru 20 Başarılı!", p1.id, p2.postId);
