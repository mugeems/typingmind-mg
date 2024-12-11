// advanced-typingmind-assistant.js
(() => {
    class AdvancedAssistant {
        constructor() {
            this.settings = {
                autoSave: true,
                darkModeEnhanced: true,
                keyboardShortcuts: true,
                statisticsTracking: true,
                autoTranslate: false
            };
            this.statistics = {
                totalTokens: 0,
                chatCount: 0,
                timeSpent: 0
            };
            this.startTime = Date.now();
            this.init();
        }

        async init() {
            this.loadSettings();
            this.createUI();
            this.initializeKeyboardShortcuts();
            this.setupEventListeners();
            this.initializeAutoSave();
            this.startStatisticsTracking();
            await this.initializeTranslationService();
        }

        createUI() {
            // Create floating control panel
            const panel = document.createElement('div');
            panel.innerHTML = `
                <div id="advanced-assistant-panel" class="assistant-panel">
                    <div class="panel-header">Advanced Assistant</div>
                    <div class="panel-content">
                        <div class="stats-section">
                            <h3>Statistics</h3>
                            <div id="token-count">Tokens: 0</div>
                            <div id="chat-count">Chats: 0</div>
                            <div id="time-spent">Time: 0min</div>
                        </div>
                        <div class="tools-section">
                            <button id="export-data">Export Data</button>
                            <button id="analyze-chats">Analyze Chats</button>
                            <button id="generate-report">Generate Report</button>
                        </div>
                    </div>
                </div>
            `;
            document.body.appendChild(panel);

            // Add custom styles
            const styles = document.createElement('style');
            styles.textContent = `
                .assistant-panel {
                    position: fixed;
                    right: 20px;
                    top: 20px;
                    background: var(--background-color);
                    border: 1px solid var(--border-color);
                    border-radius: 8px;
                    padding: 15px;
                    z-index: 1000;
                    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
                    width: 250px;
                }
                .panel-header {
                    font-weight: bold;
                    margin-bottom: 10px;
                    padding-bottom: 5px;
                    border-bottom: 1px solid var(--border-color);
                }
                .stats-section, .tools-section {
                    margin: 10px 0;
                }
                button {
                    width: 100%;
                    margin: 5px 0;
                    padding: 8px;
                    border-radius: 4px;
                    border: 1px solid var(--border-color);
                    background: var(--background-color);
                    cursor: pointer;
                }
                button:hover {
                    background: var(--hover-color);
                }
            `;
            document.head.appendChild(styles);
        }

        initializeKeyboardShortcuts() {
            const shortcuts = {
                'Alt+S': () => this.saveCurrentChat(),
                'Alt+E': () => this.exportData(),
                'Alt+A': () => this.analyzeChats(),
                'Alt+R': () => this.generateReport()
            };

            document.addEventListener('keydown', (e) => {
                const key = `${e.altKey ? 'Alt+' : ''}${e.key.toUpperCase()}`;
                if (shortcuts[key]) {
                    e.preventDefault();
                    shortcuts[key]();
                }
            });
        }

        async initializeTranslationService() {
            // Initialize translation service
            this.translator = {
                async translate(text, targetLang) {
                    // Implement translation logic here
                    return text;
                }
            };
        }

        setupEventListeners() {
            // Monitor chat interactions
            document.addEventListener('click', (e) => {
                if (e.target.matches('[data-element-id="send-message-button"]')) {
                    this.handleNewMessage();
                }
            });

            // Monitor theme changes
            const observer = new MutationObserver((mutations) => {
                mutations.forEach((mutation) => {
                    if (mutation.attributeName === 'class') {
                        this.handleThemeChange();
                    }
                });
            });

            observer.observe(document.body, {
                attributes: true,
                attributeFilter: ['class']
            });
        }

        async handleNewMessage() {
            this.statistics.chatCount++;
            await this.updateStatistics();
            if (this.settings.autoTranslate) {
                this.translateLastMessage();
            }
        }

        async translateLastMessage() {
            const lastMessage = document.querySelector('.chat-message:last-child');
            if (lastMessage) {
                const text = lastMessage.textContent;
                const translated = await this.translator.translate(text, 'en');
                // Add translation below original message
                const translationDiv = document.createElement('div');
                translationDiv.className = 'message-translation';
                translationDiv.textContent = translated;
                lastMessage.appendChild(translationDiv);
            }
        }

        async updateStatistics() {
            // Update token count
            const messages = document.querySelectorAll('.chat-message');
            this.statistics.totalTokens = this.calculateTokens(messages);
            
            // Update time spent
            this.statistics.timeSpent = Math.floor((Date.now() - this.startTime) / 60000);

            // Update UI
            document.getElementById('token-count').textContent = `Tokens: ${this.statistics.totalTokens}`;
            document.getElementById('chat-count').textContent = `Chats: ${this.statistics.chatCount}`;
            document.getElementById('time-spent').textContent = `Time: ${this.statistics.timeSpent}min`;
        }

        calculateTokens(messages) {
            let total = 0;
            messages.forEach(msg => {
                // Rough estimation: 4 chars = 1 token
                total += Math.ceil(msg.textContent.length / 4);
            });
            return total;
        }

        async exportData() {
            const data = {
                statistics: this.statistics,
                settings: this.settings,
                chats: await this.getAllChats()
            };

            const blob = new Blob([JSON.stringify(data, null, 2)], {
                type: 'application/json'
            });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `typingmind-export-${new Date().toISOString()}.json`;
            a.click();
            URL.revokeObjectURL(url);
        }

        async getAllChats() {
            // Implementation would depend on TypingMind's storage structure
            return [];
        }

        async analyzeChats() {
            // Implement chat analysis logic
            console.log('Analyzing chats...');
        }

        async generateReport() {
            const report = {
                timestamp: new Date().toISOString(),
                statistics: this.statistics,
                analysis: await this.performAnalysis()
            };

            // Create report UI
            const reportWindow = window.open('', 'Report', 'width=800,height=600');
            reportWindow.document.write(`
                <html>
                    <head>
                        <title>TypingMind Analysis Report</title>
                        <style>
                            body { font-family: Arial, sans-serif; padding: 20px; }
                            .report-section { margin: 20px 0; }
                            .stat-item { margin: 10px 0; }
                        </style>
                    </head>
                    <body>
                        <h1>TypingMind Analysis Report</h1>
                        <div class="report-section">
                            <h2>Statistics</h2>
                            <div class="stat-item">Total Tokens: ${report.statistics.totalTokens}</div>
                            <div class="stat-item">Chat Count: ${report.statistics.chatCount}</div>
                            <div class="stat-item">Time Spent: ${report.statistics.timeSpent} minutes</div>
                        </div>
                    </body>
                </html>
            `);
        }

        async performAnalysis() {
            // Implement detailed chat analysis
            return {};
        }

        loadSettings() {
            const savedSettings = localStorage.getItem('advanced-assistant-settings');
            if (savedSettings) {
                this.settings = JSON.parse(savedSettings);
            }
        }

        saveSettings() {
            localStorage.setItem('advanced-assistant-settings', 
                JSON.stringify(this.settings));
        }
    }

    // Initialize the assistant
    window.advancedAssistant = new AdvancedAssistant();
})();
