<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Mythic OS — Windows 98 Style</title>
  <style>
    * { 
      margin: 0; 
      padding: 0; 
      box-sizing: border-box; 
      font-family: "MS Sans Serif", Arial, sans-serif;
    }
    
    :root {
      --desktop-bg: #008080;
      --taskbar-bg: #c0c0c0;
      --taskbar-text: #000000;
      --window-bg: #c0c0c0;
      --window-header-bg: #000080;
      --window-header-text: #ffffff;
    }
    
    body {
      overflow: hidden;
      background: var(--desktop-bg);
      color: #000;
      height: 100vh;
      position: relative;
      font-size: 11px;
    }
    
    #matrixCanvas {
      position: fixed;
      top: 0;
      left: 0;
      z-index: -2;
      width: 100%;
      height: 100%;
      pointer-events: none;
      display: none;
    }
    
    #desktop {
      position: fixed;
      top: 0; left: 0;
      width: 100vw; height: 100vh;
      background: var(--desktop-bg);
      background-size: cover;
      background-position: center;
      z-index: 0;
      transition: background 0.5s ease;
    }
    
    .window {
      position: absolute;
      width: 80vw;
      height: 80vh;
      background: var(--window-bg);
      border: 2px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
      box-shadow: 0 0 0 1px #000;
      display: flex;
      flex-direction: column;
      z-index: 10;
      resize: both;
      overflow: hidden;
      min-width: 200px;
      min-height: 100px;
    }
    
    .window.maximized {
      width: 100vw !important;
      height: calc(100vh - 60px) !important;
      left: 0 !important;
      top: 0 !important;
      resize: none;
    }
    
    .window-header {
      background: linear-gradient(to right, var(--window-header-bg), #1084d0);
      padding: 4px 8px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      color: var(--window-header-text);
      cursor: move;
      user-select: none;
      border-bottom: 1px solid #000;
      font-size: 11px;
      font-weight: bold;
      height: 22px;
    }
    
    .window-buttons {
      display: flex;
      gap: 4px;
    }
    
    .window-button {
      width: 16px;
      height: 14px;
      border: 1px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
      background: #c0c0c0;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 10px;
      font-weight: bold;
      cursor: pointer;
      line-height: 1;
    }
    
    .window-button:hover {
      border-color: #fff #808080 #808080 #fff;
    }
    
    .window-button:active {
      border-color: #808080 #dfdfdf #dfdfdf #808080;
    }
    
    .window-content {
      flex: 1;
      overflow: hidden;
      position: relative;
      background: var(--window-bg);
    }
    
    iframe {
      width: 100%;
      height: 100%;
      border: none;
      background: #fff;
    }
    
    #taskbar {
      position: fixed;
      bottom: 0;
      width: 100%;
      height: 60px;
      background: var(--taskbar-bg);
      color: var(--taskbar-text);
      border-top: 2px solid #dfdfdf;
      display: flex;
      align-items: center;
      padding: 0 8px;
      z-index: 1000;
      box-shadow: inset 0 1px 0 #fff;
    }
    
    #search-container {
      display: flex;
      align-items: center;
      margin-right: 8px;
    }
    
    #search-input {
      width: 200px;
      background: #fff;
      border: 2px inset #c0c0c0;
      padding: 4px;
      font-size: 11px;
      font-family: "MS Sans Serif", Arial, sans-serif;
    }
    
    #search-button {
      background: #c0c0c0;
      border: 2px outset #c0c0c0;
      padding: 4px 8px;
      font-size: 11px;
      cursor: pointer;
      margin-left: 2px;
    }
    
    #start-button {
      background: #c0c0c0;
      border: 2px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
      padding: 4px 12px;
      font-weight: bold;
      cursor: pointer;
      display: flex;
      align-items: center;
      gap: 4px;
      height: 40px;
      margin-right: 8px;
    }
    
    #start-button:hover {
      border-color: #fff #808080 #808080 #fff;
    }
    
    #start-button:active {
      border-color: #808080 #dfdfdf #dfdfdf #808080;
    }
    
    #taskbar-apps {
      display: flex;
      gap: 4px;
      flex: 1;
    }
    
    .taskbar-app {
      background: #c0c0c0;
      border: 1px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
      padding: 6px 12px;
      cursor: pointer;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      max-width: 200px;
      font-size: 11px;
    }
    
    .taskbar-app:hover {
      border-color: #fff #808080 #808080 #fff;
    }
    
    .taskbar-app.active {
      border-color: #808080 #dfdfdf #dfdfdf #808080;
    }
    
    #system-tray {
      display: flex;
      align-items: center;
      gap: 4px;
      padding: 4px;
      border: 1px solid;
      border-color: #808080 #dfdfdf #dfdfdf #808080;
      background: #c0c0c0;
    }
    
    .tray-item {
      width: 24px;
      height: 24px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 14px;
    }
    
    #clock {
      padding: 4px 8px;
      font-size: 11px;
      border: 1px inset #c0c0c0;
      background: #c0c0c0;
    }
    
    #start-menu {
      position: fixed;
      bottom: 60px;
      left: 0;
      width: 500px;
      max-height: 70vh;
      overflow-y: auto;
      background: #c0c0c0;
      border: 2px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
      display: none;
      z-index: 1001;
    }
    
    #start-menu-header {
      background: linear-gradient(to right, #000080, #1084d0);
      color: #fff;
      padding: 8px;
      font-weight: bold;
      font-size: 13px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    
    #start-menu-content {
      padding: 8px;
      display: flex;
    }
    
    #category-buttons {
      width: 150px;
      border-right: 1px solid #808080;
      padding-right: 8px;
    }
    
    .category-btn {
      width: 100%;
      padding: 8px;
      margin-bottom: 4px;
      text-align: left;
      background: #c0c0c0;
      border: 2px outset #c0c0c0;
      font-size: 11px;
      cursor: pointer;
    }
    
    .category-btn:hover {
      background: #e0e0e0;
    }
    
    .category-btn.active {
      background: #000080;
      color: white;
      border: 2px inset #c0c0c0;
    }
    
    #category-content {
      flex: 1;
      padding-left: 8px;
      max-height: 400px;
      overflow-y: auto;
    }
    
    .category-panel {
      display: none;
    }
    
    .category-panel.active {
      display: block;
    }
    
    .start-menu-item {
      display: flex;
      align-items: center;
      padding: 6px 8px;
      cursor: pointer;
      border: 1px solid transparent;
    }
    
    .start-menu-item:hover {
      background: #000080;
      color: #fff;
    }
    
    .start-menu-item .icon {
      margin-right: 8px;
      font-size: 16px;
    }
    
    .start-menu-divider {
      height: 1px;
      background: #808080;
      margin: 4px 0;
    }
    
    .menu-section {
      margin: 10px 0;
    }
    
    .menu-section-title {
      background: #c0c0c0;
      padding: 4px 8px;
      font-weight: bold;
      color: #000080;
      border: 1px solid #808080;
      margin-bottom: 5px;
    }
    
    .desktop-icon {
      position: absolute;
      width: 80px;
      text-align: center;
      cursor: pointer;
      padding: 8px 4px;
      border: 1px solid transparent;
      z-index: 5;
    }
    
    .desktop-icon:hover {
      background: rgba(0, 0, 128, 0.2);
    }
    
    .desktop-icon.selected {
      background: rgba(0, 0, 128, 0.3);
    }
    
    .desktop-icon .icon {
      font-size: 32px;
      margin-bottom: 4px;
      filter: drop-shadow(1px 1px 1px rgba(0,0,0,0.5));
    }
    
    .desktop-icon .label {
      font-size: 11px;
      color: #fff;
      text-shadow: 1px 1px 1px #000;
      word-break: break-word;
      background: rgba(0, 0, 0, 0.5);
      padding: 1px 3px;
    }
    
    .wallpaper-style-btn {
      padding: 4px 8px;
      margin: 2px;
      background: #c0c0c0;
      border: 2px outset #c0c0c0;
      cursor: pointer;
      font-size: 10px;
    }
    
    .wallpaper-style-btn:hover {
      background: #e0e0e0;
    }
    
    .wallpaper-style-btn.active {
      background: #000080;
      color: white;
      border: 2px inset #c0c0c0;
    }
    
    .window-resizer {
      position: absolute;
      width: 16px;
      height: 16px;
      bottom: 0;
      right: 0;
      cursor: se-resize;
      z-index: 100;
      background: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAAAZdEVYdFNvZnR3YXJlAHBhaW50Lm5ldCA0LjAuMTnU1rJkAAABFUlEQVQ4T52T0U6DQBBFZ2kIEh+MJj74/3/8jsbHRaKNpKZQ==') no-repeat center, #c0c0c0;
      border: 1px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
    }
    
    .status-bar {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      padding: 4px 8px;
      background: #c0c0c0;
      border-top: 1px solid #808080;
      font-size: 11px;
      display: flex;
      justify-content: space-between;
    }
    
    ::-webkit-scrollbar {
      width: 16px;
    }
    
    ::-webkit-scrollbar-track {
      background: #c0c0c0;
      border: 1px inset #c0c0c0;
    }
    
    ::-webkit-scrollbar-thumb {
      background: #c0c0c0;
      border: 1px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
    }
    
    ::-webkit-scrollbar-thumb:hover {
      border-color: #fff #808080 #808080 #fff;
    }
    
    .menu-bar {
      display: flex;
      background: #c0c0c0;
      border-bottom: 1px solid #808080;
      padding: 2px;
      font-size: 11px;
    }
    
    .menu-item {
      padding: 4px 8px;
      cursor: pointer;
      border: 1px solid transparent;
    }
    
    .menu-item:hover {
      border: 1px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
    }
    
    button {
      background: #c0c0c0;
      border: 2px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
      padding: 4px 12px;
      cursor: pointer;
      font-size: 11px;
      font-family: "MS Sans Serif", Arial, sans-serif;
    }
    
    button:hover {
      border-color: #fff #808080 #808080 #fff;
    }
    
    button:active {
      border-color: #808080 #dfdfdf #dfdfdf #808080;
    }
    
    input, textarea {
      background: #fff;
      border: 2px inset #c0c0c0;
      padding: 4px;
      font-size: 11px;
      font-family: "MS Sans Serif", Arial, sans-serif;
    }
    
    .notification {
      position: fixed;
      top: 50px;
      right: 10px;
      background: #c0c0c0;
      border: 2px solid;
      border-color: #dfdfdf #808080 #808080 #dfdfdf;
      padding: 8px;
      z-index: 2000;
      transform: translateX(120%);
      transition: transform 0.3s ease;
      max-width: 300px;
      font-size: 11px;
    }
    
    .notification.show {
      transform: translateX(0);
    }
    
    .notification .close-btn {
      float: right;
      cursor: pointer;
      font-weight: bold;
    }
    
    .settings-section {
      margin-bottom: 15px;
      padding: 10px;
      border: 1px solid #808080;
      background: #dfdfdf;
    }
    
    .settings-section h3 {
      margin-bottom: 10px;
      color: #000080;
    }
    
    .theme-glass {
      background: #000;
      color: #0f0;
      font-family: 'Courier New', Courier, monospace;
    }
    
    .theme-glass #desktop {
      background: #111;
    }
    
    .theme-glass .window {
      background: rgba(10, 30, 10, 0.7);
      border: 2px solid #0f0;
      box-shadow: 
        0 0 20px rgba(0, 255, 0, 0.5),
        inset 0 0 20px rgba(0, 0, 0, 0.8);
      border-radius: 8px;
      backdrop-filter: blur(5px);
    }
    
    .theme-glass .window-header {
      background: rgba(0, 30, 0, 0.8);
      border-bottom: 1px solid #0f0;
      color: #0f0;
    }
    
    .theme-glass .window-button {
      width: 14px;
      height: 14px;
      border-radius: 50%;
      cursor: pointer;
    }
    
    .theme-glass .close { background: #ff5f56; }
    .theme-glass .minimize { background: #ffbd2e; }
    .theme-glass .maximize { background: #27c93f; }
    
    .theme-glass #taskbar {
      background: rgba(0, 30, 0, 0.8);
      border-color: #0f0;
    }
    
    .theme-glass #start-button {
      background: rgba(0, 30, 0, 0.8);
      border-color: #0f0;
      color: #0f0;
    }
    
    .theme-glass .taskbar-app {
      background: rgba(0, 30, 0, 0.8);
      border-color: #0f0;
      color: #0f0;
    }
    
    .theme-glass .taskbar-app:hover {
      background: rgba(0, 255, 0, 0.2);
    }
    
    .theme-glass .taskbar-app.active {
      background: rgba(0, 255, 0, 0.3);
    }
    
    .theme-glass #start-menu {
      background: rgba(0, 20, 0, 0.85);
      border-color: #0f0;
    }
    
    .theme-glass #start-menu-header {
      background: rgba(0, 30, 0, 0.8);
      color: #0f0;
    }
    
    .theme-glass .start-menu-item:hover {
      background: rgba(0, 255, 0, 0.2);
      color: #ff0;
    }
    
    .theme-glass .desktop-icon .label {
      color: #0f0;
      background: rgba(0, 0, 0, 0.7);
    }
    
    .theme-glass .desktop-icon:hover {
      background: rgba(0, 255, 0, 0.2);
    }
    
    .theme-glass #matrixCanvas {
      display: block;
    }
    
    .theme-dark {
      background: #000;
      color: #fff;
    }
    
    .theme-dark #desktop {
      background: #111;
    }
    
    .theme-dark .window {
      background: rgba(30, 30, 30, 0.9);
      border: 2px solid #555;
    }
    
    .theme-blue {
      background: #001f3f;
      color: #0074D9;
    }
    
    .theme-blue #desktop {
      background: #001f3f;
    }
    
    .theme-blue .window {
      background: rgba(0, 31, 63, 0.7);
      border: 2px solid #0074D9;
    }
    
    .theme-purple {
      background: #2E003E;
      color: #B19CD9;
    }
    
    .theme-purple #desktop {
      background: #2E003E;
    }
    
    .theme-purple .window {
      background: rgba(46, 0, 62, 0.7);
      border: 2px solid #B19CD9;
    }
    
    .color-picker {
      display: flex;
      align-items: center;
      margin: 10px 0;
    }
    
    .color-picker label {
      width: 150px;
      font-weight: bold;
    }
    
    .color-picker input[type="color"] {
      width: 50px;
      height: 30px;
      border: 2px inset #c0c0c0;
      margin-right: 10px;
    }
    
    .color-preset {
      width: 30px;
      height: 30px;
      border: 1px solid #000;
      margin: 5px;
      cursor: pointer;
      display: inline-block;
    }
    
    .color-presets {
      display: flex;
      flex-wrap: wrap;
      margin: 10px 0;
    }
    
    .color-section {
      margin: 15px 0;
      padding: 10px;
      border: 1px solid #808080;
      background: #dfdfdf;
    }
    
    .color-section h4 {
      margin: 0 0 10px 0;
      color: #000080;
    }
    
    .reset-colors {
      background: #c0c0c0;
      border: 2px outset #c0c0c0;
      padding: 4px 12px;
      margin: 10px 0;
      cursor: pointer;
    }
    
    .reset-colors:active {
      border: 2px inset #c0c0c0;
    }
  </style>
