# Typescript Types
TypeScript has several built-in types, including:

number
string
boolean
any
void
null and undefined
never
object
symbol
Enumerated types (enum)
Tuple types
Array types
Union types
Intersection types
Type aliases
Type assertions

Differences Between Type Aliases and Interfaces
Type aliases and interfaces are very similar, and in many cases you can choose between them freely. Almost all features of an interface are available in type, the key distinction is that a type cannot be re-opened to add new properties vs an interface which is always extendable.

# tsconfig
```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "strict": true,
    "outDir": "./dist",
    "rootDir": "./src",
  },
  "exclude": ["node_modules"],
  "include": ["src"]
}
// For example, if you were writing a project which uses Node.js version 12 and above, then you could use the npm module @tsconfig/node12:
{
  "extends": "@tsconfig/node12/tsconfig.json",
  "compilerOptions": {
    "preserveConstEnums": true
  },
  "include": ["src/**/*"],
  "exclude": ["**/*.spec.ts"]
}
```

Prior to TypeScript version 4.2, type alias names may appear in error messages, sometimes in place of the equivalent anonymous type (which may or may not be desirable). Interfaces will always be named in error messages.
Type aliases may not participate in declaration merging, but interfaces can.
Interfaces may only be used to declare the shapes of objects, not rename primitives.
Interface names will always appear in their original form in error messages, but only when they are used by name.
Using interfaces with extends can often be more performant for the compiler than type aliases with intersections
> https://www.typescriptlang.org/docs/handbook/2/everyday-types.html

Interface
Extending an interface
```ts
interface Animal {
  name: string;
}

interface Bear extends Animal {
  honey: boolean;
}
```
Adding new fields to an existing interface
```ts
interface Window {
  title: string;
}

interface Window {
  ts: TypeScriptAPI;
}
```

Type
Extending a type via intersections
```ts
type Animal = {
  name: string;
}

type Bear = Animal & { 
  honey: boolean;
}
```

```ts
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");

```

```ts
declare function handleRequest(url: string, method: "GET" | "POST"): void;
 
const req = { url: "https://example.com", method: "GET" };
handleRequest(req.url, req.method);
// Change 1:
const req = { url: "https://example.com", method: "GET" as "GET" };
// Change 2
handleRequest(req.url, req.method as "GET");
```