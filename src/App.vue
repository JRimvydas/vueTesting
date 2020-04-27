<template>
  <div id="app">
    <div class="columns">
      <div class="column">
        <About />
      </div>
      <div class="column">
       <Form />
      </div>
    </div>
  </div>
</template>

<script>
import About from "@/components/About";
import Form from "@/components/Form";
export default {
  name: "App",
  components: {
     About, Form,
  },
};
</script>

<style>
.column{
  padding-bottom: 0;
}
</style>
