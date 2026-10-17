
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>My Website</title>
<style>
body{font-family:Arial;text-align:center;margin:0;background:#f5f5f5}
header,button{background:#6c5ce7;color:white;padding:15px;border:0;border-radius:8px}
.hero{padding:60px 10px;background:var(--c,#6c5ce7);color:white}
.card{padding:20px;margin:15px;background:white;border-radius:12px}
.dark{background:#222;color:white}
</style>
</head>
<body>
<header><h2>✨ My Website</h2></header>
<div class="hero">
<h1>Welcome!</h1>
<p>Beautiful designs, endless possibilities.</p>
<button onclick="document.body.classList.toggle('dark')">Dark Mode</button>
</div>
<div class="card">
<h2>Change Website Color</h2>
<button onclick="document.documentElement.style.setProperty('--c','#e84393')">Pink</button>
<button onclick="document.documentElement.style.setProperty('--c','#0984e3')">Blue</button>
<button onclick="document.documentElement.style.setProperty('--c','#00b894')">Green</button>
</div>
<div class="card">
<h2>About Us</h2>
<p>Creative ideas. Modern websites.</p>
</div>
<footer>© 2026 My Website ❤️</footer>
</body>
</html>
