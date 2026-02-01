// --- Audio Engine (Web Audio API) ---
// No external sound files; we synthesize simple instrument-like tones/noise.

let audioCtx = null;
const statusEl = document.getElementById("status");

function ensureAudio() {
  if (!audioCtx) {
    audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    statusEl.innerHTML = "Audio: Ready<br />Controls: Mouse / Touch / Keyboard";
  }
  if (audioCtx.state === "suspended") audioCtx.resume();
}

function now() {
  return audioCtx.currentTime;
}

// Utility: ADSR-ish one-shot envelope
function applyEnvelope(param, t0, a, d, sLevel, r, peak = 1.0) {
  param.cancelScheduledValues(t0);
  param.setValueAtTime(0.0001, t0);
  param.exponentialRampToValueAtTime(peak, t0 + a);
  param.exponentialRampToValueAtTime(Math.max(sLevel, 0.0002), t0 + a + d);
  param.exponentialRampToValueAtTime(0.0001, t0 + a + d + r);
}

// Utility: white noise buffer
function createNoiseBuffer(duration = 0.25) {
  const sampleRate = audioCtx.sampleRate;
  const length = Math.floor(sampleRate * duration);
  const buffer = audioCtx.createBuffer(1, length, sampleRate);
  const data = buffer.getChannelData(0);
  for (let i = 0; i < length; i++) data[i] = (Math.random() * 2 - 1);
  return buffer;
}

// Instruments
function playKick() {
  const t = now();
  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();

  osc.type = "sine";
  osc.frequency.setValueAtTime(150, t);
  osc.frequency.exponentialRampToValueAtTime(45, t + 0.12);

  applyEnvelope(gain.gain, t, 0.005, 0.08, 0.2, 0.12, 1.0);

  osc.connect(gain).connect(audioCtx.destination);
  osc.start(t);
  osc.stop(t + 0.25);
}

function playSnare() {
  const t = now();
  const noise = audioCtx.createBufferSource();
  noise.buffer = createNoiseBuffer(0.2);

  const noiseFilter = audioCtx.createBiquadFilter();
  noiseFilter.type = "highpass";
  noiseFilter.frequency.setValueAtTime(1200, t);

  const noiseGain = audioCtx.createGain();
  applyEnvelope(noiseGain.gain, t, 0.001, 0.06, 0.12, 0.12, 0.8);

  const osc = audioCtx.createOscillator();
  osc.type = "triangle";
  osc.frequency.setValueAtTime(200, t);

  const oscGain = audioCtx.createGain();
  applyEnvelope(oscGain.gain, t, 0.001, 0.05, 0.08, 0.10, 0.35);

  noise.connect(noiseFilter).connect(noiseGain).connect(audioCtx.destination);
  osc.connect(oscGain).connect(audioCtx.destination);

  noise.start(t);
  noise.stop(t + 0.22);

  osc.start(t);
  osc.stop(t + 0.18);
}

function playHiHat() {
  const t = now();
  const noise = audioCtx.createBufferSource();
  noise.buffer = createNoiseBuffer(0.12);

  const hp = audioCtx.createBiquadFilter();
  hp.type = "highpass";
  hp.frequency.setValueAtTime(6000, t);

  const gain = audioCtx.createGain();
  applyEnvelope(gain.gain, t, 0.001, 0.03, 0.08, 0.06, 0.35);

  noise.connect(hp).connect(gain).connect(audioCtx.destination);
  noise.start(t);
  noise.stop(t + 0.13);
}

function playPiano() {
  const t = now();
  const baseFreq = 261.63;

  const osc1 = audioCtx.createOscillator();
  const osc2 = audioCtx.createOscillator();
  osc1.type = "sine";
  osc2.type = "sine";

  osc1.frequency.setValueAtTime(baseFreq, t);
  osc2.frequency.setValueAtTime(baseFreq * 2, t);
  osc2.detune.setValueAtTime(-8, t);

  const gain = audioCtx.createGain();
  applyEnvelope(gain.gain, t, 0.003, 0.12, 0.15, 0.22, 0.55);

  const lp = audioCtx.createBiquadFilter();
  lp.type = "lowpass";
  lp.frequency.setValueAtTime(2500, t);

  osc1.connect(lp);
  osc2.connect(lp);
  lp.connect(gain).connect(audioCtx.destination);

  osc1.start(t);
  osc2.start(t);
  osc1.stop(t + 0.6);
  osc2.stop(t + 0.6);
}

