<template>
  <div class="draw">
    <canvas
      ref="palette"
      class="palette"
      @mousedown="handleDownCanvas"
      @mouseup="handleOverMove"
      @mousemove="handleMove"
      @mouseout="handleOverMove"
    />
  </div>
</template>

<script lang="ts" setup>
import { ref, reactive, onMounted, onUnmounted, nextTick } from "vue";

const palette = ref<HTMLCanvasElement | null>(null);
const state = reactive({
  context: null as CanvasRenderingContext2D | null,
  isDrawing: false,
  config: {
    lineWidth: 7, // 线条的宽度
    shadowBlur: 2, // 阴影模糊的程度
    shadowColor: "#e2ebf0", // 阴影的颜色
    strokeStyle: "#fff", // 笔触的颜色
  },
  preHandle: [] as ImageData[], // 上一步
  nextHandle: [] as ImageData[], // 下一步
});

// 设置画笔的配置
const handleSetConfig = () => {
  if (state.context) {
    Object.assign(state.context, state.config);
  }
};

// 获取鼠标坐标点
const getEventXY = (e) => {
  const canvasX = e.offsetX;
  const canvasY = e.offsetY;
  return { canvasX, canvasY };
};

// 在canvas中按下鼠标
const handleDownCanvas = (e) => {
  // 是否可以开始移动绘制
  state.isDrawing = true;
  // 获取当前鼠标按下的位置
  const { canvasX, canvasY } = getEventXY(e);
  // 重置画笔参数
  handleSetConfig();
  // 清除子路径
  state.context?.beginPath();
  // 记录起点
  state.context?.moveTo(canvasX, canvasY);
  // 参数的值 x y width height
  const pre = state.context?.getImageData(
    0,
    0,
    palette.value?.width ?? 0,
    palette.value?.height ?? 0
  );
  // 记录当前操作，便于后续的撤销操作
  if (pre) state.preHandle.push(pre);
  // 重新绘画之后清除所有下一步
  state.nextHandle = [];
};

// 结束绘画
const handleOverMove = () => {
  state.isDrawing = false;
};

// 移动
const handleMove = (e) => {
  if (!state.isDrawing || !state.context) return;
  const { canvasX, canvasY } = getEventXY(e);
  // 链接每个点
  state.context.lineTo(canvasX, canvasY);
  // 绘制已定义的路径
  state.context.stroke();
};

// 调整canvas大小并保留内容
const resizeCanvas = () => {
  if (state.context && palette.value) {
    // 保存画布内容
    const canvasData = state.context.getImageData(
      0,
      0,
      palette.value.width,
      palette.value.height
    );

    palette.value.width = window.innerWidth;
    palette.value.height = window.innerHeight * 0.8;
    // 确保在DOM更新后重新绘制画布内容
    nextTick(() => {
      state.context?.putImageData(canvasData, 0, 0);
    });
  }
};

// 初始化canvas
const initCanvas = () => {
  if (palette.value) {
    state.context = palette.value.getContext("2d");
    resizeCanvas(); // 确保在初始化时调整canvas大小
  }
};

onMounted(() => {
  initCanvas();
  window.addEventListener("resize", resizeCanvas);
});

onUnmounted(() => {
  window.removeEventListener("resize", resizeCanvas);
});
</script>

<style scoped>
.palette {
  display: block; /* 移除默认的边距 */
  width: 100vw;
  height: 80vh; /* 设置初始大小以避免闪烁 */
}
</style>
