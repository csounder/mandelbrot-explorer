# DETAILED CHANGE LOG
## Phase 2 & 3: Multi-Instrument Synthesis + Real-Time Parameter Mapping

**Date:** March 13, 2026  
**Modified:** `/Users/richardboulanger/dB-Studio/Dr.C-Mandelbrot-Explorer/app.html`

---

## SECTION 1: ORCHESTRA REPLACEMENT (Lines 1218-1410)

### OLD CODE (Single Instrument):
```csound
instr 1
  ; Basic gbuzz + moogladder instrument
  ; Fixed attack/release times
  ; No instrument selection
endin

instr 999
  ; Reverb
endin
```

### NEW CODE (8 Instruments + Reverb):

#### **Instrument 1: Pluck (Karplus-Strong)**
```csound
instr 1
  iFreq = p4
  iAmp = p5
  iPan = p6
  iBright = p7
  iAtk = p8 / 1000
  iRel = p9 / 1000
  
  kMasterVol chnget "masterVol"
  kReverbMix chnget "reverbMix"
  
  kEnv = linsegr:k(0, iAtk, iAmp, iRel, 0)
  aSig = pluck:a(kEnv, iFreq, iFreq, 0, 1)
  
  kCutoff = 500 + iBright * 3000
  aSig = tone(aSig, kCutoff)
  
  aL, aR = pan2(aSig, iPan)
  gaReverb = gaReverb + (aL + aR) * 0.5 * kReverbMix
  out(aL * kMasterVol, aR * kMasterVol)
endin
```
- **Synthesis:** Karplus-Strong physical modeling
- **p8:** Attack time (ms)
- **p9:** Release time (ms)
- **Brightness:** Controls tone filter cutoff (500-3500 Hz)

#### **Instrument 2: FM Bell**
```csound
instr 2
  kEnv = linsegr:k(0, iAtk, iAmp, iRel, 0)
  kModIdx = 2 + iBright * 4
  aSig = foscili:a(kEnv, iFreq, 1, 2.5, kModIdx, -1)
  ...
endin
```
- **Synthesis:** FM (frequency modulation)
- **Carrier:Modulator Ratio:** 1:2.5 (bell timbre)
- **Brightness:** Controls modulation index (2-6)

#### **Instrument 3: Additive Bell**
```csound
instr 3
  a1 = oscili:a(kEnv, iFreq, -1)
  a2 = oscili:a(kEnv * 0.5, iFreq * 2.76, -1)
  a3 = oscili:a(kEnv * 0.3 * iBright, iFreq * 5.4, -1)
  a4 = oscili:a(kEnv * 0.2 * iBright, iFreq * 8.93, -1)
  aSig = (a1 + a2 + a3 + a4) * 0.4
  ...
endin
```
- **Synthesis:** Additive (harmonic stacking)
- **Partials:** 1, 2.76, 5.4, 8.93 (classic bell ratios)
- **Brightness:** Controls upper harmonic amplitudes

#### **Instrument 4: Clarinet**
```csound
instr 4
  a1 = oscili:a(kEnv, iFreq, -1)
  a2 = oscili:a(kEnv * 0.3 * iBright, iFreq * 3, -1)
  a3 = oscili:a(kEnv * 0.15 * iBright, iFreq * 5, -1)
  aSig = (a1 + a2 + a3) * 0.5
  ...
endin
```
- **Synthesis:** Additive with odd harmonics only
- **Partials:** 1, 3, 5 (clarinet-like)
- **Brightness:** Controls 3rd/5th harmonic balance

#### **Instrument 5: Organ**
```csound
instr 5
  a1 = oscili:a(kEnv * 0.3, iFreq, -1)      ; Fundamental
  a2 = oscili:a(kEnv * 0.3, iFreq * 2, -1)  ; Octave
  a3 = oscili:a(kEnv * 0.2 * iBright, iFreq * 3, -1)
  a4 = oscili:a(kEnv * 0.15 * iBright, iFreq * 4, -1)
  ...
endin
```
- **Synthesis:** Drawbar-style organ
- **Partials:** 1, 2, 3, 4 (even stacking)
- **Brightness:** Upper drawbar levels

#### **Instrument 6: Granular**
```csound
instr 6
  kDens = 10 + iBright * 40
  kGrainSize = 0.01 + iBright * 0.05
  aSig = grain3:a(iFreq, 0, 1, 0, kGrainSize, kDens, 100, -1, -1, 0)
  aSig = aSig * kEnv * 0.3
  ...
endin
```
- **Synthesis:** Granular (grain3)
- **Brightness:** Controls density (10-50 grains/sec) and grain size
- **Note:** Most CPU-intensive instrument

