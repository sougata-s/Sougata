<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>আদরিণী MCQ প্র্যাকটিস সেন্টার</title>
  <style>
    body { 
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
      background-color: #f4f7f6; 
      color: #333; 
      margin: 0; 
      padding: 20px; 
    }
    .container { 
      max-width: 700px; 
      background: white; 
      margin: 30px auto; 
      padding: 30px; 
      box-shadow: 0 4px 15px rgba(0,0,0,0.1); 
      border-radius: 10px; 
    }
    h1 { 
      text-align: center; 
      color: #2c3e50; 
      border-bottom: 2px solid #3498db; 
      padding-bottom: 10px; 
      margin-top: 0;
    }
    .quiz-box { 
      margin-top: 20px; 
    }
    .question { 
      font-size: 1.2rem; 
      font-weight: bold; 
      margin-bottom: 15px; 
      color: #2c3e50; 
      line-height: 1.5;
    }
    
    /* --- নতুন মডার্ন বাটন ও হোভার অ্যানিমেশন --- */
    .option-btn { 
      display: block; 
      width: 100%; 
      padding: 12px; 
      margin: 10px 0; 
      background: #ecf0f1; 
      border: 1px solid #bdc3c7; 
      border-radius: 5px; 
      text-align: left; 
      font-size: 1rem; 
      cursor: pointer; 
      transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); /* বাউন্সি ইফেক্ট */
    }
    .option-btn:hover { 
      background: #3498db; 
      color: white; 
      border-color: #3498db; 
      transform: translateY(-3px) scale(1.015); /* হালকা ওপরে ভাসবে ও বড় হবে */
      box-shadow: 0 5px 15px rgba(52, 152, 219, 0.3); /* গ্লোয়িং শ্যাডো */
    }
    
    .correct { 
      background: #2ecc71 !important; 
      color: white; 
      border-color: #2ecc71 !important;
    }
    .wrong { 
      background: #e74c3c !important; 
      color: white; 
      border-color: #e74c3c !important;
    }
    .nav-btns { 
      display: flex; 
      justify-content: space-between; 
      margin-top: 25px; 
    }
    .btn { 
      padding: 10px 20px; 
      background: #34495e; 
      color: white; 
      border: none; 
      border-radius: 4px; 
      cursor: pointer; 
      font-size: 1rem;
      transition: background 0.2s;
    }
    .btn:hover { 
      background: #2c3e50; 
    }
    .score-card { 
      text-align: center; 
      display: none; 
    }
    .score { 
      font-size: 2.5rem; 
      color: #2ecc71; 
      font-weight: bold; 
      margin: 15px 0;
    }

    /* --- ভুল উত্তরের জন্য কাঁপাকাপির (Shake) অ্যানিমেশন --- */
    @keyframes shake {
      0%, 100% { transform: translateX(0); }
      25% { transform: translateX(-8px); }
      75% { transform: translateX(8px); }
    }
    .shake-effect {
      animation: shake 0.2s ease-in-out 2;
    }

    /* --- নতুন প্রশ্ন আসার সময় স্লাইড-ইন অ্যানিমেশন --- */
    @keyframes slideIn {
      from { opacity: 0; transform: translateY(12px); }
      to { opacity: 1; transform: translateY(0); }
    }
    .slide-in-effect {
      animation: slideIn 0.35s ease-out;
    }
  </style>
</head>
<body>

<div class="container">
  <h1>আদরিণী (MCQ Practice)</h1>
  
  <div id="quiz" class="quiz-box">
    <div id="qn-number" style="color: #7f8c8d; font-weight: bold; margin-bottom: 10px;"></div>
    <div id="question" class="question"></div>
    <div id="options">
      <button class="option-btn" id="btn-A" onclick="checkAnswer('A')"></button>
      <button class="option-btn" id="btn-B" onclick="checkAnswer('B')"></button>
      <button class="option-btn" id="btn-C" onclick="checkAnswer('C')"></button>
      <button class="option-btn" id="btn-D" onclick="checkAnswer('D')"></button>
    </div>
    
    <div class="nav-btns">
      <button class="btn" id="prev-btn" onclick="prevQuestion()">আগেরটি</button>
      <button class="btn" id="next-btn" onclick="nextQuestion()">পরেরটি</button>
    </div>
  </div>

  <div id="result" class="score-card">
    <h2>তোমার প্র্যাকটিস শেষ হয়েছে!</h2>
    <p>মোট স্কোর:</p>
    <div class="score" id="final-score">0</div>
    <button class="btn" onclick="resetQuiz()" style="margin-top:20px;">আবার চেষ্টা করো</button>
  </div>
</div>

<script src="beng.js"></script>

<script>
  let currentIdx = 0;
  let score = 0;
  let answered;

  window.onload = function() {
    answered = new Array(qData.length).fill(null);
    showQuestion();
  };

  function showQuestion() {
    resetState();
    let q = qData[currentIdx];
    
    document.getElementById('qn-number').innerText = `প্রশ্ন নং: ${currentIdx + 1} / ${qData.length}`;
    document.getElementById('question').innerText = q.question;
    document.getElementById('btn-A').innerText = "ক) " + q.a;
    document.getElementById('btn-B').innerText = "খ) " + q.b;
    document.getElementById('btn-C').innerText = "গ) " + q.c;
    document.getElementById('btn-D').innerText = "ঘ) " + q.d;

    if(answered[currentIdx] !== null) {
      revealAnswer(answered[currentIdx]);
    }

    // নতুন প্রশ্ন লোড হওয়ার সময় স্লাইড অ্যানিমেশন ট্রিগার করবে
    let quizBox = document.getElementById('quiz');
    quizBox.classList.add('slide-in-effect');
    setTimeout(() => { quizBox.classList.remove('slide-in-effect'); }, 350);
  }

  function resetState() {
    let btns = document.querySelectorAll('.option-btn');
    btns.forEach(b => {
      b.className = 'option-btn';
      b.disabled = false;
    });
  }

  function checkAnswer(selected) {
    if(answered[currentIdx] !== null) return; 
    
    answered[currentIdx] = selected;
    let correct = qData[currentIdx].correct;
    
    if(selected === correct) {
      score++;
    } else {
      // ভুল উত্তর দিলে পুরো ক্যুইজ বক্সটি কেঁপে উঠবে
      let quizBox = document.getElementById('quiz');
      quizBox.classList.add('shake-effect');
      setTimeout(() => { quizBox.classList.remove('shake-effect'); }, 400);
    }
    revealAnswer(selected);
  }

  function revealAnswer(selected) {
    let correct = qData[currentIdx].correct;
    let btns = document.querySelectorAll('.option-btn');
    btns.forEach(b => b.disabled = true); 

    document.getElementById(`btn-${correct}`).classList.add('correct');
    if(selected !== correct) {
      document.getElementById(`btn-${selected}`).classList.add('wrong');
    }
  }

  function nextQuestion() {
    if(currentIdx < qData.length - 1) {
      currentIdx++;
      showQuestion();
    } else {
      showResult();
    }
  }

  function prevQuestion() {
    if(currentIdx > 0) {
      currentIdx--;
      showQuestion();
    }
  }

  function showResult() {
    document.getElementById('quiz').style.display = 'none';
    document.getElementById('result').style.display = 'block';
    document.getElementById('final-score').innerText = `${score} / ${qData.length}`;
  }

  function resetQuiz() {
    currentIdx = 0;
    score = 0;
    answered = new Array(qData.length).fill(null);
    document.getElementById('result').style.display = 'none';
    document.getElementById('quiz').style.display = 'block';
    showQuestion();
  }
</script>
</body>
</html>
