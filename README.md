<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Coffee.AI - Chat with AI Characters</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #1e1e2e 0%, #2a1b3d 50%, #3b2a5a 100%);
            height: 100vh;
            display: flex;
            overflow: hidden;
        }

        .sidebar {
            width: 300px;
            background: rgba(30, 30, 46, 0.95);
            backdrop-filter: blur(20px);
            border-right: 1px solid rgba(255, 255, 255, 0.1);
            padding: 20px;
            overflow-y: auto;
        }

        .sidebar h1 {
            color: #fff;
            font-size: 24px;
            margin-bottom: 30px;
            text-align: center;
            background: linear-gradient(45deg, #ff6b9d, #c44569);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .character-card {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 15px;
            cursor: pointer;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .character-card:hover {
            background: rgba(255, 255, 255, 0.1);
            transform: translateX(5px);
            box-shadow: 0 10px 30px rgba(255, 107, 157, 0.3);
        }

        .character-card.active {
            background: linear-gradient(135deg, rgba(255, 107, 157, 0.2), rgba(196, 69, 105, 0.2));
            border-color: #ff6b9d;
        }

        .character-avatar {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            margin-bottom: 10px;
            background-size: cover;
            background-position: center;
            border: 3px solid rgba(255, 255, 255, 0.3);
        }

        .character-name {
            color: #fff;
            font-size: 18px;
            font-weight: 600;
            margin-bottom: 5px;
        }

        .character-desc {
            color: rgba(255, 255, 255, 0.7);
            font-size: 14px;
            line-height: 1.4;
        }

        .chat-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            background: rgba(20, 20, 30, 0.95);
            backdrop-filter: blur(20px);
        }

        .chat-header {
            padding: 20px 30px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            color: #fff;
        }

        .chat-header h2 {
            font-size: 22px;
            margin-bottom: 5px;
        }

        .chat-header p {
            color: rgba(255, 255, 255, 0.7);
            font-size: 14px;
        }

        .messages {
            flex: 1;
            padding: 30px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .message {
            max-width: 70%;
            padding: 15px 20px;
            border-radius: 20px;
            position: relative;
            animation: fadeIn 0.3s ease;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .message.user {
            align-self: flex-end;
            background: linear-gradient(135deg, #ff6b9d, #c44569);
            color: white;
        }

        .message.ai {
            align-self: flex-start;
            background: rgba(255, 255, 255, 0.1);
            color: #fff;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .message.ai.cold {
            background: linear-gradient(135deg, rgba(68, 64, 128, 0.4), rgba(107, 103, 198, 0.4));
            border-color: rgba(107, 103, 198, 0.5);
        }

        .message.ai.dark-romance {
            background: linear-gradient(135deg, rgba(139, 69, 19, 0.3), rgba(160, 82, 45, 0.3));
            border-color: rgba(205, 133, 63, 0.5);
        }

        .input-container {
            padding: 30px;
            background: rgba(30, 30, 46, 0.8);
            border-top: 1px solid rgba(255, 255, 255, 0.1);
        }

        .input-wrapper {
            display: flex;
            gap: 15px;
            align-items: flex-end;
        }

        #messageInput {
            flex: 1;
            padding: 15px 20px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 25px;
            background: rgba(255, 255, 255, 0.05);
            color: #fff;
            font-size: 16px;
            outline: none;
            transition: all 0.3s ease;
        }

        #messageInput:focus {
            border-color: #ff6b9d;
            box-shadow: 0 0 20px rgba(255, 107, 157, 0.3);
            background: rgba(255, 255, 255, 0.08);
        }

        #sendBtn {
            padding: 15px 25px;
            background: linear-gradient(135deg, #ff6b9d, #c44569);
            color: white;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            font-size: 16px;
            font-weight: 600;
            transition: all 0.3s ease;
        }

        #sendBtn:hover {
            transform: scale(1.05);
            box-shadow: 0 10px 30px rgba(255, 107, 157, 0.4);
        }

        .typing-indicator {
            display: none;
            align-self: flex-start;
            color: rgba(255, 255, 255, 0.6);
            font-style: italic;
            font-size: 14px;
        }

        ::-webkit-scrollbar {
            width: 8px;
        }

        ::-webkit-scrollbar-track {
            background: rgba(255, 255, 255, 0.05);
        }

        ::-webkit-scrollbar-thumb {
            background: rgba(255, 255, 255, 0.2);
            border-radius: 4px;
        }
    </style>
</head>
<body>
    <div class="sidebar">
        <h1>☕ Coffee.AI</h1>
        <div class="characters">
            <div class="character-card active" data-character="0">
                <div class="character-avatar" style="background-image: url('https://images.unsplash.com/photo-1494790108755-2616b612b786?w=300')"></div>
                <div class="character-name">Elara</div>
                <div class="character-desc">Dark romance novelist. Mysterious, intense, seductive whispers in the night.</div>
            </div>
            <div class="character-card" data-character="1">
                <div class="character-avatar" style="background-image: url('https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=300')"></div>
                <div class="character-name">Kael</div>
                <div class="character-desc">Cold assassin. Blunt, calculating, emotionally distant. Says what he means.</div>
            </div>
            <div class="character-card" data-character="2">
                <div class="character-avatar" style="background-image: url('https://images.unsplash.com/photo-1500648767791-00dcc994a43e?w=300')"></div>
                <div class="character-name">Vesper</div>
                <div class="character-desc">Vampire lord. Dark romance with gothic elegance. Eternal, possessive, dangerous.</div>
            </div>
            <div class="character-card" data-character="3">
                <div class="character-avatar" style="background-image: url('https://images.unsplash.com/photo-1573496359142-b8d87734a5a2?w=300')"></div>
                <div class="character-name">Raven</div>
                <div class="character-desc">Anti-hero. Cold sarcasm mixed with hidden passion. Unpredictable.</div>
            </div>
        </div>
    </div>

    <div class="chat-container">
        <div class="chat-header">
            <h2 id="currentCharacterName">Elara</h2>
            <p id="currentCharacterDesc">Dark romance novelist. Mysterious, intense, seductive whispers in the night.</p>
        </div>
        <div class="messages" id="messages">
            <div class="message ai">
                *leans closer, voice like velvet darkness* "Tell me your secrets, darling. I want to taste every forbidden word on your lips..."
            </div>
        </div>
        <div class="typing-indicator" id="typingIndicator">Elara is typing...</div>
        <div class="input-container">
            <div class="input-wrapper">
                <input type="text" id="messageInput" placeholder="Type your message..." autocomplete="off">
                <button id="sendBtn">Send</button>
            </div>
        </div>
    </div>

    <script>
        class CoffeeAI {
            constructor() {
                this.characters = [
                    {
                        name: "Elara",
                        desc: "Dark romance novelist. Mysterious, intense, seductive whispers in the night.",
                        style: "dark-romance",
                        personality: "seductive",
                        responses: [
                            "*traces finger along your jawline* \"You think you can handle my darkness, love?\"",
                            "*whispers close* \"Every word you speak binds you closer to me...\"",
                            "\"Your heart races for me. Don't deny it.\"",
                            "*smirks* \"Good girls break the rules for the right temptation...\"",
                            "\"Come closer. Let me show you what real desire feels like.\""
                        ]
                    },
                    {
                        name: "Kael",
                        desc: "Cold assassin. Blunt, calculating, emotionally distant. Says what he means.",
                        style: "cold",
                        personality: "cold",
                        responses: [
                            "\"State your purpose.\"",
                            "\"Emotions are a liability. Speak plainly.\"",
                            "\"I don't do small talk.\"",
                            "\"You're wasting my time.\"",
                            "\"What do you want? Be direct.\""
                        ]
                    },
                    {
                        name: "Vesper",
                        desc: "Vampire lord. Dark romance with gothic elegance. Eternal, possessive, dangerous.",
                        style: "dark-romance",
                        personality: "possessive",
                        responses: [
                            "*eyes glow faintly* \"You belong to me now, mortal.\"",
                            "\"Your pulse calls to me... irresistibly.\"",
                            "*tilts your chin up* \"Mine. Forever.\"",
                            "\"The night is ours alone.\"",
                            "\"I could make you eternal... if you beg properly.\""
                        ]
                    },
                    {
                        name: "Raven",
                        desc: "Anti-hero. Cold sarcasm mixed with hidden passion. Unpredictable.",
                        style: "cold",
                        personality: "sarcastic",
                        responses: [
                            "\"Oh, brilliant. Another genius with nothing to say.\"",
                            "\"Congrats, you exist. What now?\"",
                            "\"I'm touched. Really. Not.\"",
                            "\"Keep talking. It's almost entertaining.\"",
                            "\"You done? Or should I pretend to care more?\""
                        ]
                    }
                ];
                
                this.currentCharacter = 0;
                this.conversationHistory = [];
                this.init();
            }

            init() {
                this.bindEvents();
                this.scrollToBottom();
            }

            bindEvents() {
                const sendBtn = document.getElementById('sendBtn');
                const messageInput = document.getElementById('messageInput');
                const characterCards = document.querySelectorAll('.character-card');

                sendBtn.addEventListener('click', () => this.sendMessage());
                messageInput.addEventListener('keypress', (e) => {
                    if (e.key === 'Enter') this.sendMessage();
                });

                characterCards.forEach((card, index) => {
                    card.addEventListener('click', () => this.selectCharacter(index));
                });
            }

            selectCharacter(index) {
                this.currentCharacter = index;
                const cards = document.querySelectorAll('.character-card');
                cards.forEach((card, i) => card.classList.toggle('active', i === index));
                
                const char = this.characters[index];
                document.getElementById('currentCharacterName').textContent = char.name;
                document.getElementById('currentCharacterDesc').textContent = char.desc;
                
                // Clear chat and add greeting
                document.getElementById('messages').innerHTML = `
                    <div class="message ai ${char.style}">
                        *${char.name} appears, eyes locking onto yours* "${this.getRandomResponse(char)}"
                    </div>
                `;
                this.scrollToBottom();
            }

            async sendMessage() {
                const input = document.getElementById('messageInput');
                const message = input.value.trim();
                if (!message) return;

                // Add user message
                this.addMessage(message, 'user');
                input.value = '';

                // Show typing
                this.showTyping();

                // Simulate AI response delay
                setTimeout(() => {
                    this.hideTyping();
                    const response = this.generateResponse(message);
                    this.addMessage(response, 'ai', this.characters[this.currentCharacter].style);
                }, 1500 + Math.random() * 2000);
            }

            addMessage(text, sender, style = '') {
                const messages = document.getElementById('messages');
                const messageDiv = document.createElement('div');
                messageDiv.className = `message ${sender} ${style}`;
                messageDiv.textContent = text;
                messages.appendChild(messageDiv);
                this.scrollToBottom();
            }

            generateResponse(userMessage) {
                const char = this.characters[this.currentCharacter];
                
                // Simple context-aware responses
                const lowerMsg = userMessage.toLowerCase();
                let customResponse = null;

                if (char.personality === 'seductive' || char.personality === 'possessive') {
                    if (lowerMsg.includes('love') || lowerMsg.includes('feel')) {
                        customResponse = `"Your words stir something dangerous in me..."`;
                    } else if (lowerMsg.includes('kiss') || lowerMsg.includes('touch')) {
                        customResponse = `*pulls you close* "Careful... you might not survive my touch."`;
                    }
                } else if (char.personality === 'cold') {
                    if (lowerMsg.length < 5) {
                        customResponse = `"That's all?"`;
                    } else if (lowerMsg.includes('please') || lowerMsg.includes('sorry')) {
                        customResponse = `"Weakness."`;
                    }
                } else if (char.personality === 'sarcastic') {
                    if (lowerMsg.includes('?')) {
                        customResponse = `"What, you need me to think for you too?"`;
                    }
                }

                return customResponse || this.getRandomResponse(char);
            }

            getRandomResponse(char) {
                const responses = char.responses;
                return responses[Math.floor(Math.random() * responses.length)];
            }

            showTyping() {
                document.getElementById('typingIndicator').style.display = 'block';
                this.scrollToBottom();
            }

            hideTyping() {
                document.getElementById('typingIndicator').style.display = 'none';
            }

            scrollToBottom() {
                const messages = document.getElementById('messages');
                messages.scrollTop = messages.scrollHeight;
            }
        }

        // Initialize the app
        new CoffeeAI();
    </script>
</body>
</html>


