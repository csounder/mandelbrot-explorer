# CSD Recording Feature - Implementation Guide

## Overview

This feature allows users to record their live audio session as a complete, standalone Csound .csd file that can be played back in any Csound environment (CsoundQt, CLI, Cabbage, etc.).

## What It Does

- **Start Recording:** Writes complete orchestra (all instruments + effects) to textarea
- **During Recording:** Captures note events with real-time timestamps as they play
- **Stop Recording:** Adds closing tags and prompts user to download
- **Download:** Saves complete .csd file with timestamp filename

## Key Requirements

### 1. Global Variables

```javascript
let csdRecording = false;
let csdRecordingStartTime = 0;
```

### 2. UI Elements Required

```html
<!-- Recording Button -->
<button class="btn" id="generateCsdBtn" onclick="generateCsound()">
  🔴 Start CSD Recording
</button>

<!-- Download Button -->
<button
  class="btn-secondary"
  id="downloadCsdBtn"
  onclick="downloadCsd()"
  disabled
>
  💾 Download .CSD File
</button>

<!-- Output Textarea -->
<textarea id="csoundOutput"></textarea>
```

### 3. HTML Element IDs Needed

You must know the correct IDs for all control channels:

- Master volume slider
- Reverb/Echo/Chorus/Flanger mix sliders
- Effect parameter sliders (time, feedback, rate, depth)

**CRITICAL:** Use `document.getElementById("actualID")` - verify IDs exist!

### 4. Core Functions

#### A. `generateCsound()` - Start/Stop Recording

```javascript
window.generateCsound = function () {
  if (!csdRecording) {
    // START RECORDING
    csdRecording = true;
    csdRecordingStartTime = Date.now();

    // Build complete header with:
    // - Comments (mode, settings, timestamp)
    // - <CsoundSynthesizer>
    // - <CsOptions>
    // - <CsInstruments>
    // - Control channel initialization (chnset statements)
    // - Global audio buses
    // - ALL instruments (copy from your Csound orchestra)
    // - ALL effects
    // - </CsInstruments>
    // - <CsScore>
    // - ftables (f1 0 16384 10 1)
    // - Comment: "recording in progress..."

    textarea.value = header;
    btn.textContent = "⏹ Stop CSD Recording";
    downloadBtn.disabled = true;
  } else {
    // STOP RECORDING
    const closingTags = `
; Start global effect processors
i 998 0 3600  ; Echo
i 997 0 3600  ; Chorus
i 996 0 3600  ; Flanger
i 999 0 3600  ; Reverb

e

</CsScore>
</CsoundSynthesizer>
`;

    textarea.value += closingTags;
    downloadBtn.disabled = false;
    csdRecording = false;
    btn.textContent = "🔴 Start CSD Recording";

    if (confirm("CSD file is ready! Download it now?")) {
      downloadCsd();
    }
  }
};
```

#### B. `addNoteToCSD()` - Capture Note Events

```javascript
function addNoteToCSD(
  time,
  duration,
  freq,
  amp,
  pan,
  brightness,
  attack,
  release,
  smoothness,
  angle,
  instrNum,
) {
  if (!csdRecording) return;

  // CRITICAL: Use real elapsed time, not loop time variable!
  const elapsedSeconds = (Date.now() - csdRecordingStartTime) / 1000;

  const noteEvent = `i ${instrNum} ${elapsedSeconds.toFixed(3)} ${duration.toFixed(3)} ${freq.toFixed(2)} ${amp.toFixed(3)} ${pan.toFixed(3)} ${brightness.toFixed(3)} ${attack} ${release} ${smoothness.toFixed(3)} ${angle.toFixed(3)}\n`;

  document.getElementById("csoundOutput").value += noteEvent;
  textarea.scrollTop = textarea.scrollHeight; // Auto-scroll
}
```

**Call this from your audio loop where notes are triggered:**

```javascript
const timeoutId = setTimeout(() => {
  if (audioPlaying && csound && csoundReady) {
    csound.inputMessage(scoreEvent);

    // ADD THIS LINE:
    addNoteToCSD(
      time,
      dur,
      freq,
      amp,
      pan,
      brightness,
      noteAttack,
      noteRelease,
      extraParam1,
      extraParam2,
      instrNum,
    );
  }
}, noteTime);
```

#### C. `downloadCsd()` - Save File

```javascript
window.downloadCsd = function () {
  try {
    const csdContent = document.getElementById("csoundOutput").value;

    if (!csdContent || csdContent.length < 100) {
      updateStatus("No CSD file to download!", "error");
      return;
    }

    if (!csdContent.includes("</CsoundSynthesizer>")) {
      updateStatus("CSD not complete - stop recording first!", "error");
      return;
    }

    // Generate filename: synthtype_YYYY-MM-DD_HH-MM-SS.csd
    const now = new Date();
    const dateStr = now.toISOString().split("T")[0];
    const timeStr = now.toTimeString().split(" ")[0].replace(/:/g, "-");
    const synthType = document.getElementById("synthType").value;
    const filename = `${synthType}_${dateStr}_${timeStr}.csd`;

    const blob = new Blob([csdContent], { type: "text/plain;charset=utf-8" });
    const url = URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.download = filename;
    link.href = url;
    link.style.display = "none";
    document.body.appendChild(link);
    link.click();

    setTimeout(() => {
      document.body.removeChild(link);
      URL.revokeObjectURL(url);
    }, 100);

    updateStatus(`CSD file saved as ${filename}!`, "success");
  } catch (error) {
    console.error("Download error:", error);
    updateStatus(`Download failed: ${error.message}`, "error");
  }
};
```

