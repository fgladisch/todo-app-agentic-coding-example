# Todo List App — Agentic Coding Example

This repository is an **assignment** designed to showcase the capabilities of open-source coding models. The goal is to implement a full-stack todo list application from scratch, following the specification below.

## Architecture

This project is a **monorepo** containing two packages:

```
├── apps/
│   ├── api/          # NestJS backend service
│   └── web/          # React frontend (Vite + shadcn/ui)
├── package.json      # Root workspace config
└── README.md
```

Use **npm workspaces** (or pnpm/yarn workspaces) to manage the monorepo.

---

## Assignment

### Backend — `apps/api`

Build a REST API using **NestJS** with the following requirements:

#### Tech Stack

- **NestJS** as the application framework
- **TypeORM** as the ORM
- **SQLite** as the database (file-based, stored at `apps/api/data/todos.db`)

#### Data Model

A **Todo** item has the following fields:

| Field         | Type      | Description                                      |
| ------------- | --------- | ------------------------------------------------ |
| `id`          | `string`  | UUID, auto-generated                             |
| `title`       | `string`  | Required. Max 255 characters.                    |
| `description` | `string`  | Optional. Free-form text.                        |
| `completed`   | `boolean` | Default `false`. Marks the item as archived.     |
| `position`    | `number`  | Integer used for drag-and-drop ordering.         |
| `createdAt`   | `datetime`| Auto-generated timestamp.                        |
| `updatedAt`   | `datetime`| Auto-updated timestamp.                          |

#### API Endpoints

| Method   | Path                  | Description                                      |
| -------- | --------------------- | ------------------------------------------------ |
| `GET`    | `/todos`              | List all todos. Supports `?completed=true/false` query filter. |
| `POST`   | `/todos`              | Create a new todo.                               |
| `GET`    | `/todos/:id`          | Get a single todo by ID.                         |
| `PATCH`  | `/todos/:id`          | Update a todo (title, description, completed).   |
| `DELETE` | `/todos/:id`          | Delete a todo.                                   |
| `PATCH`  | `/todos/reorder`      | Accept an ordered list of `{ id, position }` pairs and update positions in bulk. |

#### Validation & Error Handling

- Validate all inputs using `class-validator` and NestJS pipes.
- Return `404` for unknown todo IDs.
- Return `400` for invalid payloads with descriptive error messages.
- Enable CORS for `http://localhost:5173` (the Vite dev server).

---

### Frontend — `apps/web`

Build a single-page application using **React** with the following requirements:

#### Tech Stack

- **Vite** as the build tool
- **React 18+** with TypeScript
- **shadcn/ui** for the component library (built on Radix UI + Tailwind CSS)
- **@dnd-kit/core** for drag-and-drop reordering
- **Popper.js** (via Radix/shadcn Popover) for tooltip and popover positioning

#### Features

1. **Todo List View**
   - Display all active (non-completed) todos, ordered by `position`.
   - Each item shows its **title** and a truncated **description**.
   - Clicking an item opens an inline editor or a popover (using Popper.js positioning) to edit the title and description.

2. **Add Todo**
   - A text input at the top of the list to quickly add a new todo by title.
   - New items are added to the bottom of the list.

3. **Drag & Drop Reordering**
   - Users can drag and drop todo items to reorder them.
   - On drop, call `PATCH /todos/reorder` to persist the new order.

4. **Complete / Archive**
   - Each item has a checkbox. Checking it marks the todo as `completed`.
   - Completed items are removed from the main list with a brief animation.

5. **Archived Section**
   - A collapsible "Archived" section at the bottom of the page.
   - Hidden by default. Toggling it reveals all completed todos.
   - Archived items can be **restored** (set `completed` back to `false`) or **permanently deleted**.

6. **Delete**
   - A delete button (trash icon) on each item, with a confirmation popover before deletion.

#### UI / UX Guidelines

- Use shadcn/ui components: `Button`, `Input`, `Card`, `Checkbox`, `Popover`, `Collapsible`, `Dialog` (for confirmations).
- Keep the layout centered and responsive (max-width ~640px).
- Use subtle transitions for completing, reordering, and deleting items.
- Show loading and empty states.

---

## Getting Started

```bash
# Install dependencies
npm install

# Start the API (runs on http://localhost:3000)
npm run dev --workspace=apps/api

# Start the web app (runs on http://localhost:5173)
npm run dev --workspace=apps/web
```

## Evaluation Criteria

This assignment is evaluated on:

- **Correctness** — Does the app work end-to-end? CRUD operations, reordering, archiving.
- **Code quality** — Clean project structure, proper separation of concerns, TypeScript throughout.
- **API design** — RESTful conventions, proper status codes, input validation.
- **UI polish** — Smooth drag-and-drop, animations, responsive layout, empty/loading states.
- **Error handling** — Graceful failures on both frontend and backend.
