<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
  <title>Car Controller - Slow Speed</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      -webkit-tap-highlight-color: transparent;
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 20px;
      overflow: hidden;
    }

    .container {
      background: rgba(255, 255, 255, 0.95);
      border-radius: 30px;
      padding: 30px;
      max-width: 500px;
      width: 100%;
      box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    }

    .header {
      text-align: center;
      margin-bottom: 25px;
    }

    h1 {
      color: #667eea;
      font-size: 28px;
      margin-bottom: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 10px;
    }

    .status {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      font-size: 14px;
      color: #666;
      margin-top: 10px;
    }

    .status-dot {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      background: #e74c3c;
      animation: pulse 2s infinite;
    }

    .status-dot.connected {
      background: #2ecc71;
    }

    @keyframes pulse {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.5; }
    }

    .connect-btn {
      width: 100%;
      padding: 15px;
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      color: white;
      border: none;
      border-radius: 15px;
      font-size: 16px;
      font-weight: bold;
      cursor: pointer;
      margin-bottom: 20px;
      transition: transform 0.2s;
    }

    .connect-btn:active {
      transform: scale(0.98);
    }

    .connect-btn:disabled {
      opacity: 0.6;
      cursor: not-allowed;
    }

    .speed-control {
      background: rgba(102, 126, 234, 0.1);
      padding: 20px;
      border-radius: 15px;
      margin-bottom: 25px;
    }

    .speed-label {
      display: flex;
      justify-content: space-between;
      margin-bottom: 10px;
      font-weight: bold;
      color: #667eea;
    }

    .speed-slider {
      width: 100%;
      height: 8px;
      border-radius: 5px;
      outline: none;
      background: linear-gradient(to right, #e74c3c 0%, #f39c12 50%, #2ecc71 100%);
      -webkit-appearance: none;
    }

    .speed-slider::-webkit-slider-thumb {
      -webkit-appearance: none;
      width: 24px;
      height: 24px;
      border-radius: 50%;
      background: white;
      cursor: pointer;
      box-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
    }

    .speed-slider::-moz-range-thumb {
      width: 24px;
      height: 24px;
      border-radius: 50%;
      background: white;
      cursor: pointer;
      box-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
      border: none;
    }

    .controller {
      display: grid;
      grid-template-columns: 1fr 1fr 1fr;
      gap: 15px;
      margin-bottom: 25px;
      max-width: 350px;
      margin-left: auto;
      margin-right: auto;
    }

    .control-btn {
      aspect-ratio: 1;
      border: none;
      border-radius: 20px;
      font-size: 32px;
      cursor: pointer;
      transition: all 0.1s;
      background: linear-gradient(145deg, #f0f0f0, #cacaca);
      box-shadow: 5px 5px 10px #bebebe, -5px -5px 10px #ffffff;
      user-select: none;
      touch-action: manipulation;
    }

    .control-btn:active {
      box-shadow: inset 5px 5px 10px #bebebe, inset -5px -5px 10px #ffffff;
      transform: scale(0.95);
    }

    .control-btn.pressed {
      background: linear-gradient(145deg, #667eea, #764ba2);
      box-shadow: inset 5px 5px 10px rgba(0,0,0,0.2), inset -5px -5px 10px rgba(255,255,255,0.1);
      color: white;
    }

    .control-btn.stop-btn {
      background: linear-gradient(145deg, #e74c3c, #c0392b);
      color: white;
      font-size: 24px;
      font-weight: bold;
    }

    .control-btn.stop-btn:active {
      box-shadow: inset 5px 5px 10px rgba(0,0,0,0.3), inset -5px -5px 10px rgba(255,255,255,0.1);
    }

    .empty {
      background: transparent;
      box-shadow: none;
      pointer-events: none;
    }

    .log {
      background: #f8f9fa;
      border-radius: 15px;
      padding: 15px;
      max-height: 150px;
      overflow-y: auto;
      font-size: 12px;
      font-family: 'Courier New', monospace;
    }

    .log-entry {
      padding: 5px 0;
      border-bottom: 1px solid #dee2e6;
    }

    .log-entry:last-child {
      border-bottom: none;
    }

    @media (max-width: 480px) {
      .container {
        padding: 20px;
      }
      
      h1 {
        font-size: 24px;
      }
      
      .controller {
        gap: 10px;
      }
      
      .control-btn {
        font-size: 28px;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>🚗 Car Controller</h1>
      <div class="status">
        <div class="status-dot" id="statusDot"></div>
        <span id="statusText">Disconnected</span>
      </div>
    </div>

    <button class="connect-btn" id="connectBtn">Connect to HC-06</button>

    <div class="speed-control">
      <div class="speed-label">
        <span>🐢 Speed Control</span>
        <span id="speedValue">3</span>
      </div>
      <input type="range" class="speed-slider" id="speedSlider" min="0" max="9" value="3">
    </div>

    <div class="controller">
      <div class="empty"></div>
      <button class="control-btn" id="forwardBtn">⬆️</button>
      <div class="empty"></div>
      
      <button class="control-btn" id="leftBtn">⬅️</button>
      <button class="control-btn stop-btn" id="stopBtn">STOP</button>
      <button class="control-btn" id="rightBtn">➡️</button>
      
      <div class="empty"></div>
      <button class="control-btn" id="backwardBtn">⬇️</button>
      <div class="empty"></div>
    </div>

    <div class="log" id="log">
      <div class="log-entry">💡 Press Connect to start</div>
    </div>
  </div>

  <script>
    let bluetoothDevice = null;
    let characteristic = null;
    let isConnected = false;
    let activeButton = null;

    const connectBtn = document.getElementById('connectBtn');
    const statusDot = document.getElementById('statusDot');
    const statusText = document.getElementById('statusText');
    const speedSlider = document.getElementById('speedSlider');
    const speedValue = document.getElementById('speedValue');
    const logDiv = document.getElementById('log');

    const buttons = {
      forward: document.getElementById('forwardBtn'),
      backward: document.getElementById('backwardBtn'),
      left: document.getElementById('leftBtn'),
      right: document.getElementById('rightBtn'),
      stop: document.getElementById('stopBtn')
    };

    // Log function
    function log(message) {
      const entry = document.createElement('div');
      entry.className = 'log-entry';
      entry.textContent = `${new Date().toLocaleTimeString()}: ${message}`;
      logDiv.insertBefore(entry, logDiv.firstChild);
      if (logDiv.children.length > 20) {
        logDiv.removeChild(logDiv.lastChild);
      }
    }

    // Update status
    function updateStatus(connected) {
      isConnected = connected;
      if (connected) {
        statusDot.classList.add('connected');
        statusText.textContent = 'Connected';
        connectBtn.textContent = 'Disconnect';
        connectBtn.disabled = false;
      } else {
        statusDot.classList.remove('connected');
        statusText.textContent = 'Disconnected';
        connectBtn.textContent = 'Connect to HC-06';
        connectBtn.disabled = false;
      }
    }

    // Send command
    async function sendCommand(command) {
      if (!isConnected || !characteristic) {
        log('❌ Not connected');
        return;
      }

      try {
        const encoder = new TextEncoder();
        await characteristic.writeValue(encoder.encode(command));
      } catch (error) {
        log(`❌ Error: ${error.message}`);
      }
    }

    // Speed slider
    speedSlider.addEventListener('input', (e) => {
      const speed = e.target.value;
      speedValue.textContent = speed;
      sendCommand(speed);
      log(`🚀 Speed set to: ${speed}`);
    });

    // Connect button
    connectBtn.addEventListener('click', async () => {
      if (isConnected) {
        if (bluetoothDevice && bluetoothDevice.gatt.connected) {
          bluetoothDevice.gatt.disconnect();
        }
        updateStatus(false);
        log('🔌 Disconnected');
        return;
      }

      try {
        connectBtn.disabled = true;
        connectBtn.textContent = 'Connecting...';
        log('🔍 Searching for HC-06...');

        bluetoothDevice = await navigator.bluetooth.requestDevice({
          filters: [{ namePrefix: 'HC-' }],
          optionalServices: ['0000ffe0-0000-1000-8000-00805f9b34fb']
        });

        log('📡 Connecting...');
        const server = await bluetoothDevice.gatt.connect();
        const service = await server.getPrimaryService('0000ffe0-0000-1000-8000-00805f9b34fb');
        characteristic = await service.getCharacteristic('0000ffe1-0000-1000-8000-00805f9b34fb');

        updateStatus(true);
        log('✅ Connected to HC-06!');

        // Set initial speed
        sendCommand(speedSlider.value);

        bluetoothDevice.addEventListener('gattserverdisconnected', () => {
          updateStatus(false);
          log('🔌 Device disconnected');
        });
      } catch (error) {
        log(`❌ Connection failed: ${error.message}`);
        updateStatus(false);
      }
    });

    // Button handlers
    function setupButton(btn, command, label) {
      let intervalId = null;

      const startCommand = () => {
        if (!isConnected) return;
        btn.classList.add('pressed');
        sendCommand(command);
        log(`▶️ ${label}`);
        activeButton = command;
      };

      const stopCommand = () => {
        if (!isConnected) return;
        btn.classList.remove('pressed');
        sendCommand('S');
        if (activeButton === command) {
          log(`⏹️ Stopped`);
          activeButton = null;
        }
      };

      // Touch events
      btn.addEventListener('touchstart', (e) => {
        e.preventDefault();
        startCommand();
      });

      btn.addEventListener('touchend', (e) => {
        e.preventDefault();
        stopCommand();
      });

      // Mouse events
      btn.addEventListener('mousedown', startCommand);
      btn.addEventListener('mouseup', stopCommand);
      btn.addEventListener('mouseleave', stopCommand);
    }

    setupButton(buttons.forward, 'U', 'Forward');
    setupButton(buttons.backward, 'D', 'Backward');
    setupButton(buttons.left, 'L', 'Left');
    setupButton(buttons.right, 'R', 'Right');

    // Stop button - immediate stop
    buttons.stop.addEventListener('click', () => {
      buttons.stop.classList.add('pressed');
      sendCommand('S');
      log('🛑 Emergency Stop');
      setTimeout(() => buttons.stop.classList.remove('pressed'), 200);
      activeButton = null;
    });

    // Keyboard support
    const keyMap = {
      'ArrowUp': 'U',
      'ArrowDown': 'D',
      'ArrowLeft': 'L',
      'ArrowRight': 'R',
      'w': 'U',
      's': 'D',
      'a': 'L',
      'd': 'R',
      ' ': 'S'
    };

    let activeKeys = new Set();

    document.addEventListener('keydown', (e) => {
      if (!isConnected || activeKeys.has(e.key)) return;
      
      const command = keyMap[e.key];
      if (command) {
        e.preventDefault();
        activeKeys.add(e.key);
        sendCommand(command);
        
        if (command !== 'S') {
          log(`⌨️ ${e.key.toUpperCase()}`);
        }
      }
    });

    document.addEventListener('keyup', (e) => {
      if (!isConnected) return;
      
      const command = keyMap[e.key];
      if (command && command !== 'S') {
        e.preventDefault();
        activeKeys.delete(e.key);
        sendCommand('S');
        log('⏹️ Stopped');
      }
    });
  </script>
</body>
</html>
