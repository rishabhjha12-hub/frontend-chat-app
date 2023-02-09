<template>
  <div>
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
