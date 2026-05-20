require("dotenv").config();
const express = require("express");
const cors = require("cors");
const axios = require("axios");
const path = require("path");
const app = express();
app.use(cors());
app.use(express.json());
app.use(express.static(path.join(__dirname, "public")));
const GEMINI_API_KEY = process.env.GEMINI_API_KEY;
app.post("/chat", async (req, res) => {
try {
const userMessage = req.body.message;
2
const prompt = `
You are Moner Bondhu, a supportive AI mental health assistant.
Rules:
- Be kind and supportive.
- Reply in Bengali if user uses Bengali.
- Never give dangerous advice.
- Encourage positivity.
- Keep answers human friendly.
- If user seems suicidal, encourage contacting family or professionals.
User Message:
${userMessage}
`;
const response = await axios.post(
`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-
flash:generateContent?key=${GEMINI_API_KEY}`,
{
contents: [
{
parts: [
{
text: prompt,
},
],
},
],
}
);
const reply =
response.data.candidates[0].content.parts[0].text;
res.json({ reply });
} catch (error) {
console.log(error.message);
res.json({
reply:
"আ߯মি এখন একট সমিস্যায় আ߯ছি ߯কছিক্ষণ পߵরে আবারে ⢝চেষ্টা কߵরো।",
});
}
});
app.listen(process.env.PORT, () => {
3
console.log(`Server running on port ${process.env.PORT}`);
});
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Moner Bondhu</title>
<link rel="stylesheet" href="style.css" />
</head>
<body>
<div class="container">
<div class="top-bar">
<h1> Moner Bondhu</h1>
<button id="darkBtn"> </button>
</div>
<div id="chatBox" class="chat-box"></div>
<div class="input-area">
<input
type="text"
id="message"
placeholder="Type your feelings..."
/>
<button onclick="sendMessage()">Send</button>
</div>
<div class="emergency">
 If you are in danger or having self-harm thoughts,
 please contact trusted family members or professional help.
</div>
</div>
<script src="script.js"></script>
4
</body>
</html>
* {
margin: 0;
padding: 0;
box-sizing: border-box;
font-family: Arial, sans-serif;
}
body {
background: #f0f4ff;
height: 100vh;
display: flex;
justify-content: center;
align-items: center;
transition: 0.3s;
}
.container {
width: 95%;
max-width: 500px;
background: white;
border-radius: 20px;
overflow: hidden;
box-shadow: 0 10px 30px rgba(0,0,0,0.1);
}
.top-bar {
background: #4f46e5;
color: white;
padding: 15px;
display: flex;
justify-content: space-between;
align-items: center;
}
.top-bar button {
background: white;
border: none;
padding: 8px 12px;
border-radius: 10px;
5
cursor: pointer;
}
.chat-box {
height: 500px;
overflow-y: auto;
padding: 15px;
display: flex;
flex-direction: column;
gap: 10px;
}
.message {
padding: 12px;
border-radius: 15px;
max-width: 80%;
line-height: 1.5;
}
.user {
background: #4f46e5;
color: white;
align-self: flex-end;
}
.bot {
background: #e5e7eb;
color: black;
align-self: flex-start;
}
.input-area {
display: flex;
border-top: 1px solid #ddd;
}
.input-area input {
flex: 1;
padding: 15px;
border: none;
outline: none;
}
.input-area button {
padding: 15px;
border: none;
background: #4f46e5;
color: white;
6
cursor: pointer;
}
.emergency {
padding: 10px;
background: #fee2e2;
color: #991b1b;
font-size: 13px;
text-align: center;
}
.dark {
background: #111827;
}
.dark .container {
background: #1f2937;
}
.dark .bot {
background: #374151;
color: white;
}
.dark input {
background: #111827;
color: white;
}
Step 9 — public/script.js
const chatBox = document.getElementById("chatBox");
const input = document.getElementById("message");
const darkBtn = document.getElementById("darkBtn");
function addMessage(text, className) {
const div = document.createElement("div");
div.classList.add("message", className);
div.innerText = text;
chatBox.appendChild(div);
chatBox.scrollTop = chatBox.scrollHeight;
}
7
async function sendMessage() {
const message = input.value.trim();
if (!message) return;
addMessage(message, "user");
input.value = "";
const typing = document.createElement("div");
typing.classList.add("message", "bot");
typing.innerText = "Typing...";
chatBox.appendChild(typing);
try {
const response = await fetch("/chat", {
method: "POST",
headers: {
"Content-Type": "application/json",
},
body: JSON.stringify({ message }),
});
const data = await response.json();
typing.remove();
addMessage(data.reply, "bot");
saveChat();
} catch (error) {
typing.remove();
addMessage("Something went wrong ", "bot");
}
}
input.addEventListener("keypress", (e) => {
if (e.key === "Enter") {
sendMessage();
}
});
function saveChat() {
localStorage.setItem("chatData", chatBox.innerHTML);
}
8
function loadChat() {
const data = localStorage.getItem("chatData");
if (data) {
chatBox.innerHTML = data;
}
}
loadChat();
// Dark Mode
darkBtn.addEventListener("click", () => {
document.body.classList.toggle("dark");
});
