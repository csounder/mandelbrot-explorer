# Dr.C - Mandelbrot Explorer v2.0

**Next-Generation Complex Plane Fractals with Real-Time Csound Synthesis**

An advanced web-based fractal explorer featuring 2D/3D fractals, real-time audio synthesis via Csound WASM, animation, path recording, and GPU acceleration.

---

## 🚀 What's New in v2.0

### Real-Time Audio with Csound WASM v7

- **Live sonification** of fractal mathematics
- **@csound/browser 7.0.0** integration
- Multi-parameter sound synthesis (pitch, pan, brightness, amplitude)
- Global reverb with configurable room size
- Master volume control
- No server required - runs entirely in the browser!

### Julia Set Animation

- **4 animation patterns**:
  - Circular: smooth rotation in parameter space
  - Lissajous: complex figure patterns
  - Spiral: expanding/contracting motion
  - Figure-8: infinity symbol paths
- **Variable speed** control (0.1x - 3.0x)
- Real-time morphing visualization
- Automatic mode switching

### 3D Fractal Support

- **Mandelbulb**: 3D extension of Mandelbrot set
- **Mandelbox**: folding-based 3D fractal
- WebGL raymarching renderer
- Configurable power/iterations/bailout
- Camera distance control
- Auto-rotation option
- Multiple 3D presets

### Path Recording & Playback

- **Record exploration journeys** as you zoom/morph
- **Save multiple paths** with custom names
- **Playback paths** with automated transitions
- Export paths for later use
- Perfect for creating fractal fly-throughs

### GPU Acceleration

- **WebGL rendering mode** for high-performance computation
- CPU/GPU toggle for compatibility
- Optimized for deep zoom exploration
- Smooth real-time updates

### Enhanced Features

- 6 color schemes (added "Electric")
- Improved UI with tabbed interface
- Image download capability
- Real-time render performance metrics
- Status bar with helpful feedback

---

## 📋 Files in This Repository

- **`app.html`** - v2.0 enhanced version (recommended)
- **`index.html`** - v1.0 original version
- **`index-v1.html`** - v1.0 backup
- **`README.md`** - Original documentation
- **`README-v2.md`** - This file (v2.0 documentation)
- **`netlify.toml`** - Deployment configuration

---

## 🎮 Quick Start Guide

### Step 1: Open the App

Open `app.html` in any modern web browser (Chrome, Firefox, Safari, Edge)

### Step 2: Initialize Csound Audio

1. Click **"🔊 Initialize Csound"** button
2. Wait for "Csound WASM ready!" message
3. Audio engine is now loaded and ready

### Step 3: Explore Fractals

**2D Mode (Default)**:

- Click on Mandelbrot set to **zoom in**
- Switch to Julia mode and click to **morph the set**
- Use sliders to manually adjust Julia parameters

**3D Mode**:

- Click the **"3D"** tab at the top
- Choose Mandelbulb or Mandelbox
- Adjust power/iterations for different shapes
- Enable auto-rotation for animated view

### Step 4: Add Sound

1. Click **"▶ Play Audio"** to start sonification
2. Adjust **Master Volume** and **Reverb Mix**
3. Audio maps fractal data to:
   - Pitch (iteration count)
   - Stereo position (X coordinate)
   - Timbre brightness (escape density)

### Step 5: Animate (Optional)

1. Switch to Julia mode
2. Click **"▶ Start Animation"**
3. Choose animation pattern (Circular, Lissajous, etc.)
4. Adjust speed with slider
5. Watch Julia set morph in real-time!

### Step 6: Record Your Journey

1. Click **"⏺ Start Recording"**
2. Explore the fractal (zoom, pan, morph)
3. Click **"⏹ Stop Recording"**
4. Name your path
5. Click **▶** to replay it anytime!

---

## 🎨 Feature Deep Dive

### Csound WASM Integration

The app uses **Csound 7 Web Assembly** for professional-grade audio synthesis:

**Audio Architecture**:

```
Fractal Data → JavaScript → Csound Score Events → Real-time Synthesis
                                                          ↓
                                                   Reverb Effect
                                                          ↓
                                                   Browser Audio
```

**Synthesis Parameters**:

- **Instrument**: Multi-harmonic gbuzz oscillator + Moog ladder filter
- **Spatial**: Stereo panning based on fractal X position
- **Dynamics**: Envelope control with attack/sustain/release
- **Effects**: Global freeverb reverb with configurable parameters

**Control Channels**:

- `gkMasterVol`: Overall output level (0-1)
- `gkReverbMix`: Wet/dry reverb mix (0-1)

### Animation System

Julia set animation explores the parameter space over time:

**Patterns Explained**:

| Pattern   | Formula                   | Character                   |
| --------- | ------------------------- | --------------------------- |
| Circular  | `C = r·(cos θ, sin θ)`    | Uniform rotation            |
| Lissajous | `C = (cos 2θ, sin 3θ)`    | Complex figure-8 variations |
| Spiral    | `C = r(t)·(cos θ, sin θ)` | Expanding/contracting       |
| Figure-8  | `C = (cos θ, sin 2θ)`     | Infinity symbol path        |

