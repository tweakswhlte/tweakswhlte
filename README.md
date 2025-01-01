FreeFire Sensitivity 

<html><head><base href="." target="_blank">
<title>Free Fire Sensitivity Training Simulator</title>
<style>
:root {
  --primary: #ff4d00;
  --secondary: #ff8c00;
  --success: #16a34a; 
  --danger: #dc2626;
  --background: #1a0f0f;
}

body {
  margin: 0;
  min-height: 100vh;
  background: var(--background);
  font-family: system-ui, sans-serif;
  color: white;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.game-container {
  max-width: 800px;
  width: 95%;
  margin: 2rem auto;
  padding: 2rem;
  background: rgba(255,255,255,0.1);
  border-radius: 1rem;
  box-shadow: 0 4px 6px rgba(0,0,0,0.2);
}

.settings-panel {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
  margin: 1rem 0;
}

.setting-group {
  background: rgba(0,0,0,0.2);
  padding: 1rem;
  border-radius: 0.5rem;
}

.setting-group h3 {
  margin-top: 0;
  color: var(--primary);
}

.sensitivity-slider {
  width: 100%;
  margin: 0.5rem 0;
}

.sensitivity-display {
  font-size: 1.2rem;
  text-align: center;
  margin: 0.5rem 0;
  color: var(--primary);
}

.target-area {
  width: 100%;
  height: 300px;
  background: rgba(0,0,0,0.3);
  position: relative;
  border-radius: 1rem;
  overflow: hidden;
  cursor: crosshair;
}

.target {
  position: absolute;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  transform: translate(-50%, -50%);
  transition: all 0.3s ease;
}

.target::before,
.target::after {
  content: '';
  position: absolute;
  border-radius: 50%;
}

.target::before {
  width: 100%;
  height: 100%;
  background: var(--primary);
}

.target::after {
  width: 40%;
  height: 40%;
  background: white;
  top: 30%;
  left: 30%;
}

.target.hit {
  animation: hitEffect 0.3s ease-out;
}

.stats {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 1rem;
  margin: 1rem 0;
}

.stat-card {
  background: rgba(255,255,255,0.1);
  padding: 1rem;
  border-radius: 0.5rem;
  text-align: center;
}

@keyframes hitEffect {
  0% { transform: scale(1) translate(-50%, -50%); }
  50% { transform: scale(1.5) translate(-50%, -50%); }
  100% { transform: scale(0) translate(-50%, -50%); }
}

.controls {
  display: flex;
  gap: 1rem;
  margin: 1rem 0;
  justify-content: center;
}

button {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 0.5rem;
  background: var(--primary);
  color: white;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

button:hover {
  background: var(--secondary);
  transform: translateY(-2px);
}

input[type="range"] {
  width: 100%;
  height: 10px;
  background: rgba(255,255,255,0.1);
  border-radius: 5px;
  outline: none;
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 20px;
  height: 20px;
  background: var(--primary);
  border-radius: 50%;
  cursor: pointer;
}

.mode-select {
  padding: 0.5rem;
  background: rgba(255,255,255,0.1);
  border: 1px solid var(--primary);
  border-radius: 0.5rem;
  color: white;
  width: 100%;
  margin-top: 0.5rem;
}

.mode-select option {
  background: var(--background);
}
</style>
</head>
<body>
<div class="game-container">
  <h1>Free Fire Sensitivity Trainer</h1>
  
  <div class="settings-panel">
    <div class="setting-group">
      <h3>General Sensitivity</h3>
      <div class="sensitivity-display">
        General: <span id="general-sensitivity-value">50</span>%
      </div>
      <input type="range" min="1" max="100" value="50" class="sensitivity-slider" id="general-sensitivity">
    </div>
    
    <div class="setting-group">
      <h3>Red Dot Sensitivity</h3>
      <div class="sensitivity-display">
        Red Dot: <span id="red-dot-sensitivity-value">50</span>%
      </div>
      <input type="range" min="1" max="100" value="50" class="sensitivity-slider" id="red-dot-sensitivity">
    </div>
    
    <div class="setting-group">
      <h3>2X Scope Sensitivity</h3>
      <div class="sensitivity-display">
        2X: <span id="2x-sensitivity-value">50</span>%
      </div>
      <input type="range" min="1" max="100" value="50" class="sensitivity-slider" id="2x-sensitivity">
    </div>
    
    <div class="setting-group">
      <h3>Training Mode</h3>
      <select class="mode-select" id="training-mode">
        <option value="static">Static Targets</option>
        <option value="moving">Moving Targets</option>
        <option value="peek">Peek Training</option>
      </select>
    </div>
  </div>
  
  <div class="target-area" id="target-area">
    <div class="target" id="target"></div>
  </div>
  
  <div class="stats">
    <div class="stat-card">
      <div>Hits: <span id="hits">0</span></div>
    </div>
    <div class="stat-card">
      <div>Accuracy: <span id="accuracy">0</span>%</div>
    </div>
    <div class="stat-card">
      <div>Time: ∞</div>
    </div>
  </div>
  
  <div class="controls">
    <button id="start-btn">Start Training</button>
    <button id="reset-btn">Reset</button>
  </div>
</div>

<script>
let hits = 0;
let shots = 0;
let isGameActive = false;
let currentMode = 'static';
let moveInterval;

const targetArea = document.getElementById('target-area');
const target = document.getElementById('target');
const generalSensitivity = document.getElementById('general-sensitivity');
const redDotSensitivity = document.getElementById('red-dot-sensitivity');
const scope2xSensitivity = document.getElementById('2x-sensitivity');
const modeSelect = document.getElementById('training-mode');
const startBtn = document.getElementById('start-btn');
const resetBtn = document.getElementById('reset-btn');

function updateSensitivityDisplay(sliderId, valueId) {
  const slider = document.getElementById(sliderId);
  const value = document.getElementById(valueId);
  value.textContent = slider.value;
}

function updateAllSensitivities() {
  updateSensitivityDisplay('general-sensitivity', 'general-sensitivity-value');
  updateSensitivityDisplay('red-dot-sensitivity', 'red-dot-sensitivity-value');
  updateSensitivityDisplay('2x-sensitivity', '2x-sensitivity-value');
  
  const sensitivity = generalSensitivity.value;
  target.style.transition = `all ${0.3 - (sensitivity/200)}s ease`;
}

function moveTarget() {
  const maxX = targetArea.clientWidth - 30;
  const maxY = targetArea.clientHeight - 30;
  const randomX = Math.random() * maxX;
  const randomY = Math.random() * maxY;
  
  target.style.left = `${randomX}px`;
  target.style.top = `${randomY}px`;
}

function startMovingTarget() {
  clearInterval(moveInterval);
  moveInterval = setInterval(() => {
    if (isGameActive) {
      moveTarget();
    }
  }, 1000);
}

function updateStats() {
  document.getElementById('hits').textContent = hits;
  document.getElementById('accuracy').textContent = Math.round((hits / shots) * 100) || 0;
}

function handleShot(e) {
  if (!isGameActive) return;
  
  shots++;
  const targetRect = target.getBoundingClientRect();
  const areaRect = targetArea.getBoundingClientRect();
  
  const x = e.clientX - areaRect.left;
  const y = e.clientY - areaRect.top;
  
  const targetX = targetRect.left - areaRect.left + targetRect.width/2;
  const targetY = targetRect.top - areaRect.top + targetRect.height/2;
  
  const distance = Math.sqrt(Math.pow(x - targetX, 2) + Math.pow(y - targetY, 2));
  
  if (distance < 20) {
    hits++;
    target.classList.add('hit');
    setTimeout(() => {
      target.classList.remove('hit');
      if (currentMode === 'static') {
        moveTarget();
      }
    }, 300);
  }
  
  updateStats();
}

function startGame() {
  isGameActive = true;
  hits = 0;
  shots = 0;
  updateStats();
  currentMode = modeSelect.value;
  
  if (currentMode === 'moving') {
    startMovingTarget();
  } else {
    clearInterval(moveInterval);
    moveTarget();
  }
  
  startBtn.disabled = true;
}

function resetGame() {
  isGameActive = false;
  hits = 0;
  shots = 0;
  clearInterval(moveInterval);
  startBtn.disabled = false;
  updateStats();
  target.style.left = '50%';
  target.style.top = '50%';
}

generalSensitivity.addEventListener('input', updateAllSensitivities);
redDotSensitivity.addEventListener('input', updateAllSensitivities);
scope2xSensitivity.addEventListener('input', updateAllSensitivities);
targetArea.addEventListener('click', handleShot);
startBtn.addEventListener('click', startGame);
resetBtn.addEventListener('click', resetGame);
modeSelect.addEventListener('change', () => {
  if (isGameActive) {
    currentMode = modeSelect.value;
    if (currentMode === 'moving') {
      startMovingTarget();
    } else {
      clearInterval(moveInterval);
    }
  }
});

// Initial setup
updateAllSensitivities();
</script>
</body></html>
<!--
**tweakswhlte/tweakswhlte** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
