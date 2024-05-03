	<script context="module">
		import { writable } from 'svelte/store';
		export const canvasState = writable({
			strength: 0,
			angle: (Math.PI / 2).toFixed(2),
			radius: 25.0,
		});
	</script>

	<script>
		import { onMount } from "svelte";

		export let strength;
		export let width;
		export let angle;
		export let radius;

		let canvas;
		let gl;
		let program;
		let positionLocation;
		let resolutionLocation;
		let strengthLocation;
		let angleLocation;
		let radiusLocation;
		let positionBuffer;

		const vertexShaderSource = `
			attribute vec2 a_position;
			void main() {
				gl_Position = vec4(a_position, 0, 1);
			}
		`;

		const fragmentShaderSource = `
			precision mediump float;
			uniform vec2 u_resolution;
			uniform float u_strength;
			uniform float u_angle;
			uniform float u_radius;
			const int SAMPLES = 50;

			float circle(vec2 position, float radius) {
				vec2 center = u_resolution / 2.0;
				float distance = length(position - center);
				float smoothEdge = 0.005 * u_resolution.y;
				return smoothstep(radius, radius - smoothEdge, distance);
			}

			void main() {
				vec2 fragCoord = gl_FragCoord.xy;
				vec2 direction = vec2(cos(u_angle), sin(u_angle)) * u_strength;
				vec3 accumulatedColor = vec3(0.0); // Noir par défaut

				for (int i = 0; i < SAMPLES; i++) {
					float t = (float(i) / float(SAMPLES) - 0.5) * 2.0;
					vec2 samplePosition = fragCoord + direction * t;
					float sampleColor = circle(samplePosition, u_radius);
					accumulatedColor += vec3(sampleColor);
				}

				accumulatedColor /= float(SAMPLES);
				gl_FragColor = vec4(accumulatedColor, 1.0);
			}
		`;


		function initWebGL() {
			program = createProgram(vertexShaderSource, fragmentShaderSource);
			gl.useProgram(program);

			positionLocation = gl.getAttribLocation(program, "a_position");
			resolutionLocation = gl.getUniformLocation(program, "u_resolution");
			strengthLocation = gl.getUniformLocation(program, "u_strength");
			angleLocation = gl.getUniformLocation(program, "u_angle");
			radiusLocation = gl.getUniformLocation(program, "u_radius");

			positionBuffer = gl.createBuffer();
			gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);

			const positions = [-1, -1, 1, -1, -1, 1, 1, 1];
			gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);

			requestAnimationFrame(updateMotionBlur);
		}

		function createProgram(vertexShaderSource, fragmentShaderSource) {
			const vertexShader = createShader(gl.VERTEX_SHADER, vertexShaderSource);
			const fragmentShader = createShader(gl.FRAGMENT_SHADER, fragmentShaderSource);

			const program = gl.createProgram();
			gl.attachShader(program, vertexShader);
			gl.attachShader(program, fragmentShader);
			gl.linkProgram(program);

			if (!gl.getProgramParameter(program, gl.LINK_STATUS)) {
				console.error("Failed to link program:", gl.getProgramInfoLog(program));
				gl.deleteProgram(program);
				return null;
			}

			return program;
		}

		function createShader(type, source) {
			const shader = gl.createShader(type);
			gl.shaderSource(shader, source);
			gl.compileShader(shader);

			if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
				console.error("Failed to compile shader:", gl.getShaderInfoLog(shader));
				gl.deleteShader(shader);
				return null;
			}

			return shader;
		}

		function updateMotionBlur() {
			gl.clearColor(0, 0, 0, 1);
			gl.clear(gl.COLOR_BUFFER_BIT);

			gl.uniform2f(resolutionLocation, canvas.width, canvas.height);
			gl.uniform1f(strengthLocation, strength);
			gl.uniform1f(angleLocation, angle);
			gl.uniform1f(radiusLocation, radius);

			gl.enableVertexAttribArray(positionLocation);
			gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
			gl.vertexAttribPointer(positionLocation, 2, gl.FLOAT, false, 0, 0);

			gl.drawArrays(gl.TRIANGLE_STRIP, 0, 4);

			requestAnimationFrame(updateMotionBlur);
		}

		onMount(() => {
			gl = canvas.getContext("webgl");
			initWebGL();

			return () => {
				requestAnimationFrame(updateMotionBlur);
			};
		});
	</script>
	<canvas bind:this={canvas} width={width} height={width} style="background-color: black;"></canvas>