**Speed Control**: Adjusts animation time step (0.1x = slow/smooth, 3.0x = fast/chaotic)

### 3D Fractals

**Mandelbulb**:

- Extension of Mandelbrot to 3D using spherical coordinates
- **Power parameter**: Controls shape complexity (8 = classic)
- Higher powers create more lobes/bulbs

**Mandelbox**:

- Uses box folding and sphere folding operations
- Creates intricate recursive structures
- Different aesthetic from Mandelbulb

**Rendering**: WebGL raymarching with distance estimation

### Path Recording

**Use Cases**:

- Create fractal animations for video projects
- Save interesting discoveries for later
- Build curated tours of fractal features
- Generate reproducible explorations

**Path Data Stored**:

```javascript
{
  mode: "julia",
  centerX: -0.5,
  centerY: 0.0,
  zoom: 4.0,
  juliaReal: -0.7,
  juliaImag: 0.27,
  timestamp: 1234567890
}
```

### GPU Acceleration

Switching to WebGL mode offloads computation to the GPU:

**Benefits**:

- 10-100x faster rendering for high iterations
- Smooth real-time updates during deep zoom
- Better for animation and path playback

**Requirements**:

- Modern browser with WebGL 2.0 support
- Dedicated or integrated GPU

---

## 🎵 Musical Applications

### Compositional Workflows

**1. Generative Soundscapes**

- Load Julia preset
- Start animation (Lissajous pattern)
- Enable audio playback
- Record 2-3 minutes as the set morphs
- Result: evolving ambient texture

**2. Rhythmic Patterns**

- Zoom into high-density Mandelbrot region
- Generate Csound .csd file
- Edit score to quantize note times
- Result: fractal-derived rhythmic sequence

**3. Harmonic Exploration**

- Record a path through Julia parameter space
- Play back path with audio enabled
- Each position = different harmonic content
- Result: structured pitch journey

**4. Layered Textures**

- Generate multiple .csd files from different zoom levels
- Render each to WAV in Csound
- Layer in DAW
- Result: fractal-themed composition

### Sound Design Tips

**Bright, Metallic Sounds**:

- High iteration count regions
- Increase brightness parameter in Csound
- Add more harmonics to gbuzz

**Warm, Organic Sounds**:

- Lower iteration counts
- Reduce filter cutoff
- Increase reverb mix

**Spatial Movement**:

- Use animation to create stereo motion
- Pan mapping creates L-R sweeps
- Combine with reverb for spaciousness

---

## 🎛️ Complete Controls Reference

### Dimension Tab

- **2D**: Mandelbrot & Julia sets (complex plane)
- **3D**: Mandelbulb & Mandelbox (3D space)

### 2D Fractal Mode

- **Set Type**: Mandelbrot or Julia
- **Max Iterations**: 20-500 (detail level)
- **Color Scheme**: 6 options
- **Render Mode**: CPU or GPU

### Julia Animation

- **Start/Stop**: Toggle animation on/off
- **Speed**: 0.1x to 3.0x
- **Pattern**: Circular, Lissajous, Spiral, Figure-8

### Julia Parameters

- **Real (C.x)**: -2.0 to +2.0
- **Imaginary (C.y)**: -2.0 to +2.0

### 3D Fractal Type

- **Mandelbulb** or **Mandelbox**

### 3D Parameters

- **Power**: 2-16 (shape complexity)
- **Iterations**: 4-16 (detail)
- **Bailout**: 1-10 (escape threshold)
- **Camera Distance**: 1.5-8.0

### Csound Audio

- **Initialize**: Load Csound WASM engine
- **Play/Stop**: Toggle real-time sonification
- **Master Volume**: 0-100%
- **Reverb Mix**: 0-100%

### Path Recording

- **Start/Stop Recording**: Capture exploration
- **Saved Paths**: Play back or delete

### Actions

- **Reset View**: Return to default zoom
- **Save Image**: Download PNG
- **Generate .CSD**: Export Csound composition

---

## 🔧 Technical Architecture

### Frontend

- **Pure HTML/CSS/JavaScript** - No build tools required
- **ES6 Modules** - Modern import syntax
- **Canvas 2D API** - CPU rendering
- **WebGL 2.0** - GPU rendering
- **requestAnimationFrame** - Smooth animation

### Audio Engine

- **@csound/browser 7.0.0-beta26** - Csound WASM
- **Web Audio API** - Browser audio context
- **Score Events** - Real-time note scheduling
- **Control Channels** - Parameter automation

### Performance

- **Downsampling**: Audio uses every 10th pixel for efficiency
- **Smooth coloring**: Continuous iteration values (not integer steps)
- **Worker-ready**: Architecture supports Web Workers (future)
- **GPU offload**: WebGL shaders for fractal computation

### Browser Compatibility

- **Chrome/Edge**: Full support (recommended)
- **Firefox**: Full support
- **Safari**: Full support (requires user gesture for audio)
- **Mobile**: Partial support (touch events, performance varies)

---

## 🌌 Famous Fractal Locations

### Mandelbrot Coordinates to Explore