function playTrumpet() {
  const t = now();
  const osc = audioCtx.createOscillator();
  osc.type = "sawtooth";
  osc.frequency.setValueAtTime(440, t);

  const filter = audioCtx.createBiquadFilter();
  filter.type = "bandpass";
  filter.frequency.setValueAtTime(900, t);
  filter.Q.setValueAtTime(6, t);

  const gain = audioCtx.createGain();
  applyEnvelope(gain.gain, t, 0.01, 0.10, 0.25, 0.22, 0.45);

  osc.connect(filter).connect(gain).connect(audioCtx.destination);
  osc.start(t);
  osc.stop(t + 0.55);
}

function playBass() {
  const t = now();
  const osc = audioCtx.createOscillator();
  osc.type = "square";
  osc.frequency.setValueAtTime(82.41, t);

  const lp = audioCtx.createBiquadFilter();
  lp.type = "lowpass";
  lp.frequency.setValueAtTime(500, t);

  const gain = audioCtx.createGain();
  applyEnvelope(gain.gain, t, 0.005, 0.12, 0.2, 0.22, 0.45);

  osc.connect(lp).connect(gain).connect(audioCtx.destination);
  osc.start(t);
  osc.stop(t + 0.7);
}

function playSynth() {
  const t = now();
  const osc = audioCtx.createOscillator();
  osc.type = "sawtooth";
  osc.frequency.setValueAtTime(523.25, t);

  const lfo = audioCtx.createOscillator();
  lfo.type = "sine";
  lfo.frequency.setValueAtTime(8, t);

  const lfoGain = audioCtx.createGain();
  lfoGain.gain.setValueAtTime(12, t);

  lfo.connect(lfoGain).connect(osc.detune);

  const gain = audioCtx.createGain();
  applyEnvelope(gain.gain, t, 0.002, 0.08, 0.15, 0.18, 0.35);

  const hp = audioCtx.createBiquadFilter();
  hp.type = "highpass";
  hp.frequency.setValueAtTime(250, t);

  osc.connect(hp).connect(gain).connect(audioCtx.destination);

  lfo.start(t);
  osc.start(t);

  osc.stop(t + 0.45);
  lfo.stop(t + 0.45);
}

function playClap() {
  const t = now();
  const noiseBuffer = createNoiseBuffer(0.35);

  const hp = audioCtx.createBiquadFilter();
  hp.type = "highpass";
  hp.frequency.setValueAtTime(1800, t);

  const gain = audioCtx.createGain();
  gain.gain.setValueAtTime(0.0001, t);

  function burst(offset, peak) {
    gain.gain.setValueAtTime(0.0001, t + offset);
    gain.gain.exponentialRampToValueAtTime(peak, t + offset + 0.005);
    gain.gain.exponentialRampToValueAtTime(0.0001, t + offset + 0.06);
  }

  const src = audioCtx.createBufferSource();
  src.buffer = noiseBuffer;

  src.connect(hp).connect(gain).connect(audioCtx.destination);

  burst(0.00, 0.35);
  burst(0.03, 0.28);
  burst(0.06, 0.22);

  src.start(t);
  src.stop(t + 0.30);
}

const instrumentMap = {
  A: playKick,
  B: playSnare,
  C: playHiHat,
  D: playPiano,
  E: playTrumpet,
  F: playBass,
  G: playSynth,
  H: playClap,
};

// --- UI Interaction ---
const pads = Array.from(document.querySelectorAll(".pad"));

function pressAnimation(btn) {
  btn.classList.add("is-pressed");
  window.setTimeout(() => btn.classList.remove("is-pressed"), 120);
}

function trigger(key) {
  const upper = String(key).toUpperCase();
  const fn = instrumentMap[upper];
  if (!fn) return;

  ensureAudio();

  const btn = pads.find(p => p.dataset.key === upper);
  if (btn) pressAnimation(btn);

  fn();
}

// Click/tap
pads.forEach(btn => {
  btn.addEventListener("click", () => trigger(btn.dataset.key));
});

// Keyboard A–H
window.addEventListener("keydown", (e) => {
  const tag = (e.target && e.target.tagName) ? e.target.tagName.toLowerCase() : "";
  if (tag === "input" || tag === "textarea") return;

  trigger(e.key);
});