</head>
<body class="theme-default">
  <canvas id="matrixCanvas"></canvas>
  <div id="desktop"></div>

  <div id="taskbar">
    <button id="start-button" onclick="toggleStartMenu()">
      <span style="font-weight: bold; font-size: 14px;">Start</span>
    </button>
    
    <div id="search-container">
      <input type="text" id="search-input" placeholder="Search applications..." />
      <button id="search-button" onclick="performSearch()">🔍</button>
    </div>
    
    <div id="taskbar-apps">
      <!-- Taskbar apps will be added here dynamically -->
    </div>
    
    <div id="system-tray">
      <div class="tray-item">🔊</div>
      <div class="tray-item">🌐</div>
      <div id="clock">10:00 AM</div>
    </div>
  </div>

  <div id="start-menu">
    <div id="start-menu-header">
      <span>Mythic OS</span>
      <button onclick="toggleStartMenu()" style="padding: 2px 8px; font-size: 10px;">Close</button>
    </div>
    <div id="start-menu-content">
      <div id="category-buttons">
        <button class="category-btn active" data-category="main">Main Applications</button>
        <button class="category-btn" data-category="internet">Internet & Dev</button>
        <button class="category-btn" data-category="mythic">Mythic Network</button>
        <button class="category-btn" data-category="accessories">Accessories</button>
        <button class="category-btn" data-category="utilities">Utilities</button>
        <button class="category-btn" data-category="media">Media & Productivity</button>
        <button class="category-btn" data-category="security">Security</button>
        <button class="category-btn" data-category="misc">Miscellaneous</button>
        <div style="padding: 5px 0;">
          <div class="start-menu-divider"></div>
        </div>
        <button class="category-btn" data-category="settings">Settings</button>
        <button class="category-btn" data-category="custom">Custom URL</button>
      </div>
      
      <div id="category-content">
        <!-- Main Applications Panel -->
        <div id="main-panel" class="category-panel active">
          <div class="start-menu-item" onclick="openFileBrowser()">
            <span class="icon">📁</span> My Files
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_notepad/')">
            <span class="icon">📝</span> Notepad
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_media_player/media_player')">
            <span class="icon">🎵</span> Media Player
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/chat/')">
            <span class="icon">💬</span> Chat
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_calendar/')">
            <span class="icon">📅</span> Calendar
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_Calculator%20/')">
            <span class="icon">🧮</span> Calculator
          </div>
        </div>
        
        <!-- Internet & Development Tools Panel -->
        <div id="internet-panel" class="category-panel">
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/neural_browser/')">
            <span class="icon">🧠</span> Neural Browser
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/coder/')">
            <span class="icon">💻</span> Coder
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/html_instruct/index.HTML')">
            <span class="icon">🧰</span> HTML Instruct
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/website_compatibility_tester/')">
            <span class="icon">🔧</span> Website Compatibility Tester
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_capsule_builder/')">
            <span class="icon">📝</span> Mythic Capsule Builder
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic-capsule-pro/')">
            <span class="icon">💻</span> Mythic Capsule Pro
          </div>
        </div>
        
        <!-- Mythic Network Panel -->
        <div id="mythic-panel" class="category-panel">
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_vault_uplink/')">
            <span class="icon">🗝</span> Mythic Vault Uplink
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/neural_chat/')">
            <span class="icon">💬</span> Neural Chat
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_space/')">
            <span class="icon">🌐</span> Mythic Space
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/web_ring/')">
            <span class="icon">🔗</span> Web Ring
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/swamp_blog/')">
            <span class="icon">🪷</span> Swamp Blog
          </div>
        </div>
        
        <!-- Accessories Panel -->
        <div id="accessories-panel" class="category-panel">
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/fortune_fairy/')">
            <span class="icon">🧚</span> Fortune Fairy
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic%20pet/')">
            <span class="icon">🐾</span> Mythic Pet
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/worldmap/')">
            <span class="icon">🗺</span> World Map
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/crt/')">
            <span class="icon">🧠</span> CRT
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_radar/')">
            <span class="icon">📡</span> Mythic Radar
          </div>
        </div>
        
        <!-- Utilities Panel -->
        <div id="utilities-panel" class="category-panel">
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/passwordgenrator/')">
            <span class="icon">🔑</span> Password Generator
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/downloader/')">
            <span class="icon">💾</span> Mythic Download
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/glitch_purge/')">
            <span class="icon">⚡</span> Glitch Purge
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/matrix_encryptor/')">
            <span class="icon">🧬</span> Matrix Encryptor
          </div>
        </div>
        
        <!-- Media & Productivity Panel -->
        <div id="media-panel" class="category-panel">
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/gallery_shrine/')">
            <span class="icon">🛕</span> Gallery Shrine
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/pixel%20art%20shrine%20editor/')">
            <span class="icon">🌟</span> Pixel Art Editor
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/tag_it/')">
            <span class="icon">🏷️</span> Tag It
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/watermark_maker/')">
            <span class="icon">🧰</span> Watermark Maker
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_sticker_maker/')">
            <span class="icon">🫨</span> Mythic Sticker Maker
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/galactic_mahem_neon_skies/')">
            <span class="icon">🚀</span> Galactic Mahem Neon Skies
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_beatbox/')">
            <span class="icon">🫧</span> Mythic Beatbox
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/wizards_escape/')">
            <span class="icon">🧙</span> Wizards Escape
          </div>
        </div>
        
        <!-- Security Panel -->
        <div id="security-panel" class="category-panel">
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/securityscanner/')">
            <span class="icon">🛡</span> Security Scanner
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/security_wizard/')">
            <span class="icon">🧙</span> Security Wizard
          </div>
        </div>
        
        <!-- Miscellaneous Panel -->
        <div id="misc-panel" class="category-panel">
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/code_repository_blog/repository%20project')">
            <span class="icon">📓</span> Code Repository Blog
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythicos/')">
            <span class="icon">🎃</span> Mythicos
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/mythic_virtual_cafe/')">
            <span class="icon">🌐</span> Mythic Virtual Cafe
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/angry_ai/')">
            <span class="icon">🔥🤖</span> Angry AI
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/angrydroidwebsite/')">
            <span class="icon">👨💻</span> AngryDroid Website
          </div>
          <div class="start-menu-item" onclick="loadPage('https://angrydroid.neocities.org/')">
            <span class="icon">👾</span> AngryDroid Capsule
          </div>
        </div>
        
        <!-- Settings Panel -->
        <div id="settings-panel" class="category-panel">
          <div class="start-menu-item" onclick="openSettings()">
            <span class="icon">⚙️</span> Settings
          </div>
          <div class="start-menu-item" onclick="openDisplaySettings()">
            <span class="icon">🖼️</span> Display Properties
          </div>
          <div class="start-menu-item" onclick="openColorSettings()">
            <span class="icon">🎨</span> Color Settings
          </div>
          <div class="start-menu-item" onclick="showNotification('System updated successfully!')">
            <span class="icon">🔔</span> Notifications
          </div>
          <div class="start-menu-divider"></div>
          <div class="start-menu-item" onclick="shutdown()">
            <span class="icon">⏻</span> Shutdown
          </div>
        </div>
        
        <!-- Custom URL Panel -->
        <div id="custom-panel" class="category-panel">
          <div style="padding: 10px;">
            <b style="color: #000080;">Custom URL Loader</b>
            <input type="text" id="customURL" placeholder="Paste link to load" style="width: 100%; margin: 5px 0;" />
            <button onclick="loadCustom()" style="width: 100%;">Load Viewer</button>
            
            <input type="file" id="fileUpload" style="width: 100%; margin: 5px 0;" />
            <button onclick="shareFile()" style="width: 100%;">Upload to Viewer</button>
          </div>
          
          <div class="start-menu-divider"></div>
          
          <!-- Just for Fun Section -->
          <div class="menu-section">
            <div class="menu-section-title">Just for Fun</div>
            <div class="start-menu-item" onclick="loadPage('https://discord.gg/hJdtSEraG5')">
              <span class="icon">💬</span> Discord
            </div>
            <div class="start-menu-item" onclick="window.location.href='mailto:angrydroid@offilive.com'">
              <span class="icon">📧</span> Email AngryDroid
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <script>
    // OS Functionality
    let appCounter = 0;
    let minimizedWindows = [];
    const desktop = document.getElementById('desktop');
    const taskbarApps = document.getElementById('taskbar-apps');
    const appRegistry = {};
    let activeWindow = null;
    let currentTheme = 'theme-default';
    let currentWallpaper = 'default';
    let currentWallpaperStyle = 'fill'; // 'fill', 'center', 'tile'

    // Matrix Rain Effect (for glass theme)
    const canvas = document.getElementById("matrixCanvas");
    const ctx = canvas.getContext("2d");

    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    const letters = "アァカサタナハマヤラワイウエオカキクケコサシスセソタチツテトナニヌネノハヒフヘホマミムメモヤユヨラリルレロワヲンABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789#@%&*";
    const fontSize = 14;
    const columns = Math.floor(canvas.width / fontSize);
    const drops = Array(columns).fill(1);

    function drawMatrix() {
      ctx.fillStyle = "rgba(0, 10, 0, 0.05)";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      ctx.fillStyle = "#0f0";
      ctx.font = fontSize + "px monospace";

      for (let i = 0; i < drops.length; i++) {
        const text = letters.charAt(Math.floor(Math.random() * letters.length));
        ctx.fillText(text, i * fontSize, drops[i] * fontSize);

        if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) {
          drops[i] = 0;
        }
        drops[i]++;
      }
    }

    // Classic Windows 98 Wallpapers + New Wallpapers
    const wallpapers = {
      'default': { name: 'Default Teal', url: '' },
      'bliss': { name: 'Bliss (Windows XP)', url: 'https://upload.wikimedia.org/wikipedia/en/5/5e/Bliss_%28Windows_XP%29.png' },
      'waves': { name: 'Waves', url: 'https://i.imgur.com/3JYQZ1j.jpg' },
      'forest': { name: 'Forest', url: 'https://i.imgur.com/5r8J9ZQ.jpg' },
      'pattern': { name: 'Pattern', url: 'https://i.imgur.com/8x9KQ9b.png' },
      'space': { name: 'Space', url: 'https://i.imgur.com/9Q9Q9Q9.jpg' },
      // New wallpapers from your list
      '499': { name: 'Abstract 499', url: 'https://angrydroid.neocities.org/images/499.gif' },
      '508': { name: 'Landscape 508', url: 'https://angrydroid.neocities.org/images/508.jpg' },
      '550': { name: 'Pattern 550', url: 'https://angrydroid.neocities.org/images/550.gif' },
      '443': { name: 'Nature 443', url: 'https://angrydroid.neocities.org/images/443.jpg' },
      '447': { name: 'Art 447', url: 'https://angrydroid.neocities.org/images/447.jpg' },
      '689': { name: 'City 689', url: 'https://angrydroid.neocities.org/images/689.jpg' },
      '687': { name: 'Abstract 687', url: 'https://angrydroid.neocities.org/images/687.gif' },
      '1284': { name: 'Digital 1284', url: 'https://angrydroid.neocities.org/images/1284.gif' },
      '1290': { name: 'Pattern 1290', url: 'https://angrydroid.neocities.org/images/1290.gif' },
      '391': { name: 'Retro 391', url: 'https://angrydroid.neocities.org/images/391.gif' },
      '859': { name: 'Abstract 859', url: 'https://angrydroid.neocities.org/images/859.gif' },
      '204': { name: 'Minimal 204', url: 'https://angrydroid.neocities.org/images/204.gif' },
      '435': { name: 'Digital 435', url: 'https://angrydroid.neocities.org/images/435.gif' }
    };

    // Desktop Icons - Categorized
    const desktopIcons = [
      // Main Applications
      { name: 'My Computer', icon: '💻', x: 50, y: 50, action: () => openFileBrowser() },
      { name: 'Notepad', icon: '📝', x: 50, y: 150, action: () => loadPage('https://angrydroid.neocities.org/mythic_notepad/') },
      { name: 'Media Player', icon: '🎵', x: 50, y: 250, action: () => loadPage('https://angrydroid.neocities.org/mythic_media_player/media_player') },
      { name: 'Chat', icon: '💬', x: 50, y: 350, action: () => loadPage('https://angrydroid.neocities.org/chat/') },
      
      // Internet & Development
      { name: 'Neural Browser', icon: '🧠', x: 150, y: 50, action: () => loadPage('https://angrydroid.neocities.org/neural_browser/') },
      { name: 'Coder', icon: '💻', x: 150, y: 150, action: () => loadPage('https://angrydroid.neocities.org/coder/') },
      { name: 'Capsule Builder', icon: '📝', x: 150, y: 250, action: () => loadPage('https://angrydroid.neocities.org/mythic_capsule_builder/') },
      { name: 'HTML Instruct', icon: '🧰', x: 150, y: 350, action: () => loadPage('https://angrydroid.neocities.org/html_instruct/index.HTML') },
      
      // Mythic Network
      { name: 'Mythic Vault', icon: '🗝', x: 250, y: 50, action: () => loadPage('https://angrydroid.neocities.org/mythic_vault_uplink/') },
      { name: 'Mythic Space', icon: '🌐', x: 250, y: 150, action: () => loadPage('https://angrydroid.neocities.org/mythic_space/') },
      { name: 'Web Ring', icon: '🔗', x: 250, y: 250, action: () => loadPage('https://angrydroid.neocities.org/web_ring/') },
      { name: 'Swamp Blog', icon: '🪷', x: 250, y: 350, action: () => loadPage('https://angrydroid.neocities.org/swamp_blog/') },
      
      // Utilities & Security
      { name: 'Security', icon: '🛡', x: 350, y: 50, action: () => loadPage('https://angrydroid.neocities.org/securityscanner/') },
      { name: 'Downloads', icon: '💾', x: 350, y: 150, action: () => loadPage('https://angrydroid.neocities.org/downloader/') },
      { name: 'Password Gen', icon: '🔑', x: 350, y: 250, action: () => loadPage('https://angrydroid.neocities.org/passwordgenrator/') },
      { name: 'Tag It', icon: '🏷️', x: 350, y: 350, action: () => loadPage('https://angrydroid.neocities.org/tag_it/') },
      
      // Media & Accessories
      { name: 'Gallery', icon: '🛕', x: 450, y: 50, action: () => loadPage('https://angrydroid.neocities.org/gallery_shrine/') },
      { name: 'World Map', icon: '🗺', x: 450, y: 150, action: () => loadPage('https://angrydroid.neocities.org/worldmap/') },
      { name: 'Fortune Fairy', icon: '🧚', x: 450, y: 250, action: () => loadPage('https://angrydroid.neocities.org/fortune_fairy/') },
      { name: 'Settings', icon: '⚙️', x: 450, y: 350, action: () => openSettings() }
    ];

    function createDesktopIcons() {
      desktopIcons.forEach(icon => {
        const iconElement = document.createElement('div');
        iconElement.className = 'desktop-icon';
        iconElement.style.left = `${icon.x}px`;
        iconElement.style.top = `${icon.y}px`;
        iconElement.innerHTML = `
          <div class="icon">${icon.icon}</div>
          <div class="label">${icon.name}</div>
        `;
        iconElement.addEventListener('dblclick', icon.action);
        
        // Add single click selection
        iconElement.addEventListener('click', (e) => {
          if (e.detail === 1) { // Single click
            document.querySelectorAll('.desktop-icon').forEach(el => {
              el.classList.remove('selected');
            });
            iconElement.classList.add('selected');
          }
        });
        
        desktop.appendChild(iconElement);
      });
    }

    function toggleStartMenu() {
      const menu = document.getElementById('start-menu');
      menu.style.display = menu.style.display === 'block' ? 'none' : 'block';
    }

    // Search functionality
    function performSearch() {
      const searchTerm = document.getElementById('search-input').value.toLowerCase().trim();
      
      if (!searchTerm) {
        // If search is empty, reset to normal view
        resetSearch();
        return;
      }
      
      // Hide all category panels
      document.querySelectorAll('.category-panel').forEach(panel => {
        panel.classList.remove('active');
      });
      
      // Remove active class from all category buttons
      document.querySelectorAll('.category-btn').forEach(btn => {
        btn.classList.remove('active');
      });
      
      // Create a search results panel
      let searchResultsPanel = document.getElementById('search-results-panel');
      if (!searchResultsPanel) {
        searchResultsPanel = document.createElement('div');
        searchResultsPanel.id = 'search-results-panel';
        searchResultsPanel.className = 'category-panel';
        document.getElementById('category-content').appendChild(searchResultsPanel);
      }
      
      searchResultsPanel.innerHTML = '<h3>Search Results</h3>';
      searchResultsPanel.classList.add('active');
      
      // Create a set to store unique URLs
      const foundURLs = new Set();
      
      // Search through desktop icons
      desktopIcons.forEach(icon => {
        if (icon.name.toLowerCase().includes(searchTerm)) {
          const resultItem = document.createElement('div');
          resultItem.className = 'start-menu-item';
          resultItem.innerHTML = `<span class="icon">${icon.icon}</span> ${icon.name}`;
          resultItem.onclick = icon.action;
          searchResultsPanel.appendChild(resultItem);
          foundURLs.add(icon.action.toString());
        }
      });
      
      // Search through start menu items
      const menuItems = document.querySelectorAll('.start-menu-item');
      menuItems.forEach(item => {
        const itemText = item.textContent.toLowerCase();
        if (itemText.includes(searchTerm)) {
          // Check if we've already added this action
          if (!foundURLs.has(item.onclick.toString())) {
            const resultItem = item.cloneNode(true);
            searchResultsPanel.appendChild(resultItem);
            foundURLs.add(item.onclick.toString());
          }
        }
      });
      
      // If no results found
      if (searchResultsPanel.children.length === 1) { // Only the h3 is present
        searchResultsPanel.innerHTML += '<p>No results found.</p>';
      }
    }
    
    function resetSearch() {
      // Reset to main panel
      document.querySelectorAll('.category-panel').forEach(panel => {
        panel.classList.remove('active');
      });
      document.getElementById('main-panel').classList.add('active');
      
      // Reset category buttons
      document.querySelectorAll('.category-btn').forEach(btn => {
        btn.classList.remove('active');
      });
      document.querySelector('[data-category="main"]').classList.add('active');
      
      // Clear search input
      document.getElementById('search-input').value = '';
    }
    
    // Category switching functionality
    function setupCategoryButtons() {
      const buttons = document.querySelectorAll('.category-btn');
      buttons.forEach(button => {
        button.addEventListener('click', function() {
          // Remove active class from all buttons
          buttons.forEach(btn => btn.classList.remove('active'));
          // Add active class to clicked button
          this.classList.add('active');
          
          // Hide all panels
          document.querySelectorAll('.category-panel').forEach(panel => {
            panel.classList.remove('active');
          });
          
          // Show the selected panel
          const category = this.getAttribute('data-category');
          document.getElementById(`${category}-panel`).classList.add('active');
        });
      });
    }

    function loadCustom() {
      const url = document.getElementById('customURL').value.trim();
      if (url) {
        spawnWindow(`App ${appCounter + 1}`, url);
        document.getElementById('customURL').value = '';
        toggleStartMenu();
      }
    }

    function loadPage(url) {
      // Extract title from URL
      let title = url.split('/').filter(part => part).pop() || 'Web Page';
      if (title.includes('.')) title = title.split('.')[0];
      spawnWindow(title, url);
      toggleStartMenu();
    }

    function shareFile() {
      const file = document.getElementById('fileUpload').files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = function(e) {
        const blob = new Blob([e.target.result], { type: file.type });
        const url = URL.createObjectURL(blob);
        spawnWindow(`Shared: ${file.name}`, url);
      };
      reader.readAsText(file);
    }

    function spawnWindow(title, url, content = null) {
      const index = appCounter++;
      const win = document.createElement('div');
      win.className = 'window';
      win.dataset.title = title;
      
      // Position window within visible area
      const maxLeft = window.innerWidth - 300;
      const maxTop = window.innerHeight - 200;
      const left = Math.min(60 + index * 20, maxLeft);
      const top = Math.min(60 + index * 20, maxTop);
      
      win.style.left = `${left}px`;
      win.style.top = `${top}px`;

      win.innerHTML = `
        <div class="window-header">
          <span>${title}</span>
          <div class="window-buttons">
            <div class="window-button" onclick="minimizeWindow(this)">_</div>
            <div class="window-button" onclick="toggleMaximize(this)">□</div>
            <div class="window-button" onclick="closeWindow(this)">×</div>
          </div>
        </div>
        <div class="window-content">
          ${content || `<iframe src="${url}"></iframe>`}
          <div class="status-bar">
            <div>STATUS: <span id="status">ACTIVE</span></div>
            <div>CAPSULES: <span id="capsuleCount">0</span></div>
          </div>
        </div>
        <div class="window-resizer"></div>
      `;
      desktop.appendChild(win);
      makeDraggable(win);
      makeResizable(win);
      appRegistry[title] = { title, url, content };
      
      // Add to taskbar
      const taskbarApp = document.createElement('div');
      taskbarApp.className = 'taskbar-app';
      taskbarApp.textContent = title;
      taskbarApp.onclick = () => {
        if (win.style.display === 'none') {
          win.style.display = 'flex';
          taskbarApp.classList.remove('active');
        } else {
          bringToFront(win);
        }
      };
      taskbarApps.appendChild(taskbarApp);
      win.taskbarApp = taskbarApp;
      
      bringToFront(win);
    }

    function toggleMaximize(btn) {
      const win = btn.closest('.window');
      win.classList.toggle('maximized');
      bringToFront(win);
    }

    function minimizeWindow(btn) {
      const win = btn.closest('.window');
      win.style.display = 'none';
      win.taskbarApp.classList.remove('active');
    }

    function closeWindow(btn) {
      const win = btn.closest('.window');
      const title = win.dataset.title;
      win.remove();
      
      if (win.taskbarApp) win.taskbarApp.remove();
      delete appRegistry[title];
    }

    function makeDraggable(win) {
      const header = win.querySelector('.window-header');
      let offsetX = 0, offsetY = 0, isDragging = false;

      header.addEventListener('mousedown', e => {
        if (e.target.classList.contains('window-button')) return;
        isDragging = true;
        offsetX = e.clientX - win.offsetLeft;
        offsetY = e.clientY - win.offsetTop;
        bringToFront(win);
        e.preventDefault();
      });

      document.addEventListener('mousemove', e => {
        if (isDragging) {
          const x = Math.max(0, Math.min(e.clientX - offsetX, window.innerWidth - 100));
          const y = Math.max(0, Math.min(e.clientY - offsetY, window.innerHeight - 100));
          win.style.left = `${x}px`;
          win.style.top = `${y}px`;
        }
      });

      document.addEventListener('mouseup', () => {
        isDragging = false;
      });
    }

    function makeResizable(win) {
      const resizer = win.querySelector('.window-resizer');
      let isResizing = false;
      let startX, startY, startWidth, startHeight;

      resizer.addEventListener('mousedown', e => {
        isResizing = true;
        startX = e.clientX;
        startY = e.clientY;
        startWidth = parseInt(document.defaultView.getComputedStyle(win).width, 10);
        startHeight = parseInt(document.defaultView.getComputedStyle(win).height, 10);
        bringToFront(win);
        e.preventDefault();
      });

      document.addEventListener('mousemove', e => {
        if (isResizing) {
          const width = Math.max(200, startWidth + (e.clientX - startX));
          const height = Math.max(100, startHeight + (e.clientY - startY));
          
          const maxWidth = window.innerWidth - parseInt(win.style.left);
          const maxHeight = window.innerHeight - parseInt(win.style.top);
          
          win.style.width = Math.min(width, maxWidth) + 'px';
          win.style.height = Math.min(height, maxHeight) + 'px';
        }
      });

      document.addEventListener('mouseup', () => {
        isResizing = false;
      });
    }

    function bringToFront(win) {
      document.querySelectorAll('.window').forEach(w => {
        w.style.zIndex = 10;
      });
      
      win.style.zIndex = 100;
      activeWindow = win;
      
      document.querySelectorAll('.taskbar-app').forEach(app => {
        app.classList.remove('active');
      });
      
      if (win.taskbarApp) {
        win.taskbarApp.classList.add('active');
      }
    }

    function setWallpaper(wallpaperKey, style = currentWallpaperStyle) {
      currentWallpaper = wallpaperKey;
      currentWallpaperStyle = style;
      const wallpaper = wallpapers[wallpaperKey];
      
      if (wallpaperKey === 'default') {
        desktop.style.background = '#008080';
        desktop.style.backgroundImage = '';
      } else {
        desktop.style.backgroundImage = `url('${wallpaper.url}')`;
        
        // Apply the style
        switch (style) {
          case 'fill':
            desktop.style.backgroundSize = 'cover';
            desktop.style.backgroundRepeat = 'no-repeat';
            break;
          case 'center':
            desktop.style.backgroundSize = 'auto';
            desktop.style.backgroundRepeat = 'no-repeat';
            desktop.style.backgroundPosition = 'center';
            break;
          case 'tile':
            desktop.style.backgroundSize = 'auto';
            desktop.style.backgroundRepeat = 'repeat';
            break;
        }
      }
      
      localStorage.setItem('mythicWallpaper', wallpaperKey);
      localStorage.setItem('mythicWallpaperStyle', style);
      showNotification(`Wallpaper changed to ${wallpaper.name} (${style})`);
    }

    function setWallpaperStyle(style) {
      setWallpaper(currentWallpaper, style);
    }

    function setTheme(themeName) {
      document.body.classList.remove(currentTheme);
      currentTheme = themeName;
      document.body.classList.add(themeName);
      
      if (themeName === 'theme-glass') {
        matrixInterval = setInterval(drawMatrix, 33);
      } else {
        clearInterval(matrixInterval);
      }
      
      localStorage.setItem('mythicTheme', themeName);
      showNotification(`Theme changed to ${themeName.replace('theme-', '').replace('-', ' ')}`);
    }

    function showNotification(message) {
      const notification = document.createElement('div');
      notification.className = 'notification';
      notification.innerHTML = `
        <span class="close-btn" onclick="this.parentElement.remove()">×</span>
        ${message}
      `;
      document.body.appendChild(notification);
      
      setTimeout(() => {
        notification.classList.add('show');
      }, 10);
      
      setTimeout(() => {
        notification.classList.remove('show');
        setTimeout(() => {
          if (notification.parentElement) {
            notification.remove();
          }
        }, 300);
      }, 3000);
    }

    // Color Customization Functions
    function openColorSettings() {
      const colorSettingsContent = `
        <div style="padding: 20px;">
          <h3>Color Customization</h3>
          <p>Customize the colors of your Mythic OS interface.</p>
          
          <div class="color-section">
            <h4>Desktop Colors</h4>
            <div class="color-picker">
              <label for="desktop-bg-color">Desktop Background:</label>
              <input type="color" id="desktop-bg-color" value="#008080">
              <button onclick="applyDesktopColor()">Apply</button>
            </div>
          </div>
          
          <div class="color-section">
            <h4>Taskbar Colors</h4>
            <div class="color-picker">
              <label for="taskbar-bg-color">Taskbar Background:</label>
              <input type="color" id="taskbar-bg-color" value="#c0c0c0">
              <button onclick="applyTaskbarColor()">Apply</button>
            </div>
            <div class="color-picker">
              <label for="taskbar-text-color">Taskbar Text:</label>
              <input type="color" id="taskbar-text-color" value="#000000">
              <button onclick="applyTaskbarColor()">Apply</button>
            </div>
          </div>
          
          <div class="color-section">
            <h4>Window Colors</h4>
            <div class="color-picker">
              <label for="window-bg-color">Window Background:</label>
              <input type="color" id="window-bg-color" value="#c0c0c0">
              <button onclick="applyWindowColors()">Apply</button>
            </div>
            <div class="color-picker">
              <label for="window-header-bg-color">Window Header:</label>
              <input type="color" id="window-header-bg-color" value="#000080">
              <button onclick="applyWindowColors()">Apply</button>
            </div>
            <div class="color-picker">
              <label for="window-header-text-color">Header Text:</label>
              <input type="color" id="window-header-text-color" value="#ffffff">
              <button onclick="applyWindowColors()">Apply</button>
            </div>
          </div>
          
          <div class="color-section">
            <h4>Color Presets</h4>
            <p>Quickly apply predefined color schemes:</p>
            <div class="color-presets">
              <div class="color-preset" style="background: #008080;" onclick="applyPreset('default')" title="Default"></div>
              <div class="color-preset" style="background: #2E003E;" onclick="applyPreset('purple')" title="Purple"></div>
              <div class="color-preset" style="background: #001f3f;" onclick="applyPreset('blue')" title="Blue"></div>
              <div class="color-preset" style="background: #000;" onclick="applyPreset('dark')" title="Dark"></div>
              <div class="color-preset" style="background: #0f0;" onclick="applyPreset('matrix')" title="Matrix"></div>
            </div>
          </div>
          
          <div style="margin-top: 20px;">
            <button class="reset-colors" onclick="resetToDefaultColors()">Reset to Default</button>
            <button onclick="saveColorScheme()">Save Scheme</button>
            <button onclick="loadColorScheme()">Load Scheme</button>
          </div>
        </div>
      `;
      
      spawnWindow('Color Settings', null, colorSettingsContent);
      loadCurrentColors();
    }
    
    function loadCurrentColors() {
      // Load current colors from CSS or localStorage
      const desktopBg = getComputedStyle(document.documentElement).getPropertyValue('--desktop-bg') || '#008080';
      const taskbarBg = getComputedStyle(document.documentElement).getPropertyValue('--taskbar-bg') || '#c0c0c0';
      const taskbarText = getComputedStyle(document.documentElement).getPropertyValue('--taskbar-text') || '#000000';
      const windowBg = getComputedStyle(document.documentElement).getPropertyValue('--window-bg') || '#c0c0c0';
      const windowHeaderBg = getComputedStyle(document.documentElement).getPropertyValue('--window-header-bg') || '#000080';
      const windowHeaderText = getComputedStyle(document.documentElement).getPropertyValue('--window-header-text') || '#ffffff';
      
      document.getElementById('desktop-bg-color').value = desktopBg;
      document.getElementById('taskbar-bg-color').value = taskbarBg;
      document.getElementById('taskbar-text-color').value = taskbarText;
      document.getElementById('window-bg-color').value = windowBg;
      document.getElementById('window-header-bg-color').value = windowHeaderBg;
      document.getElementById('window-header-text-color').value = windowHeaderText;
    }
    
    function applyDesktopColor() {
      const color = document.getElementById('desktop-bg-color').value;
      document.documentElement.style.setProperty('--desktop-bg', color);
      desktop.style.backgroundColor = color;
    }
    
    function applyTaskbarColor() {
      const bgColor = document.getElementById('taskbar-bg-color').value;
      const textColor = document.getElementById('taskbar-text-color').value;
      
      document.documentElement.style.setProperty('--taskbar-bg', bgColor);
      document.documentElement.style.setProperty('--taskbar-text', textColor);
      
      const taskbar = document.getElementById('taskbar');
      taskbar.style.backgroundColor = bgColor;
      taskbar.style.color = textColor;
    }
    
    function applyWindowColors() {
      const bgColor = document.getElementById('window-bg-color').value;
      const headerBgColor = document.getElementById('window-header-bg-color').value;
      const headerTextColor = document.getElementById('window-header-text-color').value;
      
      document.documentElement.style.setProperty('--window-bg', bgColor);
      document.documentElement.style.setProperty('--window-header-bg', headerBgColor);
      document.documentElement.style.setProperty('--window-header-text', headerTextColor);
      
      // Apply to existing windows
      const windows = document.querySelectorAll('.window');
      windows.forEach(win => {
        win.style.backgroundColor = bgColor;
        
        const header = win.querySelector('.window-header');
        header.style.background = `linear-gradient(to right, ${headerBgColor}, ${lightenColor(headerBgColor, 20)})`;
        header.style.color = headerTextColor;
      });
    }
    
    function lightenColor(color, percent) {
      // Convert hex to RGB
      const r = parseInt(color.substr(1, 2), 16);
      const g = parseInt(color.substr(3, 2), 16);
      const b = parseInt(color.substr(5, 2), 16);
      
      // Lighten by percent
      const lighten = (c) => Math.min(255, c + (255 - c) * (percent / 100));
      
      // Convert back to hex
      return '#' + 
        Math.round(lighten(r)).toString(16).padStart(2, '0') +
        Math.round(lighten(g)).toString(16).padStart(2, '0') +
        Math.round(lighten(b)).toString(16).padStart(2, '0');
    }
    
    function applyPreset(preset) {
      let desktopBg, taskbarBg, taskbarText, windowBg, windowHeaderBg, windowHeaderText;
      
      switch(preset) {
        case 'default':
          desktopBg = '#008080';
          taskbarBg = '#c0c0c0';
          taskbarText = '#000000';
          windowBg = '#c0c0c0';
          windowHeaderBg = '#000080';
          windowHeaderText = '#ffffff';
          break;
        case 'purple':
          desktopBg = '#2E003E';
          taskbarBg = '#B19CD9';
          taskbarText = '#000000';
          windowBg = '#B19CD9';
          windowHeaderBg = '#4B0082';
          windowHeaderText = '#ffffff';
          break;
        case 'blue':
          desktopBg = '#001f3f';
          taskbarBg = '#0074D9';
          taskbarText = '#ffffff';
          windowBg = '#0074D9';
          windowHeaderBg = '#001f3f';
          windowHeaderText = '#ffffff';
          break;
        case 'dark':
          desktopBg = '#000000';
          taskbarBg = '#333333';
          taskbarText = '#ffffff';
          windowBg = '#333333';
          windowHeaderBg = '#000000';
          windowHeaderText = '#ffffff';
          break;
        case 'matrix':
          desktopBg = '#000000';
          taskbarBg = '#001100';
          taskbarText = '#0f0';
          windowBg = '#001100';
          windowHeaderBg = '#000';
          windowHeaderText = '#0f0';
          break;
      }
      
      // Update color pickers
      document.getElementById('desktop-bg-color').value = desktopBg;
      document.getElementById('taskbar-bg-color').value = taskbarBg;
      document.getElementById('taskbar-text-color').value = taskbarText;
      document.getElementById('window-bg-color').value = windowBg;
      document.getElementById('window-header-bg-color').value = windowHeaderBg;
      document.getElementById('window-header-text-color').value = windowHeaderText;
      
      // Apply the colors
      applyDesktopColor();
      applyTaskbarColor();
      applyWindowColors();
    }
    
    function resetToDefaultColors() {
      applyPreset('default');
    }
    
    function saveColorScheme() {
      const scheme = {
        desktopBg: document.getElementById('desktop-bg-color').value,
        taskbarBg: document.getElementById('taskbar-bg-color').value,
        taskbarText: document.getElementById('taskbar-text-color').value,
        windowBg: document.getElementById('window-bg-color').value,
        windowHeaderBg: document.getElementById('window-header-bg-color').value,
        windowHeaderText: document.getElementById('window-header-text-color').value
      };
      
      localStorage.setItem('mythicColorScheme', JSON.stringify(scheme));
      showNotification('Color scheme saved!');
    }
    
    function loadColorScheme() {
      const savedScheme = localStorage.getItem('mythicColorScheme');
      if (savedScheme) {
        const scheme = JSON.parse(savedScheme);
        
        document.getElementById('desktop-bg-color').value = scheme.desktopBg;
        document.getElementById('taskbar-bg-color').value = scheme.taskbarBg;
        document.getElementById('taskbar-text-color').value = scheme.taskbarText;
        document.getElementById('window-bg-color').value = scheme.windowBg;
        document.getElementById('window-header-bg-color').value = scheme.windowHeaderBg;
        document.getElementById('window-header-text-color').value = scheme.windowHeaderText;
        
        applyDesktopColor();
        applyTaskbarColor();
        applyWindowColors();
        
        showNotification('Color scheme loaded!');
      } else {
        showNotification('No saved color scheme found.');
      }
    }

    function openSettings() {
      const settingsContent = `
        <div style="padding: 20px;">
          <h3>System Settings</h3>
          <p>Mythic OS v1.0 - Windows 98 Style</p>
          <p>This interface combines Windows 98 aesthetics with Mythic OS functionality.</p>
          
          <div style="margin-top: 20px;">
            <button onclick="openDisplaySettings()">Display Properties</button>
            <button onclick="openColorSettings()">Color Settings</button>
          </div>
          
          <button onclick="showNotification('Settings saved!')" style="margin-top: 20px;">Save Settings</button>
        </div>
      `;
      
      spawnWindow('Settings', null, settingsContent);
    }
    
    function openDisplaySettings() {
      let displayContent = `
        <div style="padding: 20px;">
          <h3>Display Properties</h3>
          
          <div class="settings-section">
            <h3>Wallpaper Style</h3>
            <p>Select how the wallpaper is displayed:</p>
            <div style="display: flex; gap: 10px; margin-bottom: 10px;">
              <button class="wallpaper-style-btn ${currentWallpaperStyle === 'fill' ? 'active' : ''}" onclick="setWallpaperStyle('fill')">Fill</button>
              <button class="wallpaper-style-btn ${currentWallpaperStyle === 'center' ? 'active' : ''}" onclick="setWallpaperStyle('center')">Center</button>
              <button class="wallpaper-style-btn ${currentWallpaperStyle === 'tile' ? 'active' : ''}" onclick="setWallpaperStyle('tile')">Tile</button>
            </div>
          </div>
          
          <div class="settings-section">
            <h3>Wallpaper</h3>
            <p>Select a desktop background:</p>
            <div style="display: flex; flex-wrap: wrap;">
      `;
      
      for (const key in wallpapers) {
        const wallpaper = wallpapers[key];
        const isSelected = currentWallpaper === key;
        displayContent += `
          <div style="width: 100px; height: 75px; border: 2px solid ${isSelected ? '#000080' : '#c0c0c0'}; margin: 5px; cursor: pointer; background-image: ${key !== 'default' ? `url('${wallpaper.url}')` : 'none'}; background-color: ${key === 'default' ? '#008080' : 'transparent'}; background-size: cover; background-position: center;" 
               onclick="setWallpaper('${key}')">
            ${key === 'default' ? '<div style="display: flex; align-items: center; justify-content: center; height: 100%;">Default</div>' : ''}
          </div>
        `;
      }
      
      displayContent += `
            </div>
          </div>
          
          <div class="settings-section">
            <h3>Themes</h3>
            <p>Select a color scheme:</p>
            <div style="display: flex; flex-wrap: wrap;">
      `;
      
      const themes = [
        { key: 'theme-default', name: 'Windows 98', bg: '#008080', fg: '#000' },
        { key: 'theme-glass', name: 'Mythic Glass', bg: '#000', fg: '#0f0' },
        { key: 'theme-dark', name: 'Dark', bg: '#000', fg: '#fff' },
        { key: 'theme-blue', name: 'Blue', bg: '#001f3f', fg: '#0074D9' },
        { key: 'theme-purple', name: 'Purple', bg: '#2E003E', fg: '#B19CD9' }
      ];
      
      themes.forEach(theme => {
        const isSelected = currentTheme === theme.key;
        displayContent += `
          <div style="width: 100px; height: 75px; border: 2px solid ${isSelected ? '#000080' : '#c0c0c0'}; margin: 5px; cursor: pointer; background-color: ${theme.bg}; color: ${theme.fg}; display: flex; align-items: center; justify-content: center; font-weight: bold;" 
               onclick="setTheme('${theme.key}')">
            ${theme.name}
          </div>
        `;
      });
      
      displayContent += `
            </div>
          </div>
          
          <div style="text-align: center; margin-top: 20px;">
            <button onclick="openColorSettings()">Color Settings</button>
            <button onclick="showNotification('Display settings applied!')">Apply</button>
            <button onclick="closeWindow(this)">Cancel</button>
          </div>
        </div>
      `;
      
      spawnWindow('Display Properties', null, displayContent);
    }
    
    function openFileBrowser() {
      const files = [
        { name: 'Document.txt', icon: '📄', type: 'text' },
        { name: 'Image.jpg', icon: '🖼️', type: 'image' },
        { name: 'Music.mp3', icon: '🎵', type: 'audio' },
        { name: 'Video.mp4', icon: '🎬', type: 'video' },
        { name: 'Archive.zip', icon: '📦', type: 'archive' }
      ];
      
      let fileBrowserContent = '<div style="padding: 10px;">';
      fileBrowserContent += '<h3>My Computer</h3>';
      fileBrowserContent += '<div style="display: grid; grid-template-columns: repeat(auto-fill, minmax(100px, 1fr)); gap: 10px;">';
      
      files.forEach(file => {
        fileBrowserContent += `
          <div style="text-align: center; cursor: pointer;" onclick="openFile('${file.name}', '${file.type}')">
            <div style="font-size: 32px;">${file.icon}</div>
            <div>${file.name}</div>
          </div>
        `;
      });
      
      fileBrowserContent += '</div></div>';
      
      spawnWindow('My Computer', null, fileBrowserContent);
    }
    
    function openFile(name, type) {
      showNotification(`Opening ${name}`);
    }
    
    function updateClock() {
      const now = new Date();
      const timeString = now.toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
      document.getElementById('clock').textContent = timeString;
    }
    
    function shutdown() {
      showNotification('System is shutting down...');
      setTimeout(() => {
        document.body.innerHTML = '<div style="display: flex; justify-content: center; align-items: center; height: 100vh; background: #000; color: #fff; font-size: 24px;">It is now safe to turn off your computer.</div>';
      }, 2000);
    }
    
    let matrixInterval;
    window.addEventListener('load', () => {
      createDesktopIcons();
      setupCategoryButtons();
      setInterval(updateClock, 1000);
      updateClock();
      
      const savedWallpaper = localStorage.getItem('mythicWallpaper');
      const savedStyle = localStorage.getItem('mythicWallpaperStyle') || 'fill';
      if (savedWallpaper) {
        setWallpaper(savedWallpaper, savedStyle);
      }
      
      const savedTheme = localStorage.getItem('mythicTheme');
      if (savedTheme) {
        setTheme(savedTheme);
      }
      
      // Load saved color scheme
      const savedColorScheme = localStorage.getItem('mythicColorScheme');
      if (savedColorScheme) {
        const scheme = JSON.parse(savedColorScheme);
        document.documentElement.style.setProperty('--desktop-bg', scheme.desktopBg);
        document.documentElement.style.setProperty('--taskbar-bg', scheme.taskbarBg);
        document.documentElement.style.setProperty('--taskbar-text', scheme.taskbarText);
        document.documentElement.style.setProperty('--window-bg', scheme.windowBg);
        document.documentElement.style.setProperty('--window-header-bg', scheme.windowHeaderBg);
        document.documentElement.style.setProperty('--window-header-text', scheme.windowHeaderText);
        
        // Apply to elements
        desktop.style.backgroundColor = scheme.desktopBg;
        const taskbar = document.getElementById('taskbar');
        taskbar.style.backgroundColor = scheme.taskbarBg;
        taskbar.style.color = scheme.taskbarText;
        
        const windows = document.querySelectorAll('.window');
        windows.forEach(win => {
          win.style.backgroundColor = scheme.windowBg;
          const header = win.querySelector('.window-header');
          header.style.background = `linear-gradient(to right, ${scheme.windowHeaderBg}, ${lightenColor(scheme.windowHeaderBg, 20)})`;
          header.style.color = scheme.windowHeaderText;
        });
      }
      
      document.addEventListener('click', (e) => {
        if (!e.target.closest('#start-menu') && !e.target.closest('#start-button')) {
          document.getElementById('start-menu').style.display = 'none';
        }
      });
      
      desktop.addEventListener('contextmenu', e => {
        e.preventDefault();
        const input = document.createElement('input');
        input.type = 'file';
        input.accept = 'image/*';
        input.style.display = 'none';
        input.onchange = event => {
          const file = event.target.files[0];
          if (!file) return;
          const reader = new FileReader();
          reader.onload = e => {
            desktop.style.backgroundImage = `url('${e.target.result}')`;
            desktop.style.backgroundSize = 'cover';
            localStorage.setItem('mythicCustomBackground', e.target.result);
            showNotification('Custom background applied!');
          };
          reader.readAsDataURL(file);
        };
        document.body.appendChild(input);
        input.click();
        input.remove();
      });
      
      const customBg = localStorage.getItem('mythicCustomBackground');
      if (customBg) {
        desktop.style.backgroundImage = `url('${customBg}')`;
        desktop.style.backgroundSize = 'cover';
      }
      
      setTimeout(() => {
        spawnWindow('Welcome to Mythic OS', 'https://angrydroid.neocities.org/');
      }, 1000);
    });
    
    window.addEventListener('resize', () => {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
      
      document.querySelectorAll('.window').forEach(win => {
        if (!win.classList.contains('maximized')) {
          const rect = win.getBoundingClientRect();
          if (rect.right > window.innerWidth) {
            win.style.left = (window.innerWidth - rect.width - 10) + 'px';
          }
          if (rect.bottom > window.innerHeight) {
            win.style.top = (window.innerHeight - rect.height - 60) + 'px';
          }
        }
      });
    });
  </script>
</body>
</html>
