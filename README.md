
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Novareach Digital Live</title>
  <style>
    body {
      margin: 0;
      padding: 20px;
      background: #10131b;
      color: white;
      font-family: Arial, sans-serif;
      text-align: center;
    }
    .container {
      max-width: 650px;
      margin: auto;
    }
    video {
      width: 100%;
      background: #000;
      border-radius: 16px;
      aspect-ratio: 9 / 16;
      object-fit: cover;
    }
    button {
      padding: 13px 18px;
      margin: 8px 4px;
      border: 0;
      border-radius: 9px;
      font-weight: bold;
      cursor: pointer;
    }
    .start { background: #24d6a0; color: #10131b; }
    .stop { background: #ff5c68; color: white; }
    #status { color: #b8c2d8; }
  </style>
</head>
<body>
  <div class="container">
    <h1>🔴 Novareach Live</h1>
    <p>Preview your camera before going live.</p>

    <video id="camera" autoplay playsinline muted></video>

    <p id="status">Camera is off.</p>

    <button class="start" id="start">Start Camera</button>
    <button class="stop" id="stop">Stop Camera</button>

    <p>Live broadcasting to other users is not connected yet.</p>
    <a href="/" style="color:#24d6a0">Back to homepage</a>
  </div>

  <script>
    const video = document.getElementById("camera");
    const status = document.getElementById("status");
    let stream = null;

    document.getElementById("start").onclick = async () => {
      try {
        if (!navigator.mediaDevices?.getUserMedia) {
          throw new Error("Open this page using HTTPS in a supported browser.");
        }

        stream = await navigator.mediaDevices.getUserMedia({
          video: { facingMode: "user" },
          audio: true
        });

        video.srcObject = stream;
        status.textContent = "Camera preview is running.";
      } catch (error) {
        status.textContent = "Could not start camera: " + error.message;
      }
    };

    document.getElementById("stop").onclick = () => {
      if (stream) {
        stream.getTracks().forEach(track => track.stop());
        stream = null;
      }
      video.srcObject = null;
      status.textContent = "Camera is off.";
    };

    window.addEventListener("pagehide", () => {
      if (stream) stream.getTracks().forEach(track => track.stop());
    });
  </script>
</body>
</html>
