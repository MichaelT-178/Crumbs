<template>
  <!-- Search bar Dark overlay -->
  <div v-if="showDropdown" class="search-overlay" @click="hideDropdown"></div>

  <nav class="navbar" @keydown="handleKeydown">
    <!-- Dark overlay -->
    <div v-if="isSidebarOpen" class="overlay" @click="toggleSidebar"></div>

    <div class="top-navbar">
      <img :src="logoTwo" alt="Logo" class="logo" />
    </div>

    <div class="bottom-navbar">
      <div class="center-section">
        <div class="search-bar">
          <input 
            ref="searchInput" 
            type="text" 
            v-model="searchQuery" 
            placeholder="Search..." 
            @input="filterResults" 
            @focus="showDropdown = true" 
            @keydown.enter="handleSearchEnter"
          />
        </div>
      </div>
    </div>

    <!-- Sidebar -->
    <transition name="slide">
      <div v-if="isSidebarOpen" class="sidebar">
        <div class="sidebar-header">
          <h3>Menu</h3>
          <span class="material-icons close-icon" @click="toggleSidebar">close</span>
        </div>
        <ul>
          <li class="tab" @click="closeSidebar">
            <router-link to="/">
              <span class="material-symbols-outlined">home</span><span class="tab-text">Home</span>
            </router-link>
            <span class="material-symbols-outlined" id="arrow">arrow_forward_ios</span>
          </li>
          <li class="tab" @click="closeSidebar">
            <router-link to="/Order">
              <span class="material-symbols-outlined">cookie</span><span class="tab-text">Order</span>
            </router-link>
            <span class="material-symbols-outlined" id="arrow">arrow_forward_ios</span>
          </li>
          <li class="tab" @click="closeSidebar">
            <router-link to="/About">
              <span class="material-symbols-outlined">person</span><span class="tab-text">About</span>
            </router-link>
            <span class="material-symbols-outlined" id="arrow">arrow_forward_ios</span>
          </li>
          <li class="tab" @click="closeSidebar">
            <router-link to="/ContactUs">
              <span class="material-symbols-outlined">mail</span><span class="tab-text">Contact Us</span>
            </router-link>
            <span class="material-symbols-outlined" id="arrow">arrow_forward_ios</span>
          </li>
        </ul>
        <p class="logo-tag">@CarsCrumbs</p>
      </div>
    </transition>

    <!-- Dropdown search results -->
    <ul v-if="showDropdown && filteredResults.length" class="dropdown" @mousedown.prevent>
      <li 
        v-for="(result, index) in filteredResults" 
        :key="index" 
        class="result" 
        @mousedown="handleItemClick(result)"
      >
        <span>{{ result.Emoji }}</span><span class="result-text">{{ result.DisplayName }}</span>
      </li>
    </ul>

    <span 
      class="material-icons menu-icon" 
      @click="toggleSidebar" 
      @mouseover="hoverMenuIcon" 
      @mouseleave="resetMenuIconColor">
      menu
    </span>
    <router-link to="/Cart">
      <span class="material-symbols-outlined cart-icon">shopping_cart</span>
      <span v-if="cart.getItemCount() > 0" class="cart-badge">{{ cart.getItemCount() }}</span>
    </router-link>
  </nav>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import logoTwo from "../assets/logos/purple-logo.png";
import { folderRealOrTest } from '../data.config.js';
import { useRouter } from 'vue-router';
import { useCartStore } from "../stores/cart.js";

const router = useRouter();
const cart = useCartStore();

const menu = ref([]);
const searchQuery = ref('');
const showDropdown = ref(false);
const filteredResults = ref([]);
const searchInput = ref(null);
const isSidebarOpen = ref(false);
const menuIconColor = ref("white");

const filterResults = () => {
  if (searchQuery.value) {
    const queryWords = searchQuery.value.toLowerCase().split(' ');
    filteredResults.value = menu.value.filter(item => 
      queryWords.some(word => 
        item.DisplayName.toLowerCase().includes(word) ||
        (item.Tags && item.Tags.some(tag => tag.toLowerCase().includes(word))) ||
        (item.Alternative && item.Alternative.toLowerCase().includes(word))
      )
    ).slice(0, 5);
  } else {
    filteredResults.value = [];
  }
};

const toggleSidebar = () => {
  hideDropdown();
  isSidebarOpen.value = !isSidebarOpen.value;
};

const closeSidebar = () => {
  isSidebarOpen.value = false;
};

const hideDropdown = () => {
  showDropdown.value = false;
  searchQuery.value = "";
  filteredResults.value = [];
};

const handleItemClick = (item) => {
  router.push(item.Route);
  hideDropdown();
};

const handleSearchEnter = () => {
  router.push({ name: 'SearchResults', query: { search_query: searchQuery.value.trim() } });
  hideDropdown();
};

const hoverMenuIcon = () => {
  menuIconColor.value = "#FFD700";
};

const resetMenuIconColor = () => {
  menuIconColor.value = "white";
};

const closeSearchBar = () => {
  searchInput.value.blur();
};

const handleKeydown = (event) => {
  if (event.key === 'Escape') {
    closeSidebar();
    closeSearchBar();
  }
};

