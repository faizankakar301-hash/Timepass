<!DOCTYPE html>  
<html lang="en">  
<head>  
<meta charset="UTF-8">  
<title>TimePass - Final Stable</title>  
  
<style>  
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Bebas+Neue&display=swap');  
  
body{  
  margin:0;  
  padding:20px;  
  font-family:'Orbitron',sans-serif;  
  font-size:18px;  
  color:#fff;  
  display:flex;  
  flex-direction:column;  
  align-items:center;  
}  
  
body::before{  
  content:"";  
  position:fixed;  
  top:-10%; left:-10%;  
  width:120%; height:120%;  
  background:url("https://i.pinimg.com/736x/75/bb/d5/75bbd582df790f1c6da49efab18fb2e6.jpg") center/cover no-repeat;  
  z-index:-2;  
  animation:moveBg 25s infinite ease-in-out;  
}  
  
@keyframes moveBg{  
  0%{transform:scale(1)}  
  50%{transform:scale(1.05) translateY(-30px)}  
  100%{transform:scale(1)}  
}  
  
body::after{  
  content:"";  
  position:fixed;  
  inset:0;  
  background:rgba(0,0,0,0.55);  
  z-index:-1;  
}  
  
h1{  
  font-family:'Bebas Neue';  
  font-size:3.2rem;  
  text-shadow:2px 2px #dc143c;  
}  
  
.container{  
  background:#000000d9;  
  padding:25px;  
  border-radius:15px;  
  width:90%;  
  max-width:650px;  
  text-align:center;  
}  
  
input, textarea, select{  
  width:85%;  
  padding:14px;  
  margin:10px;  
  border-radius:10px;  
  border:1px solid #dc143c;  
  background:#111;  
  color:#fff;  
  font-size:1.1rem;  
}  
  
textarea{height:110px;}  
  
.dropdown{position:relative;}  
.dropdown-list{  
  position:absolute;  
  width:85%;  
  background:#111;  
  border:1px solid #dc143c;  
  border-radius:10px;  
  display:none;  
  max-height:200px;  
  overflow:auto;  
}  
.dropdown-list div{  
  padding:12px;  
  cursor:pointer;  
}  
.dropdown-list div:hover{background:#dc143c;}  
  
button{  
  padding:14px 25px;  
  background:#dc143c;  
  border:none;  
  border-radius:10px;  
  color:#fff;  
  font-size:1.1rem;  
  cursor:pointer;  
}  
  
#outputBox{  
  margin-top:15px;  
  padding:15px;  
  background:#111;  
  border:1px solid #dc143c;  
  white-space:pre-line;  
}  
  
.clock-container{  
  margin-top:25px;  
  display:flex;  
  gap:15px;  
}  
.clock{  
  background:#111;  
  padding:15px;  
  border-radius:10px;  
  border:1px solid #dc143c;  
}  
</style>  
</head>  
  
<body>  
  
<h1>TimePass</h1>  
  
<div class="container">  
  
<input id="idInput" placeholder="Enter ID">  
  
<div class="dropdown">  
<input id="dropdownInput" placeholder="Search action..." onclick="toggleDropdown()" onkeyup="filterDropdown()">  
<div class="dropdown-list" id="dropdownList">  
<div onclick="selectOption('approved')">Approved</div>  
<div onclick="selectOption('rejected')">Rejected</div>  
<div onclick="selectOption('pov')">POV</div>  
<div onclick="selectOption('onconsideration')">On Consideration</div>  
<div onclick="selectOption('nothingsus')">Nothing Suspicious</div>  
<div onclick="selectOption('povrequired')">POV Required</div>  
<div onclick="selectOption('finalpov')">Final POV</div>  
</div>  
</div>  
  
<select id="reasonSelect" onchange="setReason()">  
<option value="">Select reason</option>  
<option>Not enough evidence</option>  
<option>POV unavailable</option>  
<option value="Incorrect format||File a new complaint with proper format">  
Incorrect format + File new complaint  
</option>  
</select>  
  
<textarea id="reasonInput" placeholder="Enter reason OR:  
45556 CR  
345 VDM"></textarea>  
  
<div id="outputBox">Select action...</div>  
<button onclick="copyBBCode()">Copy BBCode</button>  
  
</div>  
  
<div class="clock-container">  
<div class="clock">OOC: <span id="oocClock"></span></div>  
<div class="clock">IC: <span id="icClock"></span></div>  
</div>  
  
<script>  
let selectedAction="";  
  
function toggleDropdown(){dropdownList.style.display="block";}  
function selectOption(v){  
  selectedAction=v;  
  dropdownInput.value=v;  
  dropdownList.style.display="none";  
  generateFormat();  
}  
function filterDropdown(){  
  let val=dropdownInput.value.toLowerCase();  
  let items=dropdownList.children;  
  for(let i of items){  
    i.style.display=i.innerText.toLowerCase().includes(val)?"":"none";  
  }  
}  
  
function setReason(){  
  let v=reasonSelect.value;  
  reasonInput.value=v.includes("||")?v.split("||").join("\n"):v;  
  generateFormat();  
}  
  
