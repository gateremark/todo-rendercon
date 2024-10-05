# todo-rendercon

1. Initial Template Application

```javascript

"use client";

import { useState } from "react";
import { FaTrash } from "react-icons/fa";

const TodoApp = () => {

const [todos, setTodos] = useState([]);

  return (
    <div className="p-6 bg-white rounded-lg shadow-md">
      <h1 className="mb-4 text-2xl font-bold">Todo App</h1>
      <div className="flex mb-4">
        <input
          type="text"
          placeholder="Add a new todo"
          className="flex-grow mr-2 w-full px-3 py-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
        <button
          className="px-4 py-2 text-white bg-blue-500 rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500"
        >
          Add
        </button>
      </div>
      <ul className="space-y-2">
        {todos.map((todo) => (
          <li
            key={todo.id}
            className="flex items-center justify-between p-2 bg-gray-100 rounded"
          >
            {todo.items.map((item, itemIndex) => (
              <span key={itemIndex}>{item.text}</span>
            ))}
            <button
              className="p-1 rounded-full hover:bg-gray-200 focus:outline-none"
            >
              <FaTrash className="text-red-500" />
            </button>
          </li>
        ))}
      </ul>
      <div className="mt-4 text-sm text-gray-600">
        {todos.length} item(s) in total
      </div>
    </div>
  );
};

export default TodoApp;

```

2. Connect to Cosmos DB

```javascript
import { CosmosClient } from "@azure/cosmos";

const client = new CosmosClient({
  endpoint: process.env.COSMOS_ENDPOINT!,
  key: process.env.COSMOS_KEY!,
});

const database = client.database(process.env.COSMOS_DATABASE_ID!);
const container = database.container(process.env.COSMOS_CONTAINER_ID!);

export { client, database, container };

```

3. addItem endpoint

```javascript
import { NextResponse } from "next/server";
import { container } from "../utils/utils";
import { v4 as uuidv4 } from "uuid";

export const POST = async (req: Request) => {
  const { items } = await req.json();
  const id = uuidv4();
  await container.items.create({
    id,
    items,
  });
  return NextResponse.json({
    success: true,
    message: "Item added successfully",
  });
};

```

3. Add Todo from UI

```javascript
const [newTodo, setNewTodo] = useState("");

  const addTodo = async () => {
    if (newTodo.trim() === "") return;
    try {
      await axios.post("/api/addItem", {
        items: [{ text: newTodo, completed: false }],
      });
      setNewTodo("");
      fetchTodos();
    } catch (error) {
      console.error("Error adding todo:", error);
    }
  };


<input
  type="text"
  value={newTodo}
  onChange={(e) => setNewTodo(e.target.value)}
  placeholder="Add a new todo"
  className="flex-grow mr-2 w-full px-3 py-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
/>


<button
  className="px-4 py-2 text-white bg-blue-500 rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500"
  onClick={addTodo}
  >
    Add
</button>
```