const loadMenuData = async () => {
  try {
    const MenuData = await import(`../assets/${folderRealOrTest}/MenuItems.json`);
    menu.value = MenuData.default.MenuItems; // Updated to access the correct JSON structure
  } catch (error) {
    console.error("Error loading menu data:", error);
  }
};

onMounted(() => {
  loadMenuData();
  window.addEventListener('keydown', handleKeydown);
});

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown);
});
</script>


<style scoped>

html, body {
  margin: 0;
  padding: 0;
  width: 100%;
}

.navbar {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #620086;
  color: white;
  width: 100%;
  box-sizing: border-box;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1003; 
  height: 112px;
}

.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.7);
  z-index: 999;
}

.search-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: #F2F2F2;
  z-index: 1003;
}

.top-navbar, .bottom-navbar {
  width: 100%;
  padding: 4px 40px;
  position: relative;
}

.logo {
  height: auto;
  width: 100px;
  margin: 0 auto;
  display: block;
  /* border: 1px solid #000; */
  border-radius: 5px;
  margin-top: 3px;
}

.cart-icon, .menu-icon {
  cursor: pointer;
  font-size: 36px;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}

.menu-icon {
  left: 25px;
  color: var(--menu-icon-color, white);
  transition: color 0.3s ease;
}

.menu-icon:hover {
 color: #FFD700;
}

.cart-icon {
  right: 25px;
  color: white;
  transition: color 0.3s ease;
}

.cart-icon:hover {
  color: #FFD700;
}

.cart-badge {
  position: absolute;
  top: 27.15px;  /* Increase number to move it down */
  right: 16px;  /* Increase number to move it to the left */
  background-color: #E50000;
  color: white;
  border-radius: 50%;
  padding: 3px 6px;
  font-size: 13px;
  display: flex;
  justify-content: center;
  align-items: center;
  min-width: 20px;
  height: 20px;
  z-index: 1000;
}

.cart-badge.hidden {
  display: none;
}

.center-section {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-grow: 1;
  position: relative;
  height: 100%;
}

.search-bar {
  position: relative;
  display: flex;
  justify-content: center;
  width: 75%;
  padding-bottom: 6px;
}

.search-bar input {
  width: 100%;
  padding: 10px;
  border-radius: 5px;
  border: 1px solid black;
}

.dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100vw;
  background-color: white;
  overflow-y: auto;
  list-style: none;
  margin: 0;
  padding: 0;
  z-index: 1001;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.result {
  padding: 10px;
  cursor: pointer;
  background: #F2F2F2;
  color: black;
  font-size: 20px;
  border-bottom: 1px solid #3C3B3B;
}

.result:last-child {
  border-bottom: none;
}

.result:hover {
  background-color: lightskyblue;
}

.result span.result-text {
  margin-left: 12px;
}

.sidebar {
  position: fixed;
  top: 0;
  left: 0;
  width: 310px;
  height: 100%;
  background-color: #F2F2F2;
  color: white;
  padding: 0;
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.5);
  z-index: 1001;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.sidebar-header {
  background-color: #620086;
  padding: 15px 20px; /* First is vertical, second is horizontal */
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.sidebar-header h3 {
  font-size: 28px;
  margin: 0;
  color: white;
}

.close-icon {
  cursor: pointer;
  font-size: 36px;
  color: white;
}

.close-icon:hover {
  color: red;
}

.sidebar ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

.sidebar .tab {
  padding: 15px 20px;
  background-color: #F2F2F2;
  transition: background-color 0.3s ease;
  display: flex;
  align-items: center;
  border-bottom: 1px solid #2b2b2b; /* black */
}

.tab-text {
  position: relative;
  top: -4.25px;
  margin: 0;
  color: #242424; /* black */
}

.sidebar .tab span.material-symbols-outlined {
  font-size: 24px;
  margin-right: 12px;
  color: #242424; /* black */
}

#arrow {
  margin-right: -2px;
}

#arrow:hover {
  cursor: pointer;
}

.sidebar .tab:hover span.material-symbols-outlined {
  color: purple;
}

.sidebar .tab:hover span.tab-text {
  color: purple;
}

.sidebar .tab a {
  color: white;
  text-decoration: none;
  font-size: 20px;
  display: block;
  width: 100%;
}

/* Slide Transition */
.slide-enter-active, .slide-leave-active {
  transition: transform 0.3s ease-out;
}

.slide-enter-from {
  transform: translateX(-100%);
}

.slide-enter-to {
  transform: translateX(0);
}

.slide-leave-from {
  transform: translateX(0);
}

.slide-leave-to {
  transform: translateX(-100%);
}

.logo-tag {
  font-size: 22px;
  text-align: center;
  padding: 5px 0;
  color: #2b2b2b; /* Black */
  margin-top: auto;
  margin-bottom: 15px;
  width: 100%;
}

@media (max-width: 500px) {
  .result {
    font-size: 18px;
  }

  .result span.result-text {
    margin-left: 10px;
  }
}

@media (max-width: 400px) {
  .sidebar {
    width: 100%;
  }
}

</style>


