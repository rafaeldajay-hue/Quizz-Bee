<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>QuizBee — Three Levels</title>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@400;600;700&family=DM+Mono:wght@300;400;500&display=swap" rel="stylesheet">
<style>
*{box-sizing:border-box;margin:0;padding:0}
:root{
  --navy:#0a1628;--navy2:#0f2044;--navy3:#162d5e;--navy4:#1e3a7a;--navy5:#2a4f9e;
  --slate:#4a6fa5;--mist:#8aa5d0;--fog:#b8cceb;--ghost:#d8e6f7;--white:#f0f4ff;
  --gold:#c9a84c;--gold2:#e8c76a;
}
body{background:var(--navy);color:var(--white);font-family:'DM Mono',monospace;min-height:100vh;overflow-x:hidden}
.screen{display:none;flex-direction:column;align-items:center;justify-content:center;min-height:100vh;padding:2rem;animation:fadeIn .5s ease}
.screen.active{display:flex}
@keyframes fadeIn{from{opacity:0;transform:translateY(16px)}to{opacity:1;transform:translateY(0)}}
.logo{font-family:'Cormorant Garamond',serif;font-size:3.8rem;font-weight:700;letter-spacing:.15em;color:var(--white);text-align:center;line-height:1}
.logo span{color:var(--gold2)}
.tagline{font-size:.68rem;letter-spacing:.4em;color:var(--mist);margin-top:.7rem;text-transform:uppercase;text-align:center}
.rule{width:80px;height:1px;background:var(--navy4);margin:2rem auto}
.level-select-label{font-size:.6rem;letter-spacing:.3em;color:var(--slate);text-transform:uppercase;margin-bottom:1.4rem;text-align:center}
.level-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:1.2rem;width:100%;max-width:680px}
.level-card{background:var(--navy2);border:1px solid var(--navy4);border-radius:3px;padding:2.2rem 1rem;text-align:center;cursor:pointer;transition:all .25s ease;position:relative;overflow:hidden}
.level-card::after{content:'';position:absolute;bottom:0;left:0;right:0;height:2px;transition:opacity .25s}
.level-card.easy::after{background:linear-gradient(90deg,transparent,#4a9a6a,transparent);opacity:0}
.level-card.medium::after{background:linear-gradient(90deg,transparent,var(--gold),transparent);opacity:0}
.level-card.hard::after{background:linear-gradient(90deg,transparent,#9a4a4a,transparent);opacity:0}
.level-card:hover::after{opacity:1}
.level-card:hover{background:var(--navy3);border-color:var(--slate);transform:translateY(-4px)}
.level-icon{font-size:.75rem;letter-spacing:.2rem;color:var(--slate);margin-bottom:.9rem;display:block}
.level-name{font-family:'Cormorant Garamond',serif;font-size:1.6rem;font-weight:600;letter-spacing:.08em;margin-bottom:.4rem}
.level-desc{font-size:.6rem;letter-spacing:.15em;color:var(--mist);text-transform:uppercase}
.quiz-wrap{width:100%;max-width:680px}
.quiz-meta{display:flex;justify-content:space-between;align-items:center;margin-bottom:.8rem}
.quiz-level-tag{font-size:.6rem;letter-spacing:.35em;color:var(--mist);text-transform:uppercase}
.quiz-counter{font-size:.7rem;letter-spacing:.15em;color:var(--ghost)}
.progress{width:100%;height:2px;background:var(--navy3);border-radius:2px;overflow:hidden;margin-bottom:1.8rem}
.progress-fill{height:100%;background:var(--gold);transition:width .45s ease;border-radius:2px}
.question-card{background:var(--navy2);border:1px solid var(--navy4);border-radius:3px;padding:2rem;margin-bottom:1.4rem}
.q-num{font-size:.55rem;letter-spacing:.35em;color:var(--slate);text-transform:uppercase;margin-bottom:.7rem}
.q-text{font-family:'Cormorant Garamond',serif;font-size:1.4rem;font-weight:600;line-height:1.55;color:var(--white)}
.choices{display:grid;gap:.65rem}
.choice{background:var(--navy2);border:1px solid var(--navy4);border-radius:3px;padding:.85rem 1.2rem;cursor:pointer;transition:all .2s;font-size:.72rem;letter-spacing:.1em;color:var(--ghost);display:flex;align-items:center;gap:.9rem;text-align:left;width:100%}
.choice:hover:not(.disabled){background:var(--navy3);border-color:var(--slate);color:var(--white)}
.choice .lbl{font-family:'Cormorant Garamond',serif;font-weight:700;font-size:1rem;color:var(--slate);min-width:18px;flex-shrink:0}
.choice.correct{background:#0b2318;border-color:#2a7a4a;color:#7dd4a8}
.choice.correct .lbl{color:#7dd4a8}
.choice.wrong{background:#240d0d;border-color:#7a2a2a;color:#d48888}
.choice.wrong .lbl{color:#d48888}
.choice.disabled{cursor:default;pointer-events:none}
.next-btn{margin-top:1rem;background:transparent;border:1px solid var(--navy5);color:var(--mist);font-family:'DM Mono',monospace;font-size:.68rem;letter-spacing:.3em;text-transform:uppercase;padding:.75rem 2.5rem;cursor:pointer;transition:all .25s;border-radius:2px;display:none}
.next-btn:hover{background:var(--navy4);border-color:var(--mist);color:var(--white)}
.result-card{background:var(--navy2);border:1px solid var(--navy4);border-radius:3px;padding:2.5rem 2rem;width:100%;max-width:520px;text-align:center}
.result-level{font-size:.58rem;letter-spacing:.4em;color:var(--mist);text-transform:uppercase;margin-bottom:1rem}
.score-big{font-family:'Cormorant Garamond',serif;font-size:5rem;font-weight:700;line-height:1;transition:color .4s}
.score-big .denom{font-size:2.2rem;color:var(--mist)}
.dots{font-size:1rem;letter-spacing:.4rem;margin:.7rem 0 1.5rem}
.quote-block{border-left:2px solid var(--navy4);padding:.8rem 1.2rem;margin:1.4rem 0;text-align:left}
.quote-text{font-family:'Cormorant Garamond',serif;font-style:italic;font-size:1.05rem;line-height:1.65;color:var(--ghost);margin-bottom:.45rem}
.quote-attr{font-size:.58rem;letter-spacing:.22em;color:var(--slate);text-transform:uppercase}
.btn-row{display:flex;gap:.9rem;justify-content:center;margin-top:1.6rem;flex-wrap:wrap}
.btn-fill{background:var(--navy4);border:1px solid var(--slate);color:var(--white);font-family:'DM Mono',monospace;font-size:.65rem;letter-spacing:.25em;text-transform:uppercase;padding:.75rem 1.8rem;cursor:pointer;transition:all .25s;border-radius:2px}
.btn-fill:hover{background:var(--navy5)}
.btn-out{background:transparent;border:1px solid var(--navy4);color:var(--mist);font-family:'DM Mono',monospace;font-size:.65rem;letter-spacing:.25em;text-transform:uppercase;padding:.75rem 1.8rem;cursor:pointer;transition:all .25s;border-radius:2px}
.btn-out:hover{border-color:var(--slate);color:var(--ghost)}
@media(max-width:520px){.level-grid{grid-template-columns:1fr}.logo{font-size:2.6rem}.score-big{font-size:3.5rem}}
</style>
</head>
<body>

<!-- MAIN MENU -->
<div id="menuScreen" class="screen active">
  <div class="logo">QUIZ<span>BEE</span></div>
  <div class="tagline">Intelligence tested — Knowledge revealed</div>
  <div class="rule"></div>
  <div class="level-select-label">Select your level to begin</div>
  <div class="level-grid">
    <div class="level-card easy" onclick="startQuiz('easy')">
      <span class="level-icon">◈</span>
      <div class="level-name" style="color:#7dd4a8">Easy</div>
      <div class="level-desc">Foundational</div>
    </div>
    <div class="level-card medium" onclick="startQuiz('medium')">
      <span class="level-icon">◈ ◈</span>
      <div class="level-name" style="color:var(--gold2)">Medium</div>
      <div class="level-desc">Balanced</div>
    </div>
    <div class="level-card hard" onclick="startQuiz('hard')">
      <span class="level-icon">◈ ◈ ◈</span>
      <div class="level-name" style="color:#d48888">Hard</div>
      <div class="level-desc">Advanced</div>
    </div>
  </div>
</div>

<!-- QUIZ SCREEN -->
<div id="quizScreen" class="screen">
  <div class="quiz-wrap">
    <div class="quiz-meta">
      <div class="quiz-level-tag" id="quizLevelTag">— easy —</div>
      <div class="quiz-counter" id="quizCounter">1 / 10</div>
    </div>
    <div class="progress"><div class="progress-fill" id="progressFill" style="width:10%"></div></div>
    <div class="question-card">
      <div class="q-num" id="qNum">Question 01</div>
      <div class="q-text" id="qText">Loading...</div>
    </div>
    <div class="choices" id="choicesBox"></div>
    <button class="next-btn" id="nextBtn" onclick="nextQuestion()">Continue →</button>
  </div>
</div>

<!-- RESULT SCREEN -->
<div id="resultScreen" class="screen">
  <div class="result-card">
    <div class="result-level" id="resultLevelLabel">Easy · Complete</div>
    <div class="score-big" id="scoreNum">0<span class="denom">/10</span></div>
    <div class="dots" id="dotsDisplay">◈◈◈◈◈◈◈◈◈◈</div>
    <div class="quote-block">
      <div class="quote-text" id="quoteText">"…"</div>
      <div class="quote-attr" id="quoteAttr">—</div>
    </div>
    <div class="btn-row">
      <button class="btn-fill" onclick="retryLevel()">Retry Level</button>
      <button class="btn-out" onclick="goMenu()">← Main Menu</button>
    </div>
  </div>
</div>

<script>
const POOL = {
  easy:[
    {q:"What is 2 + 2?",a:0,c:["4","3","5","6"]},
    {q:"How many sides does a triangle have?",a:0,c:["3","4","5","6"]},
    {q:"What is the boiling point of water at sea level (°C)?",a:1,c:["90","100","80","110"]},
    {q:"Which planet is closest to the Sun?",a:2,c:["Venus","Earth","Mercury","Mars"]},
    {q:"What is the chemical symbol for water?",a:0,c:["H₂O","CO₂","O₂","H₂"]},
    {q:"How many months are in a year?",a:1,c:["10","12","11","13"]},
    {q:"What is the square root of 25?",a:0,c:["5","4","6","7"]},
    {q:"Which animal is called the 'King of the Jungle'?",a:1,c:["Tiger","Lion","Leopard","Cheetah"]},
    {q:"What is 10 × 10?",a:2,c:["10","1000","100","110"]},
    {q:"Which gas do plants absorb from the atmosphere?",a:0,c:["Carbon Dioxide","Oxygen","Nitrogen","Hydrogen"]},
    {q:"How many days are in a week?",a:1,c:["6","7","5","8"]},
    {q:"What is the largest ocean on Earth?",a:2,c:["Atlantic","Indian","Pacific","Arctic"]},
    {q:"How many continents are on Earth?",a:0,c:["7","6","8","5"]},
    {q:"What is 15 − 8?",a:1,c:["6","7","8","9"]},
    {q:"Which planet is known as the Red Planet?",a:0,c:["Mars","Jupiter","Saturn","Venus"]},
    {q:"How many letters are in the English alphabet?",a:2,c:["24","25","26","27"]},
    {q:"What organ pumps blood through the human body?",a:1,c:["Lungs","Heart","Liver","Brain"]},
    {q:"What is the fastest land animal?",a:0,c:["Cheetah","Horse","Lion","Leopard"]},
    {q:"How many zeros are in one thousand?",a:1,c:["2","3","4","1"]},
    {q:"What color do you get mixing blue and yellow?",a:0,c:["Green","Orange","Purple","Brown"]},
  ],
  medium:[
    {q:"What is the value of π to two decimal places?",a:0,c:["3.14","3.41","3.12","3.16"]},
    {q:"Which element has atomic number 1?",a:0,c:["Hydrogen","Helium","Lithium","Carbon"]},
    {q:"What is the powerhouse of the cell?",a:2,c:["Nucleus","Ribosome","Mitochondria","Golgi body"]},
    {q:"Who wrote 'Romeo and Juliet'?",a:1,c:["Dickens","Shakespeare","Tolstoy","Hemingway"]},
    {q:"What is 7 × 8?",a:0,c:["56","54","58","52"]},
    {q:"Which country has the largest population?",a:2,c:["USA","Russia","India","China"]},
    {q:"Approximate speed of light in km/s?",a:1,c:["200,000","300,000","400,000","250,000"]},
    {q:"In which year did the First World War begin?",a:0,c:["1914","1915","1916","1913"]},
    {q:"What is the chemical symbol for gold?",a:2,c:["Gd","Gl","Au","Go"]},
    {q:"Which philosopher wrote 'The Republic'?",a:1,c:["Aristotle","Plato","Socrates","Descartes"]},
    {q:"What is the largest bone in the human body?",a:0,c:["Femur","Tibia","Spine","Humerus"]},
    {q:"Which planet has the most moons?",a:2,c:["Jupiter","Uranus","Saturn","Neptune"]},
    {q:"What is 13 squared?",a:1,c:["156","169","196","144"]},
    {q:"Who developed the theory of general relativity?",a:0,c:["Einstein","Newton","Bohr","Maxwell"]},
    {q:"What is the capital of Australia?",a:2,c:["Sydney","Melbourne","Canberra","Perth"]},
    {q:"Which organ produces insulin?",a:1,c:["Liver","Pancreas","Kidney","Spleen"]},
    {q:"What is the hardest natural substance on Earth?",a:0,c:["Diamond","Quartz","Corundum","Feldspar"]},
    {q:"How many bones are in the adult human body?",a:2,c:["196","198","206","212"]},
    {q:"Who painted the Sistine Chapel ceiling?",a:1,c:["Da Vinci","Michelangelo","Raphael","Botticelli"]},
    {q:"What is the derivative of x² with respect to x?",a:0,c:["2x","x²","x","2x²"]},
  ],
  hard:[
    {q:"What is the Riemann Hypothesis concerned with?",a:2,c:["Prime factorization","Complex integration","Non-trivial zeros of the zeta function","Differential geometry"]},
    {q:"Which quantum number describes the shape of an orbital?",a:1,c:["Principal (n)","Azimuthal (l)","Magnetic (mₗ)","Spin (mₛ)"]},
    {q:"What does Gödel's first incompleteness theorem prove?",a:0,c:["Some truths cannot be proven within the system","All axioms are self-referential","Logic is circular","Infinity has finite bounds"]},
    {q:"What is Boltzmann's entropy formula?",a:2,c:["S = ∫dQ/T","S = kT ln V","S = k ln Ω","S = −k Σpᵢ ln pᵢ"]},
    {q:"Which philosopher declared 'God is dead' in 'The Gay Science'?",a:1,c:["Kierkegaard","Nietzsche","Heidegger","Schopenhauer"]},
    {q:"The Chandrasekhar limit is approximately?",a:0,c:["1.4 solar masses","2.0 solar masses","0.8 solar masses","3.2 solar masses"]},
    {q:"In category theory, a morphism between two functors is called?",a:2,c:["Functor","Adjunction","Natural transformation","Limit"]},
    {q:"Which theorem states every polynomial of degree n has exactly n complex roots?",a:1,c:["Gauss–Bonnet theorem","Fundamental Theorem of Algebra","Cauchy's theorem","Residue theorem"]},
    {q:"The Schrödinger equation describes?",a:0,c:["Time evolution of a quantum state","Relativistic mass-energy equivalence","Thermodynamic entropy","Force between charged particles"]},
    {q:"Epictetus held that happiness depends solely on?",a:2,c:["Virtue and wealth","External circumstances","Our own judgments and will","Divine providence"]},
    {q:"Which experiment demonstrated wave-particle duality of electrons?",a:1,c:["Rutherford scattering","Double-slit experiment","Photoelectric effect","Stern-Gerlach experiment"]},
    {q:"P vs NP asks whether?",a:0,c:["Verifiable problems are efficiently solvable","Quantum computers outperform classical ones","Neural nets generalize perfectly","Algorithms have upper bounds"]},
    {q:"The Heisenberg Uncertainty Principle limits simultaneous precision of?",a:2,c:["Speed of any particle","Energy levels in atoms","Position and momentum","Quantum entanglement range"]},
    {q:"In modal logic, the operator □ represents?",a:1,c:["Possibility","Necessity","Existence","Universality"]},
    {q:"The Planck length (~1.6×10⁻³⁵m) marks?",a:0,c:["Scale where quantum gravity dominates","Smallest measurable wavelength","Radius of an electron","Minimum bond length"]},
    {q:"Kant argued that space and time are?",a:2,c:["Objective cosmic properties","Emergent properties of matter","Forms of human intuition","Constructs of language"]},
    {q:"Noether's theorem connects?",a:1,c:["Gravity and electromagnetism","Symmetries and conservation laws","Entropy and information","Quantum states and probability"]},
    {q:"Which axiom leads to the Banach-Tarski paradox?",a:0,c:["Axiom of Choice","Axiom of Infinity","Axiom of Power Set","Axiom of Regularity"]},
    {q:"The halting problem concludes that?",a:2,c:["All programs eventually halt","Turing machines are incomplete","No algorithm can determine if any program halts","NP requires exponential time"]},
    {q:"In Spinoza's metaphysics, God and Nature are?",a:1,c:["Separate but interacting","One and the same substance","Opposed forces","Unknowable to reason"]},
  ]
};

const QUOTES = {
  high:[
    {t:"The important thing is not to stop questioning. Curiosity has its own reason for existing.",a:"Albert Einstein"},
    {t:"In mathematics the art of proposing a question must be held of higher value than solving it.",a:"Georg Cantor"},
    {t:"Number rules the universe.",a:"Pythagoras"},
    {t:"Pure mathematics is, in its way, the poetry of logical ideas.",a:"Albert Einstein"},
    {t:"The measure of intelligence is the ability to change.",a:"Albert Einstein"},
    {t:"Mathematics is the language with which God has written the universe.",a:"Galileo Galilei"},
  ],
  mid:[
    {t:"It does not matter how slowly you go as long as you do not stop.",a:"Confucius"},
    {t:"We are what we repeatedly do. Excellence, then, is not an act, but a habit.",a:"Aristotle"},
    {t:"He who learns but does not think is lost. He who thinks but does not learn is in great danger.",a:"Confucius"},
    {t:"Make the most of yourself by fanning the tiny, inner sparks of possibility.",a:"Golda Meir"},
    {t:"Strength does not come from winning. Your struggles develop your strengths.",a:"Epictetus"},
  ],
  low:[
    {t:"It is not the man who has too little, but the man who craves more, that is poor.",a:"Seneca"},
    {t:"Failure is simply the opportunity to begin again, this time more intelligently.",a:"Henry Ford"},
    {t:"The only real mistake is the one from which we learn nothing.",a:"Aristotle"},
    {t:"A man who has committed a mistake and doesn't correct it is committing another mistake.",a:"Confucius"},
    {t:"I have not failed. I've just found 10,000 ways that won't work.",a:"Thomas Edison"},
    {t:"Every master was once a disaster.",a:"T. Harv Eker"},
  ]
};

let level='easy', pool=[], idx=0, score=0, answered=false;
let used={easy:[],medium:[],hard:[]};

function shuffle(arr){let a=[...arr];for(let i=a.length-1;i>0;i--){let j=Math.floor(Math.random()*(i+1));[a[i],a[j]]=[a[j],a[i]]}return a}

function pickQuestions(lv){
  let all=POOL[lv];
  let avail=all.map((_,i)=>i).filter(i=>!used[lv].includes(i));
  if(avail.length<10){used[lv]=[];avail=all.map((_,i)=>i)}
  let sel=shuffle(avail).slice(0,10);
  used[lv].push(...sel);
  return sel.map(i=>all[i]);
}

function startQuiz(lv){
  level=lv; pool=pickQuestions(lv); idx=0; score=0; answered=false;
  show('quizScreen'); render();
}

function render(){
  answered=false;
  let q=pool[idx];
  document.getElementById('quizLevelTag').textContent='— '+level+' —';
  document.getElementById('quizCounter').textContent=(idx+1)+' / 10';
  document.getElementById('progressFill').style.width=((idx+1)*10)+'%';
  document.getElementById('qNum').textContent='Question '+(idx<9?'0':'')+(idx+1);
  document.getElementById('qText').textContent=q.q;
  let labels=['A','B','C','D'];
  let box=document.getElementById('choicesBox');
  box.innerHTML='';
  q.c.forEach((ch,i)=>{
    let b=document.createElement('button');
    b.className='choice';
    b.innerHTML='<span class="lbl">'+labels[i]+'</span>'+ch;
    b.onclick=()=>pick(i,q.a);
    box.appendChild(b);
  });
  let nb=document.getElementById('nextBtn');
  nb.style.display='none';
}

function pick(chosen,correct){
  if(answered)return; answered=true;
  let cs=document.querySelectorAll('.choice');
  cs.forEach((c,i)=>{
    c.classList.add('disabled');
    if(i===correct)c.classList.add('correct');
    else if(i===chosen&&chosen!==correct)c.classList.add('wrong');
  });
  if(chosen===correct)score++;
  document.getElementById('nextBtn').style.display='block';
}

function nextQuestion(){
  idx++;
  if(idx>=10){showResult();return;}
  render();
}

function showResult(){
  show('resultScreen');
  let lname=level.charAt(0).toUpperCase()+level.slice(1);
  document.getElementById('resultLevelLabel').textContent=lname+' · Complete';
  document.getElementById('scoreNum').innerHTML=score+'<span class="denom">/10</span>';
  let dots='';
  for(let i=0;i<10;i++)dots+=(i<score?'◈':'◇');
  document.getElementById('dotsDisplay').textContent=dots;
  let pool2, color;
  if(score>=8){pool2=QUOTES.high;color='var(--gold2)'}
  else if(score>=5){pool2=QUOTES.mid;color='var(--mist)'}
  else{pool2=QUOTES.low;color='var(--fog)'}
  document.getElementById('scoreNum').style.color=color;
  let pick2=pool2[Math.floor(Math.random()*pool2.length)];
  document.getElementById('quoteText').textContent='\u201c'+pick2.t+'\u201d';
  document.getElementById('quoteAttr').textContent='\u2014 '+pick2.a;
}

function retryLevel(){startQuiz(level)}
function goMenu(){show('menuScreen')}
function show(id){
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
}
</script>
</body>
</html>
 