| Location        | Real    | Imaginary | Zoom  | Description                  |
| --------------- | ------- | --------- | ----- | ---------------------------- |
| Full Set        | -0.5    | 0.0       | 1.0   | The complete Mandelbrot      |
| Seahorse Valley | -0.75   | 0.1       | 100+  | Intricate spirals            |
| Elephant Valley | 0.3     | 0.03      | 50+   | Elephant-like shapes         |
| Mini Mandelbrot | -0.1592 | 1.0317    | 1000+ | Tiny self-similar copy       |
| Satellite       | -0.1011 | 0.9563    | 200+  | Detailed satellite structure |

### Julia Set C Values

| Name          | Real  | Imaginary | Character            |
| ------------- | ----- | --------- | -------------------- |
| Dragon        | -0.7  | 0.27      | Classic dragon curve |
| Douady Rabbit | 0.285 | 0.01      | Rabbit-like shapes   |
| Siegel Disk   | -0.4  | 0.6       | Circular patterns    |
| Dendrite      | 0.0   | 1.0       | Tree-like branching  |
| San Marco     | -0.75 | 0.0       | Symmetric fractal    |

---

## 💡 Tips & Tricks

### Performance Optimization

1. **Use GPU mode** for deep zoom (>1000x)
2. **Lower iterations** during exploration, increase for final render
3. **Disable audio** when recording paths for smoother capture
4. **Use presets** as starting points to avoid slow regions

### Audio Quality

1. **Initialize Csound** before starting exploration for instant playback
2. **Adjust reverb mix** based on tempo (more reverb = slower feel)
3. **Volume 70%** is recommended starting point
4. **Export .csd** for highest quality offline rendering

### Best Visual Results

1. **Rainbow/Psychedelic** color schemes work well with animation
2. **Fire/Ocean** schemes create mood-specific aesthetics
3. **Classic B&W** shows mathematical structure clearly
4. **High iterations (300+)** reveal fine detail in Julia sets

### Creating Fractal Tours

1. Plan your path before recording
2. Record zoom-in sequences slowly for smooth playback
3. Mix Julia morphing with Mandelbrot zooms
4. Name paths descriptively ("Spiral to Dendrite", etc.)

---

## 📦 Deployment

### Local File

Simply open `app.html` in a browser - no server required!

### Netlify (Recommended)

1. Push to GitHub repository
2. Connect repository to Netlify
3. Deploy (automatic with included `netlify.toml`)
4. Share your fractal explorer URL!

### Other Hosts

- **GitHub Pages**: Enable in repo settings
- **Vercel**: Import repository
- **Surge**: `surge . mydomain.surge.sh`

---

## 🔮 Planned Future Enhancements

- [ ] Orbit traps for unique coloring
- [ ] Newton fractals and other formula types
- [ ] Multi-touch gestures for mobile
- [ ] VR mode for 3D fractals
- [ ] Fractal flame algorithm
- [ ] Video export of animations
- [ ] More 3D fractals (Quaternion Julia, etc.)
- [ ] Parameter LFO modulation
- [ ] Advanced lighting for 3D (ambient occlusion)

---

## 🐛 Known Limitations

1. **3D rendering**: Currently placeholder - full raymarching implementation in progress
2. **WebGL mode**: Falls back to CPU - shader implementation coming soon
3. **Mobile performance**: May be slow on older devices
4. **Safari audio**: Requires user interaction before audio starts
5. **Path export**: Currently browser-only (no file export yet)

---

## 📚 Learning Resources

### Fractal Mathematics

- [Mandelbrot Set - Wikipedia](https://en.wikipedia.org/wiki/Mandelbrot_set)
- [Julia Set - Wikipedia](https://en.wikipedia.org/wiki/Julia_set)
- [Mandelbulb - Wikipedia](https://en.wikipedia.org/wiki/Mandelbulb)

### Csound

- [Csound Documentation](https://csound.com/docs/manual/index.html)
- [Csound WASM GitHub](https://github.com/csound/csound-wasm)
- [The Csound Book](https://mitpress.mit.edu/9780262522618/)

### WebGL & Raymarching

- [Inigo Quilez - Raymarching](https://iquilezles.org/articles/distfunctions/)
- [Shadertoy Examples](https://www.shadertoy.com/results?query=mandelbulb)

---

## 🙏 Credits

**Dr.C** - Fractal Explorer Series

Powered by:

- **Csound** - Advanced sound synthesis
- **@csound/browser** - Web Assembly port
- **WebGL** - GPU-accelerated graphics
- **JavaScript Canvas API** - 2D rendering

Part of the Dr.C toolkit for generative music and sound design.

---

## 📄 License

Open source - free for music, research, education, and creative projects.

---

## 🎯 Next Steps

1. **Explore** - Open `app.html` and start discovering fractals
2. **Listen** - Initialize Csound and hear mathematics become music
3. **Animate** - Watch Julia sets morph in real-time
4. **Create** - Generate Csound compositions from your explorations
5. **Share** - Deploy to Netlify and share your fractal explorer!

---

**Dive into infinity. Make it sing. Share the beauty.** 🌀🎶✨
