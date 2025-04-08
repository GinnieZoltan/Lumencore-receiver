# Lumencore-receiver
GINNIE FIRST 
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Lumencore Mobile Receiver</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 10px; background: #121212; color: #e0e0e0; }
    h1 { color: #90caf9; }
    #output { font-size: 1.2em; margin-top: 20px; background: #1e1e1e; padding: 10px; border-radius: 5px; }
    video { width: 100%; max-height: 50vh; border: 2px solid #444; border-radius: 5px; margin-top: 10px; }
  </style>
</head>
<body>
  <h1>Lumencore Light Receiver</h1>
  <p>Point your camera at the blinking light source to receive the message.</p>
  <video id="video" autoplay playsinline></video>
  <div id="output">Message: <span id="decoded">(waiting...)</span></div>
  <script>
    const video = document.getElementById('video');
    const output = document.getElementById('decoded');

    async function startCamera() {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' }, audio: false });
        video.srcObject = stream;
      } catch (err) {
        alert('Camera access denied or not available.');
      }
    }

    startCamera(); // Call the function to activate the camera
  </script>
    function getBrightness(frame) {
      let total = 0;
      for (let i = 0; i < frame.data.length; i += 4) {
        total += frame.data[i] + frame.data[i + 1] + frame.data[i + 2];
      }
      return total / (frame.data.length / 4);
    }

    let decodedMessage = "";
    let lastSymbolTime = Date.now();
    let lastBrightness = null;

    function decodeSymbol(timeDiff, delta) {
      if (delta < -40) return timeDiff < 200 ? '.' : '-';
      if (delta > 40 && timeDiff > 500) return ' ';
      return '';
    }

    function processFrame() {
      const canvas = document.createElement('canvas');
      const context = canvas.getContext('2d');
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      context.drawImage(video, 0, 0, canvas.width, canvas.height);
      const frame = context.getImageData(0, 0, canvas.width, canvas.height);

      const brightness = getBrightness(frame);
      if (lastBrightness !== null) {
        const delta = brightness - lastBrightness;
        const now = Date.now();
        const diff = now - lastSymbolTime;
        const symbol = decodeSymbol(diff, delta);
        if (symbol) {
          decodedMessage += symbol;
          output.textContent = decodedMessage;
        }
        lastSymbolTime = now;
      }
      lastBrightness = brightness;
      requestAnimationFrame(processFrame);
    }

    requestAnimationFrame(processFrame);
  </script>
</body>
</html>
<title>Lumencore Mobile Receiver</title> <style> body { font-family: Arial, sans-serif; padding: 10px; background: #121212; color: #e0e0e0; } h1 { color: #90caf9; } #output { font-size: 1.2em; margin-top: 20px; background: #1e1e1e; padding: 10px; border-radius: 5px; } video { width: 100%; max-height: 50vh; border: 2px solid #444; border-radius: 5px; margin-top: 10px; } </style>
