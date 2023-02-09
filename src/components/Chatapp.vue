<template>
  <div>
    <form @submit.prevent="sendMessage">
      <input v-model="message" type="text">
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
      await axios.post('http://localhost:3000/message', { message: this.message });
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
