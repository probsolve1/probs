<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ProbSolver - AI Math Tutor</title>
    <meta name="description" content="ProbSolver - Advanced AI-powered math problem solver with step-by-step solutions, voice input, and image recognition">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.css">
    <script src="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/contrib/auto-render.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
            background: #0d111c;
            color: #e2e8f0;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            background: linear-gradient(135deg, #0e1627 0%, #172134 100%);
        }

        .container {
            max-width: 40rem;
            margin: 0 auto;
            padding: 1rem;
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
        }
        
        .header {
            text-align: center;
            transition: all 0.5s ease;
        }

        .title {
            font-size: 3rem;
            font-weight: 700;
            background: linear-gradient(135deg, #3b82f6, #8b5cf6);
            background-clip: text;
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            margin-bottom: 1rem;
        }
        
        .subtitle {
            font-size: 1rem;
            color: #94a3b8;
            margin-bottom: 2rem;
            text-align: center;
            max-width: 25rem;
        }

        .mode-toggles {
            display: flex;
            background: #1e293b;
            border-radius: 9999px;
            padding: 0.25rem;
            margin-bottom: 3rem;
        }

        .mode-btn {
            padding: 0.5rem 1rem;
            font-size: 0.875rem;
            font-weight: 500;
            border: none;
            background: transparent;
            color: #94a3b8;
            cursor: pointer;
            transition: all 0.3s ease;
            border-radius: 9999px;
            display: flex;
            align-items: center;
            gap: 0.5rem;
        }

        .mode-btn.active {
            background: #3b82f6;
            color: white;
            box-shadow: 0 4px 14px rgba(59, 130, 246, 0.3);
        }

        .chat-container {
            flex: 1;
            overflow-y: auto;
            width: 100%;
            margin-bottom: 1rem;
            padding: 0 0.5rem;
            display: none;
        }

        .image-container {
            flex: 1;
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            width: 100%;
            padding: 1rem;
        }

        .generated-image {
            max-width: 100%;
            max-height: 80vh;
            border-radius: 1rem;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
        }
        
        .message {
            margin-bottom: 1.5rem;
            animation: slideUp 0.3s ease-out;
        }
        
        .user-message {
            display: flex;
            justify-content: flex-end;
        }
        
        .user-message .content {
            background: #3b82f6;
            color: white;
            padding: 0.75rem 1rem;
            border-radius: 1rem;
            border-bottom-right-radius: 0.25rem;
            max-width: 70%;
            word-wrap: break-word;
        }
        
        .ai-message {
            display: flex;
            justify-content: flex-start;
        }
        
        .ai-message .content {
            background: #1e293b;
            border: 1px solid #334155;
            padding: 1.5rem;
            border-radius: 1rem;
            border-bottom-left-radius: 0.25rem;
            width: 100%;
            word-wrap: break-word;
        }
        
        .solution-header {
            font-size: 1.25rem;
            font-weight: 600;
            color: #60a5fa;
            margin-bottom: 1rem;
            padding-bottom: 0.5rem;
            border-bottom: 1px solid #334155;
        }
        
        .action-buttons {
            display: flex;
            flex-wrap: wrap;
            gap: 0.5rem;
            margin-top: 1rem;
        }
        
        .action-btn {
            background: #374151;
            color: #d1d5db;
            border: none;
            padding: 0.5rem 0.75rem;
            border-radius: 0.5rem;
            font-size: 0.875rem;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            gap: 0.25rem;
        }
        
        .action-btn:hover {
            background: #4b5563;
            color: white;
        }
        
        .loading {
            display: none;
            align-items: center;
            justify-content: center;
            padding: 1rem;
            color: #94a3b8;
            width: 100%;
        }

        .loading.show {
            display: flex;
        }
        
        .loading-dots {
            display: flex;
            gap: 0.25rem;
            margin-right: 0.5rem;
        }
        
        .dot {
            width: 0.5rem;
            height: 0.5rem;
            background: #3b82f6;
            border-radius: 50%;
            animation: bounce 1.4s ease-in-out infinite both;
        }
        
        .dot:nth-child(1) { animation-delay: -0.32s; }
        .dot:nth-child(2) { animation-delay: -0.16s; }
        .dot:nth-child(3) { animation-delay: 0s; }
        
        .input-section {
            position: sticky;
            bottom: 0;
            background: transparent;
            width: 100%;
            margin-top: auto;
        }
        
        .input-bar {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 1.5rem;
            padding: 0.75rem 1rem;
            display: flex;
            align-items: center;
            gap: 0.5rem;
            transition: all 0.3s ease;
            box-shadow: 0 4px 30px rgba(0, 0, 0, 0.2);
            width: 100%;
        }
        
        .input-bar:focus-within {
            box-shadow: 0 0 0 4px rgba(59, 130, 246, 0.2);
            border-color: rgba(59, 130, 246, 0.5);
        }
        
        .input-field {
            flex: 1;
            background: transparent;
            border: none;
            outline: none;
            color: #e2e8f0;
            font-size: 1rem;
        }
        
        .input-field::placeholder {
            color: #94a3b8;
        }
        
        .input-btn {
            background: transparent;
            border: none;
            color: #cbd5e1;
            cursor: pointer;
            padding: 0.25rem;
            border-radius: 0.25rem;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        
        .input-btn:hover {
            color: #e2e8f0;
            transform: scale(1.1);
        }
        
        .send-btn {
            background: linear-gradient(45deg, #10b981, #06b6d4);
            color: white;
            padding: 0.65rem;
            border-radius: 1.5rem;
            transition: all 0.3s;
            box-shadow: 0 4px 15px rgba(0, 182, 212, 0.3);
        }
        
        .send-btn:hover {
            transform: scale(1.05);
            box-shadow: 0 6px 20px rgba(0, 182, 212, 0.4);
        }

        .typewriter-cursor {
            animation: blink 1s infinite;
        }
        
        .hidden {
            display: none !important;
        }

        /* Math styles */
        .katex-display {
            margin: 1rem 0;
        }
        
        /* Animations */
        @keyframes slideUp {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        @keyframes bounce {
            0%, 80%, 100% { transform: scale(0); }
            40% { transform: scale(1); }
        }
        
        @keyframes blink {
            0%, 50% { opacity: 1; }
            51%, 100% { opacity: 0; }
        }
        
        /* Markdown styles */
        h2 { color: #60a5fa; font-size: 1.125rem; font-weight: 600; margin: 1.5rem 0 0.75rem 0; }
        p { margin-bottom: 0.75rem; line-height: 1.6; }
        p:last-child { margin-bottom: 0; }
        
        /* Responsive */
        @media (max-width: 640px) {
            .container { padding: 0.5rem; }
            .title { font-size: 2rem; }
            .user-message .content { max-width: 85%; }
            .input-bar { padding: 0.5rem 0.75rem; }
            .subtitle { font-size: 0.875rem; }
            .mode-btn { padding: 0.5rem; }
        }
    </style>
</head>
<body>
    <div class="container">
        <header class="header" id="header">
            <h1 class="title">ProbSolver</h1>
            <p class="subtitle">Your AI math tutor and coding mentor. Upload problems, ask questions, and get step-by-step solutions.</p>
        </header>

        <section class="mode-toggles" id="modeToggles">
            <button id="studyModeBtn" class="mode-btn active">
                <span role="img" aria-label="light bulb">💡</span> Study Mode
            </button>
            <button id="chatModeBtn" class="mode-btn">
                <span role="img" aria-label="chat bubbles">💬</span> Chat Mode
            </button>
            <button id="imageModeBtn" class="mode-btn">
                <span role="img" aria-label="Image">🖼️</span> Image Mode
            </button>
        </section>
        
        <main class="chat-container" id="chatContainer">
            <!-- Messages will be added here -->
        </main>

        <main class="image-container" id="imageContainer">
            <!-- Generated image will be displayed here -->
            <img id="generatedImage" class="generated-image hidden" alt="Generated Image" />
            <div id="image-prompt-message" class="hidden" style="margin-top: 1rem; color: #94a3b8; text-align: center;">
                Please upload an image and type a prompt to get started.
            </div>
        </main>
        
        <div class="loading" id="loading">
            <div class="loading-dots">
                <div class="dot"></div>
                <div class="dot"></div>
                <div class="dot"></div>
            </div>
            <span>ProbSolver is thinking...</span>
        </div>
        
        <section class="input-section">
            <div class="input-bar">
                <input
                    type="text"
                    class="input-field"
                    id="inputField"
                    placeholder="Ask ProbSolver..."
                    autocomplete="off"
                />
                
                <label for="imageUpload" class="input-btn" id="imageUploadBtn" title="Upload image">
                    <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
                        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM5 19l3-4 4 5 6-6 4 5H5z"/>
                    </svg>
                </label>
                <input type="file" id="imageUpload" class="hidden" accept="image/*">

                <button class="input-btn" id="voiceBtn" title="Voice input">
                    <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                        <path d="M12 14c1.77 0 3-1.23 3-3V5c0-1.77-1.23-3-3-3S9 3.23 9 5v6c0 1.77 1.23 3 3 3zm5.3-3c0 3-2.54 5.1-5.3 5.1S6.7 14 6.7 11H5c0 3.41 2.72 6.23 6 6.72V21h2v-3.28c3.28-.49 6-3.31 6-6.72h-1.7z"/>
                    </svg>
                </button>
                
                <button class="input-btn send-btn" id="sendBtn" title="Send">
                    <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor" stroke="none">
                        <path d="M2.01 21L23 12 2.01 3 2 10l15 2-15 2z"/>
                    </svg>
                </button>
            </div>
        </section>
    </div>

    <script>
        class ProbSolver {
            constructor() {
                // The API key is set to an empty string. The platform will automatically inject the key.
                this.API_KEY = '';
                this.messages = [];
                this.uploadedImage = null;
                this.isLoading = false;
                this.recognition = null;
                this.isRecording = false;
                this.mode = 'study';
                this.audioPlayer = new Audio();
                
                this.initElements();
                this.initEventListeners();
                this.initSpeechRecognition();
                console.log('Using API Key:', this.API_KEY ? 'Key Found' : 'No Key');
            }
            
            initElements() {
                this.header = document.getElementById('header');
                this.chatContainer = document.getElementById('chatContainer');
                this.imageContainer = document.getElementById('imageContainer');
                this.generatedImage = document.getElementById('generatedImage');
                this.loading = document.getElementById('loading');
                this.inputField = document.getElementById('inputField');
                this.imageUpload = document.getElementById('imageUpload');
                this.voiceBtn = document.getElementById('voiceBtn');
                this.sendBtn = document.getElementById('sendBtn');
                this.studyModeBtn = document.getElementById('studyModeBtn');
                this.chatModeBtn = document.getElementById('chatModeBtn');
                this.imageModeBtn = document.getElementById('imageModeBtn');
                this.imageUploadBtn = document.getElementById('imageUploadBtn');
                this.imagePromptMessage = document.getElementById('image-prompt-message');
            }
            
            initEventListeners() {
                this.sendBtn.addEventListener('click', () => this.handleSend());
                this.inputField.addEventListener('keydown', (e) => {
                    if (e.key === 'Enter' && !e.shiftKey) {
                        e.preventDefault();
                        this.handleSend();
                    }
                });
                
                this.imageUpload.addEventListener('change', (e) => this.handleImageUpload(e));
                this.voiceBtn.addEventListener('click', () => this.toggleVoice());
                
                this.studyModeBtn.addEventListener('click', () => this.setMode('study'));
                this.chatModeBtn.addEventListener('click', () => this.setMode('chat'));
                this.imageModeBtn.addEventListener('click', () => this.setMode('image'));
            }

            setMode(newMode) {
                this.mode = newMode;
                
                // Reset UI for all modes
                this.chatContainer.style.display = 'none';
                this.imageContainer.style.display = 'none';
                this.generatedImage.classList.add('hidden');
                this.imagePromptMessage.classList.add('hidden');
                
                this.studyModeBtn.classList.remove('active');
                this.chatModeBtn.classList.remove('active');
                this.imageModeBtn.classList.remove('active');
                
                // Activate the selected mode
                if (newMode === 'study') {
                    this.studyModeBtn.classList.add('active');
                    this.chatContainer.style.display = 'flex';
                    this.chatContainer.innerHTML = '';
                    this.inputField.placeholder = "Ask ProbSolver...";
                    this.imageUploadBtn.style.display = 'flex';
                    this.voiceBtn.style.display = 'flex';
                } else if (newMode === 'chat') {
                    this.chatModeBtn.classList.add('active');
                    this.chatContainer.style.display = 'flex';
                    this.chatContainer.innerHTML = '';
                    this.inputField.placeholder = "Chat with ProbSolver...";
                    this.imageUploadBtn.style.display = 'none';
                    this.voiceBtn.style.display = 'flex';
                } else if (newMode === 'image') {
                    this.imageModeBtn.classList.add('active');
                    this.imageContainer.style.display = 'flex';
                    this.imagePromptMessage.classList.remove('hidden');
                    this.inputField.placeholder = "Describe how to edit the image...";
                    this.imageUploadBtn.style.display = 'flex';
                    this.voiceBtn.style.display = 'none';
                }

                this.messages = [];
                this.uploadedImage = null; // Clear the uploaded image when changing modes
            }

            initSpeechRecognition() {
                if ('webkitSpeechRecognition' in window || 'SpeechRecognition' in window) {
                    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
                    this.recognition = new SpeechRecognition();
                    this.recognition.continuous = false;
                    this.recognition.interimResults = false;
                    this.recognition.lang = 'en-US';
                    
                    this.recognition.onstart = () => {
                        this.isRecording = true;
                        this.voiceBtn.style.color = '#10b981';
                        this.inputField.placeholder = 'Listening...';
                    };
                    
                    this.recognition.onresult = (event) => {
                        const transcript = event.results[0][0].transcript;
                        this.inputField.value = transcript;
                    };
                    
                    this.recognition.onend = () => {
                        this.isRecording = false;
                        this.voiceBtn.style.color = '#cbd5e1';
                        this.inputField.placeholder = 'Ask ProbSolver...';
                    };
                } else {
                    this.voiceBtn.style.display = 'none';
                }
            }
            
            handleImageUpload(event) {
                const file = event.target.files[0];
                if (!file) return;
                
                const reader = new FileReader();
                reader.onload = (e) => {
                    this.uploadedImage = {
                        data: e.target.result.split(',')[1],
                        mimeType: file.type
                    };
                    if (this.mode === 'image') {
                        // In image mode, we just update the message
                        this.imagePromptMessage.textContent = 'Image uploaded. Now type a prompt to edit it.';
                    } else {
                        // In study mode, we add a message to the chat
                        this.addMessage("Image uploaded.", 'user');
                    }
                };
                reader.readAsDataURL(file);
            }

            toggleVoice() {
                if (!this.recognition) return;
                
                if (this.isRecording) {
                    this.recognition.stop();
                } else {
                    this.recognition.start();
                }
            }
            
            addMessage(content, role) {
                const messageId = Date.now().toString();
                const message = { id: messageId, content, role };
                
                this.messages.push(message);
                this.renderMessage(message);
                this.scrollToBottom();
                
                return messageId;
            }
            
            renderMessage(message) {
                this.chatContainer.style.display = 'flex';
                const messageDiv = document.createElement('div');
                messageDiv.className = `message ${message.role}-message`;
                messageDiv.id = `message-${message.id}`;
                
                const contentDiv = document.createElement('div');
                contentDiv.className = 'content';
                
                if (this.mode === 'study' && message.role === 'model') {
                    const header = document.createElement('div');
                    header.className = 'solution-header';
                    header.textContent = 'Solution';
                    contentDiv.appendChild(header);
                }
                
                const textDiv = document.createElement('div');
                textDiv.innerHTML = message.content;
                contentDiv.appendChild(textDiv);
                
                messageDiv.appendChild(contentDiv);
                this.chatContainer.appendChild(messageDiv);
            }
            
            updateMessage(messageId, content, showActions = false) {
                const messageElement = document.getElementById(`message-${messageId}`);
                if (!messageElement) return;
                
                const contentDiv = messageElement.querySelector('.content > div:last-child') || 
                                 messageElement.querySelector('.content');
                
                if (contentDiv.tagName === 'DIV' && contentDiv.innerHTML !== undefined) {
                    contentDiv.innerHTML = content;
                } else {
                    contentDiv.innerHTML = content;
                }
                
                if (showActions && !messageElement.querySelector('.action-buttons')) {
                    this.addActionButtons(messageElement);
                    this.addTTSButton(messageElement);
                }
                
                if (this.mode === 'study') {
                    this.renderMath(messageElement);
                }
            }
            
            addActionButtons(messageElement) {
                const buttonsDiv = document.createElement('div');
                buttonsDiv.className = 'action-buttons';
                
                const buttons = [
                    { text: '✨ Explain Concept', action: 'explain' },
                    { text: '✨ Practice Problems', action: 'practice' },
                    { text: '✨ Elaborate', action: 'elaborate' },
                    { text: '✨ Real-world Examples', action: 'realworld' },
                    { text: '📝 Shorten it', action: 'shorten' }
                ];
                
                buttons.forEach(btn => {
                    const button = document.createElement('button');
                    button.className = 'action-btn';
                    button.textContent = btn.text;
                    button.addEventListener('click', () => this.handleAction(btn.action));
                    buttonsDiv.appendChild(button);
                });
                
                messageElement.querySelector('.content').appendChild(buttonsDiv);
            }

            addTTSButton(messageElement) {
                const ttsButton = document.createElement('button');
                ttsButton.className = 'action-btn';
                ttsButton.textContent = '🔊 Read Aloud';
                ttsButton.style.marginTop = '0.5rem';
                ttsButton.style.display = 'block';
                ttsButton.addEventListener('click', () => this.readSolutionAloud(messageElement));
                messageElement.querySelector('.content').appendChild(ttsButton);
            }

            async readSolutionAloud(messageElement) {
                const textContent = messageElement.querySelector('.content > div:last-child').innerText;
                if (!textContent) return;

                const ttsBtn = messageElement.querySelector('.action-btn:last-child');
                const originalText = ttsBtn.textContent;
                ttsBtn.textContent = 'Loading...';
                ttsBtn.disabled = true;

                try {
                    const audioUrl = await this.callGeminiTTS(textContent);
                    this.audioPlayer.src = audioUrl;
                    this.audioPlayer.play();
                } catch (error) {
                    console.error('TTS Error:', error);
                    // Use a simple message box since alert() is not allowed
                    const tempMessage = this.addMessage('⚠️ Failed to generate audio. Please try again.', 'model');
                    setTimeout(() => document.getElementById(`message-${tempMessage}`).remove(), 5000);
                } finally {
                    ttsBtn.textContent = originalText;
                    ttsBtn.disabled = false;
                }
            }

            async callGeminiTTS(text) {
                const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-tts:generateContent?key=${this.API_KEY}`;
                const payload = {
                    contents: [{
                        parts: [{ text: text }]
                    }],
                    generationConfig: {
                        responseModalities: ["AUDIO"],
                        speechConfig: {
                            voiceConfig: {
                                prebuiltVoiceConfig: { voiceName: "Kore" }
                            }
                        }
                    },
                    model: "gemini-2.5-flash-preview-tts"
                };

                const response = await fetch(url, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                
                if (!response.ok) {
                    throw new Error(`TTS API Error: ${response.status}`);
                }

                const result = await response.json();
                const audioData = result?.candidates?.[0]?.content?.parts?.[0]?.inlineData?.data;
                const mimeType = result?.candidates?.[0]?.content?.parts?.[0]?.inlineData?.mimeType;

                if (!audioData || !mimeType) {
                    throw new Error('TTS response missing audio data.');
                }
                
                const pcmData = this.base64ToArrayBuffer(audioData);
                const pcm16 = new Int16Array(pcmData);
                const wavBlob = this.pcmToWav(pcm16, 24000); // 24000Hz is the sample rate for Kore voice
                return URL.createObjectURL(wavBlob);
            }

            base64ToArrayBuffer(base64) {
                const binaryString = window.atob(base64);
                const len = binaryString.length;
                const bytes = new Uint8Array(len);
                for (let i = 0; i < len; i++) {
                    bytes[i] = binaryString.charCodeAt(i);
                }
                return bytes.buffer;
            }

            pcmToWav(pcmData, sampleRate) {
                const dataLength = pcmData.length * 2;
                const buffer = new ArrayBuffer(44 + dataLength);
                const view = new DataView(buffer);
                let offset = 0;

                const writeString = (str) => {
                    for (let i = 0; i < str.length; i++) {
                        view.setUint8(offset++, str.charCodeAt(i));
                    }
                };

                // RIFF chunk descriptor
                writeString('RIFF');
                view.setUint32(offset, 36 + dataLength, true); offset += 4;
                writeString('WAVE');
                // fmt sub-chunk
                writeString('fmt ');
                view.setUint32(offset, 16, true); offset += 4; // Sub-chunk size
                view.setUint16(offset, 1, true); offset += 2;  // Audio format (1 = PCM)
                view.setUint32(offset, sampleRate, true); offset += 4; // Sample rate
                view.setUint32(offset, sampleRate * 2, true); offset += 4; // Byte rate
                view.setUint16(offset, 2, true); offset += 2;  // Block align
                view.setUint16(offset, 16, true); offset += 2; // Bits per sample
                // data sub-chunk
                writeString('data');
                view.setUint32(offset, dataLength, true); offset += 4; // Sub-chunk size
                
                // Write PCM data
                for (let i = 0; i < pcmData.length; i++) {
                    view.setInt16(offset, pcmData[i], true);
                    offset += 2;
                }

                return new Blob([view], { type: 'audio/wav' });
            }

            
            async handleAction(action) {
                let prompt;
                const lastMathProblem = this.messages.slice().reverse().find(msg => msg.role === 'user')?.content;
                if (!lastMathProblem) {
                    this.addMessage("Please ask a math problem first!", 'model');
                    return;
                }

                const lastAiResponse = this.messages.slice().reverse().find(msg => msg.role === 'model')?.content;

                switch (action) {
                    case 'explain':
                        prompt = `Explain the core mathematical concept behind this problem in simple terms: "${lastMathProblem}"`;
                        break;
                    case 'practice':
                        prompt = `Generate 3 similar practice problems based on: "${lastMathProblem}". Include solutions.`;
                        break;
                    case 'elaborate':
                        prompt = `Elaborate on the following solution in more detail. Use markdown and LaTeX where appropriate: "${lastAiResponse}"`;
                        break;
                    case 'realworld':
                        prompt = `Provide 3 real-world examples and applications for the concept in the following problem: "${lastMathProblem}"`;
                        break;
                    case 'shorten':
                        prompt = `Create a concise summary of the key steps to solve: "${lastMathProblem}"`;
                        break;
                }
                
                try {
                    this.addMessage(prompt, 'user');
                    await this.processMessage();
                } catch (error) {
                    this.addMessage('⚠️ Sorry, I encountered an error when trying to complete that action. Please try again.', 'model');
                    console.error('Action Error:', error);
                }
            }

            async handleSend() {
                const text = this.inputField.value.trim();
                
                if (this.mode === 'image') {
                    if (!text || !this.uploadedImage) {
                        this.imagePromptMessage.textContent = 'Please upload an image and type a prompt.';
                        return;
                    }
                    this.inputField.value = '';
                    this.processImageGeneration(text);
                } else {
                    if (!text && !this.uploadedImage) return;

                    if (text.toLowerCase().includes('who created you')) {
                        this.addMessage('I was created by naitik khandelwal', 'model');
                        this.inputField.value = '';
                        return;
                    }
                    
                    if (text) this.addMessage(text, 'user');
                    this.inputField.value = '';
                    await this.processMessage();
                }
            }
            
            async processMessage() {
                if (this.isLoading) return;
                
                this.isLoading = true;
                this.loading.classList.add('show');
                
                try {
                    const response = await this.callGeminiAPI();
                    const messageId = this.addMessage('', 'model');
                    const showActions = this.mode === 'study' && this.uploadedImage == null;
                    await this.typewriterEffect(messageId, response, showActions);
                } catch (error) {
                    this.loading.classList.remove('show');
                    throw error; // Re-throw the error to be caught by the calling function
                } finally {
                    this.isLoading = false;
                    this.loading.classList.remove('show');
                }
            }

            async processImageGeneration(prompt) {
                if (this.isLoading) return;
                
                this.isLoading = true;
                this.loading.classList.add('show');
                this.imageContainer.style.display = 'flex';
                this.generatedImage.classList.add('hidden');
                this.imagePromptMessage.classList.add('hidden');
                
                try {
                    const imageUrl = await this.callGeminiImageAPI(prompt);
                    this.displayGeneratedImage(imageUrl);
                } catch (error) {
                    console.error('Image Generation Error:', error);
                    this.imagePromptMessage.textContent = '⚠️ Sorry, I could not generate an image. Please try again.';
                    this.imagePromptMessage.classList.remove('hidden');
                } finally {
                    this.isLoading = false;
                    this.loading.classList.remove('show');
                }
            }

            displayGeneratedImage(url) {
                this.generatedImage.src = url;
                this.generatedImage.classList.remove('hidden');
            }

            async callGeminiImageAPI(prompt) {
                // Check if an image is uploaded for image-to-image conversion
                if (!this.uploadedImage) {
                    throw new Error("No image uploaded for conversion.");
                }

                const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image-preview:generateContent?key=${this.API_KEY}`;
                
                const payload = {
                    contents: [{
                        parts: [
                            { text: prompt },
                            { inlineData: this.uploadedImage }
                        ]
                    }],
                    generationConfig: {
                        responseModalities: ["IMAGE"]
                    }
                };
                
                const response = await this.callApiWithTimeout(url, payload);

                if (!response.ok) {
                    throw new Error(`Image API Error: ${response.status}`);
                }
                
                const result = await response.json();
                const base64Data = result?.candidates?.[0]?.content?.parts?.find(p => p.inlineData)?.inlineData?.data;
                
                if (!base64Data) {
                    throw new Error('Image data not found in response.');
                }
                
                // Clear the uploaded image after a successful generation
                this.uploadedImage = null;
                return `data:image/png;base64,${base64Data}`;
            }

            async callApiWithTimeout(url, payload, timeout = 60000) {
                const controller = new AbortController();
                const id = setTimeout(() => controller.abort(), timeout);
                
                try {
                    const response = await fetch(url, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload),
                        signal: controller.signal
                    });
                    clearTimeout(id);
                    return response;
                } catch (error) {
                    clearTimeout(id);
                    if (error.name === 'AbortError') {
                        throw new Error('API request timed out.');
                    }
                    throw error;
                }
            }

            async callGeminiAPI() {
                const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${this.API_KEY}`;
                
                const contents = this.messages.map(msg => ({
                    role: msg.role === 'user' ? 'user' : 'model',
                    parts: [{ text: msg.content }]
                }));
                
                if (this.uploadedImage) {
                    contents[contents.length - 1].parts.push({
                        inlineData: {
                            mimeType: this.uploadedImage.mimeType,
                            data: this.uploadedImage.data
                        }
                    });
                    this.uploadedImage = null;
                }
                
                let systemInstructionText;
                if (this.mode === 'study') {
                    systemInstructionText = "You are an expert math and programming tutor. Provide clear, step-by-step solutions for math and coding problems using markdown formatting. Use LaTeX for mathematical expressions (wrap in $ for inline math, $$ for display math). Be concise but thorough. Keep the tone professional and educational.";
                } else {
                    systemInstructionText = 'Act as a casual and friendly chatbot. Keep your answers conversational. Do not provide code or complex math equations.';
                }

                const payload = {
                    contents: contents,
                    systemInstruction: {
                        parts: [{ text: systemInstructionText }]
                    }
                };
                
                const response = await this.callApiWithTimeout(url, payload);
                
                if (!response.ok) {
                    throw new Error(`API Error: ${response.status}`);
                }
                
                const result = await response.json();
                return result?.candidates?.[0]?.content?.parts?.[0]?.text || 'Sorry, I could not process your request.';
            }

            async typewriterEffect(messageId, text, showActions = false) {
                const words = text.split(' ');
                let currentText = '';
                
                for (let i = 0; i < words.length; i++) {
                    currentText += words[i] + ' ';
                    
                    let displayText = this.parseMarkdown(currentText);
                    displayText += '<span class="typewriter-cursor">|</span>';
                    
                    this.updateMessage(messageId, displayText);
                    await new Promise(resolve => setTimeout(resolve, 30));
                }
                
                const finalContent = this.parseMarkdown(text);
                this.updateMessage(messageId, finalContent, showActions);
            }
            
            parseMarkdown(text) {
                let html = text
                    .replace(/^## (.+)$/gm, '<h2>$1</h2>')
                    .replace(/^\* (.+)$/gm, '<p style="margin-left: 1.5rem;">• $1</p>')
                    .replace(/\n\n/g, '</p><p>')
                    .replace(/^(?!<h2|<p style)(.+)$/gm, '<p>$1</p>')
                    .replace(/<p><\/p>/g, '');
                
                // KaTeX auto-render will handle the math, so no manual replacement here.
                
                return html;
            }
            
            renderMath(element) {
                if (window.renderMathInElement) {
                    renderMathInElement(element, {
                        delimiters: [
                            {left: "$$", right: "$$", display: true},
                            {left: "$", right: "$", display: false}
                        ]
                    });
                }
            }
            
            scrollToBottom() {
                this.chatContainer.scrollTop = this.chatContainer.scrollHeight;
            }
        }
        
        document.addEventListener('DOMContentLoaded', () => {
            new ProbSolver();
        });
    </script>
</body>
</html>


