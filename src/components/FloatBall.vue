<template>
  <div
    ref="floatingBall"
    class="floating-ball"
    :style="{ top: `${position.y}px`, left: `${position.x}px` }"
    @mousedown="startDrag"
  >
    <!-- 使用slot允许动态内容传入 -->
    <div>1234</div>
    <slot></slot>
  </div>
</template>

<script lang="ts" setup>
import { ref, reactive, onMounted, onUnmounted } from "vue";

const floatingBall = ref<HTMLElement | null>(null);
const position = reactive({ x: 0, y: 30 });

let start = { x: 0, y: 0 };

const updatePosition = (event: MouseEvent) => {
  // 使用偏移量更新悬浮球位置
  const deltaX = event.clientX - start.x;
  const deltaY = event.clientY - start.y;
  const ballSize = 50; // 与CSS中定义的尺寸相同
  position.x = Math.max(
    -ballSize / 2,
    Math.min(window.innerWidth - ballSize / 2, deltaX)
  );
  position.y = Math.max(
    -ballSize / 2,
    Math.min(window.innerHeight - ballSize / 2, deltaY)
  );
};

const startDrag = (event: MouseEvent) => {
  // 计算鼠标点击位置与悬浮球左上角的偏移量
  const rect = floatingBall.value?.getBoundingClientRect();
  if (rect) {
    start.x = event.clientX - rect.left; // 偏移量X
    start.y = event.clientY - rect.top; // 偏移量Y
  }
  document.addEventListener("mousemove", updatePosition);
  document.addEventListener("mouseup", stopDrag);
  event.preventDefault();
};

const stopDrag = () => {
  document.removeEventListener("mousemove", updatePosition);
  document.removeEventListener("mouseup", stopDrag);
};

onMounted(() => {
  if (floatingBall.value) {
    const { width, height } = floatingBall.value.getBoundingClientRect();
    // 默认位置：窗口右侧1/4处
    position.x = window.innerWidth - width;
    position.y = (window.innerHeight - height) / 4;
  }
});

onUnmounted(() => {
  stopDrag(); // 确保在组件卸载时移除事件监听器
});
</script>

<style scoped>
.floating-ball {
  position: fixed;
  width: 50px;
  height: 50px;
  background-color: #ffffff99;
  border-radius: 50%;
  cursor: grab;
  user-select: none;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
}
.floating-ball:active {
  cursor: grabbing;
}
</style>
