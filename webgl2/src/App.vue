<template>
  <div id="app">
    <h1>WebGL 3D App</h1>
    <canvas ref="glCanvas" width="800" height="600"></canvas>
  </div>
</template>

<script lang="ts">
import { onMounted, ref, render } from 'vue';

export default {
  name: 'App',
  setup() {
    const glCanvas = ref<HTMLCanvasElement | null>(null);

    // 编译着色器（类型声明）
    const compileShader = (gl: WebGL2RenderingContext, source: string, type: number): WebGLShader | null => {
      const shader = gl.createShader(type);
      if (!shader) return null;
      gl.shaderSource(shader, source);
      gl.compileShader(shader);
      if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
        console.error('着色器编译错误:', gl.getShaderInfoLog(shader));
        gl.deleteShader(shader);
        return null;
      }
      return shader;
    };

    const initWebGL2 = (canvas: HTMLCanvasElement) => {
      const gl = canvas.getContext('webgl2');
      if (!gl) {
        console.error('不支持WebGL2');
        return;
      }

      console.log('WebGL2上下文初始化成功:', gl);

      // 顶点着色器源码
      const vsSource = `#version 300 es
        in vec4 aPosition;
        out vec2 vUv; // 传递UV坐标到片段着色器
        void main() {
          vUv = aPosition.xy * 0.5 + 0.5; // 计算UV坐标（范围0到1）
          gl_Position = aPosition;
        }
      `;

      // 片段着色器源码（动态计算颜色渐变）
      const fsSource = `#version 300 es
        precision highp float;
        in vec2 vUv; // 接收UV坐标
        out vec4 fragColor;
        void main() {
          // 根据UV坐标计算颜色渐变（从红色到蓝色）
          fragColor = vec4(vUv.x, 0.0, vUv.y, 1.0);
        }
      `;

      // 编译和链接着色器（其余代码保持不变）
      const vertexShader = compileShader(gl, vsSource, gl.VERTEX_SHADER);
      const fragmentShader = compileShader(gl, fsSource, gl.FRAGMENT_SHADER);

      if (!vertexShader || !fragmentShader) {
        return;
      }

      const program = gl.createProgram();
      gl.attachShader(program, vertexShader);
      gl.attachShader(program, fragmentShader);
      gl.linkProgram(program);

      if (!gl.getProgramParameter(program, gl.LINK_STATUS)) {
        console.error('程序链接错误:', gl.getProgramInfoLog(program));
        return;
      }

      gl.useProgram(program);

      // 顶点数据和缓冲区绑定（保持不变）
      const positions = new Float32Array([-0.5, -0.5, 0.5, -0.5, 0.0, 0.5]);
      const positionBuffer = gl.createBuffer();
      gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
      gl.bufferData(gl.ARRAY_BUFFER, positions, gl.STATIC_DRAW);

      const positionAttributeLocation = gl.getAttribLocation(program, 'aPosition');
      gl.enableVertexAttribArray(positionAttributeLocation);
      gl.vertexAttribPointer(positionAttributeLocation, 2, gl.FLOAT, false, 0, 0);

      gl.clearColor(1.0, 1.0, 1.0, 1.0);
      gl.clear(gl.COLOR_BUFFER_BIT);
      gl.drawArrays(gl.TRIANGLES, 0, 3);

      console.log('绘制完成');
    };

    onMounted(() => {
      if (glCanvas.value) initWebGL2(glCanvas.value);
    });

    return { glCanvas };
  }
  
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
canvas {
  border: 1px solid #000;
}
</style>