# Audio Enhancement Plan for Mandelbrot Explorer

## Current Issues

1. **Limited sonic control** - Only one synth voice (gbuzz + filter)
2. **Fixed parameter mapping** - No way to change what maps to what
3. **No compositional control** - Missing tempo, register, density controls
4. **No presets** - Can't save/load audio configurations
5. **Unclear mapping** - User doesn't see what's controlling what

## Proposed Enhancements

### 1. Multiple Csound Instruments (8 Types)

Copy the proven instruments from L-Systems explorer:

- **Pluck** (Karplus-Strong) - Percussive, clean
- **FM Bell** - Metallic, harmonic-rich
- **Additive Bell** - Inharmonic partials
- **Clarinet** - Odd harmonics, woody
- **Organ** - Drawbar-style harmonics
- **Filtered Noise** - Percussive textures
- **Granular** - Clouds, atmospheres
- **Warm Pad** - Detuned oscillators

### 2. Musical Parameter Controls

```
Tempo: 30-300 BPM
Base Frequency: 55-880 Hz (A1-A5)
Pitch Range: 0.5-4 octaves
Note Density: 10-100% (how many points trigger notes)
Duration Scale: 0.1x-3x
```

### 3. Flexible Parameter Mapping

Let users choose what fractal data controls:

```
Iteration Count → [Pitch | Amplitude | Brightness | Duration]
X Position → [Pan | Pitch | Brightness]
Y Position → [Duration | Amplitude | Brightness]
```

**Mapping Display Panel:**

```
Current Mapping:
├─ Iteration Count → Pitch
├─ X Position → Stereo Pan
└─ Y Position → Duration
```

### 4. Envelope & Effects

```
Attack: 1-500ms
Release: 10-2000ms
Master Volume: 0-100%
Reverb Mix: 0-100%
```

### 5. Audio Preset System

**Save/Load Presets:**

```javascript
{
  name: "Plucky Mandelbrot",
  instrument: "pluck",
  tempo: 120,
  baseFreq: 220,
  pitchRange: 2,
  density: 50,
  mapping: {
    iteration: "pitch",
    x: "pan",
    y: "duration"
  },
  attack: 10,
  release: 100,
  volume: 70,
  reverb: 30
}
```

**Factory Presets:**

- "Plucky Melody" - Fast plucks, pitch-driven
- "Bell Cascade" - FM bells, harmonic-rich
- "Granular Cloud" - Slow granular textures
- "Organ Chords" - Sustained organ tones
- "Percussive Noise" - Filtered noise bursts

### 6. UI Layout Changes

**Option A: 3-Column Layout**

```
[Fractal Controls] [Canvas] [Audio Controls]
      280px          1fr         320px
```

**Option B: Tabbed Audio Panel (Current 2-Column)**

```
[Fractal Controls] [Canvas]
      320px           1fr

[Audio Controls - Tabbed]
  Tabs: Instrument | Mapping | Effects | Presets
```

**Recommendation: Option A** - All controls visible, no tab switching needed

### 7. Real-Time Audio Loop Enhancement

Current loop just plays fractal data once per second.

**Enhanced Loop:**

```javascript
async function audioLoop() {
  // 1. Get current fractal data
  // 2. Apply density filter (only N% of points)
  // 3. Sort by selected mapping priority
  // 4. Calculate tempo-based timing (60/BPM)
  // 5. Map parameters based on user settings
  // 6. Send notes to selected Csound instrument
  // 7. Update visual indicator (which points are playing)
  // 8. Loop at tempo-controlled rate
}
```

### 8. Parameter Mapping Logic