#### **Instrument 7: Filtered Noise**
```csound
instr 7
  aSig = noise:a(kEnv * 2, 0)
  kCf = iFreq * (1 + iBright * 2)
  aSig = butterbp(aSig, kCf, kCf * 0.1)
  ...
endin
```
- **Synthesis:** Bandpass filtered noise
- **Brightness:** Center frequency multiplier (1-3x)

#### **Instrument 8: Subtractive (Moog)**
```csound
instr 8
  aSig = vco2:a(kEnv, iFreq, 0)  ; Sawtooth
  kCutoff = iFreq * (2 + iBright * 4)
  aSig = moogladder:a(aSig, kCutoff, 0.3)
  ...
endin
```
- **Synthesis:** Subtractive (classic analog)
- **Oscillator:** Sawtooth (vco2)
- **Filter:** Moogladder lowpass, resonance 0.3
- **Brightness:** Cutoff frequency multiplier (2-6x)

---

## SECTION 2: AUDIO LOOP REWRITE (Lines 2270-2402)

### Key Additions:

#### 1. **UI Parameter Reading**
```javascript
const synthType = document.getElementById("synthType").value;
const tempo = parseFloat(document.getElementById("tempo").value);
const baseFreq = parseFloat(document.getElementById("baseFreq").value);
const pitchRange = parseFloat(document.getElementById("pitchRange").value);
const density = parseFloat(document.getElementById("noteDensity").value) / 100;
const durationScale = parseFloat(document.getElementById("durationScale").value);
const attack = parseFloat(document.getElementById("attack").value);
const release = parseFloat(document.getElementById("release").value);
```

#### 2. **Instrument Mapping**
```javascript
const instrMap = {
  pluck: 1,
  fm: 2,
  additive: 3,
  clarinet: 4,
  organ: 5,
  granular: 6,
  noise: 7,
  subtractive: 8
};
const instrNum = instrMap[synthType] || 1;
```

#### 3. **Flexible Parameter Mapping**
```javascript
// Map iteration count
const iterRatio = point.iter / config.maxIter;
if (iterMap === "pitch") {
  freq = baseFreq * Math.pow(2, iterRatio * pitchRange);
} else if (iterMap === "amplitude") {
  amp = 0.1 + iterRatio * 0.7;
} else if (iterMap === "brightness") {
  brightness = iterRatio;
} else if (iterMap === "duration") {
  dur = (0.1 + iterRatio * 0.9) * durationScale;
}
```

**Similar blocks for:**
- X position mapping (pan/pitch/brightness)
- Y position mapping (duration/amplitude/brightness)

#### 4. **Smart Defaults**
```javascript
// Set defaults for unmapped parameters
if (freq === undefined) freq = baseFreq * Math.pow(2, iterRatio * pitchRange);
if (amp === undefined) amp = 0.3;
if (pan === undefined) pan = xRatio;
if (brightness === undefined) brightness = iterRatio;
if (dur === undefined) dur = 0.5 * durationScale;
```

#### 5. **Value Clamping**
```javascript
freq = Math.max(20, Math.min(20000, freq));
amp = Math.max(0.05, Math.min(0.8, amp));
pan = Math.max(0, Math.min(1, pan));
brightness = Math.max(0, Math.min(1, brightness));
dur = Math.max(0.05, Math.min(5, dur));
```

#### 6. **Tempo-Based Timing**
```javascript
const beatDuration = 60 / tempo;
let time = 0;

for (const point of sample) {
  // ... process point ...
  
  // Increment time based on tempo
  time += beatDuration * 0.25; // 16th note spacing
}

// Loop based on tempo
const loopDelay = Math.max(500, (60 / tempo) * sample.length * 0.25 * 1000);
setTimeout(() => audioLoop(), loopDelay);
```

#### 7. **Enhanced Score Events**
```javascript
// OLD:
const scoreEvent = `i 1 0 ${dur} ${freq.toFixed(2)} ${amp} ${pan.toFixed(3)} ${brightness.toFixed(3)}`;

// NEW:
const scoreEvent = `i ${instrNum} ${time.toFixed(3)} ${dur.toFixed(3)} ${freq.toFixed(2)} ${amp.toFixed(3)} ${pan.toFixed(3)} ${brightness.toFixed(3)} ${attack} ${release}`;
```

