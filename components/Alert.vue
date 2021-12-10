<script setup lang="ts">
import { ref } from 'vue'

function generateMailBody(user) {
  return (
`Hi ${user.name},
We hope you're having a fantastic day.

That's all, thank you for reading, ${user.name} 😊.
`)
}

const onClick = () => {
  const body = generateMailBody({ name: 'everyone' });
  alert(body);
}
</script>

<template>
 <button @click="onClick" class="text-black bg-white shadow-md rounded-md px-4 py-2 font-bold">
   Show me
 </button>
</template>