```javascript
function mapParameter(fractalValue, targetParam, config) {
  switch (targetParam) {
    case "pitch":
      // iteration (0-maxIter) → frequency (baseFreq to baseFreq*2^pitchRange)
      const ratio = fractalValue / config.maxIter;
      return config.baseFreq * Math.pow(2, ratio * config.pitchRange);

    case "amplitude":
      // Map to 0.1-0.9 range
      return 0.1 + (fractalValue / config.maxIter) * 0.8;

    case "brightness":
      // Map to 0.0-1.0 for filter cutoff
      return fractalValue / config.maxIter;

    case "duration":
      // Map to duration scale
      return (0.1 + fractalValue / config.maxIter) * config.durationScale;

    case "pan":
      // X position (0-width) → 0-1 stereo
      return fractalValue / canvas.width;
  }
}
```

### 9. Csound Orchestra Structure

```csound
<CsInstruments>
sr = 44100
ksmps = 32
nchnls = 2
0dbfs = 1

; Global buses
gaRvbL init 0
gaRvbR init 0

; Instrument 1: Universal melodic (8 presets via p6)
instr 1
  iFreq = p4
  iAmp = p5
  iPreset = p6     ; 0-7 for different synth types
  iBright = p7     ; brightness 0-1
  iPan = p8        ; stereo pan 0-1
  iAtk = p9        ; attack time
  iRel = p10       ; release time

  kMasterVol chnget "masterVol"
  kReverbMix chnget "reverbMix"

  aEnv = linsegr:a(0, iAtk, iAmp, iRel, 0)

  ; Switch on preset...
  if (iPreset == 0) then
    aSig = pluck(aEnv, iFreq, iFreq, 0, 1)
  elseif (iPreset == 1) then
    aSig = foscili(aEnv, iFreq, 1, 2.5, 2+iBright*4, -1)
  ; ... etc for all 8 types
  endif

  ; Filter based on brightness
  kCutoff = 500 + iBright * 3000
  aSig = tone(aSig, kCutoff)

  ; Stereo pan
  aL, aR = pan2(aSig, iPan)

  ; Send to reverb
  gaRvbL += aL * kReverbMix
  gaRvbR += aR * kReverbMix

  ; Dry output
  out(aL * kMasterVol, aR * kMasterVol)
endin

; Instrument 999: Reverb
instr 999
  kMasterVol chnget "masterVol"
  aL, aR = freeverb(gaRvbL, gaRvbR, 0.85, 0.7)
  out(aL * kMasterVol, aR * kMasterVol)
  clear(gaRvbL, gaRvbR)
endin
</CsInstruments>
```

## Implementation Steps

1. **Phase 1: Add UI Controls** ✅
   - Add audio control panel (3-column layout)
   - Add all sliders/selects for parameters
   - Add mapping display panel
   - Wire up event listeners

2. **Phase 2: Enhance Csound Orchestra** ⏳
   - Copy proven instruments from L-Systems
   - Add control channels
   - Update instrument 1 with preset switching
   - Test each synth type

3. **Phase 3: Parameter Mapping System** ⏳
   - Create mapping configuration object
   - Implement mapParameter() function
   - Update audioLoop() to use mappings
   - Add real-time mapping changes

4. **Phase 4: Musical Controls** ⏳
   - Implement tempo-based timing
   - Add density filtering
   - Add pitch range control
   - Add duration scaling

5. **Phase 5: Preset System** ⏳
   - localStorage save/load
   - Factory presets
   - Import/export JSON

6. **Phase 6: Polish** ⏳
   - Add visual feedback (playing notes)
   - Improve status messages
   - Add tooltips
   - Performance optimization

## Timeline

- **Phase 1**: 30 minutes
- **Phase 2**: 45 minutes
- **Phase 3**: 30 minutes
- **Phase 4**: 20 minutes
- **Phase 5**: 20 minutes
- **Phase 6**: 15 minutes

**Total**: ~2.5 hours

## Questions for You

1. **UI Layout**: Prefer 3-column (all visible) or 2-column with tabs?
2. **Default Mapping**: What should be the default parameter mapping?
3. **Instruments**: Which 5-6 instruments are most important to you?
4. **Priority**: What's most important first - instruments, mapping flexibility, or presets?

---

**Next Step**: Once you approve this plan, I'll implement it systematically, testing each phase before moving to the next.

Let me know your thoughts!
