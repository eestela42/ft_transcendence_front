<template>
  <div class="container">
    <input v-model="inputString" type="text" placeholder="Enter a string" />
    <button @click="displayString">Display</button>
    <div class="messageStack">
      <p v-for="message in messageStack" :key="message">{{ message }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';

export default defineComponent({
  data() {
    return {
      inputString: '',
      messageStack: [] as string[],
    };
  },
  methods: {
    displayString(): void {
      if (this.inputString == '') {
        this.messageStack.push('Please enter a string');
      }
      if (this.inputString != '') {
        this.messageStack.push(this.inputString);
      }
      this.inputString = '';
    },
  },
});
</script>

<style>
.container {
   flex: 1;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-left: 0;
  margin-top: 10px;
}

.messageStack {
  display: flex;
  position: relative;
  flex-direction: column;
  height: 600px;
  width: 380px;
  left: 1%;
  overflow-y: auto;
}

.message {
  text-align: center;
}
</style>
