<template>
  <div class="Main-Div">
    <form @submit.prevent="handleSubmit" class="form">
      <div>
        <label for="name">Name:</label>
        <input type="text" id="name" v-model="formData.name" required />
      </div>
      <div>
        <label for="email">Email:</label>
        <input type="email" id="email" v-model="formData.email" required />
      </div>
      <div>
        <label for="password">Password:</label>
        <input
          type="password"
          id="password"
          v-model="formData.password"
          required
        />
      </div>
      <div>
        <label for="role">Role:</label>
        <input type="text" id="role" v-model="formData.role" required />
      </div>
      <button type="submit">Signup</button>
      <div>Already registered? <nuxt-link to="/login">Login</nuxt-link></div>
    </form>
  </div>
</template>

<script setup>
import { ref } from "vue";
const router = useRouter();

// Define reactive variables using ref
const formData = ref({
  name: "",
  email: "",
  password: "",
  role: "", // New field added
});
const handleSubmit = async () => {
  try {
    const response = await $fetch("http://localhost:5000/auth/register", {
      method: "POST",
      body: {
        name: formData.value.name,
        email: formData.value.email,
        password: formData.value.password,
        role: formData.value.role, // New field added
      },
    });
    console.log(formData.value);
    console.log(response.body);

    router.push("/login");

    // if (response.ok) {
    //   // Handle successful login
    //   console.log("signup successful", response);
    // } else {
    //   // Handle login failure
    //   console.error("signup failed");
    // }
  } catch (error) {
    console.error("Error during signup:", error);
  }
};
</script>

<style lang="scss" scoped>
.Main-Div {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.form {
  width: 300px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
}

.form div {
  margin-bottom: 15px;
}

label {
  font-weight: bold;
}

input[type="text"],
input[type="email"],
input[type="password"] {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 3px;
}

button[type="submit"] {
  width: 100%;
  padding: 10px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button[type="submit"]:hover {
  background-color: #0056b3;
}
</style>
