<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>لعبة حجرة ورقة مقص</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      direction: rtl;
      height: 100%;
      width: 100%;
      font-family: 'Cairo', sans-serif;
      background: linear-gradient(to right, #2193b0, #6dd5ed);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      color: white;
    }

    h1 {
      font-size: 36px;
      margin-bottom: 10px;
    }

    p {
      font-size: 20px;
    }

    .buttons {
      margin-top: 20px;
    }

    button {
      font-size: 20px;
      padding: 15px 30px;
      margin: 10px;
      border: none;
      border-radius: 10px;
      background-color: white;
      color: black;
      cursor: pointer;
      transition: 0.3s;
    }

    button:hover {
      background-color: #ddd;
    }

    #result, #life, #timer {
      margin-top: 20px;
      font-size: 22px;
    }

    #restartBtn {
      background-color: #ffe6e6;
    }

    #exitBtn {
      position: absolute;
      top: 20px;
      left: 20px;
      background-color: #ff4d4d;
      color: white;
      padding: 10px 20px;
    }
  </style>
</head>
<body>

  <button id="exitBtn" onclick="exitGame()">خروج</button>

  <h1>حجرة - ورقة - مقص</h1>
  <p>عندك 3 قلوب، وكل جولة مدتها 30 ثانية!</p>
  <div id="timer">الوقت المتبقي: 30 ثانية</div>

  <div class="buttons">
    <button onclick="play('حجرة')">حجرة</button>
    <button onclick="play('ورقة')">ورقة</button>
    <button onclick="play('مقص')">مقص</button>
  </div>

  <div id="life"></div>
  <div id="result"></div>
  <button id="restartBtn" onclick="restartGame()">إعادة الجولة</button>

  <audio id="winSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_f7dd0b79dc.mp3" preload="auto"></audio>
  <audio id="loseSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_1ff10d955d.mp3" preload="auto"></audio>
  <audio id="drawSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_2d5f293e3e.mp3" preload="auto"></audio>
  <audio id="endSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_b00e11c83a.mp3" preload="auto"></audio>

  <script>
    let playerLife = 3;
    let computerLife = 3;
    let timeLeft = 30;
    let roundActive = true;
    let countdown = setInterval(updateTimer, 1000);

    const options = ['حجرة', 'ورقة', 'مقص'];
    const timerElement = document.getElementById("timer");
    const winSound = document.getElementById("winSound");
    const loseSound = document.getElementById("loseSound");
    const drawSound = document.getElementById("drawSound");
    const endSound = document.getElementById("endSound");

    document.getElementById("life").innerText = `حياتك: ${playerLife} | حياة الكمبيوتر: ${computerLife}`;

    function updateTimer() {
      if (!roundActive) return;
      if (timeLeft > 0) {
        timeLeft--;
        timerElement.innerText = `الوقت المتبقي: ${timeLeft} ثانية`;
      } else {
        loseRound("انتهى الوقت! خسرت الجولة.");
      }
    }

    function resetRound() {
      timeLeft = 30;
      timerElement.innerText = `الوقت المتبقي: ${timeLeft} ثانية`;
      roundActive = true;
    }

    function play(playerChoice) {
      if (!roundActive || playerLife === 0 || computerLife === 0) return;
      roundActive = false;
      const computerChoice = options[Math.floor(Math.random() * options.length)];
      let resultText = "";

      if (playerChoice === computerChoice) {
        resultText = `تعادل! الكمبيوتر اختار ${computerChoice}`;
        drawSound.play();
      } else if (
        (playerChoice === 'حجرة' && computerChoice === 'مقص') ||
        (playerChoice === 'ورقة' && computerChoice === 'حجرة') ||
        (playerChoice === 'مقص' && computerChoice === 'ورقة')
      ) {
        computerLife--;
        resultText = `فزت! الكمبيوتر اختار ${computerChoice}`;
        winSound.play();
      } else {
        playerLife--;
        resultText = `خسرت! الكمبيوتر اختار ${computerChoice}`;
        loseSound.play();
      }

      document.getElementById("result").innerText = resultText;
      document.getElementById("life").innerText = `حياتك: ${playerLife} | حياة الكمبيوتر: ${computerLife}`;

      if (playerLife === 0 || computerLife === 0) {
        endGame();
      } else {
        setTimeout(resetRound, 2000);
      }
    }

    function loseRound(message) {
      roundActive = false;
      playerLife--;
      document.getElementById("result").innerText = message;
      document.getElementById("life").innerText = `حياتك: ${playerLife} | حياة الكمبيوتر: ${computerLife}`;
      loseSound.play();

      if (playerLife === 0) {
        endGame();
      } else {
        setTimeout(resetRound, 2000);
      }
    }

    function endGame() {
      clearInterval(countdown);
      let finalMessage = playerLife > 0 ? "مبروك! فزت باللعبة!" : "انتهت اللعبة! الكمبيوتر فاز.";
      document.getElementById("result").innerText = finalMessage;
      endSound.play();
    }

    function restartGame() {
      clearInterval(countdown);
      playerLife = 3;
      computerLife = 3;
      timeLeft = 30;
      roundActive = true;
      document.getElementById("life").innerText = `حياتك: ${playerLife} | حياة الكمبيوتر: ${computerLife}`;
      document.getElementById("result").innerText = '';
      timerElement.innerText = `الوقت المتبقي: ${timeLeft} ثانية`;
      countdown = setInterval(updateTimer, 1000);
    }

    function exitGame() {
      const confirmExit = confirm("هل تريد الخروج من اللعبة؟");
      if (confirmExit) {
        location.reload(); // أو يمكن توجيهك لصفحة ثانية مثل index.html
      }
    }
  </script>
</body>
</html>
