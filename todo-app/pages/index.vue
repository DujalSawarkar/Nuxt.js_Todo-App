<template>
  <div class="container">
    <h1>My To-Do List</h1>
    <input
      type="text"
      v-model="newTodo"
      @keyup.enter="addTodo"
      placeholder="Add a new todo..."
      class="input-field"
    />
    <ul class="todo-list">
      <li v-for="(todo, index) in todos" :key="index">
        <div class="todo-item">
          <input
            type="checkbox"
            v-model="todo.completed"
            @change="updateTodo(index)"
            class="checkbox"
          />
          <span
            @click="toggleEditMode(index)"
            v-if="!todo.editMode"
            :class="{ completed: todo.completed }"
            class="todo-text"
          >
            {{ todo.text }}
          </span>
          <input
            type="text"
            v-model="todo.text"
            v-else
            @blur="saveTodo(index)"
            @keyup.enter="saveTodo(index)"
            class="edit-input"
          />
          <button class="delete-btn" @click.stop="deleteTodo(index)">
            Delete
          </button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from "vue";

const newTodo = ref("");
const router = useRouter();
const todos = ref([

]);
onMounted(() => {
  if (!localStorage.getItem("userData")) {
    router.push("/login");
  }
});
const addTodo = () => {
  if (newTodo.value.trim() !== "") {
    const newTodoItem = {
      title: newTodo.value,
      text: newTodo.value,
      completed: false,
      //   editMode: false,
    };
    todos.value.push({
      text: newTodo.value,
      completed: false,
      editMode: false,
    });
    console.log(newTodoItem);
    // fetch("/api/todos", {
    //   method: "POST",
    //   headers: {
    //     "Content-Type": "application/json",
    //   },
    //   body: JSON.stringify(newTodoItem),
    // })
    //   .then((response) => {
    //     if (!response.ok) {
    //       throw new Error("Failed to add todo");
    //     }
    //     return response.json();
    //   })
    //   .then((data) => {
    //     todos.value.push(data); // Assuming the backend returns the added todo
    //     newTodo.value = "";
    //   })
    //   .catch((error) => console.error("Error adding todo:", error));
    newTodo.value = "";
  }
};

const updateTodo = (index) => {
  console.log("Updated todo:", todos.value[index]);
  const updatedTodo = todos.value[index];

  //   fetch(`/api/todos/${index}`, {
  //     method: "PUT",
  //     headers: {
  //       "Content-Type": "application/json",
  //     },
  //     body: JSON.stringify(updatedTodo),
  //   })
  //     .then((response) => {
  //       if (!response.ok) {
  //         throw new Error("Failed to update todo");
  //       }
  //       console.log("Todo updated successfully");
  //     })
  //     .catch((error) => console.error("Error updating todo:", error));
};

const deleteTodo = (index) => {
  const deletedTodo = todos.value[index];
  todos.value.splice(index, 1);
  console.log(todos.value);
  //   fetch(`/api/todos/${index}`, {
  //     method: "DELETE",
  //     headers: {
  //       "Content-Type": "application/json",
  //     },
  //     body: JSON.stringify(deletedTodo),
  //   })
  //     .then((response) => {
  //       if (!response.ok) {
  //         throw new Error("Failed to delete todo");
  //       }
  //       todos.value.splice(index, 1);
  //       console.log("Todo deleted successfully");
  //     })
  //     .catch((error) => console.error("Error deleting todo:", error));
};

const toggleEditMode = (index) => {
  todos.value.forEach((todo, i) => {
    if (i !== index) {
      todo.editMode = false;
    }
  });
  todos.value[index].editMode = true;
};

const saveTodo = (index) => {
  todos.value[index].editMode = false;
};
</script>

<style scoped>
.container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}
h1 {
  text-align: center;
  margin-bottom: 20px;
  color: #333;
}

.input-field {
  width: 100%;
  padding: 8px;
  margin-bottom: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
}

.todo-list {
  list-style: none;
  padding: 0;
}

.todo-item {
  display: flex;
  align-items: center;
  margin-bottom: 8px;
}

.checkbox {
  margin-right: 10px;
}

.todo-text {
  padding: 8px;
  border-radius: 5px;
  background-color: #f0f0f0;
  cursor: pointer;
  flex: 1;
  margin-right: 1rem;
}

.todo-text.completed {
  text-decoration: line-through;
  color: #888;
}

.edit-input {
  flex: 1;
  padding: 8px;
  margin-right: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  font-size: 16px;
}

.delete-btn {
  background-color: #ff5050;
  border: none;
  color: white;
  padding: 8px 12px;
  border-radius: 5px;
  cursor: pointer;
}

.delete-btn:hover {
  background-color: #ff1a1a;
}
/* Rest of the CSS remains unchanged */
</style>