#### D. `stopCSDRecording()` - Auto-stop Helper

```javascript
function stopCSDRecording() {
  if (csdRecording) {
    csdRecording = false; // Just stop adding notes, don't finalize
    // User must manually click "Stop Recording" to finalize
  }
}
```

**Call this when:**

- Audio stops
- Preset loads
- Fractal parameters change

### 5. Control Channel Initialization Template

In the header string, initialize ALL channels:

```csound
; Initialize control channels with current settings
chnset 0.50, "masterVol"
chnset 0.30, "reverbMix"
chnset 0.50, "echoTime"
chnset 0.40, "echoFeedback"
chnset 0.00, "echoMix"
chnset 0.80, "chorusRate"
chnset 5.00, "chorusDepth"
chnset 0.00, "chorusMix"
chnset 0.50, "flangerRate"
chnset 5.00, "flangerDepth"
chnset 0.50, "flangerFeedback"
chnset 0.00, "flangerMix"
```

**Get values from UI:**

```javascript
chnset ${(parseFloat(document.getElementById("masterVolume").value) / 100).toFixed(2)}, "masterVol"
```

### 6. Effect Instrument Start Commands

Before the `e` statement in closing tags:

```csound
; Start global effect processors (run for entire duration)
i 998 0 3600  ; Echo
i 997 0 3600  ; Chorus
i 996 0 3600  ; Flanger
i 999 0 3600  ; Reverb (master, always last)

e
```

## Common Pitfalls & Solutions

### ❌ Problem: Notes all have same timestamp

**Cause:** Using loop's `time` variable instead of real elapsed time  
**Fix:** Use `(Date.now() - csdRecordingStartTime) / 1000`

### ❌ Problem: No sound in exported CSD

**Cause:** Missing control channel initialization  
**Fix:** Add `chnset` statements for ALL channels in orchestra header

### ❌ Problem: Missing effect instruments

**Cause:** Forgot to add effect start commands in score  
**Fix:** Add `i 998/997/996/999` before `e` statement

### ❌ Problem: Cannot read properties of null

**Cause:** Wrong element ID in `getElementById()`  
**Fix:** Verify actual HTML element IDs (e.g., "masterVolume" not "masterVol")

### ❌ Problem: Notes disappear when stopping

**Cause:** Rebuilding from array or clearing textarea  
**Fix:** Use textarea as source of truth - only append, never rebuild

### ❌ Problem: Download doesn't work

**Cause:** Browser security, wrong MIME type, or link not in DOM  
**Fix:**

- Use `text/plain;charset=utf-8`
- Append link to document.body before clicking
- Add error handling with try/catch

### ❌ Problem: Header not in downloaded file

**Cause:** JavaScript error preventing header write  
**Fix:** Check console for errors, verify all element IDs exist

## Testing Checklist

- [ ] Click "Start CSD Recording" - orchestra appears in textarea
- [ ] Play audio - notes appear with incrementing timestamps
- [ ] Stop audio - notes preserved, no deletion
- [ ] Click "Stop CSD Recording" - closing tags added
- [ ] Dialog appears asking to download
- [ ] File downloads with timestamp filename
- [ ] Open .csd in CsoundQt - no errors
- [ ] Play .csd in CsoundQt - sounds identical to browser
- [ ] Verify timestamps are sequential (0.523, 0.789, 1.034...)
- [ ] Verify all instruments included
- [ ] Verify all effects included
- [ ] Verify ftables present

## File Structure of Exported CSD

```
; Comments (mode, settings, timestamp)

<CsoundSynthesizer>
<CsOptions>
-odac -d
</CsOptions>

<CsInstruments>
sr = 44100
ksmps = 32
nchnls = 2
0dbfs = 1

; Control channel initialization
chnset 0.50, "masterVol"
...

; Global audio buses
gaReverb init 0
...

; All instruments (1-11)
instr 1
  ...
endin

; All effects (998, 997, 996, 999)
instr 998
  ...
endin

</CsInstruments>

<CsScore>
f1 0 16384 10 1

; Note events with real timestamps
i 3 0.523 0.250 440.00 0.500 0.500 ...
i 3 0.789 0.250 523.25 0.600 0.600 ...
...

; Effect processors
i 998 0 3600
i 997 0 3600
i 996 0 3600
i 999 0 3600

e

</CsScore>
</CsoundSynthesizer>
```

## Integration Steps for New App

1. Add global variables (`csdRecording`, `csdRecordingStartTime`)
2. Add UI buttons (Start/Stop Recording, Download)
3. Add textarea for output
4. Create `generateCsound()` function with complete orchestra
5. Create `addNoteToCSD()` function with real timestamps
6. Create `downloadCsd()` function
7. Call `addNoteToCSD()` from audio loop
8. Call `stopCSDRecording()` on audio stop/preset change
9. Test thoroughly with checklist above

## Apps to Add This Feature To

1. ✅ **Mandelbrot Explorer** - COMPLETE
2. ⏳ **Drum Machine**
3. ⏳ **Fractal Explorer (L-Systems)**
4. ⏳ **Etude1**

## Summary

This feature turns any Dr.C web app into a **composition tool** - users can:

- Perform live with fractal/algorithmic parameters
- Capture their performance as a complete Csound file
- Share/edit/render compositions offline
- Archive creative sessions

The key insight: **Use real-time timestamps, not loop counters!**
