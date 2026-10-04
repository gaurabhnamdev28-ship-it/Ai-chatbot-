#<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>School AI Assistant - Student Helpdesk</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts: Inter & Outfit -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Outfit:wght@500;600;700&display=swap" rel="stylesheet">
  <!-- Lucide Icons -->
  <script src="https://unpkg.com/lucide@latest"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            display: ['Outfit', 'sans-serif']
          },
          colors: {
            brand: {
              50: '#eef2ff',
              100: '#e0e7ff',
              200: '#c7d2fe',
              500: '#6366f1',
              600: '#4f46e5',
              700: '#4338ca',
              900: '#312e81'
            }
          }
        }
      }
    }
  </script>
  <style>
    /* Custom scrollbar */
    #chat-box::-webkit-scrollbar {
      width: 6px;
    }
    #chat-box::-webkit-scrollbar-track {
      background: transparent;
    }
    #chat-box::-webkit-scrollbar-thumb {
      background: #cbd5e1;
      border-radius: 9999px;
    }
    #chat-box::-webkit-scrollbar-thumb:hover {
      background: #94a3b8;
    }

    @keyframes pulse-dot {
      0%, 100% { opacity: 0.3; transform: scale(0.8); }
      50% { opacity: 1; transform: scale(1.1); }
    }
    .animate-dot-1 { animation: pulse-dot 1.2s infinite ease-in-out; }
    .animate-dot-2 { animation: pulse-dot 1.2s infinite ease-in-out 0.2s; }
    .animate-dot-3 { animation: pulse-dot 1.2s infinite ease-in-out 0.4s; }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 antialiased min-h-screen flex flex-col font-sans selection:bg-indigo-100 selection:text-indigo-800">

  <!-- Top App Navigation / School Header -->
  <header class="bg-white/90 backdrop-blur-md border-b border-slate-200/80 sticky top-0 z-30 shadow-xs">
    <div class="max-w-6xl mx-auto px-4 sm:px-6 h-18 flex items-center justify-between">
      
      <!-- Brand & School Info -->
      <div class="flex items-center gap-3.5">
        <div class="w-11 h-11 rounded-xl bg-gradient-to-tr from-indigo-600 via-indigo-500 to-sky-400 flex items-center justify-center text-white shadow-md shadow-indigo-500/20">
          <i data-lucide="graduation-cap" class="w-6 h-6"></i>
        </div>
        <div>
          <div class="flex items-center gap-2">
            <h1 class="font-display font-bold text-lg text-slate-900 tracking-tight">School AI Assistant</h1>
            <span class="inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-full text-xs font-semibold bg-emerald-50 text-emerald-700 border border-emerald-200">
              <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
              Online
            </span>
          </div>
          <p class="text-xs text-slate-500 font-medium">Vidya Mandir High School • 24/7 Student Academic Helpdesk</p>
        </div>
      </div>

      <!-- Settings & Controls -->
      <div class="flex items-center gap-3">
        <!-- API Key Status / Config Button -->
        <button id="api-config-btn" onclick="openApiKeyModal()" class="flex items-center gap-2 px-3.5 py-1.5 rounded-lg border border-slate-200 hover:border-indigo-300 bg-slate-50 hover:bg-indigo-50/50 text-xs font-medium text-slate-700 hover:text-indigo-700 transition">
          <i data-lucide="key" class="w-3.5 h-3.5 text-indigo-600"></i>
          <span id="api-btn-text">Groq API Key</span>
          <span id="api-status-indicator" class="w-2 h-2 rounded-full bg-amber-400" title="API Key not configured in browser session"></span>
        </button>

        <!-- Clear Chat -->
        <button onclick="clearChat()" title="Clear conversation" class="p-2 text-slate-500 hover:text-rose-600 hover:bg-rose-50 rounded-lg transition border border-transparent hover:border-rose-100">
          <i data-lucide="trash-2" class="w-4 h-4"></i>
        </button>
      </div>

    </div>
  </header>

  <!-- Main Chat Workspace -->
  <main class="flex-1 max-w-5xl w-full mx-auto p-4 sm:p-6 flex flex-col justify-between">
    
    <!-- Chat Card Container -->
    <div class="bg-white rounded-2xl border border-slate-200 shadow-sm flex flex-col flex-1 h-[78vh] overflow-hidden">
      
      <!-- Sub-header Banner with Quick Info & Subject Badges -->
      <div class="px-5 py-3 border-b border-slate-100 bg-slate-50/60 flex flex-wrap items-center justify-between gap-2 text-xs text-slate-600">
        <div class="flex items-center gap-2">
          <i data-lucide="sparkles" class="w-4 h-4 text-indigo-600"></i>
          <span>Model: <strong class="font-semibold text-slate-800">llama-3.1-8b-instant</strong> via Groq</span>
        </div>
        <div class="hidden sm:flex items-center gap-1.5 text-slate-500">
          <span>Supported topics:</span>
          <span class="px-2 py-0.5 bg-white rounded border border-slate-200 text-slate-700 font-medium">Math</span>
          <span class="px-2 py-0.5 bg-white rounded border border-slate-200 text-slate-700 font-medium">Science</span>
          <span class="px-2 py-0.5 bg-white rounded border border-slate-200 text-slate-700 font-medium">English</span>
          <span class="px-2 py-0.5 bg-white rounded border border-slate-200 text-slate-700 font-medium">Social Studies</span>
        </div>
      </div>

      <!-- Messages Scroll Box -->
      <div id="chat-box" class="flex-1 p-4 sm:p-6 overflow-y-auto space-y-4 bg-gradient-to-b from-white to-slate-50/40">
        
        <!-- Welcome Initial Message -->
        <div class="flex items-start gap-3.5 max-w-3xl">
          <div class="w-8 h-8 rounded-full bg-indigo-600 text-white flex items-center justify-center shrink-0 shadow-sm">
            <i data-lucide="bot" class="w-4 h-4"></i>
          </div>
          <div class="space-y-1.5">
            <div class="flex items-center gap-2">
              <span class="text-xs font-semibold text-slate-800">School Assistant</span>
              <span class="text-[10px] text-slate-400">Just now</span>
            </div>
            <div class="bg-white border border-slate-200/90 rounded-2xl rounded-tl-sm p-4 text-sm text-slate-700 shadow-xs leading-relaxed">
              <p class="font-medium text-slate-900 mb-1">Namaste! 👋 Welcome to School AI Assistant.</p>
              <p>Main aapka digital study companion hoon. Aap mujhse kisi bhi subject ke questions, homework doubts, science concepts, ya grammar tips pooch sakte hain. Main saral aur step-by-step tareeqe se samjhaunga!</p>
              
              <!-- Quick Prompt chips -->
              <div class="mt-3.5 pt-3 border-t border-slate-100 flex flex-wrap gap-2">
                <button onclick="sendQuickPrompt('Pythagoras Theorem kya hai with practical example?')" class="text-xs px-3 py-1.5 rounded-lg bg-indigo-50 hover:bg-indigo-100 text-indigo-700 font-medium transition text-left flex items-center gap-1.5">
                  <i data-lucide="calculator" class="w-3.5 h-3.5"></i> Pythagoras Theorem explain karo
                </button>
                <button onclick="sendQuickPrompt('Photosynthesis ki process simple steps mein samjhaiye.')" class="text-xs px-3 py-1.5 rounded-lg bg-emerald-50 hover:bg-emerald-100 text-emerald-700 font-medium transition text-left flex items-center gap-1.5">
                  <i data-lucide="leaf" class="w-3.5 h-3.5"></i> Photosynthesis in simple words
                </button>
                <button onclick="sendQuickPrompt('Write a formal leave application to the principal for 2 days.')" class="text-xs px-3 py-1.5 rounded-lg bg-sky-50 hover:bg-sky-100 text-sky-700 font-medium transition text-left flex items-center gap-1.5">
                  <i data-lucide="file-text" class="w-3.5 h-3.5"></i> Leave Application draft
                </button>
              </div>
            </div>
          </div>
        </div>

      </div>

      <!-- Typing / Loader Indicator (Hidden by default) -->
      <div id="typing-indicator" class="hidden px-6 py-2.5 bg-slate-50/60 border-t border-slate-100 items-center gap-3">
        <div class="w-6 h-6 rounded-full bg-indigo-100 text-indigo-600 flex items-center justify-center shrink-0">
          <i data-lucide="bot" class="w-3.5 h-3.5"></i>
        </div>
        <div class="flex items-center gap-1.5">
          <span class="text-xs font-medium text-slate-500">AI soch raha hai...</span>
          <div class="flex items-center gap-1 ml-1">
            <span class="w-1.5 h-1.5 bg-indigo-500 rounded-full animate-dot-1"></span>
            <span class="w-1.5 h-1.5 bg-indigo-500 rounded-full animate-dot-2"></span>
            <span class="w-1.5 h-1.5 bg-indigo-500 rounded-full animate-dot-3"></span>
          </div>
        </div>
      </div>

      <!-- Input Bar Section -->
      <div class="p-3.5 sm:p-4 bg-white border-t border-slate-200">
        <form id="chat-form" onsubmit="handleChatSubmit(event)" class="relative flex items-center gap-2">
          
          <div class="relative flex-1">
            <input 
              type="text" 
              id="user-input" 
              placeholder="Apna sawaal ya doubt yahan type karein (e.g. Newton's 3rd Law samjhao)..."
              class="w-full pl-4 pr-10 py-3.5 rounded-xl border border-slate-200 focus:outline-none focus:ring-2 focus:ring-indigo-500/20 focus:border-indigo-600 text-sm text-slate-800 placeholder-slate-400 bg-slate-50/40 focus:bg-white transition"
              autocomplete="off"
              required
            />
            <button 
              type="button" 
              onclick="document.getElementById('user-input').value=''" 
              class="absolute right-3 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600 p-1"
              title="Clear input"
            >
              <i data-lucide="x" class="w-4 h-4"></i>
            </button>
          </div>

          <!-- Submit Button -->
          <button 
            type="submit" 
            id="send-btn"
            class="h-12 px-5 bg-indigo-600 hover:bg-indigo-700 active:bg-indigo-800 text-white rounded-xl font-medium text-sm flex items-center justify-center gap-2 transition shadow-sm shadow-indigo-600/20 disabled:opacity-50 disabled:cursor-not-allowed cursor-pointer"
          >
            <span>Send</span>
            <i data-lucide="send" class="w-4 h-4"></i>
          </button>
        </form>

        <div class="mt-2.5 flex items-center justify-between text-[11px] text-slate-400 px-1">
          <span>Tip: Press <kbd class="px-1.5 py-0.5 bg-slate-100 border border-slate-200 rounded text-slate-600 font-mono">Enter</kbd> to send message</span>
          <span>School Safe Mode: Enabled</span>
        </div>
      </div>

    </div>

  </main>

  <!-- Modal for Setting Groq API Key -->
  <div id="api-modal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-xs z-50 hidden flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-xl border border-slate-100">
      <div class="flex items-center justify-between mb-4">
        <div class="flex items-center gap-2 text-indigo-600 font-display font-bold text-lg">
          <i data-lucide="key" class="w-5 h-5"></i>
          <h2>Configure Groq API Key</h2>
        </div>
        <button onclick="closeApiKeyModal()" class="text-slate-400 hover:text-slate-600 p-1 rounded-lg">
          <i data-lucide="x" class="w-5 h-5"></i>
        </button>
      </div>

      <p class="text-xs text-slate-600 leading-relaxed mb-4">
        Direct browser execution ke liye aap apni Groq API Key yahan enter kar sakte hain (ye keval aapke browser ke <code class="bg-slate-100 text-indigo-600 px-1 rounded">localStorage</code> mein store hogi). Agar aapne code mein hardcode ki hui hai, toh wahi default use hogi.
      </p>

      <div class="space-y-3">
        <div>
          <label class="block text-xs font-semibold text-slate-700 mb-1">Groq API Key (starts with gsk_...)</label>
          <input 
            type="password" 
            id="modal-api-key-input" 
            placeholder="gsk_xxxxxxxxxxxxxxxxxxxxxxxx"
            class="w-full px-3.5 py-2.5 rounded-lg border border-slate-200 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500/20 focus:border-indigo-600"
          />
        </div>

        <div class="flex items-center justify-between pt-2">
          <a href="https://console.groq.com/keys" target="_blank" rel="noopener noreferrer" class="text-xs text-indigo-600 hover:underline flex items-center gap-1 font-medium">
            Get free key from Groq Console <i data-lucide="external-link" class="w-3 h-3"></i>
          </a>
          <button onclick="saveApiKeyFromModal()" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-lg text-xs font-medium transition">
            Save & Connect
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- JavaScript Application Logic -->
  <script>
    /* =========================================================================
       1. GROQ CONFIGURATION & CONSTANTS
       ========================================================================= */
    
    // Aap chahein toh yahan apni default API key hardcode kar sakte hain:
    // e.g. const HARDCODED_GROQ_API_KEY = "gsk_your_key_here";
    const HARDCODED_GROQ_API_KEY = ""; 

    // Groq lightweight fast model (Llama 3.1 8B Instant)
    const GROQ_MODEL = "llama-3.1-8b-instant";
    const GROQ_API_ENDPOINT = "https://api.groq.com/openai/v1/chat/completions";

    // System prompt tailored for School Student Assistant
    const SYSTEM_PROMPT = `You are a warm, encouraging, and highly knowledgeable 'School AI Assistant' for school students.
Your mission is to help students understand school concepts, solve academic doubts, and learn step-by-step.
Follow these guidelines:
1. Tone: Friendly, polite, motivating, and student-safe.
2. Structure: Use simple bullet points, clear headings, and real-life examples so complex topics (Math, Science, History, Grammar) feel easy.
3. Language: Respond in the student's preferred language (English, Hindi, or Hinglish as asked).
4. Homework policy: Do not just give blind answers for critical assignments; guide the student so they learn the method.
5. Keep explanations age-appropriate, encouraging curiosity and critical thinking.`;

    // Conversation history for context continuity
    let conversationHistory = [
      { role: "system", content: SYSTEM_PROMPT }
    ];

    /* =========================================================================
       2. API KEY MANAGEMENT (LocalStorage + Hardcoded fallback)
       ========================================================================= */
    function getActiveApiKey() {
      const stored = localStorage.getItem("groq_school_api_key");
      if (stored && stored.trim() !== "") {
        return stored.trim();
      }
      return HARDCODED_GROQ_API_KEY.trim();
    }

    function updateApiStatusIndicator() {
      const key = getActiveApiKey();
      const dot = document.getElementById("api-status-indicator");
      const text = document.getElementById("api-btn-text");
      if (key && key.length > 5) {
        dot.className = "w-2 h-2 rounded-full bg-emerald-500";
        dot.title = "Groq API Key is active";
        text.innerText = "Key Configured";
      } else {
        dot.className = "w-2 h-2 rounded-full bg-amber-400";
        dot.title = "No API Key found. Click to enter key.";
        text.innerText = "Set Groq Key";
      }
    }

    function openApiKeyModal() {
      const modal = document.getElementById("api-modal");
      const input = document.getElementById("modal-api-key-input");
      input.value = getActiveApiKey();
      modal.classList.remove("hidden");
    }

    function closeApiKeyModal() {
      document.getElementById("api-modal").classList.add("hidden");
    }

    function saveApiKeyFromModal() {
      const inputVal = document.getElementById("modal-api-key-input").value.trim();
      if (inputVal) {
        localStorage.setItem("groq_school_api_key", inputVal);
      } else {
        localStorage.removeItem("groq_school_api_key");
      }
      closeApiKeyModal();
      updateApiStatusIndicator();
    }

    /* =========================================================================
       3. CHAT INTERFACE & RENDERING FUNCTIONS
       ========================================================================= */
    const chatBox = document.getElementById("chat-box");
    const typingIndicator = document.getElementById("typing-indicator");
    const sendBtn = document.getElementById("send-btn");
    const userInput = document.getElementById("user-input");

    function scrollToBottom() {
      chatBox.scrollTop = chatBox.scrollHeight;
    }

    function getCurrentTime() {
      const now = new Date();
      return now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
    }

    function appendUserMessage(text) {
      const messageDiv = document.createElement("div");
      messageDiv.className = "flex items-start justify-end gap-3.5 max-w-3xl ml-auto";
      messageDiv.innerHTML = `
        <div class="space-y-1.5 text-right">
          <div class="flex items-center justify-end gap-2">
            <span class="text-[10px] text-slate-400">${getCurrentTime()}</span>
            <span class="text-xs font-semibold text-slate-800">You</span>
          </div>
          <div class="bg-indigo-600 text-white rounded-2xl rounded-tr-sm px-4 py-3 text-sm shadow-xs leading-relaxed text-left inline-block max-w-lg break-words">
            ${escapeHtml(text)}
          </div>
        </div>
        <div class="w-8 h-8 rounded-full bg-slate-200 text-slate-700 flex items-center justify-center shrink-0 font-bold text-xs">
          ME
        </div>
      `;
      chatBox.appendChild(messageDiv);
      scrollToBottom();
    }

    function appendBotMessage(text, isError = false) {
      const messageDiv = document.createElement("div");
      messageDiv.className = "flex items-start gap-3.5 max-w-3xl";
      
      const formattedContent = isError 
        ? `<div class="p-3 bg-rose-50 border border-rose-200 text-rose-700 rounded-xl text-xs flex items-start gap-2">
             <i data-lucide="alert-circle" class="w-4 h-4 shrink-0 text-rose-500 mt-0.5"></i>
             <div>
               <strong>Kuch gadbad hui:</strong> ${escapeHtml(text)}
               <p class="mt-1 text-slate-600">Kripya top-right me <strong>'Set Groq Key'</strong> button click karke valid Groq API Key check karein.</p>
             </div>
           </div>`
        : `<div class="prose prose-sm max-w-none text-slate-700 leading-relaxed space-y-2">
             ${formatBotReply(text)}
           </div>`;

      messageDiv.innerHTML = `
        <div class="w-8 h-8 rounded-full ${isError ? 'bg-rose-600' : 'bg-indigo-600'} text-white flex items-center justify-center shrink-0 shadow-sm">
          <i data-lucide="${isError ? 'alert-triangle' : 'bot'}" class="w-4 h-4"></i>
        </div>
        <div class="space-y-1.5 flex-1">
          <div class="flex items-center gap-2">
            <span class="text-xs font-semibold text-slate-800">School Assistant</span>
            <span class="text-[10px] text-slate-400">${getCurrentTime()}</span>
          </div>
          <div class="bg-white border ${isError ? 'border-rose-200' : 'border-slate-200/90'} rounded-2xl rounded-tl-sm p-4 text-sm shadow-xs">
            ${formattedContent}
          </div>
        </div>
      `;
      chatBox.appendChild(messageDiv);
      lucide.createIcons();
      scrollToBottom();
    }

    // Helper: basic formatting for bold, lists and linebreaks
    function formatBotReply(raw) {
      let safe = escapeHtml(raw);
      // Convert markdown-like bold **text** to <strong>
      safe = safe.replace(/\*\*(.*?)\*\*/g, '<strong class="text-slate-900 font-semibold">$1</strong>');
      // Convert `code` to formatted badge
      safe = safe.replace(/`([^`]+)`/g, '<code class="bg-slate-100 text-indigo-700 px-1 py-0.5 rounded text-xs font-mono">$1</code>');
      // Convert newlines to breaks
      safe = safe.replace(/\n\n/g, '</p><p class="mt-2">').replace(/\n/g, '<br/>');
      return `<p>${safe}</p>`;
    }

    function escapeHtml(string) {
      const div = document.createElement('div');
      div.textContent = string;
      return div.innerHTML;
    }

    function showLoading(show) {
      if (show) {
        typingIndicator.classList.remove("hidden");
        typingIndicator.classList.add("flex");
        sendBtn.disabled = true;
      } else {
        typingIndicator.classList.add("hidden");
        typingIndicator.classList.remove("flex");
        sendBtn.disabled = false;
      }
      scrollToBottom();
    }

    function clearChat() {
      if (confirm("Kya aap conversation clear karna chahte hain?")) {
        conversationHistory = [{ role: "system", content: SYSTEM_PROMPT }];
        chatBox.innerHTML = "";
        appendBotMessage("Chat history reset ho gayi hai. Aap apna naya sawaal pooch sakte hain! 📚");
      }
    }

    function sendQuickPrompt(promptText) {
      userInput.value = promptText;
      handleChatSubmit(new Event('submit'));
    }

    /* =========================================================================
       4. GROQ API CALL (fetch implementation)
       ========================================================================= */
    async function handleChatSubmit(e) {
      if (e) e.preventDefault();
      
      const query = userInput.value.trim();
      if (!query) return;

      const apiKey = getActiveApiKey();
      
      // Check if API key is present
      if (!apiKey) {
        appendUserMessage(query);
        userInput.value = "";
        openApiKeyModal();
        appendBotMessage("Groq API Key set nahi hai! Kripya pop-up modal mein apni valid Groq API Key dalein taaki AI response de sake.", true);
        return;
      }

      // Add user message to UI & history
      appendUserMessage(query);
      userInput.value = "";
      conversationHistory.push({ role: "user", content: query });

      showLoading(true);

      try {
        const response = await fetch(GROQ_API_ENDPOINT, {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            "Authorization": `Bearer ${apiKey}`
          },
          body: JSON.stringify({
            model: GROQ_MODEL,
            messages: conversationHistory,
            temperature: 0.6,
            max_tokens: 1024
          })
        });

        if (!response.ok) {
          const errData = await response.json().catch(() => ({}));
          const errMsg = errData.error?.message || `HTTP Error ${response.status}: ${response.statusText}`;
          throw new Error(errMsg);
        }

        const data = await response.json();
        const replyText = data.choices?.[0]?.message?.content || "Mujhe kshama karein, koi reply prapt nahi ho saka.";

        // Append bot reply to UI & history
        conversationHistory.push({ role: "assistant", content: replyText });
        appendBotMessage(replyText);

      } catch (error) {
        console.error("Groq API Error:", error);
        appendBotMessage(error.message || "Network error. Kripya apna internet connection aur API key check karein.", true);
      } finally {
        showLoading(false);
      }
    }

    // Initialize state & icons
    window.addEventListener("DOMContentLoaded", () => {
      lucide.createIcons();
      updateApiStatusIndicator();
    });
  </script>
</body>
</html> Ai-chatbot-
