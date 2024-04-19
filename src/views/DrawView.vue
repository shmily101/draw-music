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
import { ref, onMounted, onUnmounted, nextTick } from "vue";

const palette = ref<HTMLCanvasElement | null>(null);
// canvas对象
const context = ref<CanvasRenderingContext2D | null>(null);
// 是否开始绘制
const canvasMoveUse = ref(false);
const movex = ref(0);
const movey = ref(0);
// 画笔配置
const config = ref({
  lineWidth: 7, //  线条的宽度
  shadowBlur: 2, //  阴影模糊的程度
  shadowColor: "#e2ebf0", //  阴影的颜色
  strokeStyle: "#fff", //  笔触的颜色
});
// 上一步
const preHandle = ref([]);
// 下一步
const nextHandle = ref([]);

// 获取鼠标坐标点
const getEventXY = (e) => {
  // 默认获取pc端坐标点
  const canvasX = e.offsetX;
  const canvasY = e.offsetY;
  movex.value = canvasX;
  movey.value = canvasY;
  // 使用手机的时候
  // if (!isPC()) {
  //   canvasX = e.changedTouches[0].offsetX;
  //   canvasY = e.changedTouches[0].offsetY;
  // }
  return { canvasX, canvasY };
};

// 设置画笔的配置
const handleSetConfig = () => {
  if (context.value) {
    context.value.lineWidth = config.value.lineWidth;
    context.value.shadowBlur = config.value.shadowBlur;
    context.value.shadowColor = config.value.shadowColor;
    context.value.strokeStyle = config.value.strokeStyle;
  }
};

const resizeCanvas = () => {
  if (context.value && palette.value) {
    // 保存画布内容
    const canvasData = context.value.getImageData(
      0,
      0,
      palette.value.width,
      palette.value.height
    );

    palette.value.width = window.innerWidth;
    palette.value.height = window.innerHeight * 0.8;
    // 确保在DOM更新后重新绘制画布内容
    nextTick(() => {
      if (context.value) {
        context.value.putImageData(canvasData, 0, 0);
      }
    });
  }
};

const initCanvas = () => {
  if (palette.value) {
    context.value = palette.value.getContext("2d");
  }
};

// 在canvas中按下鼠标
const handleDownCanvas = (e) => {
  console.log("handleDownCanvas");
  if (context.value && palette.value) {
    // 是否可以开始移动绘制
    canvasMoveUse.value = true;
    // 获取当前鼠标按下的位置
    const { canvasX, canvasY } = getEventXY(e);
    // 重置画笔配置
    handleSetConfig();
    // 清除子路径
    context.value.beginPath();
    // 记录起点
    context.value.moveTo(canvasX, canvasY);
    // 参数的值 x y width height
    const pre = context.value.getImageData(
      0,
      0,
      palette.value.width,
      palette.value.height
    );
    // 记录当前操作，便于后续的撤销操作
    preHandle.value.push(pre);
    // 重新绘画之后清除所有下一步
    nextHandle.value = [];
  }
};

// 结束绘画
const handleOverMove = () => {
  canvasMoveUse.value = false;
};

// 移动
const handleMove = (e) => {
  if (!canvasMoveUse.value || !context.value) return;
  // 获取坐标点
  const { canvasX, canvasY } = getEventXY(e);
  // 链接每个点
  context.value.lineTo(canvasX, canvasY);
  //绘制已定义的路径
  context.value.stroke();
};

onMounted(() => {
  initCanvas();
  resizeCanvas();
  window.addEventListener("resize", resizeCanvas);
});

onUnmounted(() => {
  window.removeEventListener("resize", resizeCanvas);
});
</script>

<style scoped></style>
