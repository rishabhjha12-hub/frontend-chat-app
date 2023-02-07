<style>
  h1 {
    text-align: center;
  }

  .message {
    padding: 12px 20px;
    margin: 8px 0;
    width:70vw;
    box-sizing: border-box;
    border: 2px solid #ccc;
    border-radius: 4px;
    background-color: #f2f2f2;
  }
  .Input{
     width: 50%;
    padding: 12px 20px;
    margin: 8px 0;
    box-sizing: border-box;
    border: 2px solid #ccc;
    border-radius: 4px;
  }
  .InputMessage{
    width:100%;
    height:100%;
    padding: 12px 20px;
    margin: 8px 0;
    box-sizing: border-box;
    border: 2px solid #ccc;
    border-radius: 4px;
  }
</style>
<template>
  <div>
    <h1>Chat App</h1>
    <div><input v-model="username" placeholder="Enter username" class="Input"/></div>
    <div v-for="message in messages">
      <div class="message">
        {{ !message.username?"anonyms":message.username }}: {{ message.text }}
      </div>
    </div>
    <div>
      <input v-model="text" placeholder="Enter message" class="InputMessage" @keyup.enter="sendMessage" />
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      messages: [],
      username: '',
      text: ''
    };
  },
  mounted() {
    this.getMessages();
    setInterval(this.getMessages, 1000);
  },
  methods: {
    async getMessages() {
      try {
        const response = await axios.get('https://easy-pinafore-goat.cyclic.app/api/messages');
        this.messages = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    async sendMessage() {
      try {
        await axios.post('https://easy-pinafore-goat.cyclic.app/api/messages', {
          username: this.username,
          text: this.text
        });
        this.text = '';
      } catch (error) {
        console.error(error);
      }
    }
  }
};
</script>
