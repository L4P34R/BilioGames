<template>
    <div class="modal-wrapper" @click.self="$emit('closeRegister')">
        <div class="auth-container">
            <button class="close-btn" @click="$emit('closeRegister')">×</button>
            <div class="auth-left">
                <img src="@/assets/LogImage.webp" alt="Auth image" />
            </div>
            <div class="auth-right">
                <h1>Create an account</h1>
                <p>Already have an account? <a href="#" @click="toLogin()">Log in</a></p>
        
                <div class="name-fields">
                    <input type="text" placeholder="First name" v-model="firstName" />
                    <input type="text" placeholder="Last name" v-model="lastName" />
                </div>
                <input type="email" placeholder="Email" v-model="email" />
                <input type="text" placeholder="User name" v-model="userName" />
                <input type="password" placeholder="Enter your password" v-model="password" />
        
                <label class="checkbox-container">
                    <input type="checkbox" v-model="agreed" />
                <span>I agree to the <a href="#">Terms & Conditions</a></span>
                </label>
                <div v-if="alert" class="alert">{{ alert }}</div>
                <button class="create-btn" @click="handleRegister">Create account</button>
        
                <div class="or-divider">Or register with</div>
        
                <div class="social-buttons">
                    <button class="google-btn">Google</button>
                    <button class="apple-btn">Apple</button>
                </div>
            </div>
        </div>
    </div>
  </template>
  
  <script>
import axios from 'axios';


  export default {
    name: 'Register',
    methods: {
        toLogin() {
            this.$emit('toLogin');
        },
        toggleRegister() {
            this.registerPage = !this.registerPage;
        },
        handleRegister() {
            if (!this.firstName || !this.lastName || !this.email || !this.password || !this.userName) {
                this.alert = 'Please fill all fields';
                return;
            }
            if (!this.agreed) {
                this.alert = 'You must accept the terms';
                return;
            }

            const atIndex = this.email.indexOf('@');
            const dotIndex = this.email.lastIndexOf('.');
            if (atIndex === -1 || dotIndex === -1 || dotIndex < atIndex) {
                this.alert = 'Make sure you use a valid email';
                return;
            }
            
            const userData = {
                firstName: this.firstName,
                lastName: this.lastName,
                userName: this.userName,
                email: this.email,
                password: this.password,
            };
            axios.post(`${process.env.VUE_APP_BACKEND_URL}/UserRegister`, userData)
            .then(res => {
                this.alert= 'User registered successfully';
                this.lastName = '';
                this.firstName = '';
                this.userName = '';
                this.email = '';
                this.password = '';
                this.alert = '';
                this.agreed = false;
                this.toLogin()
            })
            .catch(err => {
                if (err.response && err.response.data && err.response.data.error) {
                this.alert = err.response.data.error;
                } else {
                this.alert = "An error happened during registration";
                }
            });;
        }
  },
  data() {
  return {
    firstName: '',
    lastName: '',
    userName: '',
    email: '',
    password: '',
    agreed: false,
    alert: ''
  };
}
}
  </script>
  
  <style scoped>
  </style>