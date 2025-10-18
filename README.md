<!doctype html>
<html lang="vi">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>MBTI Quiz — Lối rẽ tuổi thiếu niên</title>
<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
<style>
@font-face { font-family: 'CustomPixel'; src: url('assets/0307-LNTH-TwistyPixel.ttf') format('truetype'); font-weight: normal; font-style: normal; font-display: swap; }
body, button { font-family: 'CustomPixel', monospace; margin:0; color:#2b1323; }
:root{--panel:#ffeef8;--accent:#ff7ab6;--muted:#7a6b78;}
html,body{height:100%;background:linear-gradient(180deg,#fff0f6,#ffeef4);}
.wrap{min-height:100vh;display:flex;align-items:center;justify-content:center;padding:20px}
.game{width:940px;max-width:98vw;background:linear-gradient(180deg, rgba(255,255,255,0.6), rgba(255,255,255,0.8));border-radius:14px;padding:18px;box-shadow:0 10px 30px rgba(0,0,0,0.07)}
.pixel-logo{display:flex;gap:12px;align-items:center}
.pixel-block{width:64px;height:64px;background:repeating-linear-gradient(45deg,#ffd6eb,#ffd6eb 8px,#fff 8px,#fff 16px);border-radius:8px;display:flex;align-items:center;justify-content:center;color:#521233;font-weight:900;font-size:20px}
h1{margin:0;font-size:18px}
.lead{font-size:11px;color:var(--muted);margin-top:6px}
.start-screen{display:flex;flex-direction:column;align-items:center;padding:28px;background:linear-gradient(180deg,rgba(255,255,255,0.6),rgba(255,255,255,0.7));border-radius:10px;margin-top:12px}
.btn-play{padding:14px 28px;border-radius:8px;background:linear-gradient(180deg,#ffd6eb,#ffb0d6);border:3px solid #ff9acb;color:#521233;font-weight:900;cursor:pointer}
.quiz-area{display:none;margin-top:12px}
.card{background:#fff;padding:14px;border-radius:10px;margin-bottom:10px;border:2px solid rgba(82,18,51,0.04)}
.question{font-size:13px;margin-bottom:10px;color:#3a1020}
.answers{display:flex;gap:12px}
.answers button{flex:1;padding:12px;border-radius:8px;border:2px solid rgba(82,18,51,0.06);background:linear-gradient(180deg,#fff,#fff0f6);cursor:pointer;font-weight:800;color:#521233}
.answers button.selected{border-color:var(--accent);box-shadow:0 12px 24px rgba(255,122,182,0.08)}
.progress{display:flex;justify-content:space-between;align-items:center;margin-top:8px;font-size:12px;color:var(--muted)}
.result{margin-top:12px;padding:12px;border-radius:8px;background:#fff0fb;border:2px dashed rgba(82,18,51,0.06);color:#521233}
.content-overlay { background: rgba(255,255,255,0.6); backdrop-filter: blur(6px); border-radius: 12px; padding: 12px; }
body{background-image:url('assets/bg.jpg');background-size:cover;}
.music-btn{position:fixed;top:12px;right:12px;padding:6px 10px;border-radius:8px;background:#ffd6eb;border:none;cursor:pointer;}
@media(max-width:720px){.answers{flex-direction:column}}
</style>
</head>
<body>
<button id="musicBtn" class="music-btn">🔊 Nhạc</button>
<div class="wrap">
  <div class="content-overlay">
    <div class="game">
      <div class="pixel-logo">
        <div class="pixel-block">MB</div>
        <div>
          <h1>MBTI QUIZ</h1>
          <div class="lead">Made by Nhi Tran</div>
        </div>
      </div>

      <div id="start" class="start-screen">
        <button id="btnPlay" class="btn-play">PLAY</button>
      </div>

      <div id="quizArea" class="quiz-area">
        <div class="card" id="quizCard">
          <div class="question" id="questionText">Question</div>
          <div class="answers" id="answersList"></div>
        </div>
        <div class="progress">
          <div id="progressText">0/30</div>
        </div>
      </div>

      <div id="resultArea" style="display:none;"></div>
    </div>
  </div>
</div>

<video id="bgMusic" loop style="display:none" playsinline>
  <source src="assets/bg.mp4" type="video/mp4">
</video>

<script>
document.addEventListener('DOMContentLoaded', ()=>{

  // --- Questions (30) with correct dimension tags ---
  const QUESTIONS = [
    {"id":1,"text":"Bạn thích ở đâu khi rảnh rỗi?","A":"Ở với nhiều người, tham gia hoạt động nhóm","B":"Ở một mình hoặc với vài người thân","dimension":"EI"},
    {"id":2,"text":"Khi gặp người lạ, bạn thường:","A":"Bắt chuyện và tạo quan hệ ngay","B":"Thận trọng, quan sát trước khi nói","dimension":"EI"},
    {"id":3,"text":"Bạn nạp năng lượng bằng cách:","A":"Giao tiếp, ra ngoài gặp gỡ bạn bè","B":"Ở nhà, nghỉ ngơi một mình","dimension":"EI"},
    {"id":4,"text":"Trong nhóm, bạn thường:","A":"Nói nhiều, dẫn dắt","B":"Lắng nghe, quan sát","dimension":"EI"},
    {"id":5,"text":"Bạn cảm thấy thoải mái hơn khi:","A":"Làm việc với nhiều người","B":"Làm việc một mình hoặc vài người quen","dimension":"EI"},

    {"id":6,"text":"Bạn học theo cách nào?","A":"Học qua ví dụ thực tế","B":"Học qua ý tưởng và mô hình","dimension":"SN"},
    {"id":7,"text":"Bạn thích nhìn sự vật theo:","A":"Điều thực tế, rõ ràng","B":"Khả năng, tiềm năng và ý tưởng","dimension":"SN"},
    {"id":8,"text":"Bạn giải quyết vấn đề dựa vào:","A":"Thông tin hiện tại, dữ kiện","B":"Khả năng và mối liên hệ tiềm ẩn","dimension":"SN"},
    {"id":9,"text":"Bạn thích:","A":"Trải nghiệm thực tế","B":"Suy nghĩ và tưởng tượng","dimension":"SN"},
    {"id":10,"text":"Bạn thấy thoải mái khi:","A":"Theo hướng dẫn chi tiết","B":"Thử nghiệm ý tưởng mới","dimension":"SN"},

    {"id":11,"text":"Khi quyết định, bạn:","A":"Dựa vào logic và lý lẽ","B":"Dựa vào cảm xúc và giá trị","dimension":"TF"},
    {"id":12,"text":"Khi tranh luận, bạn:","A":"Trực tiếp, rõ ràng","B":"Nhẹ nhàng, tránh làm tổn thương","dimension":"TF"},
    {"id":13,"text":"Người khác nhận xét bạn:","A":"Lý trí, khách quan","B":"Đồng cảm, nhạy cảm","dimension":"TF"},
    {"id":14,"text":"Bạn quan tâm hơn đến:","A":"Công lý và nguyên tắc","B":"Cảm xúc và mối quan hệ","dimension":"TF"},
    {"id":15,"text":"Bạn góp ý bằng cách:","A":"Thẳng thắn, trực tiếp","B":"Tinh tế, khéo léo","dimension":"TF"},

    {"id":16,"text":"Bạn lên kế hoạch công việc:","A":"Rõ ràng, có thứ tự","B":"Linh hoạt, tùy tình huống","dimension":"JP"},
    {"id":17,"text":"Bạn hoàn thành nhiệm vụ:","A":"Theo deadline, tổ chức tốt","B":"Thường để gần deadline mới làm","dimension":"JP"},
    {"id":18,"text":"Bạn thích môi trường làm việc:","A":"Có trật tự, nguyên tắc","B":"Tự do, không gò bó","dimension":"JP"},
    {"id":19,"text":"Trong nhóm, bạn thích:","A":"Phân công rõ ràng","B":"Thảo luận, tùy biến","dimension":"JP"},
    {"id":20,"text":"Phong cách sống của bạn:","A":"Có kế hoạch, ngăn nắp","B":"Tùy hứng, linh hoạt","dimension":"JP"},

    {"id":21,"text":"Bạn thường bắt đầu ngày mới bằng:","A":"Giao tiếp, nói chuyện với người khác","B":"Chuẩn bị yên tĩnh, một mình","dimension":"EI"},
    {"id":22,"text":"Bạn tìm hiểu vấn đề qua:","A":"Dữ liệu, bằng chứng","B":"Ý tưởng, khả năng tương lai","dimension":"SN"},
    {"id":23,"text":"Bạn quyết định dựa trên:","A":"Phân tích logic","B":"Cảm xúc và giá trị","dimension":"TF"},
    {"id":24,"text":"Bạn sắp xếp công việc:","A":"Theo kế hoạch và lịch trình","B":"Linh hoạt, tùy cơ ứng biến","dimension":"JP"},
    {"id":25,"text":"Bạn thấy vui khi:","A":"Tương tác với nhiều người","B":"Ở bên vài người thân","dimension":"EI"},
    {"id":26,"text":"Bạn xử lý thông tin bằng:","A":"Quan sát, chi tiết thực tế","B":"Tưởng tượng, suy luận ý tưởng","dimension":"SN"},
    {"id":27,"text":"Bạn đánh giá người khác qua:","A":"Hành động và logic","B":"Cảm xúc và động lực cá nhân","dimension":"TF"},
    {"id":28,"text":"Bạn hoàn thành dự án:","A":"Từng bước, có kế hoạch","B":"Theo cảm hứng, linh hoạt","dimension":"JP"},
    {"id":29,"text":"Khi tham gia sự kiện, bạn:","A":"Năng nổ, trò chuyện","B":"Ít nói, quan sát","dimension":"EI"},
    {"id":30,"text":"Bạn tiếp nhận kiến thức mới:","A":"Bằng chứng, sự thật","B":"Ý tưởng, mô hình tương lai","dimension":"SN"}
  ];

  // --- Descriptions for 16 types (short,long,careers) ---
  const DESCRIPTIONS = {
    "INTJ":{"short":"Chiến lược gia độc lập.","long":"INTJ lập kế hoạch, phân tích hệ thống, thích làm việc độc lập.","careers":["Kỹ sư phần mềm","Nhà phân tích dữ liệu","Nhà khoa học","Quản lý sản phẩm"]},
    "INTP":{"short":"Nhà tư tưởng phân tích.","long":"INPT quan tâm lý thuyết, mô hình trừu tượng, giải thích nguyên lý.","careers":["Nhà nghiên cứu","Lập trình viên","Giảng viên"]},
    "ENTJ":{"short":"Lãnh đạo quyết đoán.","long":"ENTJ thiên về lập chiến lược, quản lý, ra quyết định.","careers":["CEO","Quản lý dự án","Tư vấn"]},
    "ENTP":{"short":"Sáng tạo, thích đổi mới.","long":"ENTP thích ý tưởng mới, tranh luận để mài giũa quan điểm.","careers":["Khởi nghiệp","Marketing","Tư vấn sáng tạo"]},
    "INFJ":{"short":"Hướng nội, giàu giá trị.","long":"INFJ quan tâm ý nghĩa, giúp người khác phát triển.","careers":["Tư vấn tâm lý","Nhà văn","Giáo viên"]},
    "INFP":{"short":"Lý tưởng, giàu nội tâm.","long":"INFP sống theo giá trị cá nhân, sáng tạo.","careers":["Nhà văn","Thiết kế","Nghệ sĩ"]},
    "ENFJ":{"short":"Truyền cảm hứng, hỗ trợ cộng đồng.","long":"ENFJ giỏi kết nối, lãnh đạo vì con người.","careers":["Giáo dục","Nhân sự","Tư vấn"]},
    "ENFP":{"short":"Nhiệt huyết, sáng tạo.","long":"ENFP nhiều năng lượng, thích khám phá cơ hội.","careers":["Marketing","PR","Sáng tạo nội dung"]},
    "ISTJ":{"short":"Trách nhiệm, thực tế.","long":"ISTJ tuân thủ quy tắc, làm việc có cấu trúc.","careers":["Kế toán","Hành chính","Quản lý vận hành"]},
    "ISFJ":{"short":"Chu đáo, tận tâm.","long":"ISFJ chăm sóc người khác, chú ý chi tiết.","careers":["Y tá","Giáo viên","Dịch vụ khách hàng"]},
    "ESTJ":{"short":"Tổ chức, quyết đoán.","long":"ESTJ giỏi tổ chức, thi hành quy trình.","careers":["Quản lý","Giám sát","Nhân sự"]},
    "ESFJ":{"short":"Quan tâm cộng đồng.","long":"ESFJ chu đáo, giỏi giao tiếp, hỗ trợ tập thể.","careers":["Giáo dục","Quản lý sự kiện","Chăm sóc khách hàng"]},
    "ISTP":{"short":"Thực hành, phân tích.","long":"ISTP thích khám phá vận hành của mọi thứ, giải quyết thực tế.","careers":["Kỹ thuật","Lập trình hệ thống","Thợ kỹ thuật"]},
    "ISFP":{"short":"Nghệ sĩ, nhạy cảm.","long":"ISFP sáng tạo, có con mắt thẩm mỹ, sống trong hiện tại.","careers":["Thiết kế","Nhiếp ảnh","Nghệ thuật"]},
    "ESTP":{"short":"Năng động, ưa mạo hiểm.","long":"ESTP hành động nhanh, thích thử thách, xử lý khủng hoảng.","careers":["Kinh doanh","Sự kiện","Bán hàng"]},
    "ESFP":{"short":"Biểu diễn, thân thiện.","long":"ESFP thích giao tiếp, tạo niềm vui cho người khác.","careers":["Nghệ sĩ","Du lịch & Khách sạn","PR/Marketing"]}
  };

  // --- State & Elements ---
  let current = 0;
  let answers = Array(QUESTIONS.length).fill(null); // will store 'A' or 'B'
  const btnPlay = document.getElementById('btnPlay');
  const start = document.getElementById('start');
  const quizArea = document.getElementById('quizArea');
  const questionText = document.getElementById('questionText');
  const answersList = document.getElementById('answersList');
  const progressText = document.getElementById('progressText');
  const resultArea = document.getElementById('resultArea');
  const music = document.getElementById('bgMusic');
  const musicBtn = document.getElementById('musicBtn');

  // simple 8-bit click (safe)
  let audioCtx;
  function initAudio(){ if(audioCtx) return; audioCtx = new (window.AudioContext||window.webkitAudioContext)(); }
  function playClick(freq=800,dur=0.06,vol=0.06){
    try{
      if(!audioCtx) initAudio();
      const o=audioCtx.createOscillator(), g=audioCtx.createGain();
      o.type='square'; o.frequency.value=freq;
      g.gain.value = 0.0001;
      o.connect(g); g.connect(audioCtx.destination);
      g.gain.setTargetAtTime(vol, audioCtx.currentTime, 0.01);
      o.start(); o.stop(audioCtx.currentTime + dur);
    }catch(e){}
  }

  // Start quiz
  btnPlay.addEventListener('click', ()=>{
    try{ initAudio(); audioCtx.resume && audioCtx.resume(); playClick(1000,0.06,0.12); }catch(e){}
    start.style.display = 'none';
    resultArea.style.display = 'none';
    quizArea.style.display = 'block';
    current = 0;
    answers = Array(QUESTIONS.length).fill(null);
    loadQuestion();
    try{ music.play(); musicBtn.textContent='🔈 Tắt nhạc'; }catch(e){}
  });

  // Music toggle
  musicBtn.addEventListener('click', ()=>{
    if(music.paused){ music.play(); musicBtn.textContent='🔈 Tắt nhạc'; }
    else { music.pause(); musicBtn.textContent='🔊 Nhạc'; }
  });

  // Load question
  function loadQuestion(){
    const q = QUESTIONS[current];
    questionText.textContent = `${current+1}. ${q.text}`;
    answersList.innerHTML = '';
    ['A','B'].forEach((opt, idx)=>{
      const btn = document.createElement('button');
      btn.innerHTML = `<strong>${opt}</strong>. ${q[opt]}`;
      btn.className = '';
      btn.addEventListener('click', ()=>{
        // save answer
        answers[current] = opt;
        // visual
        Array.from(answersList.children).forEach(c=>c.classList.remove('selected'));
        btn.classList.add('selected');
        playClick(700,0.07,0.08);
        // auto next after tiny delay so click visible
        setTimeout(()=>{
          current++;
          if(current >= QUESTIONS.length){ computeAndShowResult(); }
          else{ loadQuestion(); }
        }, 220);
      });
      answersList.appendChild(btn);
    });
    progressText.textContent = `${current+1}/${QUESTIONS.length}`;
  }

  // Compute result robustly
  function computeAndShowResult(){
    // count A selections per dimension and total per dimension
    const countsA = {EI:0,SN:0,TF:0,JP:0};
    const totals = {EI:0,SN:0,TF:0,JP:0};
    QUESTIONS.forEach((q,i)=>{
      totals[q.dimension] = (totals[q.dimension] || 0) + 1;
      if(answers[i] === 'A') countsA[q.dimension] = (countsA[q.dimension] || 0) + 1;
    });

    // determine letter by majority A vs B (A -> left letter; B -> right letter)
    const EI_letter = (countsA.EI >= (totals.EI/2)) ? 'E' : 'I';
    const SN_letter = (countsA.SN >= (totals.SN/2)) ? 'S' : 'N';
    const TF_letter = (countsA.TF >= (totals.TF/2)) ? 'T' : 'F';
    const JP_letter = (countsA.JP >= (totals.JP/2)) ? 'J' : 'P';
    const code = EI_letter + SN_letter + TF_letter + JP_letter;

    // build score display
    const scoreHtml = `
      <div style="display:flex;gap:8px;margin-top:8px;flex-wrap:wrap;">
        <div style="background:#ffeef8;padding:8px;border-radius:8px;">E: ${countsA.EI}/${totals.EI}</div>
        <div style="background:#fff0f6;padding:8px;border-radius:8px;">I: ${totals.EI - countsA.EI}/${totals.EI}</div>
        <div style="width:8px"></div>
        <div style="background:#ffeef8;padding:8px;border-radius:8px;">S: ${countsA.SN}/${totals.SN}</div>
        <div style="background:#fff0f6;padding:8px;border-radius:8px;">N: ${totals.SN - countsA.SN}/${totals.SN}</div>
        <div style="width:8px"></div>
        <div style="background:#ffeef8;padding:8px;border-radius:8px;">T: ${countsA.TF}/${totals.TF}</div>
        <div style="background:#fff0f6;padding:8px;border-radius:8px;">F: ${totals.TF - countsA.TF}/${totals.TF}</div>
        <div style="width:8px"></div>
        <div style="background:#ffeef8;padding:8px;border-radius:8px;">J: ${countsA.JP}/${totals.JP}</div>
        <div style="background:#fff0f6;padding:8px;border-radius:8px;">P: ${totals.JP - countsA.JP}/${totals.JP}</div>
      </div>
    `;

    // get description safely
    const desc = DESCRIPTIONS[code] || {short:'Không có mô tả', long:'', careers:[]};

    // show result
    resultArea.innerHTML = `
      <div class="result">
        <h3>Kết quả MBTI của bạn: <strong>${code}</strong></h3>
        <p><strong>Mô tả ngắn:</strong> ${desc.short}</p>
        <p><strong>Mô tả dài:</strong> ${desc.long || 'Không có'}</p>
        <p><strong>Gợi ý nghề nghiệp:</strong> ${desc.careers && desc.careers.length? desc.careers.join(', '): 'Không có'}</p>
        ${scoreHtml}
        <div style="margin-top:12px;">
          <button id="btnRestart" class="btn-play">Chơi lại</button>
        </div>
      </div>
    `;
    // attach restart
    document.getElementById('btnRestart').addEventListener('click', ()=>{
      // reload to reset everything simply
      location.reload();
    });

    // ensure visibility toggles
    quizArea.style.display = 'none';
    resultArea.style.display = 'block';
  }

  // Start in initial state (show start)
  quizArea.style.display = 'none';
  resultArea.style.display = 'none';
});
</script>
</body>
</html>
