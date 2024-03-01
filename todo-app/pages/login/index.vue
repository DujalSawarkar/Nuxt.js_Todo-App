<template>
  <div class="container">
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
        <button type="submit" class="btn">Login</button>
        <div class="notrege">
          not registered? <nuxt-link to="/signup">Create an account</nuxt-link>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
const router = useRouter();

const formData = ref({
  email: "",
  password: "",
});

const handleSubmit = async () => {
  try {
    const response = await $fetch("http://localhost:5000/auth/login", {
      method: "POST",
      body: {
        email: formData.value.email,
        password: formData.value.password,
      },
    });

    // console.log(response);

    localStorage.setItem("userData", response.token);
    formData.value.email = "";
    formData.value.password = "";
    if (response.role == "admin") {
      router.push("/adminpage");
    } else {
      router.push("/");
    }
    // router.push("/");
  } catch (error) {
    console.error("Error during login:", error);
  }
};
</script>
<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: rgb(219, 219, 219);
}

.Main-Div {
  /* background-color: #5c5959; */
  background: white;
  height: 300px;
  width: 300px;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px 0 rgba(255, 0, 0, 0.2),
    0 6px 20px 0 rgba(255, 0, 0, 0.19);
  display: flex;
  justify-content: center;
  align-items: center;
}

.form {
  display: flex;
  flex-direction: column;
}

.form-group {
  margin-bottom: 15px;
}

label {
  font-weight: bold;
}

input {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 100%;
  height: 20px;
  margin: 10px 0px 20px 0px;
}

.btn {
  width: 100%;
  padding: 2%;
  background-color: #007bff;
  color: #fff;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-left: 10px;
  margin-bottom: 5px;
}

button.login-button:hover {
  background-color: #0056b3;
}

.signup-link {
  margin-top: 10px;
  font-size: 14px;
}

.signup-link a {
  color: #007bff;
  text-decoration: none;
}
.notrege {
  margin-left: 20px;
}
.signup-link a:hover {
  text-decoration: underline;
}
</style>
