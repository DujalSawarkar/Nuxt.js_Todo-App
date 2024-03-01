<template>
  <div class="admin-page">
    <h1>Admin Page</h1>
    <button v-if="usertoken" class="log-btn" @click="logoutHandler">
      Log Out
    </button>
    <div class="user-list">
      <div v-for="user in users" :key="user.id" class="user-item">
        <div>Name : {{ user.name }}</div>
        <div>Email : {{ user.email }}</div>
        <div>{{ user.role }}</div>
        <button @click="deleteUser(user.id)" class="delete-button">
          Delete
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";

const users = ref([]);
const router = useRouter();
let usertoken;
onMounted(() => {
  usertoken = localStorage.getItem("userData");
  if (!usertoken) {
    router.push("/login");
  }
});

const logoutHandler = () => {
  localStorage.removeItem("userData");

  router.push("/login");
};
const fetchUsers = async () => {
  try {
    const response = await $fetch("http://localhost:5000/admin", {
      method: "GET",
      headers: { Authorization: `Bearer ${usertoken}` },
    });
    console.log(response);

    users.value.push(...response);
    // console.log(users.value[0]);
  } catch (error) {
    console.error("Error fetching users:", error);
  }
};

const deleteUser = async (userId) => {
  try {
    console.log(userId);
    const response = await $fetch(`http://localhost:5000/admin/${userId}`, {
      method: "DELETE",
      headers: { Authorization: `Bearer ${usertoken}` },
    });
    window.location.reload();
  } catch (error) {
    console.error("Error deleting user:", error);
  }
};

onMounted(() => {
  fetchUsers();
});
</script>

<style lang="scss" scoped>
.admin-page {
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.user-list {
  margin-top: 50px;
  margin-left: 15%;
  width: 1000px;
  display: flex;
  //   justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  gap: 20px;
  font-size: 20px;
}

.user-item {
  width: 350px;
  border: 1px solid #ccc;
  padding: 10px;
  border-radius: 20px;
}

.delete-button {
  background-color: #ff4136;
  color: #fff;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 5px;
}
.log-btn {
  position: absolute;
  right: 2%;
  top: 5%;
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
.delete-button:hover {
  background-color: #d50000;
}
</style>
