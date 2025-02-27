<template>
  <div class="recommendation-container">
    <div class="top-section">
      <div
        class="circle"
        :style="{
          background: `conic-gradient(#00A200 ${percentRecommended}%, #e0e0e0 ${percentRecommended}%)`
        }"
      >
        <div class="circle-inner">
          {{ Math.round(percentRecommended) }}
        </div>
      </div>

      <div class="text-content">
        <div class="recommend-text">{{ Math.round(percentRecommended) }}% would recommend</div>
        <div class="recommend-count">{{ numOfRecommendations }} recommendations</div>
      </div>
    </div>

    <div
      @click="goToWriteReview"
      class="write-review-button"
    >
      Write a review
    </div>
  </div>
</template>


<script setup>
import { useRouter } from "vue-router";
import { useAuthStore } from "../../stores/auth";

const authStore = useAuthStore();
const router = useRouter();

const props = defineProps({
  percentRecommended: {
    type: Number,
    required: true
  },
  numOfRecommendations: {
    type: Number,
    required: true
  },
  itemName: {
    type: String,
    required: true
  }
});

const goToWriteReview = () => {

  if (authStore.getIsLoggedIn()) {
      router.push({
        path: '/write-review',
        query: { itemName: props.itemName },
      });
    } else {
      router.push({ 
        path: '/sign-in', 
        query: { 
          successRoute: 'write-review',
          successRouteProp: JSON.stringify({ itemName: props.itemName })
        } 
      });
    }
};

</script>


<style scoped>
.recommendation-container {
  display: flex;
  width: 301px;
  flex-direction: column;
  align-items: flex-start;
}

.top-section {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}

.circle {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: conic-gradient(#00A200 0%);
  display: flex;
  justify-content: center;
  align-items: center;
  margin-right: 10px;
}

.circle-inner {
  width: 87%;
  height: 87%;
  border-radius: 50%;
  background-color: #F2F2F2;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 18px;
  font-weight: bold;
  color: #00A200;
}

.text-content {
  display: flex;
  flex-direction: column;
}

.recommend-text {
  font-size: 18px;
  font-weight: bold;
  font-family: 'Helvetica', sans-serif;
}

.recommend-count {
  font-size: 15px;
  color: #555;
  font-family: 'Helvetica', sans-serif;
}

.write-review-button {
  padding: 13px 12px;
  background-color: #007bff;
  color: white;
  text-decoration: none;
  border-radius: 30px;
  font-size: 16px;
  font-family: 'Helvetica', sans-serif;
  font-weight: bold;
  display: inline-block;
  text-align: center;
}

.write-review-button:hover {
  background-color: #0056b3;
  cursor: pointer;
}

@media (max-width: 1000px) {
  .write-review-button {
    margin: 0 auto;
    display: block;
  }
}

</style>
