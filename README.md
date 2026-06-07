Responses.json
{
  "hello": "Hello! How can I help you today?",
  "hi": "Hi there! Welcome to AI Chatbot.",
  "machine learning": "Machine Learning is a branch of AI that enables systems to learn from data.",
  "python": "Python is a powerful programming language.",
  "bye": "Goodbye! Have a great day."
}
Chatbot.py
import json

with open("responses.json", "r") as file:
    responses = json.load(file)

def get_response(user_input):
    user_input = user_input.lower()

    for key in responses:
        if key in user_input:
            return responses[key]

    return "Sorry, I don't understand that."
app.py
from flask import Flask, render_template, request, jsonify
from chatbot import get_response

app = Flask(__name__)

@app.route("/")
def home():
    return render_template("index.html")

@app.route("/chat", methods=["POST"])
def chat():
    user_message = request.json["message"]
    response = get_response(user_message)

    return jsonify({
        "response": response
    })

if __name__ == "__main__":
    app.run(debug=True)
index.html
<!DOCTYPE html>
<html>
<head>
<title>AI Chatbot</title>
<link rel="stylesheet"
href="{{ url_for('static', filename='style.css') }}">
</head>

<body>

<div class="chat-container">

<h1>AI Chatbot</h1>

<div id="chat-box"></div>

<div class="input-area">
<input type="text"
id="user-input"
placeholder="Type your message">

<button onclick="sendMessage()">
Send
</button>

</div>
</div>

<script>

function sendMessage(){

let input =
document.getElementById("user-input");

let message = input.value;

if(message===""){
return;
}

let chatBox =
document.getElementById("chat-box");

chatBox.innerHTML +=
"<div class='user-msg'><b>You:</b> "
+ message +
"</div>";

fetch("/chat",{

method:"POST",

headers:{
"Content-Type":"application/json"
},

body:JSON.stringify({
message:message
})

})
.then(response=>response.json())
.then(data=>{

chatBox.innerHTML +=
"<div class='bot-msg'><b>Bot:</b> "
+ data.response +
"</div>";

});

input.value="";
}

</script>

</body>
</html>
Style.css
body{
font-family:Arial;
background:#f4f4f4;
display:flex;
justify-content:center;
align-items:center;
height:100vh;
}

.chat-container{
width:500px;
background:white;
padding:20px;
border-radius:10px;
box-shadow:0 0 10px gray;
}

#chat-box{
height:300px;
border:1px solid #ccc;
overflow-y:auto;
padding:10px;
margin-bottom:10px;
}

.user-msg{
text-align:right;
color:blue;
margin:5px;
}

.bot-msg{
text-align:left;
color:green;
margin:5px;
}

.input-area{
display:flex;
}

input{
flex:1;
padding:10px;
}

button{
padding:10px;
background:#007bff;
color:white;
border:none;
cursor:pointer;
}
