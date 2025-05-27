<div align="center">

# Reminist

![license-info](https://img.shields.io/github/license/Ashu11-A/Reminist?style=for-the-badge&colorA=302D41&colorB=f9e2af&logoColor=f9e2af)
![stars-info](https://img.shields.io/github/stars/Ashu11-A/Reminist?colorA=302D41&colorB=f9e2af&style=for-the-badge)

![last-commit](https://img.shields.io/github/last-commit/Ashu11-A/Reminist?style=for-the-badge&colorA=302D41&colorB=b4befe)
![commit-activity](https://img.shields.io/github/commit-activity/y/Ashu11-A/Reminist?style=for-the-badge&colorA=302D41&colorB=f9e2af)
![code-size](https://img.shields.io/github/languages/code-size/Ashu11-A/Reminist?style=for-the-badge&colorA=302D41&colorB=90dceb)

![top-language](https://img.shields.io/github/languages/top/Ashu11-A/Reminist?style=for-the-badge&colorA=302D41&colorB=90dceb)
![bundle-size](https://img.shields.io/bundlejs/size/Reminist?style=for-the-badge&colorA=302D41&colorB=3ac97b)

</div>

## 💡 About

> Blazing fast, zero-dependency, TypeScript-native router for any environment.

Reminist is a high-performance routing library built with TypeScript. It uses an optimized **Radix Tree** structure to deliver `O(1)` (constant time) lookups for static routes and extremely fast resolution for dynamic and wildcard routes. It's designed for high-throughput environments where every microsecond counts.

-----

## 🚀 Key Features

  * **Exceptional Performance**: Radix tree implementation with `O(1)` lookups for static nodes and optimized path resolution.
  * **Type-Safe by Design**: Written entirely in TypeScript for a great developer experience.
  * **Zero Dependencies**: Lightweight and easy to integrate into any project.
  * **Flexible Route Patterns**: Full support for static, dynamic, wildcard, and catch-all routes.
  * **Environment Agnostic**: Works seamlessly in Node.js, Bun, Deno, and modern browsers.
  * **Path Caching**: Automatically caches processed URL paths to avoid redundant string manipulation.

-----

## 🏁 Benchmarks

The following benchmarks compare Reminist against Memoirist, another high-performance router, to showcase its performance.

> Lower latency is better. Higher throughput is better.

### Addition Test (Setup)

This test measures the performance of adding a large set of routes.

| Task Name | Latency avg (ns) | Throughput avg (ops/s) |
| :--- | :--- | :--- |
| **Reminist: Add All Routes** | **4904.6** | **212,549** |
| Memoirist: Add All Routes | 7108.7 | 151,965 |

### Search Test (Runtime)

This test measures the lookup performance for various route types.

| Task Name | Latency avg (ns) | Throughput avg (ops/s) |
| :--- | :--- | :--- |
| **Reminist: Find Static Routes** | **195.36** | **5,363,768** |
| Memoirist: Find Static Routes | 212.94 | 4,951,915 |
| **Reminist: Find Dynamic/Wildcard Routes** | **296.40** | **3,646,520** |
| Memoirist: Find Dynamic/Wildcard Routes | 477.33 | 2,154,391 |
| **Reminist: Find Non-Existent Route** | **54.60** | **19,483,306** |
| Memoirist: Find Non-Existent Route | 55.95 | 18,049,404 |

#### Analysis

  * **Setup Performance**: Reminist is approximately **45% faster** at adding routes, resulting in over **40% higher throughput** during the setup phase.
  * **Dynamic & Wildcard Routes**: For dynamic and wildcard routes (e.g., `/users/:id` or `/assets/*`), Reminist is over **60% faster**, a significant advantage for modern API-driven applications.
  * **Static Routes**: Reminist maintains a consistent edge in lookups for static routes, delivering higher throughput.
  * **Non-Existent Routes**: Both routers are exceptionally fast at handling non-existent paths, with Reminist showing a slight advantage in both latency and throughput.

Overall, the benchmarks demonstrate Reminist's superior performance across the board, especially in the most critical and frequent operations of adding and finding routes.

-----

## 📦 Installation

```bash
# Using npm
npm install reminist

# Using bun
bun add reminist

# Using yarn
yarn add reminist

# Using pnpm
pnpm add reminist
```

-----

## 🏁 Getting Started

Here's a quick example to get you up and running:

```typescript
import { Reminist } from 'reminist'

/**
 * Defines the structure for the data stored with each route.
 */
interface MyRouteData {
  id: string
  handler: (req: any, res: any) => void
}

/**
 * A typed router instance created using Reminist.
 * For comparison, here is how you would create a router that is not typed:
 * const router = new Reminist({ keys: ['GET', 'POST'] })
 */
const router = Reminist.create({ keys: ['GET', 'POST'] }).withData<MyRouteData>()

/**
 * Adding routes
 */
router.add('GET', '/', { id: 'home', handler: (_, res) => res.send('Welcome!') })
router.add('GET', '/users/:id', { id: 'getUser', handler: (req, res) => res.send(`User: ${req.params.id}`) })
router.add('POST', '/users', { id: 'createUser', handler: (_, res) => res.send('User created') })
router.add('GET', '/assets/*', { id: 'static', handler: (_, res) => res.send('Serving asset') })

/**
 * Attempts to find a route that matches the 'GET' method and '/users/10' path.
 */
const result = router.find('GET', '/users/10')

if (result.node) {
  console.log('Route found!')
  console.log('ID:', result.node.store?.id)
  console.log('Params:', result.params)
}
```

-----

## 📖 API Reference

### `new Reminist<Data, Keys>(options)`

Creates a new router instance.

  \* **`Data`**: A generic type for the data you want to store in each route's endpoint.
  \* **`Keys`**: A `const` array of strings representing the top-level keys (e.g., HTTP methods).
  \* **`options`**: An object containing:
      \* **`keys`**: `Keys`. The array of top-level keys.

### `.add(key, path, store)`

Adds a route to the tree.

  \* `key`: `Keys[number]`. The top-level key (e.g., `'GET'`).
  \* `path`: `string`. The URL path for the route.
  \* `store`: `Data`. The data to store at this endpoint.

### `.find(key, path)`

Finds a node in the tree that matches the given path. This is the primary method for routing.

  \* `key`: `Keys[number]`. The top-level key to search within.
  \* `path`: `string`. The URL path to look up.
  \* **Returns**: An object `{ node: Node | null; params: Record<string, string> }`.
      \* `node`: The matching `Node` object if found, otherwise `null`. Check `node.endpoint` to see if it's a usable route.
      \* `params`: An object containing any dynamic parameters extracted from the path.

### `.has(key, path)`

Checks if a route exists and is a valid endpoint.

  \* `key`: `Keys[number]`.
  \* `path`: `string`.
  \* **Returns**: `boolean`.

### `.delete(key, path)`

Removes a route from the tree. This method will also perform "pruning" by removing orphan nodes up the tree.

  \* `key`: `Keys[number]`.
  \* `path`: `string`. The *exact* literal path to remove (e.g., `'/users/:id'`).
  \* **Returns**: `boolean`. `true` if the route was successfully deleted, `false` if it was not found.

-----

## 🧠 How It Works: The Radix Tree

At its core, Reminist uses a **Radix Tree** (a highly efficient type of Trie) to store and retrieve routes.

#### The Problem

A simple array of routes would require iterating through every single route for each incoming request (`O(N)`), which is extremely slow. A basic hash map (`Map`) could find static routes quickly (`O(1)`), but it can't handle dynamic parameters like `/users/:id`.

#### The Solution: A Tree of Prefixes

A Radix Tree solves this by breaking paths down into segments and storing them in a tree structure. Each node in the tree represents a part of a URL.

For example, the routes `/users/profile` and `/users/settings` would be stored like this:

```
(GET)
  └── "users"
      ├── "profile"  (endpoint)
      └── "settings" (endpoint)
```

When you search for `/users/profile`, the router doesn't compare against all routes. It traverses the tree:

1.  Finds the "users" child.
2.  From there, finds the "profile" child.
3.  The search depth is proportional to the number of segments in the URL, **not** the total number of routes in the system.

#### The Reminist Optimization ✨

Reminist takes this a step further. Instead of storing a node's children in an array and iterating to find a match (which is `O(N)`), we do the following:

  \* **Static children** are stored in a `Map`. This makes finding the next segment an **`O(1)`** operation.
  \* **Dynamic, wildcard, and catch-all children** are stored in dedicated properties on the parent node. Since a node can only have one dynamic child, this lookup is also an **`O(1)`** property access.

This structure eliminates loops in the critical path of the `find` method, making it one of the fastest possible router implementations.

-----

## 🛣️ Route Syntax

Reminist supports several common routing patterns.

| Type | Syntax | Example | Description |
| :--- | :--- | :--- | :--- |
| **Static** | `/path/to/page` | `/about/contact` | Matches the exact path. The fastest type of route. |
| **Dynamic** | `/:param` | `/users/:id` | Matches any segment and captures its value in `params`. |
| **Wildcard** | `*` | `/assets/*` | A standalone `*` consumes the rest of the URL. The captured value is available in `params['*']`. |
| **Catch-All** | `/[...param]` | `/files/[...filePath]` | Captures all remaining segments as a single value in `params`. |
| **Optional Catch-All** | `/[[...param]]` | `/docs/[[...slug]]` | Behaves like a catch-all but also matches the route if no further segments are provided. |

-----

## 🤝 Contributing

Contributions are welcome\! If you find a bug or have a feature request, please open an issue. If you want to contribute code, please open a pull request.

-----

## 📜 License

This project is licensed under the **MIT License**. See the [LICENSE](https://github.com/Ashu11-A/Reminist/blob/main/LICENSE.md) file for details.
