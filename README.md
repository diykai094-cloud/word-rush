# <!doctype html>
<html lang="ko">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<title>1분 영어 단어 암기 챌린지</title>
<style>
:root{color-scheme:light;--ink:#172033;--muted:#64748b;--line:#dbe3ef;--blue:#3867e8;--bg:#f4f7fc;--green:#13845b;--red:#c43d4d}
*{box-sizing:border-box}body{margin:0;background:var(--bg);font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif;color:var(--ink)}
main{max-width:760px;margin:auto;padding:22px 16px 40px}.hero{background:linear-gradient(135deg,#243e85,#527be9);color:white;border-radius:24px;padding:24px 20px;margin-bottom:16px}
h1{font-size:25px;margin:0 0 8px}.hero p{margin:0;color:#e7edff;line-height:1.5}.panel{background:white;border:1px solid var(--line);border-radius:20px;padding:18px;margin:14px 0;box-shadow:0 5px 18px #243e850a}
h2{font-size:18px;margin:0 0 12px}.muted{color:var(--muted);font-size:14px;line-height:1.5}
input,textarea,button{font:inherit}input[type=file]{width:100%;padding:12px;background:#f8faff;border:1px dashed #aab8d0;border-radius:12px}
textarea{width:100%;min-height:145px;padding:12px;border:1px solid var(--line);border-radius:12px;resize:vertical;line-height:1.5}
button{border:0;border-radius:13px;padding:13px 16px;font-weight:700;cursor:pointer;background:var(--blue);color:white}
button.secondary{background:#eaf0ff;color:#2449b8}button.ghost{background:#eef2f7;color:#334155}button:disabled{opacity:.45;cursor:not-allowed}
.row{display:flex;gap:10px;flex-wrap:wrap}.row button{flex:1}.timer{font-size:54px;font-weight:850;text-align:center;letter-spacing:2px;font-variant-numeric:tabular-nums;margin:12px 0}.timer.danger{color:var(--red)}
.status{text-align:center;font-weight:700;color:var(--muted);min-height:24px}.progress{height:9px;background:#e9eef7;border-radius:20px;overflow:hidden}.bar{height:100%;width:100%;background:#4c76ed;transition:width .2s linear}
.wordlist{display:grid;gap:8px}.word{display:flex;justify-content:space-between;gap:12px;padding:10px 12px;background:#f7f9fd;border-radius:10px}.word b{overflow-wrap:anywhere}.word span{color:var(--muted);text-align:right}
.quizword{font-size:27px;font-weight:800;text-align:center;padding:24px 8px}.answer{width:100%;padding:14px;border:1px solid var(--line);border-radius:12px;font-size:18px}
.hidden{display:none!important}.small{font-size:12px;color:var(--muted)}.score{font-size:38px;font-weight:850;text-align:center;color:var(--green)}
footer{text-align:center;color:var(--muted);font-size:12px;margin-top:22px}
</style>
</head>
<body>
<main>
  <section class="hero">
    <h1>🧠 1분 영어 단어 암기 챌린지</h1>
    <p>사진을 올리고 60초 동안 외운 다음, 한국어 뜻만 보고 영어 단어를 입력해 봐!</p>
  </section>

  <section class="panel" id="setup">
    <h2>1. 단어 사진 올리기</h2>
    <input id="photo" type="file" accept="image/*">
    <p class="muted">영어 단어와 한국어 뜻이 함께 보이는 사진을 선택해 줘. 사진 글씨가 선명하고 표가 반듯하면 더 잘 읽혀. iPhone에서는 파일 미리보기 대신 Safari에서 열어 줘.</p>
    <div class="row" style="margin-top:12px">
      <button id="ocrBtn" class="secondary">📷 사진에서 단어 읽기</button>
    </div>
    <p id="ocrStatus" class="status" aria-live="polite"></p>
    <div id="previewWrap" class="hidden"><img id="preview" alt="업로드한 단어 사진" style="max-width:100%;max-height:260px;border-radius:12px"></div>
  </section>

  <section class="panel" id="editPanel">
    <h2>2. 단어 확인 및 수정</h2>
    <p class="muted">한 줄에 하나씩 <b>영어 단어 = 한국어 뜻</b> 형식으로 적어 줘. 사진 인식 결과가 틀리면 여기서 고칠 수 있어.</p>
    <textarea id="pairs" spellcheck="false" placeholder="rest = 쉬다&#10;outside = 밖에서&#10;take medicine = 약을 먹다"></textarea>
    <p class="small">예: take medicine = 약을 먹다 (등호 앞은 영어, 뒤는 한국어)</p>
    <div class="row" style="margin-top:12px"><button id="startBtn">60초 암기 시작!</button></div>
    <p id="parseStatus" class="status" aria-live="polite"></p>
  </section>

  <section class="panel hidden" id="memorize">
    <h2>3. 지금 외워 봐!</h2>
    <div class="timer" id="timer">01:00</div>
    <div class="progress"><div class="bar" id="bar"></div></div>
    <p class="status" id="memStatus">단어를 눈으로 익혀 봐!</p>
    <div class="wordlist" id="memoryList"></div>
    <div class="row" style="margin-top:14px"><button class="ghost" id="endEarly">암기 끝! 시험 시작</button></div>
  </section>

  <section class="panel hidden" id="quiz">
    <h2>4. 영어 단어 시험</h2>
    <p class="muted" id="quizCount"></p>
    <div class="quizword" id="meaning"></div>
    <input class="answer" id="answer" type="text" autocomplete="off" autocapitalize="none" placeholder="영어 단어를 입력해 봐">
    <div class="row" style="margin-top:12px"><button id="submitBtn">정답 확인</button></div>
    <p class="status" id="feedback" aria-live="polite"></p>
  </section>

  <section class="panel hidden" id="result">
    <h2>🏆 시험 결과</h2>
    <div class="score" id="score"></div>
    <p class="status" id="scoreText"></p>
    <div id="review" class="wordlist"></div>
    <div class="row" style="margin-top:14px"><button id="retryBtn">처음부터 다시 하기</button></div>
  </section>
  <footer>사진 인식은 인터넷 연결이 필요할 수 있어. 인식 결과는 꼭 확인해 줘.</footer>
</main>
<script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
<script>
const $=id=>document.getElementById(id);
let words=[],index=0,answers=[],timerId=null,remaining=60,quizLocked=false;
const show=(id,on=true)=>$(id).classList.toggle('hidden',!on);
$('photo').addEventListener('change',e=>{
 const f=e.target.files[0]; if(!f)return;
 $('preview').src=URL.createObjectURL(f);show('previewWrap');
 $('ocrStatus').textContent='사진이 선택됐어. 이제 사진에서 단어를 읽어 봐!';
});
$('ocrBtn').addEventListener('click',async()=>{
 const f=$('photo').files[0];
 if(!f){$('ocrStatus').textContent='먼저 사진을 선택해 줘!';return}
 $('ocrBtn').disabled=true;
 $('ocrStatus').textContent='OCR 엔진을 불러오는 중… 인터넷 연결을 확인해 줘.';
 try{
  if(!window.Tesseract){
   await new Promise((resolve,reject)=>{
    const script=document.createElement('script');
    script.src='https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js';
    script.onload=resolve;
    script.onerror=()=>reject(new Error('OCR 라이브러리 다운로드 실패'));
    document.head.appendChild(script);
    setTimeout(()=>reject(new Error('OCR 라이브러리 로딩 시간 초과')),20000);
   });
  }
  if(!window.Tesseract)throw new Error('OCR 엔진을 사용할 수 없음');
  $('ocrStatus').textContent='사진 읽는 중… 잠시만 기다려 줘!';
  const result=await Tesseract.recognize(f,'eng+kor',{logger:m=>{
   if(m.status==='recognizing text')$('ocrStatus').textContent='사진 읽는 중… '+Math.round((m.progress||0)*100)+'%';
   else if(m.status)$('ocrStatus').textContent='처리 중: '+m.status;
  }});
  const raw=result.data.text||'';
  const lines=raw.split(/\n/).map(x=>x.trim()).filter(Boolean);
  const converted=lines.map(line=>{
   let v=line.replace(/\s{2,}/g,' = ').replace(/\s+[|｜]\s+/,' = ');
   if(!v.includes('=')){
    const m=v.match(/^([A-Za-z][A-Za-z '\-]*?)\s+(.+)$/);
    if(m && /[가-힣]/.test(m[2]))v=m[1]+' = '+m[2];
   }
   return v;
  }).filter(x=>x.includes('='));
  if(converted.length){
   $('pairs').value=converted.join('\n');
   $('ocrStatus').textContent='읽기 완료! 결과를 확인하고 틀린 부분을 수정해 줘.';
  }else{
   $('pairs').value=raw;
   $('ocrStatus').textContent='글자는 읽었지만 영어와 뜻을 자동으로 짝짓지 못했어. 아래 칸에서 영어 = 한국어 형식으로 고쳐 줘.';
  }
 }catch(err){
  $('ocrStatus').textContent='사진 인식 기능을 불러오지 못했어. Safari에서 열고 인터넷 연결을 확인해 줘. 그래도 안 되면 영어 = 한국어 형식으로 직접 입력할 수 있어.';
 }
 $('ocrBtn').disabled=false;
});
function parsePairs(){
 return $('pairs').value.split(/\n/).map(line=>{
  const p=line.split('=');
  if(p.length<2)return null;
  const en=p.shift().trim(),ko=p.join('=').trim();
  return en&&ko?{en,ko}:null;
 }).filter(Boolean);
}
$('startBtn').addEventListener('click',()=>{
 words=parsePairs();
 if(!words.length){$('parseStatus').textContent='단어를 영어 = 한국어 형식으로 한 줄 이상 입력해 줘.';return}
 if(words.length>80){$('parseStatus').textContent='한 번에 최대 80개까지 할 수 있어.';return}
 clearInterval(timerId);remaining=60;$('timer').textContent='01:00';$('timer').classList.remove('danger');$('bar').style.width='100%';
 $('memoryList').innerHTML=words.map(w=>`<div class="word"><b>${escapeHtml(w.en)}</b><span>${escapeHtml(w.ko)}</span></div>`).join('');
 show('setup',false);show('editPanel',false);show('memorize');show('quiz',false);show('result',false);
 timerId=setInterval(()=>{
  remaining--; $('timer').textContent='00:'+String(remaining).padStart(2,'0');$('bar').style.width=(remaining/60*100)+'%';
  if(remaining<=10)$('timer').classList.add('danger');
  if(remaining<=0){clearInterval(timerId);beginQuiz(true)}
 },1000);
});
$('endEarly').addEventListener('click',()=>{clearInterval(timerId);beginQuiz(false)});
function beginQuiz(timeout){
 show('memorize',false);show('quiz');index=0;answers=[];quizLocked=false;
 $('feedback').textContent=timeout?'⏰ TIME OVER! 이제 시험 시작!':'좋아! 바로 시험 시작!';
 $('answer').value='';renderQuestion();setTimeout(()=>$('answer').focus(),100);
}
function renderQuestion(){
 if(index>=words.length){finish();return}
 $('quizCount').textContent=`${index+1} / ${words.length}번`;
 $('meaning').textContent=words[index].ko;$('answer').value='';$('feedback').textContent='';
 $('submitBtn').textContent=index===words.length-1?'마지막 답 제출':'정답 확인';
}
$('submitBtn').addEventListener('click',submitAnswer);
$('answer').addEventListener('keydown',e=>{if(e.key==='Enter')submitAnswer()});
function submitAnswer(){
 if(quizLocked)return;
 const typed=$('answer').value.trim();
 if(!typed){$('feedback').textContent='영어 단어를 입력해 줘!';return}
 const correct=typed.toLowerCase().replace(/\s+/g,' ')===words[index].en.toLowerCase().replace(/\s+/g,' ');
 answers.push({typed,correct,word:words[index]});
 $('feedback').textContent=correct?'정답! ✅':'아쉽다! 다음 문제로 가자.';
 index++;quizLocked=true;$('submitBtn').disabled=true;
 setTimeout(()=>{quizLocked=false;$('submitBtn').disabled=false;renderQuestion();if(index<words.length)$('answer').focus()},650);
}
function finish(){
 show('quiz',false);show('result');
 const n=answers.filter(a=>a.correct).length,percent=Math.round(n/words.length*100);
 $('score').textContent=`${n} / ${words.length} (${percent}점)`;
 $('scoreText').textContent=percent===100?'완벽해! 전부 맞혔어! 🎉':percent>=80?'잘했어! 조금만 더 복습하자!': '괜찮아! 틀린 단어를 다시 확인해 보자.';
 $('review').innerHTML=answers.map((a,i)=>`<div class="word"><div><b>${escapeHtml(a.word.ko)}</b><div class="small">내 답: ${escapeHtml(a.typed)}</div></div><span>${a.correct?'✅ 정답':'❌ '+escapeHtml(a.word.en)}</span></div>`).join('');
}
$('retryBtn').addEventListener('click',()=>{
 clearInterval(timerId);show('result',false);show('setup');show('editPanel');$('parseStatus').textContent='';$('ocrStatus').textContent='';$('pairs').focus();
});
function escapeHtml(s){return String(s).replace(/[&<>"']/g,c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]))}
</script>
</body>
</html>
