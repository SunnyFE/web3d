<script setup lang="ts">
import { ref, onMounted } from 'vue'
const compileShader = (gl: WebGL2RenderingContext, source: string, type: number) => {
  const shader = gl.createShader(type)
  if (!shader) {
    throw new Error('Failed to create shader')
  }
  gl.shaderSource(shader, source)
  gl.compileShader(shader)
  if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
    throw new Error('Failed to compile shader: ' + gl.getShaderInfoLog(shader))
  }
  return shader
}
const initCanvas = (canvas: HTMLCanvasElement) => {
  console.log('init')
  const gl = canvas.getContext('webgl2');
}
const canvas = ref<HTMLCanvasElement | null>(null)
onMounted(() => {
  if (canvas.value) {
    initCanvas(canvas.value)
  }
});

</script>

<template>
  <div>
    <header>
      <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

      <div class="wrapper">
        <HelloWorld msg="You did it!" />
      </div>
    </header>
    <canvas ref="canvas" />
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
