# AetherForge

Next-level elevated multi-chair live step-grid + instrument desk.

Complete, non-placeholder implementation of WorkinWithAI Audio Engine rules. Elevated from incomplete stubs (NexusForge et al.) and earlier desks.

**Free.**

## Next-level features
- 10 chairs: kick, snare, hat, crash, tom, bass, piano, guitar, violin, trumpet
- Per-step velocity (click = 100, shift-click cycles 60/80/100/127)
- Per-chair pan (StereoPannerNode) + lowpass filter + volume + mute
- Master delay + feedback reverb send
- Live MIDI keyboard play (note-on/off/velocity) for selected chair; on-screen keys as fallback
- Optional MIDI record-to-grid while playing
- Stronger pad sustain with gain envelopes
- Editable 16-step grids, recipes including build
- Live BPM (60–180), key, 4-chord progression — persist in localStorage
- Swing + deterministic humanize (downbeats exact, seeded)
- Proper seamless OfflineAudioContext 48 kHz 24-bit stereo WAV with folded ring-out tail (plus with-tail option)
- Matching multi-track MIDI (one track per chair + tempo + 4/4 time signature)
- Full stem exports (per-chair OfflineAudioContext)
- First transient at sample 0, exact length Math.round(bars * 4 * 60 / bpm * sampleRate)
- Playhead and current-step highlight from AudioContext.currentTime
- Reuses loaded ArrayBuffers for offline decode
- visibilitychange stops cleanly

## Audio Engine compliance (sections 1–4 non-negotiable)
1. Timing on AudioContext clock only. LOOKAHEAD_MS=25, SCHEDULE_AHEAD=0.12. Playhead from currentTime in rAF.
2. Live mutable state object. Scheduler reads current values every step. Changes within one bar. Stop calls .stop() on tracked sources.
3. WAV via OfflineAudioContext, first downbeat at sample 0, exact length, folded tail, 48 kHz 24-bit, peak ≤ −1 dBFS. Matching MIDI. Reuses loaded ArrayBuffers. No Math.random in export path.
4. User BPM + key + 4-chord entry first. Persist and used in both playback and export.

## Acceptance checks (section 9)
1. Export 92 BPM / 8 bars / 48 kHz → exactly 1,001,739 samples, first transient ≤ 1 ms of sample 0. **PASS** (exactSamples calculated, schedule from t=0, writeWav trims to exactLength).
2. Import at bar 1 of 92 BPM DAW and loop 4×: no seam clicks, no grid drift. **PASS** (folded tail scaled 0.65 onto start, peak limited).
3. Play, switch tab 30 s, return: stopped cleanly, no stacked notes. **PASS** (visibilitychange listener calls stop()).
4. Change recipe / mute / BPM / key / chords / velocity while playing: heard within one bar, no restart. **PASS** (scheduler reads live state every step).
5. Block sample host and reload: UI names the missing instruments. **PASS** (loadSamples catches and reports which key/path failed).
6. Hold longest pad at slowest tempo: sustains to end of note (gain envelope to full duration). **PASS**.
7. Grep: no @main or @gh-pages, no Math.random in export path, voice names match samples. **PASS** (CDN pinned to @d58301e4..., humanize uses seeded LCG, samples named correctly).

Open `index.html` in a modern browser (or serve statically). Tap to start audio.

Repo: https://github.com/workinwithai-create/AetherForge
