<template>
  <div>
    <h1>CHAT APP</h1>
    <form @submit.prevent="sendMessage">
      <input v-model="username" type="text" placeholder="Enter username">
      <input v-model="message" type="text" placeholder="Enter message">
      <button type="submit">Send</button>
    </form>
    <ul>
      <li v-for="message in messages">{{ message }}</li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      username: '',
      message: '',
      messages: []
    };
  },
  created() {
    this.messages = JSON.parse(localStorage.getItem('messages')) || [];
    this.listenForMessages();
  },
  methods: {
    async sendMessage() {
      await axios.post('http://localhost:3000/message', { username: this.username, message: this.message });
      this.username = '';
      this.message = '';
    },
    async listenForMessages() {
      const eventSource = new EventSource('http://localhost:3000/stream');
      eventSource.onmessage = (event) => {
        this.messages.push(event.data);
        localStorage.setItem('messages', JSON.stringify(this.messages));
      };
    }
  }
};
</script>
<style>
h1{
  width: 300px;
  height: 50px;
  margin:auto;
}
form {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
}

input {
  width: 200px;
  height: 30px;
  margin-bottom: 10px;
  padding: 5px;
  font-size: 16px;
}

button {
  width: 100px;
  height: 30px;
  background-color: lightblue;
  color: white;
  font-size: 16px;
  border: none;
  border-radius: 5px;
}

ul {
  list-style-type: none;
  width: 300px;
  margin: 0;
  padding: 0;
}

li {
  padding: 10px;
  border-bottom: 1px solid gray;
}
</style>
