<template>
  <div class="home">
    <div class="home-tip">Draw to create MUSIC!</div>
    <div class="home-btn" @click="handleRouterDraw()">
      Start!
      <span></span>
      <span></span>
      <span></span>
      <span></span>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { useRouter } from "vue-router";

const router = useRouter();

const handleRouterDraw = () => {
  router.push({
    name: "draw",
  });
};
</script>

<style scoped>
.home {
  width: 100vw;
  height: 85vh;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.home-tip {
  font-weight: 800;
  font-size: 110px;
  color: #ffffff;
  margin-bottom: 70px;
}

.home-tip::before {
  position: absolute;
  transform: translateX(0.5%) translateY(3%);
  content: "Draw to create MUSIC!";
  font-weight: 800;
  font-size: 110px;
  color: #ffffff;
  opacity: 0.3;
}

.home-btn,
.home-btn:focus,
.home-btn:hover {
  position: relative;
  min-width: 200px;
  border: 1px solid #ffffff;
  color: #ffffff;
  font-size: 1rem;
  font-weight: bold;
  text-align: center;
  text-decoration: none;
  text-transform: uppercase;
  -webkit-font-smoothing: antialiased;
  padding: 10px 20px;
  cursor: pointer;
}
.home-btn span:nth-child(1),
.home-btn span:nth-child(2),
.home-btn span:nth-child(3),
.home-btn span:nth-child(4) {
  content: "";
  display: block;
  position: absolute;
  background-color: #ffffff;
}
.home-btn span:nth-child(1) {
  width: 1px;
  left: 0;
  bottom: 0;
}
.home-btn span:nth-child(2) {
  height: 1px;
  left: 0;
  top: 0;
}
.home-btn span:nth-child(3) {
  width: 1px;
  right: 0;
  top: 0;
}
.home-btn span:nth-child(4) {
  height: 1px;
  right: 0;
  bottom: 0;
}
.home-btn:hover {
  border: none;
}
.home-btn:hover span:nth-child(1) {
  animation: move1 1500ms infinite ease;
}
.home-btn:hover span:nth-child(2) {
  animation: move2 1500ms infinite ease;
}
.home-btn:hover span:nth-child(3) {
  animation: move3 1500ms infinite ease;
}
.home-btn:hover span:nth-child(4) {
  animation: move4 1500ms infinite ease;
}
@keyframes move1 {
  0% {
    height: 100%;
    bottom: 0;
  }
  54% {
    height: 0;
    bottom: 100%;
  }
  55% {
    height: 0;
    bottom: 0;
  }
  100% {
    height: 100%;
    bottom: 0;
  }
}
@keyframes move2 {
  0% {
    width: 0;
    left: 0;
  }
  50% {
    width: 100%;
    left: 0;
  }
  100% {
    width: 0;
    left: 100%;
  }
}
@keyframes move3 {
  0% {
    height: 100%;
    top: 0;
  }
  54% {
    height: 0;
    top: 100%;
  }
  55% {
    height: 0;
    top: 0;
  }
  100% {
    height: 100%;
    top: 0;
  }
}
@keyframes move4 {
  0% {
    width: 0;
    right: 0;
  }
  55% {
    width: 100%;
    right: 0;
  }
  100% {
    width: 0;
    right: 100%;
  }
}
</style>
