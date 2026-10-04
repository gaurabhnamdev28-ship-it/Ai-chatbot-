<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>School AI Assistant</title>
    <!-- Tailwind CSS CDN for styling -->
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-slate-50 flex flex-col h-screen font-sans">

    <!-- Header Section -->
    <header class="bg-white border-b border-slate-200 px-4 py-3 flex items-center justify-between shadow-sm">
        <div>
            <div class="flex items-center gap-2">
                <div class="bg-indigo-600 text-white p-2 rounded-lg font-bold text-sm">🤖</div>
                <h1 class="font-bold text-slate-800 text-lg">School AI Assistant</h1>
                <span class="bg-emerald-100 text-emerald-700 text-xs px-2 py-0.5 rounded-full font-medium flex items-center gap-1">
                    <span class="w-1.5 h-1.5 bg-emerald-500 rounded-full animate-pulse"></span> Online
                </span>
            </div>
            <p class="text-xs text-slate-500 mt-0.5">Vidya Mandir High School • 24/7 Student Academic Helpdesk</p>
        </div>
        <div class="flex items-center gap-2">
            <button onclick="openKeyModal()" id="keyStatusBtn" class="text-xs bg-indigo-50 text-indigo-600 border border-indigo-200 px-3 py-1.5 rounded-lg font-medium hover:bg-indigo-100 transition">
                🔑 Set Groq Key
            </button>
        </div>
    </header>

    <!-- Chat Container -->
    <main id="chatContainer" class="flex-1 overflow-y-auto p-4 space-y-4 max-w-2xl w-full mx-auto">
        <!-- Welcome Message -->
        <div class="flex items-start gap-3">
            <div class="bg-indigo-600 text-white rounded-full w-8 h-8 flex items-center justify-center shrink-0 font-bold text-sm">AI</div>
            <div class="bg-white border border-slate-200 rounded-2xl p-4 text-slate-700 text-sm shadow-sm max-w-[80%]">
                <p>Namaste! Main aapka School AI Assistant hoon. Aaj aapko padhai ya kisi project mein kya madad chahiye?</p>
            </div>
        </div>
    </main>

    <!-- Input Footer -->
    <footer class="bg-white border-t border-slate-200 p-3 shadow-lg">
        <div class="max-w-2xl mx-auto flex items-center gap-2">
            <input type="text" id="userInput" placeholder="Apna sawaal ya doubt yahan type karein..." class="flex-1 border border-slate-300 rounded-xl px-4 py-2.5 text-sm focus:outline-none focus:border-indigo-500" onkeypress="handleKeyPress(event)">
            <button onclick="sendMessage()" class="bg-indigo-600 text-white px-5 py-2.5 rounded-xl text-sm font-medium hover:bg-indigo-700 transition flex items-center gap-1.5">
                Send ➔
            </button>
        </div>
        <div class="max-w-2xl mx-auto flex justify-between items-center mt-2 px-1">
            <span class="text-[11px] text-slate-400">Tip: Press <b>Enter</b> to send message</span>
            <span class="text-[11px] text-slate-400">School Safe Mode: Enabled</span>
        </div>
    </footer>

    <!-- API Key Modal -->
    <div id="keyModal" class="fixed inset-0 bg-black/50 backdrop-blur-sm hidden items-center justify-center p-4 z-50">
        <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-2xl space-y-4">
            <h3 class="text-lg font-bold text-slate-800">Groq API Key Darj Karein</h3>
            <p class="text-xs text-slate-500">Apni Groq API Key yahan enter karein taaki chatbot kaam kar sake. Yeh key aapke browser mein hi safe rahegi.</p>
            <input type="password" id="apiKeyInput" placeholder="gsk_..." class="w-full border border-slate-300 rounded-xl px-4 py-2.5 text-sm focus:outline-none focus:border-indigo-500">
            <div class="flex justify-end gap-2 pt-2">
                <button onclick="closeKeyModal()" class="px-4 py-2 text-xs font-medium text-slate-600 hover:bg-slate-100 rounded-lg">Cancel</button>
                <button onclick="saveApiKey()" class="px-4 py-2 text-xs font-medium bg-indigo-600 text-white rounded-lg hover:bg-indigo-700">Save Key</button>
            </div>
        </div>
    </div>

    <!-- JavaScript Logic -->
    <script>
        // Check if API Key exists in localStorage on load
        window.onload = function() {
            const savedKey = localStorage.getItem("groq_api_key");
            if (savedKey) {
                document.getElementById("keyStatusBtn").innerText = "✅ Key Configured";
                document.getElementById("keyStatusBtn").className = "text-xs bg-emerald-50 text-emerald-600 border border-emerald-200 px-3 py-1.5 rounded-lg font-medium";
            }
        }

        function openKeyModal() {
            document.getElementById("keyModal").style.display = "flex";
            const savedKey = localStorage.getItem("groq_api_key");
            if (savedKey) {
                document.getElementById("apiKeyInput").value = savedKey;
            }
        }

        function closeKeyModal() {
            document.getElementById("keyModal").style.display = "none";
        }

        function saveApiKey() {
            const key = document.getElementById("apiKeyInput").value.trim();
            if (key) {
                localStorage.setItem("groq_api_key", key);
                document.getElementById("keyStatusBtn").innerText = "✅ Key Configured";
                document.getElementById("keyStatusBtn").className = "text-xs bg-emerald-50 text-emerald-600 border border-emerald-200 px-3 py-1.5 rounded-lg font-medium";
                closeKeyModal();
                alert("API Key successfully save ho gayi hai!");
            } else {
                alert("Kripya valid API key enter karein.");
            }
        }

        function handleKeyPress(event) {
            if (event.key === "Enter") {
                sendMessage();
            }
        }

        async function sendMessage() {
            const inputField = document.getElementById("userInput");
            const messageText = inputField.value.trim();
            const apiKey = localStorage.getItem("groq_api_key");

            if (!messageText) return;

            if (!apiKey) {
                alert("Pehle apni Groq API Key set karein (Top-right button par click karke).");
                openKeyModal();
                return;
            }

            // Append User Message
            appendMessage(messageText, "user");
            inputField.value = "";

            // Show Loading Indicator
            const loadingId = appendMessage("Thinking...", "ai", true);

            try {
                const response = await fetch("https://api.groq.com/openai/v1/chat/completions", {
                    method: "POST",
                    headers: {
                        "Authorization": `Bearer ${apiKey}`,
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({
                        model: "llama-3.3-70b-versatile",
                        messages: [
                            {
                                role: "system",
                                content: "Aap ek helpful school assistant hain jo students ke doubts solve karta hai aur unki padhai mein madad karta hai. Hamesha bhasha simple aur saaf rakhein."
                            },
                            {
                                role: "user",
                                content: messageText
                            }
                        ]
                    })
                });

                const data = await response.json();
                
                // Remove Loading Indicator
                document.getElementById(loadingId).remove();

                if (response.ok && data.choices && data.choices.length > 0) {
                    const aiReply = data.choices[0].message.content;
                    appendMessage(aiReply, "ai");
                } else {
                    const errorMsg = data.error ? data.error.message : "Kuch gadbad ho gayi.";
                    appendMessage("⚠️️ Error: " + errorMsg, "ai");
                }

            } catch (error) {
                document.getElementById(loadingId).remove();
                appendMessage("⚠️ Network Error: API request fail ho gayi.", "ai");
            }
        }

        function appendMessage(text, sender, isLoading = false) {
            const chatContainer = document.getElementById("chatContainer");
            const messageId = "msg-" + Date.now() + Math.random();
            
            const messageDiv = document.createElement("div");
            messageDiv.id = messageId;
            messageDiv.className = `flex items-start gap-3 ${sender === 'user' ? 'flex-row-reverse' : ''}`;

            const avatar = document.createElement("div");
            avatar.className = `rounded-full w-8 h-8 flex items-center justify-center shrink-0 font-bold text-sm ${sender === 'user' ? 'bg-slate-700 text-white' : 'bg-indigo-600 text-white'}`;
            avatar.innerText = sender === 'user' ? 'YOU' : 'AI';

            const bubble = document.createElement("div");
            bubble.className = `border rounded-2xl p-4 text-sm shadow-sm max-w-[80%] ${
                sender === 'user' 
                    ? 'bg-indigo-600 text-white border-indigo-600' 
                    : 'bg-white border-slate-200 text-slate-700'
            } ${isLoading ? 'italic text-slate-400' : ''}`;
            
            bubble.innerText = text;

            messageDiv.appendChild(avatar);
            messageDiv.appendChild(bubble);
            chatContainer.appendChild(messageDiv);

            // Auto scroll to bottom
            chatContainer.scrollTop = chatContainer.scrollHeight;

            return messageId;
        }
    </script>
</body>
</html>
