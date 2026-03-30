<div align="center">
  <img src="https://github.com/user-attachments/assets/992cb51f-fbc6-48ff-886e-ea15e0d5d42c" alt="Windows Media Player 8 Web Logo" width="150"/>
  
  # Windows Media Player 8 Series: Web Edition
  **A fully functional, browser-based recreation of the classic media player.**
</div>

---

## Overview

A InBrowser recreation of Windows Media Player 8 Series. Features local audio/video playback, YouTube & HLS radio streams, 10-band EQ, spatial audio, beat-synced visualizations, in-browser MKV remuxing via FFmpeg.wasm, voice control, recording, and 12+ custom skins. happily but occasionally angrily built with vanilla JS, HTML, and CSS!. 

<img width="1131" height="715" alt="image" src="https://github.com/user-attachments/assets/85f217f4-d687-4536-be33-27fa4a400323" />
The media player surrounds a central viewing area. The top bar holds the app icon, File, View, Play, Tools, and Help. The left sidebar lists Now Playing, Media Guide, Copy from CD, Media Library, Radio Tuner, Copy to CD/Device, Premium Services, and Skin Chooser. Blending into the left sidebar, the bottom section houses playback controls (play/pause, stop, back, skip, volume, mute). Just above this, a console bar displays track info (title, KHz), visualizer effect buttons, and a seek bar flanked by time-skip buttons. Finally, the right-side Info Center manages your active playlist (view, select, rearrange) and calculates total running time, sitting directly above a color-customizable mini sound-wave visualizer.

| <img width="450" alt="image" src="https://github.com/user-attachments/assets/8557166f-9b6a-46d7-abbb-5b26d082ec1b" /> | <img width="550" alt="image" src="https://github.com/user-attachments/assets/344d1bbc-7c61-41e9-b36d-812360426a63" /> |
| :---: | :---: |

The left side of the player features a seamless recreation of the original Windows Media Player visualizers, including the popular Ambiance and Battery options. To accurately replicate the long and complex Ambiance skin, it was recorded and split into multiple WebM files that automatically download and play smoothly throughout your music. You can skip forward through different visualizer effects using the arrow buttons on the console bar, and even overlay additional filter effects using the magic wand icon. The player on the right side shows video playback for formats not natively supported by browsers, such as .MKV. By utilizing FFmpeg via WebAssembly, the player quickly converts the file into a high-quality MP4, displaying the conversion progress in an integrated console window right before playback begins.

---

## Core Architecture & Media Engine

The project utilizes a robust media routing engine capable of handling a vast array of sources natively in the browser.

### Supported Media Sources
* **Local Files:** Supports drag-and-drop and file selection for standard web formats (`.mp4`, `.webm`, `.ogg`, `.mp3`, `.wav`, `.flac`, `.aac`, `.m4a`).
* **YouTube Integration:** Detects YouTube URLs, parses metadata via the Noembed API, and seamlessly plays content using the hidden YouTube IFrame API mapped to the WMP transport controls.
* **Network Streams & Radio:** Plays direct HTTP streams and parses `.m3u`, `.m3u8`, and `.pls` playlist files to extract stream URLs. 
* **HLS Support:** Natively integrates `hls.js` to support Apple HTTP Live Streaming directly in the custom video container.
* **Live Capture:** Hooks into the user's webcam and microphone via `navigator.mediaDevices.getUserMedia` for live device playback.

### Client-Side MKV Remuxing
<table>
  <tr>
    <td width="60%">
      One of the most advanced features is the integration of <strong>FFmpeg.wasm</strong>.
      <ul>
        <li>When a user imports an unsupported <code>.mkv</code> file, the app boots a local FFmpeg instance.</li>
        <li>It remuxes the file to <code>.mp4</code> entirely in the browser's RAM (<code>-c copy</code>) without re-encoding, allowing instant playback of previously incompatible formats.</li>
        <li>Includes a retro "Terminal" dialog window showing the live FFmpeg compilation logs.</li>
      </ul>
    </td>
    <td width="40%" align="center">
      <img width="300" alt="MKV Remuxing Terminal Overlay" src="https://github.com/user-attachments/assets/bf2485db-c1d1-4ac6-98c2-7f4bf4bd88e5" />
    </td>
  </tr>
</table>


---

## Advanced Audio & Visual Processing

