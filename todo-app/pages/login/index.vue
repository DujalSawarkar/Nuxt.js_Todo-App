<template>
  <div class="Main-Div">
    <form @submit.prevent="handleSubmit" class="form">
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
      <button type="submit">Login</button>
      <div>
        not registered? <nuxt-link to="/signup">Create an account</nuxt-link>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref } from "vue";

// Define reactive variables using ref
const formData = ref({
  email: "",
  password: "",
});

// Handle form submission
const handleSubmit = async () => {
  try {
    // const response = await fetch("http://localhost:4000/api/login", {
    //   method: "POST",
    //   body: JSON.stringify(formData.value),
    // });
    // console.log(formData.value);
    // console.log(response.formData);
    localStorage.setItem("userData", JSON.stringify(formData.value));

    // Redirect or handle success message
    // console.log("login successful", responseData);
    // if (response.ok) {
    //   // Handle successful login
    //   console.log("login successful", response);
    // } else {
    //   // Handle login failure
    //   console.error("login failed");
    // }
  } catch (error) {
    console.error("Error during login:", error);
  }
};
</script>

<style lang="scss" scoped>
.Main-Div {
  display: flex;
  justify-content: center;
  // flex-direction: column;
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
