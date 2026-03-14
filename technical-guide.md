# Technical Guide - Dr.C Mandelbrot Explorer

## Table of Contents

1. [System Architecture](#system-architecture)
2. [Technology Stack](#technology-stack)
3. [Code Structure](#code-structure)
4. [Csound Integration](#csound-integration)
5. [Audio Engine Details](#audio-engine-details)
6. [Fractal Rendering](#fractal-rendering)
7. [Parameter Mapping System](#parameter-mapping-system)
8. [Quantization Algorithms](#quantization-algorithms)
9. [Preset System](#preset-system)
10. [Performance Optimization](#performance-optimization)
11. [Extending the System](#extending-the-system)

---

## System Architecture

### High-Level Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      Browser Runtime                         │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────┐         ┌──────────────┐                  │
│  │   HTML/CSS   │◄────────┤  JavaScript  │                  │
│  │   UI Layer   │         │  Controller  │                  │
│  └──────────────┘         └──────┬───────┘                  │
│                                   │                           │
│  ┌──────────────┐         ┌──────▼───────┐                  │
│  │   Canvas 2D  │◄────────┤   Fractal    │                  │
│  │   Renderer   │         │   Engine     │                  │
│  └──────────────┘         └──────────────┘                  │
│                                   │                           │
│  ┌──────────────┐         ┌──────▼───────┐                  │
│  │ Csound WASM  │◄────────┤    Audio     │                  │
│  │   v7.0.0     │         │   Engine     │                  │
│  └──────┬───────┘         └──────────────┘                  │
│         │                                                     │
│  ┌──────▼───────┐         ┌──────────────┐                  │
│  │  Web Audio   │─────────┤  localStorage│                  │
│  │     API      │         │   (Presets)  │                  │
│  └──────────────┘         └──────────────┘                  │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow

```
User Input
    ↓
UI Controls → State Update → Fractal Render
    ↓                             ↓
Parameter Mapping ←──── Fractal Data (x,y,iter,smooth,angle,mag)
    ↓
Quantization (Pitch/Rhythm)
    ↓
Csound Score Events (i-statements)
    ↓
Csound Synthesis (11 instruments)
    ↓
Global Effects (4 buses)
    ↓
Web Audio API
    ↓
Audio Output
```

---

## Technology Stack

### Core Technologies

**Frontend:**
- Pure JavaScript (ES6+)
- HTML5 Canvas 2D API
- WebGL (optional GPU rendering)
- CSS3 (custom properties, gradients, flexbox, grid)

**Audio:**
- Csound WASM v7.0.0-beta26
- Web Audio API
- AudioWorklet (via Csound)

**Storage:**
- localStorage (preset persistence)
- JSON (preset serialization)

**Math:**
- Native JavaScript complex number operations
- Iterative fractal algorithms
- Musical scale algorithms

### Browser Requirements

**Minimum Versions:**
- Chrome/Edge: 90+
- Firefox: 88+
- Safari: 14+

**Required APIs:**
- WebAssembly 1.0
- Web Audio API
- Canvas 2D / WebGL
- localStorage
- ES6+ JavaScript

---

## Code Structure

### File Organization

```
Dr.C-Mandelbrot-Explorer/
├── app-v2.1.html           # Main application (7568 lines)
├── index.html              # Landing page
├── quickstart.html         # Quick start guide
├── users-guide.md          # User documentation
├── technical-guide.md      # Technical documentation
└── (Browser loads Csound WASM from CDN)
```

### app-v2.1.html Structure

**Lines 1-550: HTML Structure**
```html
<!DOCTYPE html>
<head>
  - Meta tags
  - CSS (inline, lines 7-550)
</head>
<body>
  - Left Column (Fractal Controls)
  - Center (Canvas)
  - Right Column (Audio Controls)
</body>
```

**Lines 551-1750: HTML Controls**
- Audio Control section
- Randomize button
- Preset management UI
- Fractal controls (mode, zoom, parameters)
- Animation controls
- Synthesis type selector
- Quantization controls (key, scale, rhythm)
- Master volume
- Envelope controls
- Global effects (Reverb, Echo, Chorus, Flanger)
- Musical parameters
- Parameter mapping dropdowns
- Effect mapping dropdowns

**Lines 1751-1890: Csound Orchestra**
```csound
<CsoundSynthesizer>
<CsOptions>
  -n -d -m0
</CsOptions>
<CsInstruments>
  sr = 44100
  ksmps = 32
  nchnls = 2
  0dbfs = 1
  
  ; 4 global audio buses
  ; 11 synthesis instruments (1-11)
  ; 4 effect instruments (996-999)
</CsInstruments>
<CsScore>
  ; Function table definitions
</CsScore>
</CsoundSynthesizer>
```

**Lines 1891-7568: JavaScript**

*Initialization (lines 1891-2350)*
- Csound WASM loading from CDN
- Orchestra compilation
- Control channel setup
- Event listener registration

*Fractal Rendering (lines 2600-3100)*
- 2D Mandelbrot/Julia algorithms
- Color scheme functions
- Canvas rendering
- Zoom/pan handlers

*Audio Loop (lines 4200-4900)*
- Fractal data filtering (5-95% maxIter)
- Density filtering
- Parameter extraction
- Mapping application
- Quantization
- Csound score generation

*Quantization Functions (lines 4280-4450)*
- `quantizePitch()` - Scale quantization
- `quantizeDuration()` - Rhythm quantization

*Utility Functions (lines 3027-3550)*
- `setSliderValue()` - UI helper
- `setControlValue()` - UI helper
- `updateStatus()` - Status bar
- `updateMappingDisplay()` - Parameter mapping display

*Preset System (lines 3550-3900)*
- `savePreset()` - Capture state to localStorage
- `loadSavedPreset()` - Restore state from localStorage
- `deletePreset()` - Remove preset
- `refreshPresetList()` - Update dropdown

*Factory Presets (lines 5300-7540)*
- `initializeFactoryPresets()` - 45 preset definitions
- Called on page load

*Application Lifecycle (lines 7540-7568)*
- `initializeFactoryPresets()` call
- `refreshPresetList()` call
- `init()` call
- Csound auto-initialization (1 second delay)

---

## Csound Integration

### Loading Csound WASM

```javascript
// Lines 1891-1920
const script = document.createElement("script");
script.src =
  "https://unpkg.com/@csound/browser@7.0.0-beta26/dist/index.js";
script.type = "module";

script.onload = async () => {
  const { Csound } = await import(
    "https://unpkg.com/@csound/browser@7.0.0-beta26/dist/index.js"
  );
  
  csound = await Csound();
  await initCsound();
};
```

### Csound Initialization Flow

```javascript
async function initCsound() {
  // 1. Set audio options
  await csound.setOption("-n");  // No audio output device (Web Audio)
  await csound.setOption("-d");  // Suppress displays
  await csound.setOption("-m0"); // Message level 0

  // 2. Compile orchestra
  const orchestra = document.getElementById("csoundCode").textContent;
  await csound.compileOrc(orchestra);

  // 3. Read score (function tables)
  await csound.readScore("f1 0 16384 10 1");

  // 4. Start Csound
  await csound.start();

  // 5. Create function table for oscillators
  await csound.readScore("f1 0 16384 10 1");

  // 6. Initialize control channels
  await csound.setControlChannel("masterVolume", 0.5);
  await csound.setControlChannel("reverbMix", 0.3);
  // ... (all 15+ control channels)

  // 7. Start effect instruments (run indefinitely)
  await csound.inputMessage("i 998 0 -1"); // Echo
  await csound.inputMessage("i 997 0 -1"); // Chorus
  await csound.inputMessage("i 996 0 -1"); // Flanger
  await csound.inputMessage("i 999 0 -1"); // Reverb

  // 8. Resume audio context (required for playback)
  const audioContext = await csound.getAudioContext();
  await audioContext.resume();

  // 9. Mark ready
  csoundReady = true;
}
```

### Control Channels

Csound and JavaScript communicate via **control channels** (k-rate variables):

**JavaScript → Csound:**
```javascript
await csound.setControlChannel("masterVolume", 0.7);
```

**Csound reads:**
```csound
kMasterVol chnget "masterVolume"
```

**All Control Channels:**
```javascript
masterVolume    // 0.0 - 1.0
reverbMix       // 0.0 - 1.0
echoTime        // 0.01 - 2.0 seconds
echoFeedback    // 0.0 - 0.9
echoMix         // 0.0 - 1.0
chorusRate      // 0.1 - 10.0 Hz
chorusDepth     // 0.0 - 20.0 ms
chorusMix       // 0.0 - 1.0
flangerRate     // 0.1 - 10.0 Hz
flangerDepth    // 0.0 - 10.0 ms
flangerFeedback // -0.8 - 0.8
flangerMix      // 0.0 - 1.0
```

### Score Events (i-statements)

JavaScript sends **score events** to trigger notes:

```javascript
const scoreEvent = `i ${instrNum} 0 ${dur} ${freq} ${amp} ${pan} ${brightness} ${attack} ${release} ${param1} ${param2}`;
await csound.inputMessage(scoreEvent);
```

**Example:**
```javascript
// Instrument 3 (Additive), duration 0.5s, 440Hz, amp 0.3, pan center
await csound.inputMessage("i 3 0 0.5 440 0.3 0.5 0.7 5 200 0.5 0.5");
```

**p-field Mapping:**
```
p1  = instrument number (1-11)
p2  = start time (0 = now)
p3  = duration (seconds)
p4  = frequency (Hz)
p5  = amplitude (0-1)
p6  = pan (0=left, 1=right)
p7  = brightness (0-1, filter cutoff)
p8  = attack (ms)
p9  = release (ms)
p10 = extra param 1 (smoothness, 0-1)
p11 = extra param 2 (angle, 0-1)
```

---

## Audio Engine Details

### Csound Orchestra Architecture

**Header:**
```csound
sr = 44100      ; Sample rate
ksmps = 32      ; k-rate samples (audio block size)
nchnls = 2      ; Stereo output
0dbfs = 1       ; 0 dB = 1.0 (normalized)
seed 0          ; Random seed
```

**Global Audio Buses:**
```csound
gaReverb init 0    ; Reverb send bus
gaEcho init 0      ; Echo send bus
gaChorus init 0    ; Chorus send bus
gaFlanger init 0   ; Flanger send bus
```

### Instrument Template

All 11 synthesis instruments follow this structure:

```csound
instr N  ; Instrument number (1-11)
  ; Read p-fields
  iFreq = p4         ; Frequency (Hz)
  iAmp = p5          ; Amplitude (0-1)
  iPan = p6          ; Pan (0-1)
  iBright = p7       ; Brightness (0-1)
  iAttack = p8       ; Attack (ms)
  iRelease = p9      ; Release (ms)
  iParam1 = p10      ; Extra parameter 1
  iParam2 = p11      ; Extra parameter 2

  ; Convert ms to seconds
  iAtt = iAttack / 1000
  iRel = iRelease / 1000

  ; Envelope
  kEnv = linsegr:k(0, iAtt, 1, iRel, 0)

  ; Synthesis algorithm
  aSig = [synthesis code here]

  ; Apply envelope and amplitude
  aSig = aSig * kEnv * iAmp

  ; Pan to stereo
  aL = aSig * sqrt(1 - iPan)
  aR = aSig * sqrt(iPan)

  ; Send to effect buses
  gaReverb = gaReverb + (aL + aR) * 0.5 * 0.2  ; 20% reverb send
  gaEcho = gaEcho + (aL + aR) * 0.5 * 0.1      ; 10% echo send
  gaChorus = gaChorus + (aL + aR) * 0.5 * 0.1  ; 10% chorus send
  gaFlanger = gaFlanger + (aL + aR) * 0.5 * 0.1 ; 10% flanger send

  ; Direct output
  out(aL, aR)
endin
```

### Synthesis Algorithm Examples

**1. Pluck (Karplus-Strong):**
```csound
; Excitation: short noise burst
aNoise = rand:a(1)
aExcite = aNoise * linseg:a(1, 0.001, 0)

; Delay line (string simulation)
kCutoff = 2000 + iBright * 8000
aFiltered = tone:a(aExcite, kCutoff)
aPluck = pluck:a(aFiltered, iFreq, iFreq, 1, 1)

aSig = aPluck * 2
```

**2. FM Bell:**
```csound
; 2-operator FM
kModIndex = 2 + iBright * 3
aModulator = oscili:a(kModIndex * iFreq, iFreq * 2)
aCarrier = oscili:a(0.6, iFreq + aModulator)

aSig = aCarrier
```

**3. Additive (8 partials):**
```csound
; Harmonic series
a1 = oscili:a(0.5, iFreq * 1)
a2 = oscili:a(0.3, iFreq * 2)
a3 = oscili:a(0.2, iFreq * 3)
a4 = oscili:a(0.15, iFreq * 4)
a5 = oscili:a(0.1, iFreq * 5)
a6 = oscili:a(0.08, iFreq * 6)
a7 = oscili:a(0.06, iFreq * 7)
a8 = oscili:a(0.05, iFreq * 8)

aSig = (a1 + a2 + a3 + a4 + a5 + a6 + a7 + a8) * 0.3
```

**4. Subtractive (Moog Filter):**
```csound
; Sawtooth source
aSaw = vco2:a(0.8, iFreq)

; Moog ladder filter (24dB/octave)
kCutoff = 200 + iBright * 4800
kRes = 0.7
aFiltered = moogladder:a(aSaw, kCutoff, kRes)

aSig = aFiltered * 1.2
```

### Effect Implementations

**Reverb (Instrument 999):**
```csound
instr 999
  kMix = chnget:k("reverbMix")
  
  ; Freeverb algorithm
  aL, aR = freeverb:aa(gaReverb, gaReverb, 0.85, 0.7)
  
  ; Mix control
  aOutL = gaReverb * (1 - kMix) + aL * kMix
  aOutR = gaReverb * (1 - kMix) + aR * kMix
  
  out(aOutL * 0.7, aOutR * 0.7)
  
  ; Clear bus
  gaReverb = 0
endin
```

**Echo (Instrument 998):**
```csound
instr 998
  kTime = chnget:k("echoTime")
  kFeedback = chnget:k("echoFeedback")
  kMix = chnget:k("echoMix")
  
  ; Variable delay with feedback
  aDelayed = vdelay:a(gaEcho, kTime * 1000, 2000)
  aFeedback = aDelayed * kFeedback
  
  ; Mix
  aOut = gaEcho * (1 - kMix) + aDelayed * kMix
  
  out(aOut, aOut)
  
  ; Feedback loop
  gaEcho = gaEcho + aFeedback
  gaEcho = 0  ; Clear for next k-cycle
endin
```

**Chorus (Instrument 997):**
```csound
instr 997
  kRate = chnget:k("chorusRate")
  kDepth = chnget:k("chorusDepth")
  kMix = chnget:k("chorusMix")
  
  ; Multi-tap delay with LFO
  kLFO1 = oscili:k(kDepth, kRate)
  kLFO2 = oscili:k(kDepth, kRate * 1.3, 0.3)
  kLFO3 = oscili:k(kDepth, kRate * 0.7, 0.7)
  
  aTap1 = vdelay:a(gaChorus, 10 + kLFO1, 50)
  aTap2 = vdelay:a(gaChorus, 15 + kLFO2, 50)
  aTap3 = vdelay:a(gaChorus, 20 + kLFO3, 50)
  
  aChorus = (aTap1 + aTap2 + aTap3) / 3
  aOut = gaChorus * (1 - kMix) + aChorus * kMix
  
  out(aOut, aOut)
  gaChorus = 0
endin
```

**Flanger (Instrument 996):**
```csound
instr 996
  kRate = chnget:k("flangerRate")
  kDepth = chnget:k("flangerDepth")
  kFeedback = chnget:k("flangerFeedback")
  kMix = chnget:k("flangerMix")
  
  ; Short delay (0-10ms) with LFO
  kLFO = oscili:k(kDepth, kRate)
  aDelayed = vdelay:a(gaFlanger, kLFO, 20)
  
  ; Feedback
  aFB = aDelayed * kFeedback
  
  ; Comb filtering (dry + delayed)
  aFlange = gaFlanger + aDelayed
  aOut = gaFlanger * (1 - kMix) + aFlange * kMix
  
  out(aOut, aOut)
  
  gaFlanger = gaFlanger + aFB
  gaFlanger = 0
endin
```

### Audio Loop Logic

**High-Level Flow:**

```javascript
async function audioLoop() {
  // 1. Check state
  if (!audioPlaying || !csoundReady) return;
  
  // 2. Resume audio context (CRITICAL)
  const audioContext = await csound.getAudioContext();
  if (audioContext.state === "suspended") {
    await audioContext.resume();
  }
  
  // 3. Check for fractal data
  if (fractalData.length === 0) {
    setTimeout(() => audioLoop(), 1000);
    return;
  }
  
  // 4. Clear scheduled notes
  scheduledTimeouts.forEach(id => clearTimeout(id));
  scheduledTimeouts = [];
  
  // 5. Get UI settings
  const synthType = document.getElementById("synthType").value;
  const tempo = parseFloat(document.getElementById("tempo").value);
  const baseFreq = parseFloat(document.getElementById("baseFreq").value);
  const pitchRange = parseFloat(document.getElementById("pitchRange").value);
  const density = parseFloat(document.getElementById("noteDensity").value) / 100;
  const durationScale = parseFloat(document.getElementById("durationScale").value);
  const attack = parseFloat(document.getElementById("attack").value);
  const release = parseFloat(document.getElementById("release").value);
  
  // 6. Get quantization settings
  const pitchQuantization = document.getElementById("pitchQuantization").value;
  const durationQuantization = document.getElementById("durationQuantization").value;
  const keyRoot = document.getElementById("keyRoot").value;
  
  // 7. Get parameter mappings
  const iterMap = document.getElementById("iterMap").value;
  const xMap = document.getElementById("xMap").value;
  const yMap = document.getElementById("yMap").value;
  // ... (all 6 mappings)
  
  // 8. Filter fractal data (5-95% of maxIter)
  const minIter = config.maxIter * 0.05;
  const maxIter = config.maxIter * 0.95;
  const interestingPoints = fractalData.filter(
    p => p.iter >= minIter && p.iter < maxIter
  );
  
  // 9. Apply density filter
  const filtered = interestingPoints.filter(() => Math.random() < density);
  
  // 10. Shuffle for spatial randomness
  for (let i = filtered.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [filtered[i], filtered[j]] = [filtered[j], filtered[i]];
  }
  
  // 11. Sample (max 50 notes)
  const sample = filtered.slice(0, Math.min(50, filtered.length));
  
  // 12. Calculate timing
  const beatDuration = 60 / tempo;
  let time = 0;
  
  // 13. For each note...
  for (let i = 0; i < sample.length; i++) {
    const point = sample[i];
    
    // Extract fractal data (normalized 0-1)
    const iterRatio = point.iterNorm || point.iter / config.maxIter;
    const xRatio = point.x / canvas2D.width;
    const yRatio = 1 - point.y / canvas2D.height;
    const smoothness = point.smoothness || 0.5;
    const angle = point.angle || 0.5;
    const magnitude = Math.min(1, (point.magnitude || 0) / 3);
    
    // Initialize parameters with defaults
    let freq = baseFreq * Math.pow(2, iterRatio * pitchRange);
    let amp = 0.3;
    let pan = xRatio;
    let brightness = iterRatio;
    let dur = 0.5 * durationScale;
    let noteAttack = attack;
    let noteRelease = release;
    let noteSpacing = beatDuration * 0.25;
    
    // Apply mappings (see Parameter Mapping section)
    applyMapping("iter", iterRatio, iterMap);
    applyMapping("x", xRatio, xMap);
    applyMapping("y", yRatio, yMap);
    applyMapping("smooth", smoothness, smoothMap);
    applyMapping("angle", angle, angleMap);
    applyMapping("mag", magnitude, magMap);
    
    // Clamp to safe ranges
    freq = Math.max(20, Math.min(20000, freq));
    amp = Math.max(0.05, Math.min(0.8, amp));
    pan = Math.max(0, Math.min(1, pan));
    brightness = Math.max(0, Math.min(1, brightness));
    dur = Math.max(0.05, Math.min(5, dur));
    noteAttack = Math.max(1, Math.min(500, noteAttack));
    noteRelease = Math.max(10, Math.min(2000, noteRelease));
    noteSpacing = Math.max(0.01, Math.min(2, noteSpacing));
    
    // Apply quantization
    freq = quantizePitch(freq, pitchQuantization, baseFreq, keyRoot);
    dur = quantizeDuration(dur, durationQuantization, tempo);
    
    // Build score event
    const instrNum = instrMap[synthType] || 1;
    const scoreEvent = `i ${instrNum} 0 ${dur.toFixed(3)} ${freq.toFixed(2)} ${amp.toFixed(3)} ${pan.toFixed(3)} ${brightness.toFixed(3)} ${noteAttack} ${noteRelease} ${smoothness.toFixed(3)} ${angle.toFixed(3)}`;
    
    // Schedule note
    const timeoutId = setTimeout(() => {
      if (audioPlaying && csound && csoundReady) {
        csound.inputMessage(scoreEvent);
        
        // Apply effect mappings (real-time)
        // ... (see Effect Mapping section)
        
        // Visual feedback
        highlightPoint(point, dur, freq, amp);
      }
    }, time * 1000);
    
    scheduledTimeouts.push(timeoutId);
    time += noteSpacing;
  }
  
  // 14. Schedule next loop
  setTimeout(() => {
    if (fractalChanged) {
      fractalChanged = false;
      audioLoop();  // Restart immediately
    } else {
      audioLoop();  // Wait for current notes to finish
    }
  }, time * 1000 + 100);
}
```

---

## Fractal Rendering

### Mandelbrot Algorithm

```javascript
function calculateMandelbrot(x, y, maxIter) {
  // Convert pixel coordinates to complex plane
  const real = (x / width - 0.5) * 4 / zoom + centerX;
  const imag = (y / height - 0.5) * 3 / zoom + centerY;
  
  let zReal = 0;
  let zImag = 0;
  let iter = 0;
  
  // Iterate: z = z² + c
  while (iter < maxIter) {
    const zReal2 = zReal * zReal;
    const zImag2 = zImag * zImag;
    
    // Escape condition: |z| > 2
    if (zReal2 + zImag2 > 4) break;
    
    // z² = (zReal + i*zImag)²
    //    = zReal² - zImag² + 2i*zReal*zImag
    const newReal = zReal2 - zImag2 + real;
    const newImag = 2 * zReal * zImag + imag;
    
    zReal = newReal;
    zImag = newImag;
    iter++;
  }
  
  // Calculate additional fractal parameters
  const magnitude = Math.sqrt(zReal * zReal + zImag * zImag);
  const angle = (Math.atan2(zImag, zReal) + Math.PI) / (2 * Math.PI);
  
  // Smooth iteration (continuous coloring)
  let iterNorm = iter;
  if (iter < maxIter) {
    iterNorm = iter + 1 - Math.log2(Math.log2(magnitude));
  }
  iterNorm = iterNorm / maxIter;
  
  return {
    iter,
    iterNorm,
    magnitude,
    angle,
    x,
    y
  };
}
```

### Julia Algorithm

```javascript
function calculateJulia(x, y, maxIter, juliaReal, juliaImag) {
  // Start z at pixel position
  let zReal = (x / width - 0.5) * 4 / zoom + centerX;
  let zImag = (y / height - 0.5) * 3 / zoom + centerY;
  
  // Fixed c parameter
  const cReal = juliaReal;
  const cImag = juliaImag;
  
  let iter = 0;
  
  // Iterate: z = z² + c (where c is constant)
  while (iter < maxIter) {
    const zReal2 = zReal * zReal;
    const zImag2 = zImag * zImag;
    
    if (zReal2 + zImag2 > 4) break;
    
    const newReal = zReal2 - zImag2 + cReal;
    const newImag = 2 * zReal * zImag + cImag;
    
    zReal = newReal;
    zImag = newImag;
    iter++;
  }
  
  const magnitude = Math.sqrt(zReal * zReal + zImag * zImag);
  const angle = (Math.atan2(zImag, zReal) + Math.PI) / (2 * Math.PI);
  
  let iterNorm = iter;
  if (iter < maxIter) {
    iterNorm = iter + 1 - Math.log2(Math.log2(magnitude));
  }
  iterNorm = iterNorm / maxIter;
  
  return {
    iter,
    iterNorm,
    magnitude,
    angle,
    x,
    y
  };
}
```

### Smoothness Calculation

```javascript
function calculateSmoothness(x, y, iter, data, width) {
  // Sample 4 neighbors
  const neighbors = [
    getIterAt(x - 1, y, data, width),
    getIterAt(x + 1, y, data, width),
    getIterAt(x, y - 1, data, width),
    getIterAt(x, y + 1, data, width)
  ];
  
  // Calculate variance
  const avg = neighbors.reduce((a, b) => a + b, 0) / 4;
  const variance = neighbors.reduce((sum, n) => sum + Math.pow(n - avg, 2), 0) / 4;
  
  // Normalize to 0-1 (higher = more textured)
  const smoothness = 1 - Math.min(1, variance / 100);
  
  return smoothness;
}
```

### Render Loop

```javascript
function render2D() {
  const width = canvas2D.width;
  const height = canvas2D.height;
  const imageData = ctx2D.createImageData(width, height);
  const data = imageData.data;
  
  fractalData = [];
  
  // For each pixel...
  for (let y = 0; y < height; y++) {
    for (let x = 0; x < width; x++) {
      const idx = (y * width + x) * 4;
      
      // Calculate fractal
      const result = config.mode === "mandelbrot"
        ? calculateMandelbrot(x, y, config.maxIter)
        : calculateJulia(x, y, config.maxIter, config.juliaReal, config.juliaImag);
      
      // Store for audio
      fractalData.push(result);
      
      // Color mapping
      const color = getColor(result.iterNorm, result.iter, config.maxIter);
      data[idx] = color.r;
      data[idx + 1] = color.g;
      data[idx + 2] = color.b;
      data[idx + 3] = 255;
    }
  }
  
  // Calculate smoothness (post-process)
  for (let i = 0; i < fractalData.length; i++) {
    const point = fractalData[i];
    point.smoothness = calculateSmoothness(point.x, point.y, point.iter, fractalData, width);
  }
  
  // Render to canvas
  ctx2D.putImageData(imageData, 0, 0);
  
  // Trigger audio update
  fractalChanged = true;
}
```

---

## Parameter Mapping System

### Mapping Function

```javascript
function applyMapping(source, value, mapping) {
  if (mapping === "pitch") {
    freq = baseFreq * Math.pow(2, value * pitchRange);
  } else if (mapping === "amplitude") {
    amp = 0.05 + value * 0.75;
  } else if (mapping === "pan") {
    pan = value;
  } else if (mapping === "brightness") {
    brightness = value;
  } else if (mapping === "duration") {
    dur = (0.05 + value * 2.95) * durationScale;
  } else if (mapping === "tempo") {
    noteSpacing = beatDuration * (0.02 + value * 1.98);
  } else if (mapping === "attack") {
    noteAttack = 1 + Math.pow(value, 2) * 999;
  } else if (mapping === "release") {
    noteRelease = 5 + Math.pow(value, 1.5) * 2995;
  }
}
```

### Mapping Ranges

**Pitch:**
```javascript
freq = baseFreq * 2^(value * pitchRange)
// value: 0-1
// pitchRange: 0.5-4 octaves
// Example: baseFreq=220, pitchRange=2, value=0.5
//   freq = 220 * 2^(0.5 * 2) = 220 * 2 = 440 Hz
```

**Amplitude:**
```javascript
amp = 0.05 + value * 0.75
// Range: 0.05 - 0.8
// Never silent, never clipping
```

**Pan:**
```javascript
pan = value
// Range: 0 (left) to 1 (right)
// Applied as: aL = aSig * sqrt(1 - pan)
//             aR = aSig * sqrt(pan)
// Equal-power panning
```

**Brightness:**
```javascript
brightness = value
// Range: 0-1
// Used for filter cutoff: cutoff = 200 + brightness * 4800 Hz
```

**Duration:**
```javascript
dur = (0.05 + value * 2.95) * durationScale
// Range: 0.05s to 3s (before scale)
// With scale: 0.005s to 9s
```

**Tempo/Spacing:**
```javascript
noteSpacing = beatDuration * (0.02 + value * 1.98)
// Range: 0.02 to 2.0 beats
// Fast notes to slow notes
```

**Attack:**
```javascript
noteAttack = 1 + value^2 * 999
// Range: 1ms to 1000ms
// Squared for more sharp attacks
```

**Release:**
```javascript
noteRelease = 5 + value^1.5 * 2995
// Range: 5ms to 3000ms
// Power curve for more sustain
```

---

## Quantization Algorithms

### Pitch Quantization

```javascript
function quantizePitch(freq, scaleType, baseFreq, keyRoot) {
  if (scaleType === "none") return freq;
  
  // If atonal, no key transposition
  if (keyRoot === "atonal") return freq;
  
  // Convert frequency to MIDI note number
  const midiNote = 69 + 12 * Math.log2(freq / 440);
  const octave = Math.floor(midiNote / 12);
  const pitch = Math.round(midiNote) % 12;
  
  // Define scale patterns (in semitones from root)
  const scales = {
    chromatic: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11],
    major: [0, 2, 4, 5, 7, 9, 11],
    minor: [0, 2, 3, 5, 7, 8, 10],
    // ... (all 14 scales)
  };
  
  // Key offsets
  const keyOffsets = {
    C: 0, "C#": 1, D: 2, "D#": 3, E: 4, F: 5,
    "F#": 6, G: 7, "G#": 8, A: 9, "A#": 10, B: 11
  };
  
  const keyOffset = keyOffsets[keyRoot];
  const scale = scales[scaleType] || scales.chromatic;
  
  // Transpose scale to selected key
  const transposedScale = scale.map(note => (note + keyOffset) % 12);
  
  // Find nearest scale degree
  let closestPitch = transposedScale[0];
  let minDist = Math.abs(pitch - transposedScale[0]);
  
  for (const scalePitch of transposedScale) {
    // Check current octave, previous, and next
    for (const offset of [-12, 0, 12]) {
      const testPitch = scalePitch + offset;
      const dist = Math.abs(pitch - testPitch);
      if (dist < minDist) {
        minDist = dist;
        closestPitch = testPitch;
      }
    }
  }
  
  // Convert back to frequency
  const quantizedMidi = octave * 12 + closestPitch;
  return 440 * Math.pow(2, (quantizedMidi - 69) / 12);
}
```

### Microtonal Quantization

**Bohlen-Pierce (13-ET, tritave 3:1):**
```javascript
if (scaleType === "bohlen-pierce") {
  const stepsPerTritave = 13;
  const tritaveRatio = 3;
  const stepRatio = Math.pow(tritaveRatio, 1 / stepsPerTritave);
  const step = Math.round(Math.log(freq / baseFreq) / Math.log(stepRatio));
  return baseFreq * Math.pow(stepRatio, step);
}
```

**Equal Temperament (24-ET, 19-ET, 31-ET, 53-ET):**
```javascript
if (scaleType === "19tet") {
  const stepsPerOctave = 19;
  const stepRatio = Math.pow(2, 1 / stepsPerOctave);
  const step = Math.round(Math.log2(freq / baseFreq) * stepsPerOctave);
  return baseFreq * Math.pow(stepRatio, step);
}
```

### Duration Quantization

```javascript
function quantizeDuration(dur, quantType, tempo) {
  if (quantType === "none") return dur;
  
  const beatDuration = 60 / tempo;
  let availableDurations = [];
  
  // Build palette based on type
  if (quantType === "quarter") {
    availableDurations = [1.0, 0.5, 0.25];  // Quarter, eighth, sixteenth
  } else if (quantType === "dotted-quarter") {
    availableDurations = [1.5, 0.75, 0.375];
  } else if (quantType === "triplet-eighth") {
    availableDurations = [1/6, 1/12, 1/24];
  }
  // ... (all 25 rhythm types)
  
  if (availableDurations.length === 0) return dur;
  
  // Find nearest duration
  const durInBeats = dur / beatDuration;
  let closestDur = availableDurations[0];
  let minDist = Math.abs(durInBeats - closestDur);
  
  for (const candidate of availableDurations) {
    const dist = Math.abs(durInBeats - candidate);
    if (dist < minDist) {
      minDist = dist;
      closestDur = candidate;
    }
  }
  
  return closestDur * beatDuration;
}
```

---

## Preset System

### Storage Format

```javascript
{
  "Preset Name": {
    // Fractal settings
    mode: "mandelbrot" | "julia",
    centerX: number,
    centerY: number,
    zoom: number,
    maxIter: number,
    juliaReal: number,
    juliaImag: number,
    colorScheme: string,
    animating: boolean,
    animSpeed: number,
    animPattern: string,
    
    // Synthesis
    synthType: string,
    tempo: string,
    baseFreq: string,
    pitchRange: string,
    noteDensity: string,
    durationScale: string,
    keyRoot: string,
    pitchQuantization: string,
    durationQuantization: string,
    attack: string,
    release: string,
    
    // Effects
    reverbMix: string,
    echoTime: string,
    echoFeedback: string,
    echoMix: string,
    chorusRate: string,
    chorusDepth: string,
    chorusMix: string,
    flangerRate: string,
    flangerDepth: string,
    flangerFeedback: string,
    flangerMix: string,
    
    // Mappings
    iterMap: string,
    xMap: string,
    yMap: string,
    smoothMap: string,
    angleMap: string,
    magMap: string,
    echoTimeMap: string,
    echoFeedbackMap: string,
    chorusRateMap: string,
    chorusDepthMap: string,
    flangerRateMap: string,
    flangerDepthMap: string
  }
}
```

### Save Implementation

```javascript
function savePreset() {
  const name = document.getElementById("presetName").value.trim();
  if (!name) {
    alert("Please enter a preset name");
    return;
  }
  
  // Capture all settings
  const preset = {
    // Fractal
    mode: config.mode,
    centerX: config.centerX,
    centerY: config.centerY,
    zoom: config.zoom,
    maxIter: config.maxIter,
    juliaReal: config.juliaReal,
    juliaImag: config.juliaImag,
    colorScheme: config.colorScheme,
    animating: config.animating,
    animSpeed: config.animSpeed,
    animPattern: config.animPattern,
    
    // Synthesis (read from DOM)
    synthType: document.getElementById("synthType").value,
    tempo: document.getElementById("tempo").value,
    // ... (all parameters)
  };
  
  // Load existing presets
  let presets = JSON.parse(localStorage.getItem("mandelbrotPresets") || "{}");
  
  // Add new preset
  presets[name] = preset;
  
  // Save to localStorage
  localStorage.setItem("mandelbrotPresets", JSON.stringify(presets));
  
  // Refresh dropdown
  refreshPresetList();
}
```

### Load Implementation

```javascript
function loadSavedPreset() {
  const select = document.getElementById("presetList");
  const name = select.value;
  
  if (!name || name === "none") {
    alert("Please select a preset to load");
    return;
  }
  
  const presets = JSON.parse(localStorage.getItem("mandelbrotPresets") || "{}");
  const preset = presets[name];
  
  if (!preset) {
    alert("Preset not found");
    return;
  }
  
  // Restore fractal settings
  if (preset.mode) {
    config.mode = preset.mode;
    document.getElementById("mode").value = preset.mode;
    document.getElementById("mode").dispatchEvent(new Event("change"));
  }
  if (preset.centerX !== undefined) config.centerX = preset.centerX;
  if (preset.centerY !== undefined) config.centerY = preset.centerY;
  if (preset.zoom !== undefined) config.zoom = preset.zoom;
  // ... (all fractal settings)
  
  // Re-render fractal
  render2D();
  updateInfo();
  
  // Restore audio settings (using helper functions)
  setControlValue("synthType", preset.synthType || "additive", false);
  document.getElementById("tempo").value = preset.tempo || "120";
  document.getElementById("tempoDisplay").textContent = preset.tempo || "120";
  // ... (all audio settings)
  
  // Update Csound control channels
  updateCsoundControls();
  
  // Update mapping display
  updateMappingDisplay();
  
  // Restart audio if playing
  if (audioPlaying) {
    fractalChanged = true;
  }
}
```

---

## Performance Optimization

### Fractal Rendering

**Canvas Size Optimization:**
```javascript
// Default: 1024x768
// For better performance: 800x600 or 640x480
canvas2D.width = 800;
canvas2D.height = 600;
```

**Iteration Count:**
```javascript
// Lower iterations = faster render
// 50-100: Fast, less detail
// 100-150: Balanced
// 200-500: Slow, maximum detail
```

**GPU Rendering (WebGL):**
```javascript
// Fragment shader (GLSL)
precision highp float;
uniform vec2 resolution;
uniform vec2 center;
uniform float zoom;
uniform int maxIter;

void main() {
  vec2 c = (gl_FragCoord.xy / resolution - 0.5) * 4.0 / zoom + center;
  vec2 z = vec2(0.0);
  
  int iter = 0;
  for (int i = 0; i < 500; i++) {
    if (i >= maxIter) break;
    if (dot(z, z) > 4.0) break;
    z = vec2(z.x*z.x - z.y*z.y, 2.0*z.x*z.y) + c;
    iter++;
  }
  
  float color = float(iter) / float(maxIter);
  gl_FragColor = vec4(vec3(color), 1.0);
}
```

### Audio Performance

**Note Density:**
```javascript
// Lower density = fewer notes = better performance
// 1-3%: Sparse, best performance
// 3-7%: Balanced
// 10-20%: Dense, high CPU
```

**Sample Limit:**
```javascript
// Maximum 50 notes per audio loop
const sample = filtered.slice(0, Math.min(50, filtered.length));
```

**Scheduled Timeouts:**
```javascript
// Clear old timeouts before scheduling new ones
scheduledTimeouts.forEach(id => clearTimeout(id));
scheduledTimeouts = [];
```

### Memory Management

**Fractal Data Array:**
```javascript
// Clear before re-render
fractalData = [];

// Typical size: 1024 * 768 = 786,432 points
// Each point: ~50 bytes (6 properties)
// Total: ~37 MB per render
```

**localStorage Limits:**
```javascript
// ~5-10 MB per domain
// Each preset: ~2-3 KB
// Maximum ~2000 presets (practical limit: ~100)
```

---

## Extending the System

### Adding a New Synthesis Engine

**Step 1: Add Csound Instrument**

```csound
instr 12  ; New instrument number
  iFreq = p4
  iAmp = p5
  iPan = p6
  iBright = p7
  iAttack = p8
  iRelease = p9
  iParam1 = p10
  iParam2 = p11

  iAtt = iAttack / 1000
  iRel = iRelease / 1000

  kEnv = linsegr:k(0, iAtt, 1, iRel, 0)

  ; YOUR SYNTHESIS ALGORITHM HERE
  aSig = oscili:a(0.5, iFreq)  ; Example: simple sine

  aSig = aSig * kEnv * iAmp

  aL = aSig * sqrt(1 - iPan)
  aR = aSig * sqrt(iPan)

  gaReverb = gaReverb + (aL + aR) * 0.5 * 0.2
  gaEcho = gaEcho + (aL + aR) * 0.5 * 0.1
  gaChorus = gaChorus + (aL + aR) * 0.5 * 0.1
  gaFlanger = gaFlanger + (aL + aR) * 0.5 * 0.1

  out(aL, aR)
endin
```

**Step 2: Add to HTML Dropdown**

```html
<select id="synthType">
  <!-- Existing options -->
  <option value="newsynthtype">New Synthesis Type</option>
</select>
```

**Step 3: Add to JavaScript Mapping**

```javascript
const instrMap = {
  pluck: 1,
  fm: 2,
  // ... existing
  newsynthtype: 12  // Your new instrument
};
```

**Step 4: Add to Randomize Function**

```javascript
const instruments = [
  "pluck", "fm", "additive", /* ... */ "newsynthtype"
];
```

**Step 5: Create Factory Presets**

```javascript
"New Synth: Preset 1": {
  synthType: "newsynthtype",
  // ... all other settings
}
```

### Adding a New Effect

**Step 1: Add Global Bus**

```csound
gaNewEffect init 0
```

**Step 2: Implement Effect Instrument**

```csound
instr 995
  kParam1 = chnget:k("newEffectParam1")
  kMix = chnget:k("newEffectMix")
  
  ; Effect algorithm
  aProcessed = [your effect here]
  
  aOut = gaNewEffect * (1 - kMix) + aProcessed * kMix
  out(aOut, aOut)
  
  gaNewEffect = 0
endin
```

**Step 3: Add to All Instruments**

```csound
gaNewEffect = gaNewEffect + (aL + aR) * 0.5 * 0.1
```

**Step 4: Add Control Channels**

```javascript
await csound.setControlChannel("newEffectParam1", 0.5);
await csound.setControlChannel("newEffectMix", 0.0);
```

**Step 5: Add HTML Controls**

```html
<div class="control-group">
  <label>New Effect Parameter
    <span class="value-display" id="newEffectParam1Display">0.5</span>
  </label>
  <input type="range" id="newEffectParam1" 
         min="0" max="1" step="0.01" value="0.5" disabled />
</div>
```

**Step 6: Add Event Listeners**

```javascript
document.getElementById("newEffectParam1").addEventListener("input", async (e) => {
  document.getElementById("newEffectParam1Display").textContent = e.target.value;
  if (csoundReady) {
    await csound.setControlChannel("newEffectParam1", parseFloat(e.target.value));
  }
});
```

### Adding a New Scale

**Step 1: Add to Dropdown**

```html
<option value="newscale">New Scale Name</option>
```

**Step 2: Add to Quantization Function**

```javascript
const scales = {
  // ... existing scales
  newscale: [0, 2, 3, 6, 7, 9, 10]  // Intervals in semitones
};
```

**Step 3: Add to Randomize Function**

```javascript
const pitchScales = [
  "none", "chromatic", /* ... */ "newscale"
];
```

### Adding a New Rhythm

**Step 1: Add to Dropdown**

```html
<option value="newrhythm">New Rhythm Type</option>
```

**Step 2: Add to Quantization Function**

```javascript
if (quantType === "newrhythm") {
  availableDurations = [1.0, 0.666, 0.333];  // Example: triplet-based
}
```

**Step 3: Add to Randomize Function**

```javascript
const durationTypes = [
  "none", "whole", /* ... */ "newrhythm"
];
```

### Adding a New Fractal Parameter

**Step 1: Calculate in Fractal Function**

```javascript
const result = calculateMandelbrot(x, y, maxIter);

// Add new parameter
result.newParam = calculateNewParam(result);

fractalData.push(result);
```

**Step 2: Add to Mapping Dropdowns**

```html
<option value="newparam">New Parameter</option>
```

**Step 3: Add to Mapping Function**

```javascript
const newParam = point.newParam || 0.5;

// In mapping dropdown change handlers
const newParamMap = document.getElementById("newParamMap").value;

// In applyMapping
if (source === "newparam") {
  applyMapping("newparam", newParam, newParamMap);
}
```

**Step 4: Add to Preset System**

```javascript
// Save
newParamMap: document.getElementById("newParamMap").value,

// Load
document.getElementById("newParamMap").value = preset.newParamMap || "none";
```

---

## Debugging and Development

### Enabling Csound Logging

```javascript
// Change -m0 to -m7 for verbose output
await csound.setOption("-m7");
```

### Browser Console Debugging

```javascript
// Audio loop logging
console.log(`Scheduling ${sample.length} notes`);
console.log(`Fractal data points: ${fractalData.length}`);
console.log(`Pitch quantization: ${originalFreq} → ${freq} Hz`);
```

### Common Issues

**Audio Not Playing:**
```javascript
// Check audio context state
const audioContext = await csound.getAudioContext();
console.log("Audio context state:", audioContext.state);

// Must be "running", not "suspended"
if (audioContext.state === "suspended") {
  await audioContext.resume();
}
```

**Presets Not Loading:**
```javascript
// Check localStorage
console.log(localStorage.getItem("mandelbrotPresets"));

// Verify JSON parsing
try {
  const presets = JSON.parse(localStorage.getItem("mandelbrotPresets"));
  console.log("Presets loaded:", Object.keys(presets));
} catch (e) {
  console.error("JSON parse error:", e);
}
```

**Csound Compilation Errors:**
```javascript
// Check for syntax errors in orchestra
try {
  await csound.compileOrc(orchestra);
  console.log("✓ Orchestra compiled successfully");
} catch (e) {
  console.error("Csound compilation error:", e);
}
```

---

## API Reference

### Key JavaScript Functions

**Initialization:**
```javascript
initCsound()                    // Initialize Csound WASM
init()                          // Initialize application
```

**Fractal:**
```javascript
render2D()                      // Render fractal to canvas
calculateMandelbrot(x, y, max)  // Mandelbrot algorithm
calculateJulia(x, y, max, cr, ci) // Julia algorithm
zoomIn()                        // Zoom in 2x
zoomOut()                       // Zoom out 2x
resetView()                     // Reset to default
```

**Audio:**
```javascript
toggleAudio()                   // Start/stop audio
audioLoop()                     // Main audio generation loop
quantizePitch(freq, scale, base, key) // Pitch quantization
quantizeDuration(dur, type, tempo)    // Duration quantization
updateCsoundControls()          // Sync UI → Csound
```

**Presets:**
```javascript
savePreset()                    // Save current state
loadSavedPreset()              // Load from dropdown
deletePreset()                  // Delete selected preset
refreshPresetList()            // Update dropdown
initializeFactoryPresets()     // Load 45 factory presets
```

**UI Helpers:**
```javascript
setSliderValue(id, value, displayId)  // Set slider + display
setControlValue(id, value, isSlider)  // Set dropdown + trigger event
updateStatus(msg, type)               // Update status bar
updateMappingDisplay()                // Update mapping indicators
```

### Csound Opcodes Used

**Oscillators:**
- `oscili` - Interpolating oscillator
- `vco2` - Virtual analog oscillator

**Filters:**
- `tone` - 1-pole lowpass
- `moogladder` - Moog ladder filter (24dB/oct)

**Delays:**
- `vdelay` - Variable delay line
- `pluck` - Karplus-Strong algorithm

**Effects:**
- `freeverb` - Reverb algorithm

**Envelopes:**
- `linseg` - Linear segments
- `linsegr` - Linear segments with release
- `expseg` - Exponential segments
- `madsr` - MIDI ADSR envelope

**Utilities:**
- `chnget` - Get control channel value
- `out` - Stereo output
- `rand` - Random number generator
- `table` - Table lookup

---

## Performance Benchmarks

### Typical Performance (Modern Laptop)

**Fractal Rendering:**
- 1024x768, 100 iterations: ~30ms (30 FPS)
- 1024x768, 200 iterations: ~60ms (16 FPS)
- GPU mode: ~5ms (200 FPS)

**Audio Generation:**
- 50 notes, simple synthesis (Pluck): ~2ms
- 50 notes, complex synthesis (Granular): ~5ms
- Effect processing: ~1ms per effect

**Total CPU Usage:**
- Idle: 5-10%
- Fractal rendering: 20-30%
- Audio playing: 10-15%
- Both: 30-45%

---

## Conclusion

This technical guide covers the complete architecture of the Dr.C Mandelbrot Explorer, from high-level system design to low-level implementation details. The modular design makes it easy to extend with new synthesis engines, effects, scales, rhythms, and fractal parameters.

Key architectural decisions:
- **Csound WASM** for professional audio synthesis
- **Pure JavaScript** for maximum compatibility
- **localStorage** for preset persistence
- **Modular design** for easy extension
- **Real-time parameter mapping** for organic sonic exploration

For further assistance:
- [Quick Start Guide](quickstart.html)
- [User's Guide](users-guide.md)
- [Return to Home](index.html)

---

**End of Technical Guide**
