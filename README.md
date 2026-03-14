# Dr.C - Mandelbrot Explorer

**Interactive Complex Plane Fractals → Csound Score Generator**

An interactive web-based explorer for Mandelbrot and Julia sets that generates musical Csound scores from fractal mathematics.

---

## 🌀 Features

### Dual Fractal Modes

- **Mandelbrot Set**: The classic fractal - click to zoom infinitely deep
- **Julia Set**: Beautiful variations - click to morph the set in real-time

### Real-Time Interaction

- **Click to Zoom** (Mandelbrot): Dive deeper into infinite complexity
- **Click to Morph** (Julia): Set the C parameter by clicking anywhere
- **Live Parameter Control**: Adjust iterations, escape radius, and Julia parameters
- **Deep Zoom**: Explore self-similar patterns at any scale

### Visual Options

- **5 Color Schemes**: Classic B&W, Rainbow, Fire, Ocean, Psychedelic
- **Smooth Coloring**: Beautiful gradient rendering using escape-time algorithm
- **Configurable Iterations**: 20-500 iterations for detail control
- **Variable Escape Radius**: Adjust the boundary condition

### Csound Integration

- **Automatic Score Generation**: Convert fractal data to musical notation
- **Multi-Parameter Mapping**:
  - Iteration count → Pitch (MIDI notes)
  - X coordinate → Stereo panning
  - Y coordinate → Note duration
  - Fractal density → Amplitude
- **Complete .csd Output**: Ready-to-run Csound files with orchestra + score

### Curated Presets

- Mandelbrot classic view
- Julia Set variations (3 beautiful configurations)
- Spiral patterns
- Dendrite structures

---

## 🚀 Quick Start

1. **Open `index.html`** in any modern web browser
2. **Explore the Mandelbrot set**:
   - Click anywhere to zoom in
   - Watch the fractal recalculate in real-time
3. **Switch to Julia mode**:
   - Select "Julia Set" from the dropdown
   - Click to change the C parameter
   - Adjust sliders for fine control
4. **Generate Music**:
   - Click "Generate Csound Score"
   - Copy the complete .csd file
   - Run in Csound to hear your fractal!

---

## 🎨 How It Works

### The Mathematics

**Mandelbrot Set**: For each point `c` in the complex plane, iterate:

```
z_{n+1} = z_n² + c
```

Starting with `z_0 = 0`. Points that don't escape to infinity are in the set.

**Julia Set**: For a fixed complex parameter `C`, iterate:

```
z_{n+1} = z_n² + C
```

Starting with `z_0 = c` (the point being tested).

### The Music Mapping

Fractal properties map to musical parameters:

| Fractal Data    | Musical Parameter | Range           |
| --------------- | ----------------- | --------------- |
| Iteration count | MIDI pitch        | 36-84 (C2-C6)   |
| X coordinate    | Stereo pan        | 0.0-1.0 (L-R)   |
| Y coordinate    | Duration          | 0.5-2.5 seconds |
| Density         | Amplitude         | 0.3-0.9         |

The generator creates **staggered note sequences** with 50ms spacing, producing cascading musical textures that reflect the fractal's spatial structure.

---

## 🎹 Musical Applications

### Compositional Ideas

1. **Melodic Contours**: Zoom into different Mandelbrot regions - each yields unique pitch sequences
2. **Harmonic Variation**: Julia sets with similar C values create related harmonic material
3. **Rhythmic Density**: High-iteration areas produce dense note clusters
4. **Spatial Textures**: The pan mapping creates stereo soundscapes that mirror fractal symmetry

### Csound Expansion

The generated score uses a simple sine tone instrument (`i1`). Expand it by:

- Adding filters, reverb, delay effects
- Using different synthesis methods (FM, wavetable, granular)
- Processing with time-varying envelopes
- Layering multiple instruments reading the same score

---

## 🎛️ Controls Reference

### Fractal Mode

- **Set Type**: Toggle between Mandelbrot and Julia
- **Max Iterations**: Detail level (higher = more accuracy, slower)
- **Escape Radius**: Boundary threshold (2.0 standard, higher = larger set)

### Julia Parameters

- **Real (C.x)**: Real component of Julia constant
- **Imaginary (C.y)**: Imaginary component of Julia constant

### Visual

- **Color Scheme**: Choose rendering palette
- **Canvas Click**: Zoom (Mandelbrot) or set C (Julia)

### Actions

- **Reset View**: Return to default zoom/center
- **Generate Csound Score**: Create complete .csd file

---

## 📐 Technical Details

- **Resolution**: 800×600 canvas
- **Precision**: Smooth iteration coloring using log-log smoothing
- **Performance**: Downsampled data grid (every 10th pixel) for Csound generation
- **Score Size**: ~200 notes maximum per generation
- **Browser Compatibility**: Works in all modern browsers (Chrome, Firefox, Safari, Edge)

---

## 🔮 Famous Locations

Try zooming into these Mandelbrot coordinates:

| Location        | Real    | Imaginary | Description           |
| --------------- | ------- | --------- | --------------------- |
| Classic         | -0.5    | 0.0       | The full set          |
| Seahorse Valley | -0.75   | 0.1       | Intricate spirals     |
| Elephant Valley | 0.3     | 0.03      | Elephant-like shapes  |
| Mini Mandelbrot | -0.1592 | 1.0317    | Tiny copy of full set |

For Julia sets, try these C values:

| Name          | Real  | Imaginary | Character            |
| ------------- | ----- | --------- | -------------------- |
| Dragon        | -0.7  | 0.27      | Classic dragon curve |
| Douady Rabbit | 0.285 | 0.01      | Rabbit-like shapes   |
| Siegel Disk   | -0.4  | 0.6       | Circular patterns    |
| Dendrite      | 0.0   | 1.0       | Tree-like branching  |

---

## 🎵 Example Workflow

1. **Exploration Phase**:
   - Load "Spiral" preset (Julia set)
   - Adjust iterations to 200 for detail
   - Switch to "Psychedelic" colors
   - Click around to morph the pattern

2. **Generation Phase**:
   - Click "Generate Csound Score"
   - Copy output to `fractal.csd`
   - Modify the Csound instrument (add reverb, change synthesis)

3. **Rendering**:

   ```bash
   csound fractal.csd -o fractal.wav
   ```

4. **Iterate**:
   - Return to browser, zoom deeper
   - Generate new score
   - Layer multiple renders in DAW

---

## 🌟 Future Enhancements

- **Animation**: Animate Julia C parameter over time
- **3D Rendering**: Mandelbulb and Mandelbox exploration
- **Real-time Audio**: Web Audio API synthesis
- **MIDI Export**: Generate .mid files
- **Parameter Recording**: Save/load exploration paths
- **GPU Acceleration**: WebGL rendering for deep zoom

---

## 📚 Resources

- [Mandelbrot Set - Wikipedia](https://en.wikipedia.org/wiki/Mandelbrot_set)
- [Julia Set - Wikipedia](https://en.wikipedia.org/wiki/Julia_set)
- [Csound Documentation](https://csound.com/docs/manual/index.html)
- [Complex Plane on Wolfram](https://mathworld.wolfram.com/ComplexPlane.html)

---

## 🙏 Credits

Created by **Dr.C** - Fractal Explorer Series

Part of the Dr.C toolkit for generative music and sound design.

---

## 📄 License

Open source - use freely for music, research, and education.

---

**Explore the infinite. Make it sing.** 🎶✨
