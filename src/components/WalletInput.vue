<template>
    <div>
      <textarea v-model="inputText" class="w-full h-48 border border-gray-300 rounded-lg p-2"></textarea>
      <button @click="emitSubmit" class="mt-4 bg-blue-500 hover:bg-blue-600 text-white font-semibold py-2 px-4 rounded">Submit</button>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        inputText: ''
      };
    },
    methods: {
      emitSubmit() {
        this.$emit('submit', this.inputText);
      }
    }
  };
  </script>
  