### Web Audio API Integration
The application intercepts the HTML5 `<video>` and `<audio>` elements using `AudioContext` to route audio through a custom DSP (Digital Signal Processing) chain.
* **10-Band Graphic Equalizer:** Features a fully interactive EQ with bands ranging from 60Hz to 16kHz, utilizing `BiquadFilterNode`s.
* **SRS WOW Effects (Spatial Audio):** Simulates 3D/spatial audio enhancements using an `AudioContext PannerNode` set to the 'HRTF' panning model.
* **Digital Gain:** Volume is controlled digitally via a `GainNode`, bypassing the browser's default volume limitations allowing up to 200% amplification.

### Beat-Synced Visualizations
* **Oscilloscope:** Uses an `AnalyserNode` to extract real-time `timeDomainData`, drawing a live, beat-synced waveform on an HTML Canvas embedded in the transport bar.
<img width="912" height="280" alt="The fully functional 10-band Graphic Equalizer and Video Settings panel" src="https://github.com/user-attachments/assets/a4c52c8f-bfb2-4310-8673-ca3bbeedb328" />

---

## Dynamic Skinning Engine

The project features a highly complex CSS and JavaScript skinning system that completely transforms the layout, shape, and structure of the application.

### The 10+ Built-in Skins
Users can switch between skins using the "Skin Chooser" tab. The CSS utilizes `clip-path`, `border-radius`, and absolute positioning to break out of the standard rectangular window.
1.  **Default (Luna):** The classic WMP8 blue interface.
1.  **Royal:** The classic WMP9 blue interface.
2.  **Miniplayer & Compact:** Minimized, space-saving widget views.
3.  **Christmas Bauble & Xmas Wreath:** Festive circular skins with custom overlays.
4.  **Diamond Prism:** A sharp, 45-degree angled geometric UI.
5.  **Vertical Pillar:** A tall, narrow interface.
6.  **BMO Console:** A highly custom Adventure Time-themed skin complete with a D-Pad and an animated face overlay.
7.  **Floating Capsule:** An elongated pill-shaped UI.
8.  **Hex Grid:** A sharp hexagonal cutout.
9.  **Cyber Shield:** A futuristic crest shape.
10. **Organic Blob:** A fluid, amorphous shape.

### Pop-out Player Mode
The app includes a "Pop-out Player" function that opens the current skin in a detached, minimal browser popup window (`window.open`), hiding standard UI elements for a true desktop-widget feel.

![The classic Skin Chooser view ](https://github.com/user-attachments/assets/2f6278d3-0f4d-487e-a351-6f7e2c5e683e)

---

## Auxiliary Tools & Features

* **Subtitles & Captions:** Users can upload `.srt` files. The app parses the SRT format, converts it to `WebVTT` in memory, and injects it as a `<track>` element into the player.
* **Media Recorder:** Utilizes the `MediaRecorder` API to record system/microphone audio and generate downloadable `.webm` files directly in the browser.
* **Voice Control:** Integrates the `webkitSpeechRecognition` API. Users can speak commands like "Play", "Stop", "Next", or "Back" to control the player hands-free.
* **Video Filters:** A dedicated "Video Settings" panel allows users to adjust CSS filters for Brightness, Contrast, Saturation, Hue, and Sepia in real-time.
* **Metadata Extraction:** Uses `jsmediatags` to read ID3 tags from local MP3s, displaying Album Art, Title, and Artist. If missing, it falls back to the iTunes Search API to fetch artwork based on the filename.
* **Retro Emulation:** Features accurate "Media Guide", "Radio Tuner", and "Premium Services" (Napster, CinemaNow) UI mockups, alongside a multi-proxy system to load archived versions of WindowsMedia.com.

---

## Keyboard Shortcuts

The player includes extensive global keyboard event listeners for power users:

| Shortcut | Action |
| :--- | :--- |
| `Space` | Play / Pause |
| `Ctrl + S` | Stop |
| `Ctrl + P` | Play |
| `Ctrl + F` / `Ctrl + B`| Next / Previous Track |
| `Ctrl + H` | Toggle Shuffle |
| `Ctrl + T` | Toggle Repeat |
| `Ctrl + O` | Open File Dialog |
| `Ctrl + U` | Open URL Dialog |
| `Ctrl + E` | Open Equalizer |
| `Ctrl + L` | Toggle Info Center / Playlist |
| `F1` | Open Help Dialog |
| `F8` | Toggle Mute |
| `F9` / `F10` | Volume Down / Volume Up |
| `Shift + S` | Capture Video Frame |
| `Arrow Left/Right` | Seek Video (-10s / +10s) |
| `Enter` | Apply Skin (In Skin Chooser) |
| `Escape` | Exit custom skin / Return to Default |

<img width="1219" height="717" alt="The Radio Tuner tab featuring live streaming stations." src="https://github.com/user-attachments/assets/72d735b0-6e4c-4235-ba8b-81bb4955de2b" />
