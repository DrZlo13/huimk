<script context="module">
  export const pixelFormatEnum = Object.freeze({ bw: 1, rgb: 2, rgb565: 3 });
</script>

<script>
  import { onMount } from "svelte";

  export let width = 320;
  export let height = 240;
  export let scale = 0;
  export let pixelFormat = pixelFormatEnum.rgb;

  let canvasWidth = width * scale;
  let canvasHeight = height * scale;
  let pixelsCount = width * height;

  let canvas;
  let render = true;

  let glVertexBufferArray = new Float32Array(pixelsCount * 2);
  for (let i = 0; i < pixelsCount; i = i + 1) {
    glVertexBufferArray[i * 2] = (i % width) + 0.5; // x position
    glVertexBufferArray[i * 2 + 1] = Math.floor(i / width) + 0.5; // y position
  }

  let glColorBufferArray = new Float32Array(pixelsCount * 4);
  for (let i = 0; i < pixelsCount; i = i + 1) {
    glColorBufferArray[i * 4] = 0.0; // r
    glColorBufferArray[i * 4 + 1] = 0.0; // g
    glColorBufferArray[i * 4 + 2] = 0.0; // b
    glColorBufferArray[i * 4 + 3] = 1.0; // a
  }

  onMount(() => {
    let frame;

    var createGLProgram = function (gl, shaders) {
      var program = gl.createProgram();
      for (var i = 0; i < shaders.length; i += 1) {
        gl.attachShader(program, shaders[i]);
      }

      gl.linkProgram(program);

      // Check the link status
      var linked = gl.getProgramParameter(program, gl.LINK_STATUS);
      if (!linked) {
        // Something went wrong with the link
        var lastError = gl.getProgramInfoLog(program);
        window.console.error("Error in program linking: " + lastError);

        gl.deleteProgram(program);
        return null;
      }
      return program;
    };

    var createShader = function (gl, shaderScriptText, shaderType) {
      // Create the shader object
      var shader = gl.createShader(shaderType);

      // Load the shader source
      gl.shaderSource(shader, shaderScriptText);

      // Compile the shader
      gl.compileShader(shader);
      return shader;
    };

    let gl = canvas.getContext("webgl", { antialias: false });

    let floatScale = parseFloat(scale).toFixed(2);
    let vertexShaderText =
      `
		    attribute vec2 a_position;
            attribute vec4 a_color;
            varying vec4 vColor;

            uniform vec2 u_resolution;
            void main() {
                gl_PointSize = ` +
      floatScale +
      `;
                //convert to display scale
                vec2 a_position2 = a_position * ` +
      floatScale +
      `;

                // convert the rectangle from pixels to 0.0 to 1.0
                vec2 zeroToOne = a_position2 / u_resolution;

                // convert from 0 -> 1 to 0 -> 2
                vec2 zeroToTwo = zeroToOne * 2.0;

                // convert from 0 -> 2 to -1 -> +1 (clipspace)
                vec2 clipSpace = zeroToTwo - 1.0;

                // Flip 0,0 from bottom left to conventional 2D top left.
                gl_Position = vec4(clipSpace * vec2(1, -1), 0, 1);
                vColor = a_color;
            }
        `;

    let vertexShader = createShader(gl, vertexShaderText, gl.VERTEX_SHADER);

    let fragmentShaderText = `
		    precision mediump float;
            varying vec4 vColor;

            void main() {
                gl_FragColor = vColor;
            }
        `;

    let fragmentShader = createShader(
      gl,
      fragmentShaderText,
      gl.FRAGMENT_SHADER
    );

    let program = createGLProgram(gl, [vertexShader, fragmentShader]);
    gl.useProgram(program);

    // get attributes
    let colorLocation = gl.getAttribLocation(program, "a_color");
    let positionLocation = gl.getAttribLocation(program, "a_position");

    // set resolution
    let resolutionLocation = gl.getUniformLocation(program, "u_resolution");
    gl.uniform2f(resolutionLocation, canvas.width, canvas.height);

    // bind vertex data
    var buffer = gl.createBuffer();
    gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
    gl.enableVertexAttribArray(positionLocation);
    gl.bufferData(gl.ARRAY_BUFFER, glVertexBufferArray, gl.STATIC_DRAW);
    gl.vertexAttribPointer(positionLocation, 2, gl.FLOAT, false, 0, 0);

    // bind color data
    var color_buffer = gl.createBuffer();
    gl.bindBuffer(gl.ARRAY_BUFFER, color_buffer);
    gl.bufferData(gl.ARRAY_BUFFER, glColorBufferArray, gl.STATIC_DRAW);
    gl.vertexAttribPointer(colorLocation, 4, gl.FLOAT, false, 0, 0);
    gl.enableVertexAttribArray(colorLocation);

    (function loop() {
      frame = requestAnimationFrame(loop);

      // draw pixels
      if (render) {
        gl.bufferData(gl.ARRAY_BUFFER, glColorBufferArray, gl.STATIC_DRAW);
        gl.drawArrays(gl.POINTS, 0, pixelsCount);
        render = false;
      }
    })();

    return () => {
      cancelAnimationFrame(frame);
    };
  });

  //
  export function generateNoise() {
    for (let x = 0; x < width; x++) {
      for (let y = 0; y < height; y++) {
        setPixel(
          x,
          y,
          colorRGB(
            Math.random() * 255,
            Math.random() * 255,
            Math.random() * 255
          )
        );
      }
    }

    render = true;
  }

  export function gradientFill() {
    for (let x = 0; x < width; x++) {
      for (let y = 0; y < height; y++) {
        setPixel(
          x,
          y,
          colorRGB(
            Math.floor((255 / height) * y),
            Math.floor((255 / width) * x),
            0
          )
        );
      }
    }

    render = true;
  }

  function componentToHex(c) {
    var hex = c.toString(16);
    return hex.length === 1 ? "0" + hex : hex;
  }

  // color
  export function colorRGB(red, green, blue) {
    red = Math.floor(red);
    green = Math.floor(green);
    blue = Math.floor(blue);

    switch (pixelFormat) {
      case pixelFormatEnum.rgb:
        break;
      case pixelFormatEnum.bw:
        // TODO: function to set threshold
        if (red + green + blue > 1) {
          red = green = blue = 255;
        } else {
          red = green = blue = 0;
        }
        break;
      case pixelFormatEnum.rgb565:
        red = Math.floor(red / 8) * 8;
        green = Math.floor(green / 4) * 4;
        blue = Math.floor(blue / 8) * 8;
        break;
    }

    return { r: red / 255.0, g: green / 255.0, b: blue / 255.0 };
  }

  export function setPixel(x, y, color) {
    // TODO: speedup this
    if (x < 0 || x >= width || y < 0 || y >= height) return;
    let i = y * width + x;
    glColorBufferArray[i * 4] = color.r;
    glColorBufferArray[i * 4 + 1] = color.g;
    glColorBufferArray[i * 4 + 2] = color.b;
  }

  export function clear(color) {
    for (let x = 0; x < width; x++) {
      for (let y = 0; y < height; y++) {
        setPixel(x, y, color);
      }
    }
  }

  export function drawHLine(y, color) {
    for (let x = 0; x < width; x++) {
      setPixel(x, y, color);
    }
  }

  export function drawVLine(x, color) {
    for (let y = 0; y < height; y++) {
      setPixel(x, y, color);
    }
  }

  export function fillRect(x1, y1, x2, y2, color) {
    for (let x = x1; x <= x2; x++) {
      for (let y = y1; y <= y2; y++) {
        setPixel(x, y, color);
      }
    }
  }

  export function drawLine(x1, y1, x2, y2, color) {
    x1 = Math.floor(x1);
    y1 = Math.floor(y1);
    x2 = Math.floor(x2);
    y2 = Math.floor(y2);

    var deltaX = Math.abs(x2 - x1);
    var deltaY = Math.abs(y2 - y1);
    var signX = x1 < x2 ? 1 : -1;
    var signY = y1 < y2 ? 1 : -1;
    var error = deltaX - deltaY;

    setPixel(x2, y2, color);
    while (x1 !== x2 || y1 !== y2) {
      setPixel(x1, y1, color);
      var error2 = error * 2;
      //
      if (error2 > -deltaY) {
        error -= deltaY;
        x1 += signX;
      }
      if (error2 < deltaX) {
        error += deltaX;
        y1 += signY;
      }
    }
  }

  export function doRender() {
    render = true;
  }
</script>

<canvas width={canvasWidth} height={canvasHeight} bind:this={canvas} />
