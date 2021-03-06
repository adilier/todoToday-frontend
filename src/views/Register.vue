<template>
  <div class="login">
    <div class="login-container">
      <h1>To-do Today</h1>
      <form @submit.prevent="handleRegister">
        <div v-if="avatarUrl" class="avatar-container">
          <label for="avatar">
            <img :src="this.avatarUrl" alt="user avatar">
          </label>
        </div>
        <div v-else class="avatar-container">
          <label for="avatar">
            <img src="../assets/default.png" alt="user avatar">
          </label>
        </div>
        <input type="file" id="avatar" ref="avatar" v-on:change="uploadFile"/>
        <label for="name">Name:</label>
        <input type="name" id="name" v-model.trim="$v.name.$model">
        <div class="error" v-bind:class="[this.errorMessage]" v-if="!$v.name.required">Field is required</div>
        <div class="error" v-bind:class="[this.errorMessage]" v-if="!$v.name.minLength">Min {{$v.name.$params.minLength.min}} letters.</div>
        <label for="email">Email:</label>
        <input type="text" id="email" v-model="email">
        <div class="error" v-bind:class="[this.errorMessage]" v-if="!$v.email.email">Must be an email</div>
        <label for="password">Password:</label>
        <input type="password" id="password" v-model.trim="$v.password.$model" >
        <div class="error" v-bind:class="[this.errorMessage]" v-if="!$v.password.minLength">Min {{$v.password.$params.minLength.min}} letters.</div>
        <div class="form-button">
          <button type="submit">Register <ArrowRightIcon /></button>
        </div>
        <p>Already have an account? <router-link to="/">Login</router-link></p>
      </form>
    </div>  
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator';
import { ArrowRightIcon  } from 'vue-feather-icons';
import { minLength, required, email} from 'vuelidate/lib/validators';

@Component({
  components: {
    ArrowRightIcon 
  },
  validations: {
    name: {
      required,
      minLength: minLength(3)
    },
    email: {
      email
    },
    password: {
      required,
      minLength: minLength(6)
    }
  }
})

export default class Login extends Vue {

  avatar: any =  null;
  avatarUrl: any = null;
  name: string = '';
  email: string = '';
  password: string = '';
  errorMessage: string = 'invisible';
  $api: any;

  async uploadFile(event: any){
    this.avatar = event.target.files[0];
    this.avatarUrl = URL.createObjectURL(this.avatar);
    const reader = new FileReader();
  }
  
  async handleRegister(): Promise<void>{
    this.$v.$touch()  
    if(this.$v.$invalid){
      this.errorMessage = 'visible'
    } else {
      try {

        if(this.avatar){
          var data = new FormData();
          data.append('avatar', this.avatar);
          
          await this.$api.post('/users/avatar', data)
          .then((res: any) => {
            this.avatar = res.data.key
          });
        }
        
        await this.$api.post('/users', {
          name: this.name,
          email: this.email,
          password: this.password,
          avatar: this.avatar
        });

        this.$router.push('/');

      } catch (err) {
        const errorMessage = JSON.parse(err.request.responseText);
        const {status, message} = errorMessage;

        this.errorMessage = message;
      }
    }
  }
  

}
</script>

<style scoped>
  .login {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100vh;
  }

  .login .login-container {
    display: flex;
    justify-content: center;
    flex-direction: column;
    border: solid 2px #C4D0D9;
    padding: 100px;
  }

  .login .login-container h1 {
    color: #6456F4;
    margin-bottom: 30px;
    text-align: center;
  }

  .login .login-container form {
    display: flex;
    justify-content: center;
    align-items: flex-start;
    flex-direction: column;
  }

  .login .login-container form .avatar-container {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
  }

  .login .login-container form .avatar-container img {
    width: 200px;
    height: 200px;
    border-radius: 100%;
    object-fit: cover;
    cursor: pointer;
  }

  .login .login-container form .avatar-container img:hover {
    filter: brightness(80%);
  }

  .login .login-container form input[type = "file"] {
    width: 0.1px;
    height: 0.1px;
    opacity: 0;
    overflow: hidden;
    position: absolute;
    z-index: -1;
  }
  
  .login .login-container form input {
    border: solid 2px #C4D0D9;
    border-radius: 2px;
    padding: 7px 5px;
    font-size: 15px;
    width: 100%;
  }

  .login .login-container form label {
    margin-bottom: 2px;
    margin-top: 10px;
    color: #5c7181;
    font-weight: bold;
    font-size: 15px;
  }

  .login .login-container form .error {
    margin-top: 5px;
    color: #D3455A;
    font-weight: bold;
    font-size: 15px;
  }

  .login .login-container form .form-button{
    width: 100%;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    margin-top: 15px;
  }

  .login .login-container form .form-button button{
    width: 100%;
    padding: 5px 3px;
    border-radius: 2px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    background-color: #6456F4;
    cursor: pointer;
    font-weight: 500;
    font-size: 14px;
  }

  .login .login-container form .form-button button svg{
    margin-left: 5px;
  }

  .login .login-container form p, login .login-container form p a{
    margin-top: 5px;
    color: #5c7181;
    font-weight: bold;
    font-size: 15px;
    text-align: center;
    margin-top: 15px;
  }

  .login .login-container form p a{ 
    color: #6456F4;
  }
  
  .invisible {
    display: none;
  }

  .visible {
    display: block;
  }

  @media (max-width: 560px){
    .login .login-container {
      display: flex;
      justify-content: center;
      flex-direction: column;
      border: 0;
      padding: 0px;
    }
  }
</style>