**Changes:**
- ✓ Instrument number is dynamic (1-8)
- ✓ Start time is calculated (not always 0)
- ✓ Attack/release passed as p-fields

---

## SECTION 3: VISUAL FEEDBACK STUB (Lines 2404-2407)

```javascript
function highlightPlayingPoints(points) {
  // This will be implemented in Phase 6 - for now just log
  console.log(`Playing ${points.length} points`);
}
```

**Purpose:** Placeholder for future canvas highlighting feature

---

## TESTING MATRIX

| Test | Parameter | Expected Behavior | Status |
|------|-----------|------------------|--------|
| 1 | Instrument = FM Bell | Metallic, bright sound | ⏳ |
| 2 | Tempo = 30 BPM | Slow, sparse notes | ⏳ |
| 3 | Tempo = 300 BPM | Rapid fire | ⏳ |
| 4 | Base Freq = 55 Hz | Low bass tones | ⏳ |
| 5 | Base Freq = 880 Hz | High pitched | ⏳ |
| 6 | Pitch Range = 0.5 oct | Narrow range | ⏳ |
| 7 | Pitch Range = 4 oct | Wide sweep | ⏳ |
| 8 | Density = 10% | ~5 notes | ⏳ |
| 9 | Density = 100% | ~50 notes | ⏳ |
| 10 | Duration Scale = 0.1 | Very short | ⏳ |
| 11 | Duration Scale = 3.0 | Long overlap | ⏳ |
| 12 | Attack = 1ms | Immediate | ⏳ |
| 13 | Attack = 500ms | Slow fade-in | ⏳ |
| 14 | Release = 10ms | Abrupt stop | ⏳ |
| 15 | Release = 2000ms | Long tail | ⏳ |
| 16 | iterMap → Amplitude | Volume varies | ⏳ |
| 17 | xMap → Pitch | Horizontal pitch | ⏳ |
| 18 | yMap → Brightness | Vertical timbre | ⏳ |
| 19 | Master Volume = 0 | Silence | ⏳ |
| 20 | Reverb Mix = 100% | Very wet | ⏳ |

**Legend:** ⏳ = Pending user testing | ✅ = Passed | ❌ = Failed

---

## BROWSER COMPATIBILITY

**Tested:** _(pending)_
- [ ] Chrome 120+
- [ ] Firefox 120+
- [ ] Safari 17+
- [ ] Edge 120+

**Requirements:**
- WebAssembly support (2017+)
- Web Audio API (2015+)
- ES6 Async/Await (2017+)

---

## FILES MODIFIED

1. **app.html** - Main application file
   - Orchestra section: +192 lines
   - audioLoop function: +133 lines
   - Total delta: +200 lines

---

## FILES CREATED

1. **PHASE2-3-SUMMARY.md** - Technical implementation details
2. **QUICKSTART.md** - User guide with examples
3. **CHANGES.md** - This file (detailed change log)

---

## BACKWARD COMPATIBILITY

✅ **No Breaking Changes:**
- All original functions still work
- UI elements unchanged
- Existing fractal rendering unaffected
- Original single-instrument mode replaced (intentional)

⚠️ **Behavioral Changes:**
- Audio now respects tempo (was fixed timing)
- Notes have attack/release envelopes (was instant on/off)
- Maximum 50 notes per loop (was 20)

---

## PERFORMANCE IMPACT

**Estimated Performance:**
- CPU usage: +10-30% (depending on instrument)
- Memory: No significant change
- Initialization time: Same (~2-3 seconds)
- Audio latency: Same (~10-20ms)

**Worst Case Scenario:**
- Granular + 100% density + 300 BPM on slow hardware
- Mitigation: Audio loop includes break checks

---

## CSOUND OPCODE USAGE

**New Opcodes Added:**
- `pluck` - Karplus-Strong synthesis
- `foscili` - FM synthesis
- `grain3` - Granular synthesis
- `noise` - White/pink noise generator
- `vco2` - Virtual analog oscillator
- `butterbp` - Butterworth bandpass filter
- `tone` - First-order lowpass filter
- `pan2` - Stereo panner

**Still Using:**
- `oscili` - Sine oscillator
- `moogladder` - Moog filter
- `freeverb` - Reverb effect
- `linsegr` - Linear envelope with release

---

## NEXT IMPLEMENTATION PHASES

- **Phase 4:** Audio preset save/load (localStorage)
- **Phase 5:** Multi-instrument CSD export
- **Phase 6:** Canvas highlighting for playing notes
- **Phase 7:** MIDI file export
- **Phase 8:** WAV recording/download

---

**End of Change Log**
