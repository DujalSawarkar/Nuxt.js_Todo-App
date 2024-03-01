<template>
  <div class="container">
    <div class="left">
      <h1>My To-Do List</h1>
      <input
        type="text"
        v-model="newTodo"
        @keyup.enter="addTodo"
        placeholder="Add a new todo..."
        class="input-field"
      />
      <ul class="todo-list">
        <li v-for="todo in todos" :key="todo.id">
          <div v-if="todo.todostatus == false" class="todo-item">
            <input
              type="checkbox"
              v-model="todo.completed"
              @change="updateTodo(todo.id)"
              class="checkbox"
            />
            <span
              @click="toggleEditMode(todo.id)"
              v-if="!todo.editMode"
              :class="{ completed: todo.completed }"
              class="todo-text"
            >
              {{ todo.content }}
            </span>
            <input
              type="text"
              v-model="todo.text"
              v-else
              @blur="saveTodo(todo.id)"
              @keyup.enter="saveTodo(todo.id)"
              class="edit-input"
            />
            <button class="delete-btn" @click.stop="deleteTodo(todo.id)">
              Delete
            </button>
          </div>
        </li>
      </ul>
    </div>
    <!-- Completed Tasks Section -->
    <div class="right">
      <h2>Completed Tasks</h2>
      <ul class="completed-tasks">
        <li v-for="todo in completedTodos" :key="'completed-' + todo.id">
          <span class="completed-text">{{ todo.content }}</span>
        </li>
      </ul>
    </div>
    <button v-if="user" class="log-btn" @click="logoutHandler">Log Out</button>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";

const newTodo = ref("");
const router = useRouter();
let todos = ref([]);
const completedTodos = ref([]);
let user;

onMounted(() => {
  user = localStorage.getItem("userData");

  if (!localStorage.getItem("userData")) {
    router.push("/login");
  }
  fetchTodosByUser();
});
const logoutHandler = () => {
  localStorage.removeItem("userData");

  router.push("/login");
};
const fetchTodosByUser = async () => {
  try {
    const response = await $fetch("http://localhost:5000/user/todo", {
      method: "GET",
      headers: { Authorization: `Bearer ${user}` },
    });
    response.forEach((item) => {
      if (item.todostatus == false) {
        todos.value.push(item);
      } else {
        completedTodos.value.push(item);
      }
    });
    todos.value = [...response];
    const uniqueIds = {};
    // Filter the completedTodos array to remove duplicates
    const uniqueCompletedTodos = completedTodos.value.filter((todo) => {
      // If the ID is not in the uniqueIds object, add it and keep the todo
      if (!uniqueIds[todo.id]) {
        uniqueIds[todo.id] = true;
        return true;
      }
      // If the ID is already in the uniqueIds object, filter it out (remove duplicate)
      return false;
    });
    completedTodos.value = [...uniqueCompletedTodos];
  } catch (error) {
    console.error("Error to get data:", error);
  }
};

const addTodo = async () => {
  if (newTodo.value.trim() !== "") {
    const newTodoItem = {
      title: newTodo.value,
      content: newTodo.value,
    };
    todos.value.push({
      text: newTodo.value,
      completed: false,
      editMode: false,
    });

    try {
      const response = await $fetch("http://localhost:5000/user", {
        method: "POST",
        headers: { Authorization: `Bearer ${user}` },
        body: {
          body: newTodoItem,
        },
      });
      fetchTodosByUser();
    } catch (error) {
      console.error("Error during login:", error);
    }
  }
};

const updateTodo = async (index) => {
  const newupdatedTodo = todos.value.find((obj) => obj.id === index);

  if (newupdatedTodo.completed) {
    completedTodos.value.push(newupdatedTodo);
    // todos.value.splice(index - 1, 1);
    fetchTodosByUser();
  }

  const response = await $fetch(`http://localhost:5000/user/${index}`, {
    method: "PUT",
    headers: { Authorization: `Bearer ${user}` },
    body: newupdatedTodo,
  })
    .then((response) => {
      // if (!response.ok) {
      //   throw new Error("Failed to update todo");
      // }
      console.log("Todo updated successfully", response);
      fetchTodosByUser();
    })
    .catch((error) => console.error("Error updating todo:", error));
};

const deleteTodo = async (index) => {
  const deletedTodo = todos.value.find((obj) => obj.id === index);
  // todos.value.splice(index, 1);
  try {
    const response = await $fetch(`http://localhost:5000/user/${index}`, {
      method: "DELETE",
      headers: { Authorization: `Bearer ${user}` },
      body: {
        body: deletedTodo,
      },
    });
    window.location.reload();
    // fetchTodosByUser();
  } catch (error) {
    console.error("Can't delete:", error);
  }
};

const toggleEditMode = (index) => {
  todos.value.forEach((todo, i) => {
    if (i !== index - 1) {
      todo.editMode = false;
    }
  });
  todos.value[index - 1].editMode = true;
};

const saveTodo = (index) => {
  todos.value[index - 1].editMode = false;
};
</script>

<style scoped>
/* Your existing CSS styles */

.left {
  width: 500px;
  margin: 50px 0px;
}
.right {
  padding: 30px;
  width: 500px;
  height: 400px;
  margin: 50px 0px;
  border: 1px solid black;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  border-radius: 10px;
}
.log-btn {
  top: 3%;
  position: absolute;
  right: 2%;
  font-size: 15px;
  padding: 1% 3%;
  border-radius: 10px;
  background: rgb(235, 60, 60);
  color: white;
  border: 1px solid black;
  font-weight: 600;
}
.log-btn:hover {
  cursor: pointer;
}
.right h2 {
  margin-left: 30%;
  font-size: 25px;
}
.completed-tasks {
  margin-top: 20px;
}

.completed-tasks h2 {
  font-size: 1.2em;
  color: #333;
  margin-bottom: 10px;
}

.completed-tasks ul {
  list-style: none;
  padding: 0;
}

.completed-tasks li {
  background-color: #f0f0f0;
  border-radius: 5px;
  margin-bottom: 8px;
  padding: 8px;
}

.completed-tasks li .completed-text {
  color: #666;
  text-decoration: line-through;
}

.container {
  max-width: 1000px;
  margin: 3% auto;
  padding: 20px;
  font-family: Arial, sans-serif;
  display: flex;
  gap: 6rem;
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
</style>
