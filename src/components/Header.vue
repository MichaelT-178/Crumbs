<template>
  <div class="circle-wrapper">
		<img class="circle" :src="WordPressCircle" alt="Circle" />
		<hr class="circle-line" />
	</div>

	<p class="title">{{ titleText }}</p>
</template>


<script setup>
import WordPressCircle from "../assets/WordPressCircle.png";

const props = defineProps({
  titleText: {
    type: String,
    required: true
  }
});

</script>


<style scoped>

.circle-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
  /* max-width: 400px; */
	max-width: 30%;
  min-width: 230px;
  margin: 20px auto 20px auto;
}

.circle {
  width: 40px;
  height: auto;
  margin-bottom: 10px; /* Padding below the About text */
	top: -100px;
	z-index: 1;
}

.circle-line {
  position: absolute;
  top: 43%;
  left: 0;
  width: 100%;
  height: 1px;
	border-width: 0px;
  /* background-color: gray; */
  background-color: white;
  z-index: 0;
}

.title {
  color: #1a1c1e;
  font-family: Georgia, serif;
  font-size: 36px;
  text-align: center;
  font-weight: 700;
  margin-top: 0;
  padding-bottom: 20px; /* Padding below About */
  margin-top: -15px; /* Moves the About title up  */
}

</style>
