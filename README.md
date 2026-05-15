# Linked-List-Management-System-Web-Application
Interactive Linked List Management System built with HTML, React.js, JavaScript, and Tailwind CSS. Features Singly &amp; Doubly Linked List operations including insertion, deletion, traversal, searching, recycle bin recovery, and real-time node visualization through a modern interactive UI for Data Structures learning.

# 🔗 Linked List Management System

> An interactive web application for visualizing and manipulating Singly and Doubly Linked Lists — built with React, Tailwind CSS, and vanilla JavaScript in a single-file architecture.

---

## 📖 Table of Contents

- [Overview](#overview)
- [Live Demo & Usage](#live-demo--usage)
- [Features](#features)
- [Project Structure](#project-structure)
- [Data Structures](#data-structures)
- [Operations Reference](#operations-reference)
- [Visualization Engine](#visualization-engine)
- [State & Persistence](#state--persistence)
- [Complexity Analysis](#complexity-analysis)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Educational Context](#educational-context)

---

## Overview

The **Linked List Management System** is a browser-based educational tool that simulates a simplified social-media style data management system using Linked Lists as the underlying data structure. Users can dynamically create, delete, search, traverse, and recover nodes — all visualized in real time.

The application is delivered as a single `index.html` file that requires no build step, no server, and no dependencies beyond a modern browser. It uses a Babel transpiler loaded from CDN to parse JSX at runtime.

---

## Live Demo & Usage

Open `index.html` directly in any modern browser (Chrome, Firefox, Edge, Safari).

```
File → Open → index.html
```

No installation, no build process, no internet connection required after first load (CDN assets are cached by the browser).

---

## Features

### Core Capabilities

| Feature | Singly Linked List | Doubly Linked List |
|---|---|---|
| Insert at Beginning | ✅ | ✅ |
| Insert at End | ✅ | ✅ |
| Insert at Position | ✅ | ✅ |
| Delete to Recycle Bin | ✅ | ✅ |
| Permanent Delete | ❌ | ✅ |
| Forward Traversal | ✅ | ✅ |
| Backward Traversal | ❌ | ✅ |
| Search by Tweet ID | ✅ | ✅ |
| Search by User Name | ✅ | ✅ |
| Node Count | ✅ | ✅ |
| Recycle Bin (Recover) | ✅ | ✅ |
| Session Persistence | ✅ | ✅ |

### UX Highlights

- **Live Visualizer** — nodes and pointer arrows update immediately after every operation
- **Terminal Output** — timestamped log panel shows exact operation results, traversal paths, and errors
- **Highlighted Search Results** — matched nodes are visually highlighted with a yellow border and scale animation
- **Recycle Bin View** — toggle the visualizer between the active list and the recycle bin
- **Auto-incremented IDs** — each new node receives a unique `TWEETxxxx` identifier automatically

---

## Project Structure

```
index.html
│
├── <head>
│   ├── Tailwind CSS (CDN)
│   ├── FontAwesome 6.4 (CDN)
│   ├── React 18 + ReactDOM (CDN)
│   └── Babel Standalone (CDN)
│
└── <script type="text/babel">
    │
    ├── Button          — Reusable button component (5 variants)
    ├── Input           — Labeled input component
    ├── NodeVisualizer  — Renders the linked list as visual node cards
    │
    └── App             — Root component
        ├── State management (nodes, recycleNodes, autoId, logs, …)
        ├── localStorage load/save effects
        ├── handleInsert()
        ├── handleDelete()
        ├── handleSearch()
        ├── handleTraverse()
        ├── handleRecycleRecover()
        ├── handleRecycleDelete()
        └── JSX render tree (Menu screen → Operations screen)
```

---

## Data Structures

### NODE — Singly Linked List Node

```js
{
  id:    "TWEET1001",   // Auto-generated unique identifier
  name:  "Alice",       // User name
  tweet: "Hello World!" // Tweet content
  // next pointer is implicit (array index + 1)
}
```

### DNODE — Doubly Linked List Node

```js
{
  id:    "TWEET1001",   // Auto-generated unique identifier
  name:  "Alice",       // User name
  tweet: "Hello World!" // Tweet content
  // prev pointer is implicit (array index - 1)
  // next pointer is implicit (array index + 1)
}
```

Both node types share the same object schema. The distinction between Singly and Doubly is expressed through the `appMode` state flag (`'singly'` | `'doubly'`), which controls which operations and traversal directions are enabled, and how the visualizer renders pointer arrows.

In-memory representation uses a JavaScript array — this gives O(n) traversal semantics consistent with a real linked list, while simplifying React state management.

---

## Operations Reference

### Insertion

| Sub-operation | Behaviour | Key Step |
|---|---|---|
| At Beginning | Prepends node to list | `newNodes.unshift(newNode)` |
| At End | Appends node to list | `newNodes.push(newNode)` |
| At Position `p` | Inserts before index `p-1` | `newNodes.splice(p-1, 0, newNode)` |

After every insertion `autoId` increments and the visualizer re-renders.

### Deletion

| Sub-operation | Destination | Key Step |
|---|---|---|
| Temp — Beginning | Recycle Bin | `newNodes.shift()` |
| Temp — End | Recycle Bin | `newNodes.pop()` |
| Temp — Position | Recycle Bin | `newNodes.splice(p-1, 1)` |
| Perm — Beginning *(Doubly only)* | Destroyed | Same as above, no Recycle push |
| Perm — End *(Doubly only)* | Destroyed | Same as above |
| Perm — Position *(Doubly only)* | Destroyed | Same as above |

### Search

Linear search traversal scans the node array from head to tail.

```
Search by ID:   node.id === query            (case-insensitive prefix "TWEET" normalised)
Search by Name: node.name.toLowerCase() === query.toLowerCase()
```

On match: the matching node's ID is stored in `highlightedId` state, causing the visualizer to apply a yellow highlight style to that card.

### Traversal

```
Forward:   HEAD → [node1] → [node2] → ... → NULL
Backward:  NULL ← [nodeN] ← [nodeN-1] ← ... ← NULL   (Doubly only)
```

Output is printed to the terminal log panel in a single timestamped line.

### Recycle Bin

| Action | Behaviour |
|---|---|
| Recover | Moves node from `recycleNodes` back to `nodes` (appended at tail) |
| Permanent Delete | Removes node from `recycleNodes` with no recovery path |

---

## Visualization Engine

The `NodeVisualizer` component renders the linked list as a horizontal scrollable strip of node cards connected by directional arrow icons from FontAwesome.

```
Singly:   HEAD ⟶  [TWEET1001]  →  [TWEET1002]  →  [TWEET1003]  ⟶ NULL
Doubly:   NULL ⟵  [TWEET1001] ↔ [TWEET1002] ↔ [TWEET1003]  ⟶ NULL
```

Each card displays:
- **Tweet ID** (top-left, blue)
- **User Name** (bold, truncated)
- **Tweet Content** (italic, 2-line clamp)

Highlighted node (search result) receives:
- `border-yellow-400` — yellow border
- `shadow-yellow-100 shadow-lg` — yellow glow shadow
- `scale-105` — slight zoom
- `transition-all duration-300` — smooth animation

The same component is reused for the Recycle Bin view by passing `recycleNodes` and `isRecycleBin={true}`.

---

## State & Persistence

All application state lives in the `App` component as React `useState` hooks:

| State Variable | Type | Purpose |
|---|---|---|
| `appMode` | `'menu' \| 'singly' \| 'doubly'` | Controls which screen is shown |
| `nodes` | `Node[]` | Active linked list |
| `recycleNodes` | `Node[]` | Soft-deleted nodes |
| `autoId` | `number` | Next Tweet ID counter (starts at 1001) |
| `activeMenu` | `string` | Which control panel menu is open |
| `activeSubMenu` | `string` | Sub-operation within a menu |
| `logs` | `string[]` | Terminal output lines |
| `highlightedId` | `string \| null` | ID of the search-highlighted node |
| `formData` | `object` | Controlled form inputs |

### localStorage Persistence

On every state change the full snapshot is serialised and saved:

```js
localStorage.setItem(`${appMode}_data`, JSON.stringify({ nodes, recycleNodes, autoId }));
```

On mode switch, the saved snapshot is loaded:

```js
const dataStr = localStorage.getItem(`${appMode}_data`);
if (dataStr) { /* restore nodes, recycleNodes, autoId */ }
```

This means the Singly and Doubly lists are stored independently and survive page refreshes.

---

## Complexity Analysis

| Operation | Time Complexity | Space Complexity | Notes |
|---|---|---|---|
| Insert at Beginning | O(1) | O(1) | `unshift` is O(n) on arrays — conceptually O(1) for a true linked list |
| Insert at End | O(1) | O(1) | `push` is amortized O(1) |
| Insert at Position p | O(n) | O(1) | Must traverse to position p |
| Delete at Beginning | O(1) | O(1) | `shift` |
| Delete at End | O(1) | O(1) | `pop` |
| Delete at Position p | O(n) | O(1) | Traverse + splice |
| Search by ID / Name | O(n) | O(1) | Linear scan |
| Forward Traversal | O(n) | O(n) | Output buffer grows with list size |
| Backward Traversal | O(n) | O(n) | Array reverse + output |
| Count Nodes | O(1) | O(1) | `nodes.length` |
| Recover from Recycle Bin | O(n) | O(1) | Linear scan of recycle array |

---

## Tech Stack

| Layer | Technology | Version | Purpose |
|---|---|---|---|
| UI Framework | React | 18 | Component model, state management |
| Styling | Tailwind CSS | 3 (CDN) | Utility-first styling |
| Icons | FontAwesome | 6.4 | Arrow and action icons |
| JSX Transpiler | Babel Standalone | Latest (CDN) | Parses JSX in-browser |
| Persistence | Browser localStorage | — | Cross-session data retention |
| Runtime | Browser (no server) | — | Zero-dependency deployment |

---

## Getting Started

### Prerequisites

- Any modern web browser (Chrome 90+, Firefox 88+, Edge 90+, Safari 14+)
- No Node.js, no npm, no build tools required

### Run Locally

```bash
# Option 1 — Open directly
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux

# Option 2 — Serve with any static server (avoids some CORS edge cases)
npx serve .
python3 -m http.server 8080
```

Then navigate to `http://localhost:8080` (if using a server).

### First Steps

1. Choose **Singly Linked List** or **Doubly Linked List** from the home screen
2. Use **Insertion Operation** to add your first node — enter a name and tweet content
3. Watch the node appear in the **Live Linked List Visualizer**
4. Use **Traversal Operation** to see the full list printed to the terminal
5. Try **Search Operation** → By Tweet ID or By User Name to highlight a node
6. Use **Delete Operation** → At Beginning to move a node to the Recycle Bin
7. Visit **Recycle Bin** to recover or permanently delete it

---

## Educational Context

This project is designed to help students understand:

**Why Linked Lists?**
Unlike arrays, linked lists allow O(1) insertion and deletion at the head without shifting elements. They are the foundation of stacks, queues, and many advanced data structures.

**Singly vs Doubly**
A Singly Linked List stores only a `next` pointer — traversal is one-directional. A Doubly Linked List adds a `prev` pointer, enabling bidirectional traversal and more flexible deletion but at the cost of extra memory per node.

**Pointer Visualization**
The arrow icons (→ and ↔) between node cards represent the `next` and `prev` pointers respectively. The `NULL` labels at each end represent the sentinel values that terminate the list.

**Recycle Bin Pattern**
The soft-delete pattern (move to Recycle Bin before permanent deletion) mirrors real-world data safety practices used in operating systems and databases — where destructive operations are made recoverable before being committed.

---
