# Box
<!DOCTYPE html>
<html>
  <head>
	  <link rel="stylesheet" href="index.css">
  </head>
  <canvas id="canvas"></canvas>
<style>
	html, body {
	margin: 0;
	padding: 0;
	overflow: hidden;
	width: 100%;
	height: 100%;
	}

	canvas {
		display: block;
		position: absolute;
		left: 0;
		top: 0;
	}
</style>
  <script>{
	const BOX = 5;
	let WIDTH = window.innerWidth;
	let HEIGHT = window.innerHeight
	let ctx = canvas.getContext("2d");
	function resizeCanvas() {
		WIDTH = window.innerWidth;
		HEIGHT = window.innerHeight;
		canvas.width = WIDTH;
		canvas.height = HEIGHT;
		ctx = canvas.getContext("2d");
		clear();
	}
	function clear() {
		ctx.fillStyle = "#ff6e00";
		ctx.fillRect(0,0,WIDTH,HEIGHT);
	}
	window.addEventListener('resize', resizeCanvas);
	window.addEventListener('load', resizeCanvas);
	
	function screen(p) {
	    return {
	        x: (p.x + 1)/2*canvas.width,
	        y: (1 - (p.y + 1)/2)*canvas.height,
	    }    
	}
	
	function point(p) {
		ctx.fillStyle = "#33cc33";
		ctx.fillRect(p.x-BOX/2, p.y-BOX/2,BOX,BOX);
	}
	
	function project({x, y, z}) {
	    return {
	        x: x/z,
	        y: y/z,
	    }
	}
	
	const lines = [
		[0,1],
		[0,2],
		[1,3],
		[3,2],
		[1,5],
		[3,7],
		[2,6],
		[0,4],
		[5,4],
		[5,7],
		[4,6],
		[6,7]
	];
	
	const FPS = 120;
	const points = [{x: 0.25, y: 0.25,   z: 0.25}, {x: -0.25, y: 0.25,   z: 0.25}, {x: 0.25, y: -0.25,   z: 0.25}, {x: -0.25, y: -0.25,   z: 0.25},
		{x: 0.25, y: 0.25,  z: -0.25}, {x: -0.25, y: 0.25,  z: -0.25}, {x: 0.25, y: -0.25,  z: -0.25}, {x: -0.25, y: -0.25,  z: -0.25}];
	
	
	function translate_z(p, dz) {
		return {x: p.x, y: p.y, z: p.z + dz};
	}
	
	function rotate_xz({x, y, z}, angle) {
	    	const c = Math.cos(angle);
	    	const s = Math.sin(angle);
	    	const ret = {
	        	x: x*c-z*s,
	        	y,
	        	z: x*s+z*c,
	    	};
		console.log(ret);
		return ret;
	}
	
	function line(p1, p2) {
		ctx.strokeStyle = "#33cc33";
		ctx.lineWidth = 5;
		ctx.beginPath();
		ctx.moveTo(p1.x, p1.y);
		ctx.lineTo(p2.x, p2.y);
		ctx.stroke();
	}
	
	let angle = 0;
	let time = 0;
	const dt = 1/FPS;
	function frame() {
	    	time += dt;
		let dz = 1.5 + 0.5 * Math.cos(2*time);
	    	angle += Math.PI*dt;
		clear();
		let p = [];
		for(const v of points) {
			point(screen(project(translate_z(rotate_xz(v,angle), dz))));
			p.push(screen(project(translate_z(rotate_xz(v, angle), dz))));
		}
		console.log(p);
	
		for(const [p1,p2] of lines) {
			line(p[p1],p[p2]);
		}	
		
		setTimeout(frame, 1000/FPS);
	}
	
	
	setTimeout(frame, 1000/FPS);
  }
  </script>
</html>