function parseMulti(text){  
  let arr=[];  
  text.split("\n").forEach(l=>{  
    let m=l.match(/(\d+)\s*(.+)/);  
    if(m) arr.push({id:m[1],reason:m[2]});  
  });  
  return arr;  
}  
  
function generateFormat(){  
  let id=idInput.value.trim();  
  let txt=reasonInput.value.trim();  
  let p=parseMulti(txt);  
  
  if(!selectedAction){  
    outputBox.textContent="⚠️ Select action first";  
    return;  
  }  
  
  if(selectedAction==="approved" && p.length>1){  
    let out="Upon reviewing the evidence, I have decided to Accept this report.\n\n";  
    p.forEach(i=> out+=`ID ${i.id} will receive a punishment for ${i.reason}.\n`);  
    outputBox.textContent=out;  
  }  
  else if(selectedAction==="approved"){  
    outputBox.textContent=`Upon reviewing the evidence, I have decided to Accept this report.\nID ${id} will receive a punishment for ${txt}.`;  
  }  
  else if(selectedAction==="rejected"){  
    outputBox.textContent=`Upon reviewing the evidence, I have decided to Deny this report.\nReason:\n${txt}`;  
  }  
  else if(selectedAction==="pov"){  
    outputBox.textContent=`Requesting ID ${id} to Provide Event POV\nYou have 12 hours to do so`;  
  }  
  else if(selectedAction==="onconsideration"){  
    outputBox.textContent=`On Consideration`;  
  }  
  else if(selectedAction==="nothingsus"){  
    outputBox.textContent=`Upon reviewing the evidence, I have decided to Deny this report.\nNothing Suspicious was observed in Provided Event POV`;  
  }  
  else if(selectedAction==="povrequired"){  
    outputBox.textContent=`Explanation: Thread owner:  
  
Suspicion: POV request required?  
  
Victim POV:  
  
Timestamp:  
  
Thread:  
<@&1121483727445430362>`;  
  }  
  else if(selectedAction==="finalpov"){  
    outputBox.textContent=`Explanation: Thread owner:  
  
Suspicion:  
  
Victim POV:  
  
Killer POV:  
  
Timestamp:  
  
Thread:  
<@&1121483727445430362>`;  
  }  
}  
  
function copyBBCode(){  
  let id=idInput.value.trim();  
  let txt=reasonInput.value.trim();  
  let p=parseMulti(txt);  
  let bb="";  
  
  if(selectedAction==="approved" && p.length>1){  
    bb=`[center]Upon reviewing the evidence, I have decided to [color=green][b]Accept[/b][/color] this report.\n\n`;  
    p.forEach(i=>{  
      bb+=`[b]ID[/b] [b]${i.id}[/b] will receive a punishment for [b]${i.reason}[/b].\n`;  
    });  
    bb+="[/center]";  
  }  
  else if(selectedAction==="approved"){  
    bb=`[center]Upon reviewing the evidence, I have decided to [color=green][b]Accept[/b][/color] this report.\n[b]ID[/b] [b]${id}[/b] will receive a punishment for [b]${txt}[/b].[/center]`;  
  }  
  else if(selectedAction==="rejected"){  
    bb=`[center]Upon reviewing the evidence, I have decided to [color=red][b]Deny[/b][/color] this report.\n[b]Reason:[/b]\n[color=cyan]${txt}[/color][/center]`;  
  }  
  else if(selectedAction==="pov"){  
    bb=`[center]Requesting [b]ID[/b] [b]${id}[/b] to Provide Event POV\nYou have 12 hours to do so[/center]`;  
  }  
  else if(selectedAction==="onconsideration"){  
    bb=`[center][color=skyblue][b]On Consideration[/b][/color][/center]`;  
  }  
  else if(selectedAction==="nothingsus"){  
    bb=`[center]Upon reviewing the evidence, I have decided to [color=red][b]Deny[/b][/color] this report.\nNothing Suspicious was observed in [b]Provided Event POV[/b][/center]`;  
  }  
  else if(selectedAction==="povrequired"){  
    bb=`Explanation: Thread owner:  
  
Suspicion: POV request required?  
  
Victim POV:  
  
Timestamp:  
  
Thread:  
<@&1121483727445430362>`;  
  }  
  else if(selectedAction==="finalpov"){  
    bb=`Explanation: Thread owner:  
  
Suspicion:  
  
Victim POV:  
  
Killer POV:  
  
Timestamp:  
  
Thread:  
<@&1121483727445430362>`;  
  }  
  
  navigator.clipboard.writeText(bb);  
  
  // auto clear  
  idInput.value="";  
  reasonInput.value="";  
  outputBox.textContent="Select action...";  
}  
  
/* REAL TIME FIX */  
idInput.addEventListener("input", generateFormat);  
reasonInput.addEventListener("input", generateFormat);  
  
function updateClocks(){  
  let now=new Date();  
  oocClock.textContent=now.toLocaleTimeString();  
  let ic=new Date(now.getTime()-(4.5*60*60*1000));  
  icClock.textContent=ic.toLocaleTimeString();  
}  
setInterval(updateClocks,1000);  
updateClocks();  
</script>  
  
</body>  
</html>  
