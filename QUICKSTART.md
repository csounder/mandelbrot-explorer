# QUICK START GUIDE
## Dr.C Mandelbrot Explorer v2.0 - Enhanced Audio Edition

---

## 🚀 GETTING STARTED

### 1. Open the Application
```bash
# Option A: Double-click app.html in Finder
# Option B: Open in browser:
open /Users/richardboulanger/dB-Studio/Dr.C-Mandelbrot-Explorer/app.html
```

### 2. Initialize Csound
1. Click **"🔊 Initialize Csound"** button (right panel)
2. Wait for status: **"Csound WASM ready! Click 'Play Audio' to start."**
3. _(This loads the Csound WebAssembly engine from CDN)_

### 3. Render a Fractal
- The Mandelbrot set renders automatically on load
- Click anywhere to zoom in
- Use **Reset View** to return to origin

### 4. Start Audio
1. Click **"▶ Play Audio"** button
2. You should hear plucked string sounds mapping the fractal
3. The **"♪ Audio Playing"** indicator appears bottom-right

---

## 🎹 EXPLORING THE INSTRUMENTS

### Quick Instrument Tour:
1. **Pluck** (default) - Karplus-Strong strings
2. **FM Bell** - Metallic, bright bells
3. **Additive Bell** - Warm, harmonic bells
4. **Clarinet** - Woody, mellow timbre
5. **Organ** - Drawbar-style organ
6. **Granular** - Textured, cloud-like sounds
7. **Filtered Noise** - Pitched noise bursts
8. **Subtractive** - Classic analog synth sounds

### Try This:
1. Change **Synthesis Type** dropdown to **"FM Bell"**
2. Increase **Reverb Mix** to 60%
3. Set **Tempo** to 180 BPM
4. Listen to the difference!

---

## 🎛️ PARAMETER MAPPING EXPERIMENTS

### Experiment 1: Stereo Panorama → Pitch Field
**Goal:** Make horizontal position control pitch instead of pan

1. Change **X Position** → **Pitch**
2. Keep **Iteration Count** → **Pitch** (or change to Amplitude)
3. Result: Notes sweep from low to high across the fractal

### Experiment 2: Brightness Explosion
**Goal:** Hear timbral variations based on iteration count

1. Set **Iteration Count** → **Brightness**
2. Set **X Position** → **Pan** (default)
3. Set **Y Position** → **Amplitude**
4. Select **Subtractive** or **Clarinet** instrument
5. Result: Filter cutoff varies with escape-time

### Experiment 3: Rhythmic Patterns
**Goal:** Create rhythmic variations with duration mapping

1. Set **Y Position** → **Duration**
2. Set **Duration Scale** to **2.0**
3. Set **Tempo** to **90 BPM**
4. Result: Notes have varying lengths creating rhythmic patterns

---

## 🎨 FRACTAL EXPLORATION

### Julia Set Animation:
1. Switch **Mode** to **Julia Set**
2. Click **"▶ Start Animation"**
3. Select **Pattern** → **Lissajous** or **Spiral**
4. Watch/hear the fractal morph in real-time

### Recommended Julia Presets:
- **Julia 1** - Classic spiral arms
- **Julia 2** - Dendrite structure
- **Julia 3** - Chaotic tendrils

---

## 🎚️ OPTIMAL SETTINGS BY GENRE

### 🌊 Ambient/Atmospheric:
```
Instrument: Granular or FM Bell
Tempo: 60 BPM
Note Density: 30%
Duration Scale: 2.5
Reverb Mix: 70%
Attack: 200ms
Release: 1000ms
```

### ⚡ Energetic/Rhythmic:
```
Instrument: Pluck or Subtractive
Tempo: 180 BPM
Note Density: 80%
Duration Scale: 0.5
Reverb Mix: 20%
Attack: 5ms
Release: 100ms
```

### 🔔 Melodic/Bell-like:
```
Instrument: Additive Bell
Tempo: 120 BPM
Note Density: 50%
Duration Scale: 1.5
Reverb Mix: 50%
Attack: 10ms
Release: 500ms
```

### 🎺 Organic/Acoustic:
```
Instrument: Clarinet
Tempo: 90 BPM
Note Density: 40%
Duration Scale: 1.0
Reverb Mix: 35%
Attack: 50ms
Release: 300ms
```

---

## 💾 EXPORTING YOUR COMPOSITION

### Generate .CSD File:
1. Render the fractal you want to sonify
2. Click **"Generate .CSD File"** (right panel, bottom)
3. Copy the code from the output box
4. Save to a `.csd` file
5. Render with standalone Csound:
   ```bash
   csound your_fractal.csd
   ```

---

## 🔧 TROUBLESHOOTING

### "Csound initialization failed"
- **Check:** Internet connection (needs CDN access)
- **Try:** Refresh the page and initialize again
- **Check Console:** Open browser DevTools (F12) → Console tab

### "No fractal data to sonify"
- **Wait:** Let the fractal finish rendering (progress bar should complete)
- **Try:** Click Reset View, then Play Audio

### Audio sounds choppy/glitchy
- **Reduce:** Note Density to 30-50%
- **Increase:** Duration Scale to 1.5+
- **Avoid:** Tempo > 240 BPM with Granular synthesis

### No sound at all
- **Check:** Master Volume is not 0%
- **Check:** Browser audio is not muted
- **Check:** You clicked "Initialize Csound" first
- **Try:** Different synthesis type

---

## 📊 PERFORMANCE TIPS

### For Smooth Performance:
- **Max Iterations:** 100-200 (higher = slower render)
- **Note Density:** 30-60% (sweet spot)
- **Instrument:** Pluck/Clarinet/Organ are lightest
- **Avoid:** Granular + 100% density + high tempo

### CPU-Intensive Combinations:
⚠️ **Granular** + **100% density** + **300 BPM** = May lag  
✓ **Pluck** + **50% density** + **120 BPM** = Smooth

---

## 🎯 KEYBOARD SHORTCUTS
_(Not yet implemented - planned for Phase 9)_

---

## 📝 NEXT STEPS

After exploring the basics:
1. Save your favorite settings as **Audio Presets**
2. Record a **Path** through the Mandelbrot set
3. Experiment with **Color Schemes** (affects visual, not audio)
4. Try **GPU Rendering** mode for faster fractals
5. Export compositions and render offline with Csound

---

## 🐛 KNOWN LIMITATIONS

- Max 50 notes per audio loop (prevents overload)
- Granular synthesis may sound different than offline Csound
- Parameter changes don't affect notes already scheduled
- 3D fractals (Mandelbulb) not yet sonified

---

## 📞 SUPPORT

Console output (F12 → Console) will show:
```
Sending 27 notes (fm, 120 BPM)...
```

This confirms the audio engine is working correctly.

---

**Enjoy exploring the sonic landscapes of the Mandelbrot set! 🎵🎨**
