<template>
  <div class="about">
    <h1>This is an about pageaa</h1>
  </div>
</template>
<script>
  import axios from 'axios';
  export default {
    setup() {

    },
    created() {
      axios
        .get('https://reqres.in/api/users?page=2')
        .then((response) => {
         console.log(response.data);

        })
        .catch(error => {
          console.log(error);
        });
    },
  }
</script>