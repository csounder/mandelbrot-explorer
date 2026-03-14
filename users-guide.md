# User's Guide - Dr.C Mandelbrot Explorer

## Table of Contents

1. [Introduction](#introduction)
2. [Interface Overview](#interface-overview)
3. [Audio System](#audio-system)
4. [Fractal Visualization](#fractal-visualization)
5. [Synthesis Engines](#synthesis-engines)
6. [Musical Quantization](#musical-quantization)
7. [Effects Processing](#effects-processing)
8. [Parameter Mapping](#parameter-mapping)
9. [Preset Management](#preset-management)
10. [Advanced Techniques](#advanced-techniques)

---

## Introduction

Dr.C - Mandelbrot Explorer is a real-time fractal visualization and sonification application that transforms mathematical beauty into music. It combines:

- **Mandelbrot and Julia Set** visualization with infinite zoom
- **11 synthesis algorithms** powered by Csound WASM v7
- **Musical quantization** to scales, keys, and rhythmic grids
- **Global effects processing** (Reverb, Echo, Chorus, Flanger)
- **Parameter mapping** from fractal geometry to synthesis
- **Preset system** with 45 factory presets

---

## Interface Overview

### Left Column (Fractal & Control)

**Audio Control**
- Play/Stop audio button
- Spacebar keyboard shortcut

**Randomize All**
- One-click randomization of all parameters
- Excludes Master Volume

**Presets**
- Save custom presets
- Load from 45 factory presets
- Delete unwanted presets

**Fractal Mode**
- Mandelbrot Set: Classic fractal exploration
- Julia Set: Morphable parameter space

**Zoom Controls**
- In/Out buttons for precise zoom
- Reset View to return to default
- Mousewheel zoom support

**Parameters**
- Max Iterations (20-500)
- Color Scheme (6 options)
- Render Mode (CPU/GPU)
- Julia Parameters (Real/Imaginary)

**Animation**
- Toggle animation on/off
- Speed control (0.1x - 2.0x)
- Pattern selection (Circular, Zoom, Wave)

### Right Column (Audio Panel)

**Synthesis Type**
- 11 synthesis algorithms
- Real-time switching

**Quantize - Key, Scale, Rhythm**
- Key/Root Note: 12 keys + Atonal
- Scale Quantization: 20 scales/modes
- Rhythm Quantization: 25 rhythmic grids

**Master Volume**
- 0-100% control
- Defaults to 50%

**Envelope**
- Attack: 1-500ms
- Release: 10-2000ms

**Global Effects**
- Reverb (freeverb algorithm)
- Echo (variable delay)
- Chorus (multi-tap)
- Flanger (short delay + LFO)

**Musical Parameters**
- Tempo (30-300 BPM)
- Base Frequency (55-880 Hz)
- Pitch Range (0.5-4 octaves)
- Note Density (1-33%)
- Duration Scale (0.1-3.0x)

**Parameter Mapping**
- 6 fractal sources → 8 synthesis targets
- Real-time mapping display

**Effect Mapping**
- 6 fractal sources → 6 effect parameters

---

## Audio System

### Audio Engine

- **Technology**: Csound WASM v7.0.0-beta26
- **Sample Rate**: 44.1 kHz
- **Block Size**: 32 samples (ksmps)
- **Output**: Stereo (2 channels)
- **Bit Depth**: 32-bit float
- **0 dBFS**: 1.0 (normalized)

### Audio Flow

```
Fractal Data → Filter (5-95% maxIter)
    ↓
Density Filter (1-33%)
    ↓
Sample (max 50 notes per cycle)
    ↓
Parameter Mapping (fractal → synthesis)
    ↓
Quantization (key/scale/rhythm)
    ↓
Synthesis Engine (11 types)
    ↓
Global Effects (4 buses)
    ↓
Master Volume
    ↓
Stereo Output
```

### Note Filtering

The audio engine intelligently filters fractal data:

1. **Interesting Regions Only**: Only triggers notes from regions where iteration count is 5%-95% of maxIter
2. **Excludes Empty Space**: Filters out points that escaped too quickly
3. **Excludes Interior**: Filters out solid black areas (maxIter reached)
4. **Visual Feedback**: Green circles appear on triggered notes

This ensures musical output comes from the **visually interesting boundary regions** of the fractal.

### Real-Time Response

Audio restarts in 100ms when:
- Fractal changes (zoom, pan, mode switch)
- Synthesis type changes
- Quantization changes
- Animation updates

---

## Fractal Visualization

### Mandelbrot Set

The Mandelbrot set is defined by the iteration:
```
z(n+1) = z(n)² + c
```
where `z` starts at 0 and `c` is the complex coordinate.

**Interaction:**
- **Click**: Zoom in at clicked point
- **Mousewheel**: Smooth zoom in/out
- **Zoom Range**: 1x to practically infinite

**Visual Encoding:**
- **Color**: Iteration count before escape
- **Black**: Points that never escaped (interior)
- **Boundary**: Where the magic happens (musically interesting)

### Julia Set

Julia sets use a fixed `c` parameter:
```
z(n+1) = z(n)² + c
```
where `z` starts at the coordinate and `c` is constant.

**Interaction:**
- **Click**: Morph the `c` parameter
- **Sliders**: Adjust Real/Imaginary components
- **Animation**: Smoothly morph through parameter space

**Musical Mapping:**
- Different Julia parameters create completely different fractal shapes
- Each shape maps to unique musical material
- Perfect for evolving sonic textures

### Color Schemes

1. **Classic**: Black & white (iteration count)
2. **Fire**: Warm oranges and reds
3. **Rainbow**: Full spectrum
4. **Ocean**: Blues and cyans
5. **Psychedelic**: Vibrant multi-hue
6. **Electric**: Neon greens and purples

### Animation Patterns

**Circular**
- Rotates Julia parameter around complex plane
- Creates smooth morphing effect
- Speed: 0.1x - 2.0x

**Zoom**
- Gradually zooms into fractal
- Reveals increasing detail
- Automatic reset at deep zoom

**Wave**
- Oscillates Julia parameters
- Creates breathing effect
- Gentle, meditative motion

---

## Synthesis Engines

### 1. Pluck (Karplus-Strong)

**Character**: Acoustic, plucked string simulation

**Parameters:**
- Frequency determines string pitch
- Brightness controls filter cutoff
- Attack/Release shape amplitude envelope

**Use Cases:**
- Harp-like textures
- Guitar-style articulation
- Fast rhythmic passages

**Factory Presets:**
- Pluck: Ambient Sparkle
- Pluck: Rhythmic Pulse
- Pluck: Deep Resonance

---

### 2. FM Bell

**Character**: Bright, metallic, bell-like tones

**Algorithm**: 2-operator FM (sine waves)
- Carrier frequency = note pitch
- Modulator creates harmonic complexity
- High modulation index = more harmonics

**Use Cases:**
- Ethereal, shimmering textures
- Gamelan-style percussion
- Crystalline soundscapes

**Factory Presets:**
- FM Bell: Shimmer
- FM Bell: Crystalline
- FM Bell: Gamelan

---

### 3. Additive Bell

**Character**: Rich, harmonic, glockenspiel-like

**Algorithm**: 8 sine wave partials
- Harmonic series (1x, 2x, 3x, 4x...)
- Amplitude decay on higher partials
- Pure, non-aliasing sound

**Use Cases:**
- Chimes and bells
- Melodic leads
- Harmonic drones

**Factory Presets:**
- Additive: Bright Chimes
- Additive: Glockenspiel
- Additive: Temple Bells

---

### 4. Soft Waveshaping

**Character**: Warm, analog-style distortion

**Algorithm**: Soft clipping waveshaper
- Tanh() transfer function
- Adds odd harmonics
- Smooth saturation

**Use Cases:**
- Warm pads
- Breathy textures
- Bass tones

**Factory Presets:**
- Waveshape: Warm Pad
- Waveshape: Breathy Texture
- Waveshape: Growling Bass

---

### 5. Organ (8 Partials)

**Character**: Hammond-style organ with drawbar control

**Algorithm**: 8 independent sine waves
- Drawbar-style amplitude control
- Pure harmonic series
- Classic organ sound

**Use Cases:**
- Cathedral drones
- Jazz organ
- Dark ambient textures

**Factory Presets:**
- Organ: Cathedral
- Organ: Jazz Hammond
- Organ: Dark Drone

---

### 6. Granular

**Character**: Cloud-like, textural, glitchy

**Algorithm**: Grain synthesis
- Short overlapping grains
- Random pitch/time variation
- Dense texture generation

**Use Cases:**
- Ambient clouds
- Glitch storms
- Ethereal washes

**Factory Presets:**
- Granular: Cloud Texture
- Granular: Glitch Storm
- Granular: Ethereal Wash

---

### 7. Filtered Noise

**Character**: Wind, percussion, textural

**Algorithm**: White noise + resonant filter
- Pitch controls filter center frequency
- Brightness controls resonance
- Percussive or sustained

**Use Cases:**
- Wind textures
- Percussion hits
- Ocean surf sounds

**Factory Presets:**
- Noise: Wind Texture
- Noise: Percussion Hits
- Noise: Ocean Surf

---

### 8. Subtractive (Moog)

**Character**: Analog synthesizer, classic subtractive

**Algorithm**: Sawtooth + lowpass filter
- Moog ladder filter (24dB/octave)
- Brightness = cutoff frequency
- Fat, warm tones

**Use Cases:**
- Analog leads
- Fat bass
- Acid sequences

**Factory Presets:**
- Subtractive: Analog Lead
- Subtractive: Fat Bass
- Subtractive: Acid Sequence

---

### 9. FM Mallet

**Character**: Pitched percussion, marimba, vibraphone

**Algorithm**: FM with exponential envelope
- Fast attack, medium release
- Transposed down one octave
- Wooden, resonant quality

**Use Cases:**
- Marimba textures
- Vibraphone pads
- Kalimba patterns

**Factory Presets:**
- Mallet: Marimba
- Mallet: Vibraphone
- Mallet: Kalimba

---

### 10. Waveguide String

**Character**: Physical modeling, string resonance

**Algorithm**: Digital waveguide synthesis
- Plucked/bowed string simulation
- Natural harmonic series
- Reduced volume (70%)

**Use Cases:**
- Plucked strings
- Bowed strings
- Harp glissandos

**Factory Presets:**
- Waveguide: Plucked String
- Waveguide: Bowed String
- Waveguide: Harp Glissando

---

### 11. Supersaw

**Character**: Thick, detuned, trance/EDM

**Algorithm**: 7 detuned sawtooth waves
- Stereo spread
- Massive, wide sound
- Dance music staple

**Use Cases:**
- Epic pads
- Dance leads
- Trance chords

**Factory Presets:**
- Supersaw: Epic Pad
- Supersaw: Dance Lead
- Supersaw: Trance Chord

---

## Musical Quantization

### Key / Root Note

**Purpose**: Sets the tonal center for scale quantization

**Options:**
- **Atonal (Curve-Based)**: No key center, pure fractal mapping
- **12 Chromatic Notes**: C, C#, D, D#, E, F, F#, G, G#, A, A#, B

**How It Works:**
When a key is selected (not Atonal), all frequencies are quantized to the nearest note in the selected scale, transposed to that key.

**Example:**
- Key: C#, Scale: Pentatonic Major
- Result: All notes snap to C#, D#, F#, G#, A#

---

### Scale Quantization

**Purpose**: Restricts pitches to musically consonant intervals

#### 12-TET Scales

**Chromatic (12-TET)**
- All 12 semitones
- No restriction
- 12 notes per octave

**Major Scale**
- Pattern: W-W-H-W-W-W-H
- Intervals: 0, 2, 4, 5, 7, 9, 11
- Happy, bright character

**Natural Minor**
- Pattern: W-H-W-W-H-W-W
- Intervals: 0, 2, 3, 5, 7, 8, 10
- Sad, dark character

**Dorian Mode**
- Intervals: 0, 2, 3, 5, 7, 9, 10
- Jazz, folk flavor

**Phrygian Mode**
- Intervals: 0, 1, 3, 5, 7, 8, 10
- Spanish, exotic sound

**Lydian Mode**
- Intervals: 0, 2, 4, 6, 7, 9, 11
- Dreamy, floating quality

**Mixolydian Mode**
- Intervals: 0, 2, 4, 5, 7, 9, 10
- Blues, rock flavor

**Locrian Mode**
- Intervals: 0, 1, 3, 5, 6, 8, 10
- Unstable, dissonant

**Harmonic Minor**
- Intervals: 0, 2, 3, 5, 7, 8, 11
- Classical, Middle Eastern

**Melodic Minor**
- Intervals: 0, 2, 3, 5, 7, 9, 11
- Jazz, classical

**Pentatonic Major**
- Intervals: 0, 2, 4, 7, 9
- Universal, consonant (5 notes)

**Pentatonic Minor**
- Intervals: 0, 3, 5, 7, 10
- Blues, rock (5 notes)

**Blues Scale**
- Intervals: 0, 3, 5, 6, 7, 10
- Blues, expressive (6 notes)

**Whole Tone**
- Intervals: 0, 2, 4, 6, 8, 10
- Debussy, impressionist (6 notes)

#### Microtonal Systems

**Bohlen-Pierce (13-ET)**
- 13 steps per tritave (3:1 ratio)
- Non-octave-based tuning
- Alien, otherworldly sound

**Quarter-Tone (24-ET)**
- 24 steps per octave
- Half the semitone
- Middle Eastern flavor

**19-TET**
- 19 equal divisions per octave
- Better pure thirds than 12-TET
- Renaissance-era tuning

**31-TET**
- 31 equal divisions per octave
- Excellent approximation of just intonation
- Classical meantone

**53-TET**
- 53 equal divisions per octave
- Near-perfect fifths and thirds
- Turkish/Arabic music

---

### Rhythm Quantization

**Purpose**: Snaps note durations to metric grid

#### How It Works

Instead of forcing all notes to a single duration, the system:
1. Calculates a raw duration from fractal data
2. Finds the **nearest** duration from the selected palette
3. Allows variety while maintaining rhythmic coherence

#### Standard Note Values

**Whole Notes**: 4.0 beats
- Palette: [4.0, 2.0, 1.0] (Whole, Half, Quarter)

**Half Notes**: 2.0 beats
- Palette: [2.0, 1.0, 0.5] (Half, Quarter, Eighth)

**Quarter Notes**: 1.0 beats
- Palette: [1.0, 0.5, 0.25] (Quarter, Eighth, Sixteenth)

**Eighth Notes**: 0.5 beats
- Palette: [0.5, 0.25, 0.125] (Eighth, Sixteenth, 32nd)

**Sixteenth Notes**: 0.25 beats
- Palette: [0.25, 0.125, 0.0625] (16th, 32nd, 64th)

**Thirty-Second Notes**: 0.125 beats
**Sixty-Fourth Notes**: 0.0625 beats
**128th Notes**: 0.03125 beats

#### Dotted Rhythms

Dotted notes = 1.5x base duration

**Dotted Whole**: 6.0 beats
**Dotted Half**: 3.0 beats
**Dotted Quarter**: 1.5 beats
**Dotted Eighth**: 0.75 beats
**Dotted Sixteenth**: 0.375 beats
**Dotted 32nd**: 0.1875 beats
**Dotted 64th**: 0.09375 beats
**Dotted 128th**: 0.046875 beats

#### Triplet Rhythms

Triplets = base duration ÷ 3

**Triplet Half**: 2/3 beats
**Triplet Quarter**: 1/3 beats
**Triplet Eighth**: 1/6 beats
**Triplet Sixteenth**: 1/12 beats
**Triplet 32nd**: 1/24 beats
**Triplet 64th**: 1/48 beats
**Triplet 128th**: 1/96 beats

---

## Effects Processing

### Signal Flow

All 11 synthesis engines route to 4 global effect buses:

```
Instrument Output
    ↓
Send → Reverb Bus (Instrument 999)
Send → Echo Bus (Instrument 998)
Send → Chorus Bus (Instrument 997)
Send → Flanger Bus (Instrument 996)
    ↓
All buses summed
    ↓
Master Volume
    ↓
Stereo Output
```

### Reverb (Freeverb)

**Algorithm**: Freeverb by Jezar at Dreampoint

**Parameters:**
- **Mix**: 0-100% wet/dry blend
- **Room Size**: 0.85 (fixed, large hall)
- **Damping**: 0.7 (fixed, warm character)

**Character:**
- Lush, diffuse reverb
- Long decay time
- Suitable for all synthesis types

**Typical Settings:**
- Subtle: 20-30%
- Ambient: 50-70%
- Extreme: 80-90%

---

### Echo (Variable Delay)

**Algorithm**: Single delay line with feedback

**Parameters:**
- **Time**: 0.01-2.0 seconds
- **Feedback**: 0-90%
- **Mix**: 0-100%

**Character:**
- Discrete echoes at set interval
- Self-oscillates at high feedback
- Rhythmic enhancement

**Typical Settings:**
- Slapback: Time=0.1s, Feedback=20%, Mix=25%
- Rhythmic: Time=0.375s, Feedback=50%, Mix=30%
- Ambient: Time=1.0s, Feedback=40%, Mix=15%

**Tempo Sync:**
To sync echo to tempo, calculate:
```
Time = (60 / BPM) * beats
```
Example: 120 BPM, dotted quarter (1.5 beats)
```
Time = (60 / 120) * 1.5 = 0.75 seconds
```

---

### Chorus (Multi-Tap)

**Algorithm**: Multi-tap delay with LFO modulation

**Parameters:**
- **Rate**: 0.1-10 Hz (LFO speed)
- **Depth**: 0-20ms (pitch variation)
- **Mix**: 0-100%

**Character:**
- Thickens and widens sound
- Subtle pitch variation
- Classic 80s synth effect

**Typical Settings:**
- Subtle: Rate=0.5Hz, Depth=3ms, Mix=15%
- Classic: Rate=0.8Hz, Depth=7ms, Mix=25%
- Vibrato: Rate=4Hz, Depth=15ms, Mix=50%

---

### Flanger (Short Delay + LFO)

**Algorithm**: Very short delay (0-10ms) with LFO + feedback

**Parameters:**
- **Rate**: 0.1-10 Hz
- **Depth**: 0-10ms
- **Feedback**: -80% to +80%
- **Mix**: 0-100%

**Character:**
- Jet-plane sweeping effect
- Comb filtering (notches in frequency)
- Dramatic modulation

**Typical Settings:**
- Subtle: Rate=0.3Hz, Depth=2ms, Feedback=20%, Mix=10%
- Classic: Rate=0.5Hz, Depth=5ms, Feedback=50%, Mix=30%
- Extreme: Rate=1.5Hz, Depth=8ms, Feedback=70%, Mix=50%

**Negative Feedback:**
Creates inverted comb filtering for different tonal character

---

## Parameter Mapping

### Concept

Parameter mapping connects **fractal data** to **synthesis parameters**, creating organic, evolving musical relationships.

### 6 Fractal Sources

1. **Iteration Count** (iterNorm)
   - Normalized iteration count (0-1)
   - Higher at fractal boundary
   - Lower in solid regions

2. **X Position** (xRatio)
   - Horizontal position (0-1)
   - Left to right across canvas
   - Good for pan, stereo imaging

3. **Y Position** (yRatio)
   - Vertical position (0-1, inverted)
   - Top to bottom
   - Good for pitch, duration

4. **Smoothness**
   - Local texture variation
   - Based on neighbor iteration counts
   - Good for brightness, modulation

5. **Angle**
   - Phase in complex plane
   - Cyclic parameter (0-1)
   - Good for cyclic modulation

6. **Magnitude**
   - Distance from origin
   - Normalized 0-1 (capped at 3)
   - Good for amplitude, release

### 8 Synthesis Targets

1. **Pitch**
   - Maps to frequency
   - Range: baseFreq * 2^(value * pitchRange)
   - Most common mapping: Iteration → Pitch

2. **Amplitude**
   - Maps to note volume
   - Range: 0.05 to 0.8
   - Common mappings: Magnitude → Amplitude

3. **Pan**
   - Stereo position
   - Range: 0 (left) to 1 (right)
   - Most common: X Position → Pan

4. **Brightness**
   - Filter cutoff or harmonic content
   - Range: 0-1
   - Common: Iteration → Brightness

5. **Duration**
   - Note length
   - Range: 0.05s to 3s (scaled by Duration Scale)
   - Common: Y Position → Duration

6. **Tempo/Spacing**
   - Time between notes
   - Range: 0.02 to 2.0 beats
   - Common: Smoothness → Tempo

7. **Attack**
   - Envelope attack time
   - Range: 1ms to 1000ms
   - Common: Iteration → Attack (swells on boundary)

8. **Release**
   - Envelope release time
   - Range: 5ms to 3000ms
   - Common: Magnitude → Release (longer at extremes)

### Default Mapping

```
Iteration → Pitch (most important)
X Position → Pan (stereo imaging)
Y Position → Duration (vertical = time)
Smoothness → None
Angle → None
Magnitude → None
```

### Advanced Mapping Examples

**Organic Melody:**
```
Iteration → Pitch
Y Position → Tempo
Magnitude → Amplitude
Smoothness → Brightness
```
Result: Melodic lines that speed up/slow down based on vertical position, with dynamic brightness.

**Textural Cloud:**
```
Iteration → Brightness
X Position → Pan
Y Position → Release
Angle → Duration
Smoothness → Pitch
```
Result: Non-pitched texture with spatial movement and evolving character.

**Rhythmic Pulse:**
```
Iteration → Tempo
X Position → Pan
Y Position → Amplitude
Angle → Pitch
```
Result: Rhythmic acceleration/deceleration with pitched hits.

### Effect Mapping

Maps fractal data to effect parameters in real-time:

**Echo Parameters:**
- Echo Time (0.01-2.0s)
- Echo Feedback (0-0.9)

**Chorus Parameters:**
- Chorus Rate (0.1-10 Hz)
- Chorus Depth (0-20ms)

**Flanger Parameters:**
- Flanger Rate (0.1-10 Hz)
- Flanger Depth (0-10ms)

**Example:**
```
Smoothness → Echo Time
Angle → Chorus Rate
Magnitude → Flanger Depth
```
Result: Effects modulate with fractal texture, creating living, breathing soundscapes.

---

## Preset Management

### Factory Presets (45 Total)

**Instrument-Focused (33 presets):**
- 3 per synthesis engine
- Showcase different musical contexts
- Professionally balanced

**Visual-Focused (12 presets):**
- Highlight fractal exploration
- 2 per fractal type
- Cinematic soundscapes

### Saving Presets

1. Configure all parameters (synthesis, fractal, effects, mappings)
2. Type a name in the "Preset name..." field
3. Click **💾 Save**
4. Preset appears in dropdown list

**What's Saved:**
- Fractal settings (mode, zoom, center, Julia params, color, animation)
- Synthesis type
- All musical parameters (tempo, pitch, density, duration)
- Key, scale, rhythm quantization
- Attack/Release envelope
- All 4 effects (settings + mix levels)
- All parameter mappings (synth + effects)

### Loading Presets

1. Select from **Saved Presets** dropdown
2. Click **📂 Load**
3. All settings update instantly
4. Audio restarts automatically (if playing)

### Deleting Presets

1. Select preset from dropdown
2. Click **🗑️ Delete**
3. Confirm deletion
4. Preset removed from list

### Preset Storage

Presets stored in browser's **localStorage**:
- Persistent across sessions
- Unique to each browser/device
- Not synced across browsers

**Data Format**: JSON
**Key**: `"mandelbrotPresets"`

### Organizing Presets

**Naming Convention:**
```
Type: Descriptor
```

Examples:
- FM Bell: Shimmer
- Granular: Cloud Texture
- Visual: Seahorse Valley
- Custom: My Ambient Dream

**Tips:**
- Use descriptive names
- Include synthesis type for quick reference
- Create categories (Ambient, Rhythmic, Percussive, Drone)

---

## Advanced Techniques

### 1. Evolving Soundscapes

**Goal**: Create continuously changing sonic environments

**Method:**
1. Select **Julia Set** mode
2. Enable **Animation** (Circular pattern)
3. Set Animation Speed to 0.3-0.5
4. Choose **Granular** or **Organ** synthesis
5. Set high **Reverb** (70-80%)
6. Map **Angle → Chorus Rate**
7. Map **Smoothness → Echo Time**

Result: Fractal morphs continuously, creating ever-evolving textures with synchronized effects.

---

### 2. Rhythmic Exploration

**Goal**: Create complex, generative rhythmic patterns

**Method:**
1. Select fast **Tempo** (140-180 BPM)
2. Choose **Pluck** or **Mallet** synthesis
3. Set **Rhythm Quantization** to Sixteenth or Triplet Eighth
4. Increase **Note Density** (8-12%)
5. Map **Y Position → Tempo** (rhythmic acceleration)
6. Map **Smoothness → Duration**
7. Add **Echo** with rhythmic time (e.g., 0.375s = dotted eighth)

Result: Polyrhythmic, generative patterns with organic variation.

---

### 3. Melodic Phrases

**Goal**: Create recognizable melodic material

**Method:**
1. Select a **Key** (e.g., C#)
2. Choose a **Scale** (e.g., Pentatonic Major)
3. Set **Rhythm Quantization** (Quarter or Eighth)
4. Use **FM Bell** or **Additive** synthesis
5. **Iteration → Pitch** (default)
6. **Y Position → Duration**
7. Zoom into boundary regions slowly
8. Low **Note Density** (3-5%)

Result: Recognizable melodic phrases that follow the scale, with controlled note lengths.

---

### 4. Extreme Microtonal

**Goal**: Explore non-Western tuning systems

**Method:**
1. Select **Key**: Atonal
2. **Scale**: Bohlen-Pierce or 19-TET or 53-TET
3. **Base Frequency**: 165 Hz (lower)
4. **Pitch Range**: 3-4 octaves
5. Use **Subtractive** or **Waveshaping** synthesis
6. Medium **Note Density** (5-7%)
7. **Iteration → Pitch**
8. Add subtle **Chorus** for texture

Result: Alien, non-octave-based harmonies that explore unfamiliar sonic territory.

---

### 5. Glitch/Noise Textures

**Goal**: Create rhythmic, glitchy, noisy soundscapes

**Method:**
1. **Synthesis**: Filtered Noise or Granular
2. **Tempo**: 160-200 BPM
3. **Rhythm**: Triplet Sixteenth or 128th notes
4. **Note Density**: 15-20% (high)
5. **Attack**: 1-2ms
6. **Release**: 50-100ms (short)
7. Map **Iteration → Tempo** (chaotic acceleration)
8. Map **Smoothness → Brightness**
9. Add **Flanger** (high rate, high depth)

Result: Dense, glitchy, rhythmic noise textures perfect for experimental music.

---

### 6. Ambient Drone

**Goal**: Sustained, slowly evolving drone

**Method:**
1. **Synthesis**: Organ or Supersaw
2. **Tempo**: 30-50 BPM (very slow)
3. **Rhythm**: Whole or Dotted Whole
4. **Duration Scale**: 2.5-3.0 (very long)
5. **Note Density**: 2-3% (sparse)
6. **Attack**: 100-200ms (slow swell)
7. **Release**: 1500-2000ms (long decay)
8. **Reverb**: 80-90% (massive)
9. Map **Magnitude → Release**
10. Julia Set with slow **Circular** animation

Result: Massive, evolving drone with natural harmonic movement.

---

### 7. Fractal Zoom Journey

**Goal**: Sonic journey through increasing detail

**Method:**
1. Start at **Mandelbrot Set**, default zoom
2. Select **Additive Bell** or **FM Bell**
3. Enable **Animation** (Zoom pattern)
4. **Reverb**: 60%
5. **Echo**: Time=0.5s, Mix=20%
6. As zoom increases, iteration count rises
7. **Iteration → Pitch**: creates rising melody
8. **Y Position → Duration**: rhythmic variation

Result: Musical journey that rises in pitch as fractal reveals increasing detail.

---

### 8. Call-and-Response

**Goal**: Create musical dialogue between fractal regions

**Method:**
1. Select **Julia Set**
2. **Synthesis**: Waveguide String
3. **Rhythm**: Eighth or Quarter notes
4. Map **X Position → Pan** (left/right separation)
5. Map **Iteration → Pitch**
6. Map **Y Position → Amplitude** (top = loud, bottom = quiet)
7. Click different regions to explore

Result: Musical phrases emerge from different spatial locations, creating conversational texture.

---

### 9. Parameter Morphing

**Goal**: Smoothly transition between sonic states

**Method:**
1. Save Preset A (e.g., "Ambient Pad")
2. Change parameters dramatically
3. Save Preset B (e.g., "Rhythmic Pulse")
4. **Load Preset A** → Play → Listen
5. **Load Preset B** → Audio restarts with new character
6. Create 3-5 presets as waypoints
7. Perform by loading presets in sequence

Result: Live performance technique for structured sonic evolution.

---

### 10. Effect Automation

**Goal**: Effects that respond to fractal complexity

**Method:**
1. Choose any synthesis type
2. Map **Iteration → Echo Time** (longer echo on boundary)
3. Map **Smoothness → Chorus Rate** (faster on textured regions)
4. Map **Angle → Flanger Depth** (cyclic sweeping)
5. Zoom and pan through fractal
6. Effects intensify/change based on region

Result: Effects become integral to the sonic narrative, not static processors.

---

## Troubleshooting

### Audio Issues

**Problem**: No sound when playing
- Check Master Volume > 0%
- Verify system volume
- Check browser isn't muted
- Try headphones to test output
- Refresh page and restart

**Problem**: Distortion or clipping
- Lower Master Volume to 30-40%
- Reduce Reverb Mix
- Lower Note Density
- Check individual effect levels

**Problem**: Audio cuts out intermittently
- Browser may be throttling
- Close other tabs
- Reduce Note Density
- Increase Duration Scale (fewer note triggers)

### Performance Issues

**Problem**: Slow fractal rendering
- Switch to **GPU (WebGL)** render mode
- Lower Max Iterations (50-100)
- Disable animation
- Reduce browser zoom

**Problem**: Audio glitches/stuttering
- Close other applications
- Reduce Note Density
- Disable real-time parameter mapping
- Check browser's performance settings

### Preset Issues

**Problem**: Presets not appearing
- Refresh page (factory presets auto-load)
- Check browser console for errors
- Clear browser cache
- Try different browser

**Problem**: Preset loads wrong settings
- Verify preset was saved correctly
- Check console for loading errors
- Delete and re-save preset

### Browser Compatibility

**Supported:**
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

**Required Features:**
- WebAssembly support
- Web Audio API
- Canvas 2D / WebGL
- localStorage

---

## Keyboard Shortcuts

- **Spacebar**: Toggle audio on/off

---

## Performance Tips

1. **Start Simple**: Low density, simple synthesis
2. **Optimize Effects**: Use 1-2 effects at a time
3. **Close Tabs**: Free browser resources
4. **GPU Rendering**: Use WebGL for large fractals
5. **Moderate Iterations**: 100-150 is usually sufficient

---

## Best Practices

1. **Save Often**: Create presets for discoveries
2. **Name Clearly**: Use descriptive preset names
3. **Start with Presets**: Use factory presets as templates
4. **Experiment**: Randomize to find happy accidents
5. **Layer Effects**: Combine effects for rich textures
6. **Zoom Slowly**: Best sounds emerge at boundaries
7. **Adjust Density**: More isn't always better (3-7% is sweet spot)

---

**End of User's Guide**

[Return to Documentation Home](index.html)
