<script setup lang="ts">
import { ref, onMounted } from 'vue'
import type { Ref } from 'vue'

const userPrompt = ref('')
const chatHistory: Ref<ChatHistory[]> = ref([])

interface ChatHistory {
  role: string
  content: string
}

onMounted(() => generateResponse(""))

const submitRequest = async () => {
  await generateResponse(userPrompt.value)
}

const generateResponse = async (prompt: string) => {
  const url = 'http://localhost:5092/generate'
  const response = await fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      userPrompt: prompt,
    }),
  })

  const result = await response.json();
  chatHistory.value = result.chatHistory.filter(x => x.role !== "system");
  resetForm();
}

const resetForm = () => userPrompt.value = "";
</script>

<template>

  <body>
    <header>
      <h1>Welcome to Office Store!</h1>
    </header>
    <section class="conversation-log" v-if="chatHistory.length > 0">
      <h2>Conversation</h2>
      <ul>
        <li v-for="(item, index) in chatHistory" :key="index">
          <strong>{{ item.role }}:</strong> {{ item.content }}
        </li>
      </ul>
    </section>
    <form @submit.prevent="submitRequest">
      <p>
        <label for="chat">Message:</label>
        <textarea name="chat" id="chat" v-model="userPrompt"></textarea>
      </p>
      <p><button type="submit">Chat</button></p>
    </form>
  </body>
</template>

<style scoped>
body {
  /* Center the form on the page */
  text-align: center;
  font-family: sans-serif;
  background-color: #f9f9f9;
  margin: 0;
  padding: 20px;
}

h1 {
  color: #333;
}

.conversation-log {
  max-width: 600px;
  margin: 0 auto 24px auto;
  text-align: left;
  color: #555;
  font-size: 0.9em;
}

.conversation-log h2 {
  font-size: 0.85em;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: #888;
  margin-bottom: 8px;
  border-bottom: 1px solid #e0e0e0;
  padding-bottom: 4px;
}

.conversation-log ul {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.conversation-log li {
  line-height: 1.5;
}

form {
  display: inline-block;
  /* Form outline */
  padding: 1.5em;
  border: 1px solid #cccccc;
  border-radius: 1em;
  background-color: #fff;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
}

p+p {
  margin-top: 1em;
}

label {
  /* Uniform size & alignment */
  display: inline-block;
  min-width: 90px;
  text-align: right;
  margin-right: 10px;
  font-weight: 500;
  color: #444;
}

input,
textarea {
  /* To make sure that all text fields have the same font settings
     By default, text areas have a monospace font */
  font: 1em sans-serif;
  /* Uniform text field size */
  width: 320px;
  box-sizing: border-box;
  /* Match form field borders */
  border: 1px solid #ccc;
  border-radius: 6px;
  padding: 10px;
  transition: border-color 0.2s, box-shadow 0.2s;
}

input:focus,
textarea:focus {
  /* Set the outline width and style */
  outline: none;
  border-color: #007bff;
  box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.1);
}

textarea {
  /* Align multiline text fields with their labels */
  vertical-align: top;
  /* Provide space to type some text */
  height: 5em;
  resize: vertical;
}

button {
  /* This extra margin represent roughly the same space as the space
     between the labels and their text fields */
  margin-left: 0.5em;
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 1em;
  font-weight: 600;
  transition: background-color 0.2s;
}

button:hover {
  background-color: #0056b3;
}
</style>
