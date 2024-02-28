<template>
    <div>
      <textarea v-model="inputText"></textarea>
      <button @click="handleSubmit">Submit</button>
    </div>
</template>
  
  
<script>
export default {
  data() {
    return {
      inputText: '',
      linesArray: []
    };
  },
  methods: {
    handleSubmit() {
      this.linesArray = this.inputText.split('\n');
      console.log(this.linesArray);
    }
  }
}
</script>
