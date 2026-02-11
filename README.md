# lovessoccer15.github.io
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>OCG Concierge Widget</title>

 <style>
  :root{
    --ocg-green: rgb(18, 71, 52);
    --ocg-white: #ffffff;
    --ocg-black: #111111;
    --ocg-muted: rgba(255,255,255,0.82);
    --ocg-shadow: 0 18px 50px rgba(0,0,0,0.25);
    --ocg-radius: 16px;
  }

  /* Expanded widget */
  .ocg-widget{
    position: fixed;
    right: 18px;
    bottom: 18px;
    width: min(390px, calc(100vw - 36px));
    background: var(--ocg-green);
    color: var(--ocg-white);
    border-radius: var(--ocg-radius);
    box-shadow: var(--ocg-shadow);
    z-index: 999999;
    overflow: hidden;

    transform: translateY(12px);
    opacity: 0;
    pointer-events: none;
    transition: opacity 220ms ease, transform 220ms ease;

    font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
  }
  .ocg-widget.is-open{
    opacity: 1;
    transform: translateY(0);
    pointer-events: auto;
  }

  .ocg-widget__header{
    display:flex;
    align-items:flex-start;
    justify-content:space-between;
    gap:10px;
    padding:14px 14px 10px 14px;
    border-bottom: 1px solid rgba(255,255,255,0.18);
  }
  .ocg-widget__title{
    margin:0;
    font-size:14px;
    font-weight:800;
    line-height:1.2;
    color: var(--ocg-white);
  }
  .ocg-widget__subtitle{
    margin:6px 0 0 0;
    font-size:12px;
    line-height:1.35;
    color: var(--ocg-muted);
  }
  .ocg-widget__close{
    border:0;
    background:transparent;
    cursor:pointer;
    padding:6px;
    border-radius:10px;
    line-height:0;
    color: var(--ocg-white);
    font-size: 16px;
  }
  .ocg-widget__close:hover{ background: rgba(255,255,255,0.14); }

  .ocg-widget__body{
    padding:12px 14px 14px 14px;
    display:grid;
    gap:10px;
  }

  /* White buttons + black text (expanded state) */
  .ocg-widget__btn{
    display:flex;
    align-items:center;
    justify-content:space-between;
    gap:10px;
    text-decoration:none;
    padding:12px 12px;
    border-radius:12px;
    background: var(--ocg-white);
    color: var(--ocg-black);
    border: 1px solid rgba(0,0,0,0.08);
    font-size:14px;
    font-weight:750;
    cursor:pointer;
  }
  .ocg-widget__btn:hover{ background: rgba(255,255,255,0.92); }
  .ocg-widget__btn small{
    display:block;
    font-weight:500;
    font-size:12px;
    color: rgba(0,0,0,0.70);
    margin-top:2px;
  }
  .ocg-widget__arrow{
    opacity:.55;
    font-size:16px;
    flex: 0 0 auto;
    color: var(--ocg-black);
  }

  /* Other questions block */
  .ocg-qa{
    display:none; /* toggled by JS */
    margin-top: 2px;
    padding-top: 8px;
    border-top: 1px dashed rgba(255,255,255,0.35);
  }
  .ocg-qa textarea{
    width:100%;
    resize:vertical;
    padding:10px 12px;
    border-radius:12px;
    border:1px solid rgba(0,0,0,0.15);
    font:inherit;
    background: #ffffff;
    color: #111111;
  }
  .ocg-qa__row{
    display:flex;
    gap:10px;
    margin-top: 10px;
  }
  .ocg-qa__primary{
    flex:1;
    padding:10px 12px;
    border-radius:12px;
    border:1px solid rgba(0,0,0,0.12);
    background:#ffffff;
    color:#111111;
    font:inherit;
    cursor:pointer;
    font-weight:800;
  }
  .ocg-qa__primary:hover{ background: rgba(255,255,255,0.92); }

  .ocg-qa__secondary{
    padding:10px 12px;
    border-radius:12px;
    border:1px solid rgba(255,255,255,0.35);
    background: transparent;
    color: #ffffff;
    font:inherit;
    cursor:pointer;
    font-weight:750;
  }
  .ocg-qa__secondary:hover{ background: rgba(255,255,255,0.14); }

  /* Answer / confirm boxes */
  .ocg-qa__box{
    display:none;
    padding:12px;
    border-radius:12px;
    border:1px solid rgba(0,0,0,0.10);
    background:#ffffff;
    margin-top: 10px;
    font-size: 14px;
    line-height: 1.35;
    color: #111111;
  }
  .ocg-qa__box a{ color:#111111; text-decoration: underline; }

  .ocg-qa__ctaRow{
    display:flex;
    gap:10px;
    margin-top:10px;
    flex-wrap: wrap;
  }
  .ocg-qa__cta{
    display:inline-flex;
    align-items:center;
    justify-content:center;
    padding:10px 12px;
    border-radius:12px;
    border:1px solid rgba(0,0,0,0.12);
    background:#111111;
    color:#ffffff;
    text-decoration:none;
    font-weight:800;
    font-size: 13px;
  }
  .ocg-qa__cta:hover{ filter: brightness(1.05); }

  /* Launcher bubble (collapsed state): GREEN + WHITE TEXT */
  .ocg-launcher{
    position: fixed;
    right: 18px;
    bottom: 18px;
    z-index: 999998;
    border: 1px solid rgba(255,255,255,0.25);
    background: var(--ocg-green);  /* rgb(18,71,52) */
    box-shadow: 0 10px 30px rgba(0,0,0,0.22);
    border-radius: 999px;
    padding: 10px 12px;
    cursor: pointer;
    display:none;
    align-items:center;
    gap:8px;
    color: #ffffff;               /* white text */
  }
  .ocg-launcher.is-visible{ display:inline-flex; }
  .ocg-launcher:hover{ filter: brightness(1.05); }
  .ocg-launcher span{ font-size:13px; font-weight:850; color:#ffffff; }
</style>

<!-- Widget HTML -->
<div class="ocg-widget" id="ocgWidget" role="dialog" aria-label="OCG site helper">
  <div class="ocg-widget__header">
    <div>
      <p class="ocg-widget__title">What are you here for?</p>
      <p class="ocg-widget__subtitle">Pick a quick path — we’ll take you there.</p>
    </div>
    <button class="ocg-widget__close" id="ocgWidgetClose" aria-label="Close">✕</button>
  </div>

  <div class="ocg-widget__body">
    <!-- Update hrefs to match your Squarespace page slugs -->
    <a class="ocg-widget__btn" href="/join">
      <div>Join OCG<small>Recruiting cycles + application info</small></div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/client-inquiry">
      <div>Become a client<small>Submit a client inquiry</small></div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/services">
      <div>Services<small>What we can help with</small></div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/project-portfolio">
      <div>Past projects<small>Examples of our work</small></div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/student-inquiry">
      <div>Student inquiry<small>Questions about recruiting / membership</small></div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <button class="ocg-widget__btn" type="button" id="ocgOtherBtn">
      <div>Other questions<small>Ask anything — we’ll match our FAQ bank</small></div>
      <span class="ocg-widget__arrow">›</span>
    </button>

    <div class="ocg-qa" id="ocgQABox">
      <textarea id="ocgQuestionInput" rows="3" placeholder="Type your question…"></textarea>

      <div class="ocg-qa__row">
        <button class="ocg-qa__primary" id="ocgAskBtn" type="button">Ask</button>
        <button class="ocg-qa__secondary" id="ocgCancelAskBtn" type="button">Cancel</button>
      </div>

      <div class="ocg-qa__box" id="ocgAnswerBox"></div>

      <div class="ocg-qa__box" id="ocgConfirmBox">
        <div id="ocgConfirmText" style="margin-bottom:10px;"></div>
        <div class="ocg-qa__row">
          <button class="ocg-qa__primary" id="ocgConfirmYes" type="button">Yes</button>
          <button class="ocg-qa__secondary" id="ocgConfirmNo" type="button">No</button>
        </div>
      </div>
    </div>
  </div>
</div>

<button class="ocg-launcher" id="ocgLauncher" aria-label="Open site helper">💬 <span>Help</span></button>

<script>
(function () {
  try {
    // ---------- Config ----------
    const SHOW_DELAY_MS = 1800;
    const DISMISS_DAYS = 7;
    const STORAGE_KEY = "ocg_concierge_dismissed_until";

    // Fuzzy thresholds: tuned to answer "almost always"
    const THRESH_AUTO = 0.68;        // lower than before to answer more often
    const THRESH_CONFIRM_MIN = 0.55; // confirm more frequently instead of failing

    // Update these to your real Squarespace slugs/URLs:
    const CLIENT_INQUIRY_URL  = "/client-inquiry";
    const STUDENT_INQUIRY_URL = "/student-inquiry";
    const SERVICES_URL        = "/services";
    const PORTFOLIO_URL       = "/project-portfolio";
    const JOIN_URL            = "/join";

    // ---------- Helpers: show/hide ----------
    const $ = (id) => document.getElementById(id);
    const widget     = $("ocgWidget");
    const closeBtn   = $("ocgWidgetClose");
    const launcher   = $("ocgLauncher");
    const otherBtn   = $("ocgOtherBtn");
    const qaBox      = $("ocgQABox");
    const qInput     = $("ocgQuestionInput");
    const askBtn     = $("ocgAskBtn");
    const cancelBtn  = $("ocgCancelAskBtn");
    const answerBox  = $("ocgAnswerBox");
    const confirmBox = $("ocgConfirmBox");
    const confirmText= $("ocgConfirmText");
    const confirmYes = $("ocgConfirmYes");
    const confirmNo  = $("ocgConfirmNo");

    if (!widget || !closeBtn || !launcher || !otherBtn || !qaBox || !qInput || !askBtn || !cancelBtn ||
        !answerBox || !confirmBox || !confirmText || !confirmYes || !confirmNo) return;

    function show(el){ el.style.display = "block"; }
    function hide(el){ el.style.display = "none"; }

    // ---------- Open/Close ----------
    function nowMs(){ return Date.now(); }
    function dismissedUntil(){
      const v = localStorage.getItem(STORAGE_KEY);
      return v ? Number(v) : 0;
    }
    function setDismissed(days){
      localStorage.setItem(STORAGE_KEY, String(nowMs() + days*24*60*60*1000));
    }
    function openWidget(){
      widget.classList.add("is-open");
      launcher.classList.remove("is-visible");
    }
    function closeWidget(){
      widget.classList.remove("is-open");
      launcher.classList.add("is-visible");
      setDismissed(DISMISS_DAYS);
    }
    closeBtn.addEventListener("click", closeWidget);
    launcher.addEventListener("click", openWidget);
    document.addEventListener("keydown", (e) => {
      if (e.key === "Escape" && widget.classList.contains("is-open")) closeWidget();
    });

    // ---------- Homepage-only auto-open ----------
    const isHome =
      window.location.pathname === "/" ||
      window.location.pathname === "/index.html" ||
      document.body.classList.contains("collection-type-index");
    const shouldAutoOpen = isHome && nowMs() > dismissedUntil();
    if (shouldAutoOpen) setTimeout(openWidget, SHOW_DELAY_MS);
    else launcher.classList.add("is-visible");

    // ---------- Other Questions UI ----------
    otherBtn.addEventListener("click", () => {
      const isHidden = window.getComputedStyle(qaBox).display === "none";
      if (isHidden) {
        show(qaBox);
        hide(answerBox);
        hide(confirmBox);
        qInput.value = "";
        setTimeout(() => qInput.focus(), 30);
      } else {
        hide(qaBox);
      }
    });
    cancelBtn.addEventListener("click", () => {
      hide(answerBox);
      hide(confirmBox);
      hide(qaBox);
      qInput.value = "";
    });

    // ---------- Normalization + matching ----------
    function normalize(s){
      return (s || "")
        .toLowerCase()
        .replace(/[\u2019']/g, "'")
        .replace(/[^a-z0-9\s]/g, " ")
        .replace(/\s+/g, " ")
        .trim();
    }
    function tokens(s){
      const t = normalize(s).split(" ").filter(Boolean);
      // remove ultra-common filler words to reduce noise
      const stop = new Set(["the","a","an","to","of","for","and","or","on","in","at","is","are","do","does","did","i","we","you","me","my","our","your","with","about","from","it","this","that","there","here","what","when","where","who","how","can","could","would","should","please"]);
      return t.filter(x => !stop.has(x));
    }
    function bigrams(s){
      const str = " " + s + " ";
      const out = [];
      for(let i=0;i<str.length-1;i++) out.push(str.slice(i,i+2));
      return out;
    }
    function dice(a,b){
      if(!a || !b) return 0;
      if(a === b) return 1;
      const A = bigrams(a);
      const B = bigrams(b);
      const freq = new Map();
      for(const x of A) freq.set(x, (freq.get(x)||0)+1);
      let matches = 0;
      for(const y of B){
        const c = freq.get(y) || 0;
        if(c>0){ freq.set(y, c-1); matches++; }
      }
      return (2*matches)/(A.length+B.length);
    }
    function jaccardTokens(a,b){
      const A = new Set(tokens(a));
      const B = new Set(tokens(b));
      if(!A.size || !B.size) return 0;
      let inter = 0;
      for (const x of A) if (B.has(x)) inter++;
      const union = A.size + B.size - inter;
      return union ? inter/union : 0;
    }

    // ---------- "Excessive" phrase generation ----------
    // Instead of manually listing hundreds of strings per FAQ, we generate them:
    // - Many stems (“what is…”, “tell me about…”, “need help with…”)
    // - Synonyms
    // - Common misspellings for high-value words
    // - Keyword combos
    function expandPhrases(base, opts){
      const stems = opts.stems || [];
      const nouns = opts.nouns || [];
      const verbs = opts.verbs || [];
      const extras = opts.extras || [];
      const miss = opts.misspell || [];
      const combos = new Set();

      // direct base phrases
      for (const p of base) combos.add(normalize(p));

      // stem + noun/verb combos
      for (const s of stems){
        for (const n of nouns){
          combos.add(normalize(`${s} ${n}`));
          combos.add(normalize(`${s} the ${n}`));
          combos.add(normalize(`${s} about ${n}`));
        }
        for (const v of verbs){
          combos.add(normalize(`${s} ${v}`));
          combos.add(normalize(`${s} to ${v}`));
        }
      }

      // noun + verb combos
      for (const n of nouns){
        for (const v of verbs){
          combos.add(normalize(`${n} ${v}`));
          combos.add(normalize(`${v} ${n}`));
          combos.add(normalize(`${n} for ${v}`));
        }
      }

      // extras and misspellings (sprinkled into combos)
      for (const e of extras) combos.add(normalize(e));
      for (const m of miss) combos.add(normalize(m));

      // return as array
      return Array.from(combos).filter(Boolean);
    }

    // Misspelling buckets we reuse a lot
    const MISS = {
      ocg: ["orgn consulting group","oregon consultng group","oregon consuting group","oregon consulting groop","ocg?","ocg info"],
      services: ["servces","serivces","servics","servise","serices"],
      application: ["aplication","applcation","applicaton","aply","aplly","applycation","applciation"],
      recruiting: ["recruting","recruitng","recruitingg","recruitment"],
      portfolio: ["protfolio","portoflio","porfolio","testimonals","case study","case studies"],
      confidentiality: ["confidenciality","confidentality","confidentiality nda","n da","ndaa"],
      pricing: ["prcing","prise","costs","how much $$","priceing"]
    };

    // ---------- FAQ definitions (answers can be edited anytime) ----------
    // IMPORTANT: set the answerHTML to your final copy.
    const RAW_FAQS = [
      {
        id: "what_is_ocg",
        title: "What is OCG?",
        answerHTML: `The Oregon Consulting Group (OCG) is a student-run consulting organization housed in the Lundquist College of Business at the University of Oregon.`,
        basePhrases: ["what is ocg", "what is the oregon consulting group", "about ocg", "tell me about ocg", "who are you"],
        gen: { stems:["what is","tell me about","info on","learn about","who is","explain"], nouns:["ocg","oregon consulting group","the group","your organization","oregon consulting"], verbs:["ocg is","you are","this group is"], extras:["about the oregon consulting group","ocg overview","ocg description"], misspell: MISS.ocg }
      },
      {
        id: "client_types",
        title: "What types of clients do you work with?",
        answerHTML: `We work with a wide range of clients across industries (including nonprofits and larger companies). If you’re unsure fit-wise, submit a client inquiry and we’ll route you.`,
        basePhrases: ["what types of clients do you work with","who do you work with","who are your clients","client types","do you work with nonprofits"],
        gen: { stems:["do you work with","can you work with","what kind of","which types of","who are your"], nouns:["clients","organizations","companies","nonprofits","startups","small businesses","out of state clients"], verbs:["work with","help","support","take projects from"], extras:["are you taking clients","are you accepting clients","client eligibility"], misspell:["cleints","clinet","non profit","nonprofits?"] }
      },
      {
        id: "services",
        title: "What services do you offer?",
        answerHTML: `We offer services spanning business strategy, market analysis, and financial analysis (and custom projects). See details: <a href="${SERVICES_URL}">Services</a>.`,
        basePhrases: ["what services do you offer","services","service offerings","what do you do","what can you help with"],
        gen: { stems:["what services","what do you","can you","do you"], nouns:["services","offerings","capabilities","deliverables","help"], verbs:["offer","provide","do","support"], extras:["business strategy","market analysis","financial analysis","research and analysis","consulting services"], misspell: MISS.services }
      },
      {
        id: "which_service",
        title: "Which service is best for what I need?",
        answerHTML: `If you’re not sure which service fits, that’s normal — we can scope it with you. Submit a client inquiry and we’ll recommend the best path: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["which service is best","what service do i need","help me choose a service","not sure what i need","which offering"],
        gen: { stems:["which","what","help me","can you help me","i need"], nouns:["service","offering","package","option","type of help"], verbs:["choose","pick","decide","select","figure out"], extras:["strategy vs market research","market research vs financial analysis","what should i do","i don't know what to ask for"], misspell:["wich service","servise","help me chuse"] }
      },
      {
        id: "engagement",
        title: "What does a typical engagement look like?",
        answerHTML: `Most engagements include kickoff + scope alignment, research/analysis, check-ins, and a final deliverable presentation with recommendations.`,
        basePhrases: ["typical engagement","what does an engagement look like","project process","kickoff to final deliverable","how does it work"],
        gen: { stems:["what does","how does","walk me through","explain","what is"], nouns:["an engagement","a project","the process","the timeline","kickoff"], verbs:["work","look like","run","happen"], extras:["from kickoff to final deliverable","what are the steps","how do projects run"], misspell:["engagment","engagement proces","kikoff"] }
      },
      {
        id: "timeline",
        title: "How long do projects take?",
        answerHTML: `Timeline depends on scope and academic-term scheduling. Submit an inquiry with your target date and we’ll confirm fit: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["how long do projects take","timeline","timeframe","project length","turnaround time"],
        gen: { stems:["how long","what is the","do projects","typical"], nouns:["timeline","timeframe","turnaround","duration","project length"], verbs:["take","run","last","finish"], extras:["when would this be done","how quickly can you deliver"], misspell:["timline","time line","turn arond"] }
      },
      {
        id: "deliverables",
        title: "What do clients receive at the end of a project?",
        answerHTML: `Deliverables typically include a final presentation and written recommendations, often with supporting research/analysis. If you want a specific format, include it in your inquiry: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["what do clients receive","deliverables","final deliverable","what do i get","end of project"],
        gen: { stems:["what do","do we","will we","what is the"], nouns:["deliverable","deliverables","final presentation","report","recommendations","analysis"], verbs:["receive","get","deliver","provide"], extras:["do you give a slide deck","do we get a report","what outputs do you provide"], misspell:["deliverabels","delivarables","deliverbales"] }
      },
      {
        id: "what_needed",
        title: "What do you need from me to get started?",
        answerHTML: `Usually we need your goal/problem, a point of contact, and any relevant context/data. Start with a client inquiry and we’ll guide you: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["what do you need from me","what do you need to get started","what should i prepare","what information do you need"],
        gen: { stems:["what do you need","what should i","how do i","what do we"], nouns:["to get started","before kickoff","for kickoff","to begin","to start"], verbs:["prepare","send","provide","share"], extras:["do you need data","do you need access","what should i have ready"], misspell:["geting started","preprare","beginn"] }
      },
      {
        id: "become_client",
        title: "How do I become a client or submit a client inquiry?",
        answerHTML: `Submit a client inquiry here: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["how do i become a client","client inquiry","client interest form","work with ocg","hire ocg"],
        gen: { stems:["how do i","where do i","can i","i want to"], nouns:["become a client","submit an inquiry","start a project","work with ocg","hire ocg"], verbs:["apply","submit","start","begin","contact"], extras:["client form","client intake","new client"], misspell:["clinet inquiry","client intrest","hireing"] }
      },
      {
        id: "pricing",
        title: "How much does a project cost? Nonprofit options?",
        answerHTML: `Pricing depends on scope and timing. If you’re a nonprofit, mention that in your inquiry and we’ll discuss options: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["how much does it cost","pricing","cost","price","how much do you charge","nonprofit pricing","discount"],
        gen: { stems:["how much","what is the","tell me the","do you have"], nouns:["pricing","cost","price","rate","fee"], verbs:["charge","cost","run","be"], extras:["do you discount for nonprofits","is there a nonprofit rate","budget","affordable"], misspell: MISS.pricing }
      },
      {
        id: "confidentiality",
        title: "How do you handle confidentiality?",
        answerHTML: `If confidentiality is needed, we can discuss appropriate handling (including NDAs) during onboarding. Start here: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["confidentiality","nda","can you sign an nda","sensitive info","is it confidential"],
        gen: { stems:["how do you","can you","do you"], nouns:["handle confidentiality","keep things confidential","sign an nda","protect data","protect information"], verbs:["handle","protect","secure","keep"], extras:["is my information safe","will you share our data","data privacy"], misspell: MISS.confidentiality }
      },
      {
        id: "scope_unknown",
        title: "Can OCG help even if I don’t know my scope yet?",
        answerHTML: `Yes — we can help you clarify goals and shape a scope on an initial call. Start here: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["i don't know my scope","dont know scope","not sure what i need","help define scope","unclear scope","scope help"],
        gen: { stems:["i don't","im not","i am not","help me"], nouns:["sure what i need","sure on scope","sure what to ask","sure where to start"], verbs:["define scope","figure out scope","shape scope","clarify scope"], extras:["i have a vague idea","i need direction","i have an idea but not sure"], misspell:["scop","scoping help","dont no scope"] }
      },
      {
        id: "remote",
        title: "Do you work with organizations outside Oregon / remote projects?",
        answerHTML: `We can often work with organizations outside Oregon and support remote collaboration depending on fit and timing. Submit a client inquiry to confirm logistics: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["outside oregon","remote projects","do you do remote work","out of state","virtual projects","remote"],
        gen: { stems:["do you","can you","are you able to"], nouns:["work remotely","work outside oregon","work out of state","do virtual projects","do remote presentations"], verbs:["work","support","take","accept"], extras:["we are not in oregon","we are in another state","we are national"], misspell:["remtoe","ouitside oregon","virutal"] }
      },
      {
        id: "portfolio",
        title: "Where can I see examples of past projects or clients?",
        answerHTML: `You can browse examples here: <a href="${PORTFOLIO_URL}">Project Portfolio</a>.`,
        basePhrases: ["examples","past projects","portfolio","case studies","testimonials","previous clients"],
        gen: { stems:["where can i","show me","do you have","can i see"], nouns:["examples","past work","case studies","portfolio","previous projects","testimonials"], verbs:["see","view","read","browse","look at"], extras:["project examples","client examples","past deliverables"], misspell: MISS.portfolio }
      },
      {
        id: "who_contact_general",
        title: "Who should I contact if I’m not sure where my question fits?",
        answerHTML: `If you’re not sure where your question fits, start with the right form and we’ll route you:<div class="ocg-qa__ctaRow"><a class="ocg-qa__cta" href="${CLIENT_INQUIRY_URL}">Client Inquiry</a><a class="ocg-qa__cta" href="${STUDENT_INQUIRY_URL}">Student Inquiry</a></div>`,
        basePhrases: ["who should i contact","who do i email","not sure where my question fits","general questions","contact"],
        gen: { stems:["who do i","where do i","i'm not sure"], nouns:["contact","reach out to","send this to","ask"], verbs:["contact","email","message","reach"], extras:["wrong place","not sure who to ask","who handles this"], misspell:["contcat","emial"] }
      },
      {
        id: "join",
        title: "How do I join OCG?",
        answerHTML: `Recruiting details and how to apply are here: <a href="${JOIN_URL}">Join OCG</a>.`,
        basePhrases: ["how do i join","join ocg","apply to ocg","application","membership","recruiting"],
        gen: { stems:["how do i","where do i","can i","i want to"], nouns:["join ocg","apply","become a member","get involved","join the team"], verbs:["apply","join","get in","sign up"], extras:["membership application","recruiting cycles","how to get in"], misspell: MISS.application.concat(MISS.recruiting) }
      },
      {
        id: "applications_process",
        title: "When do applications open/close and what does the process look like?",
        answerHTML: `Application timing and steps are on the Join page. If you still have questions, use the Student Inquiry form:<div class="ocg-qa__ctaRow"><a class="ocg-qa__cta" href="${JOIN_URL}">Join OCG</a><a class="ocg-qa__cta" href="${STUDENT_INQUIRY_URL}">Student Inquiry</a></div>`,
        basePhrases: ["when do applications open","when do applications close","application deadline","application process","interview process","case interview"],
        gen: { stems:["when do","what is the","how does the","tell me the"], nouns:["application timeline","deadline","process","interview process","case interview"], verbs:["open","close","work","look like"], extras:["recruiting schedule","application dates","what are the steps"], misspell: MISS.application.concat(MISS.recruiting) }
      },
      {
        id: "time_commitment",
        title: "What’s the weekly time commitment for members?",
        answerHTML: `Weekly time commitment varies by role and project, but expect a meaningful weekly workload. For the most accurate expectations, ask via Student Inquiry:<div class="ocg-qa__ctaRow"><a class="ocg-qa__cta" href="${STUDENT_INQUIRY_URL}">Student Inquiry</a><a class="ocg-qa__cta" href="${JOIN_URL}">Join OCG</a></div>`,
        basePhrases: ["weekly time commitment","how many hours","hours per week","time commitment","workload"],
        gen: { stems:["how many","what is the","weekly","typical"], nouns:["hours","hours per week","time commitment","workload"], verbs:["is","are","do you expect","should i plan"], extras:["weekly workload","time per week","commitment level"], misspell:["committment","weekley","hourss"] }
      },
      {
        id: "experience",
        title: "Do I need prior consulting experience / what backgrounds are you looking for?",
        answerHTML: `You don’t necessarily need prior consulting experience — backgrounds vary. The best place for recruiting-specific questions is Student Inquiry:<div class="ocg-qa__ctaRow"><a class="ocg-qa__cta" href="${STUDENT_INQUIRY_URL}">Student Inquiry</a><a class="ocg-qa__cta" href="${JOIN_URL}">Join OCG</a></div>`,
        basePhrases: ["do i need experience","consulting experience","backgrounds","what majors","requirements","qualifications"],
        gen: { stems:["do i need","what","are you","who should"], nouns:["experience","consulting experience","a business background","certain majors","requirements","qualifications"], verbs:["need","look for","prefer","want"], extras:["is consulting experience required","can non business majors apply","what skills do you want"], misspell:["experiance","requriements","qualifcations"] }
      },
      {
        id: "recruiting_contact",
        title: "Who can I contact with recruiting / student questions?",
        answerHTML: `Use the Student Inquiry form and we’ll route your question: <a href="${STUDENT_INQUIRY_URL}">Student Inquiry</a>.`,
        basePhrases: ["recruiting questions","student questions","who do i contact about recruiting","application questions","recruiting contact"],
        gen: { stems:["who do i","where do i","how do i"], nouns:["ask about recruiting","ask about joining","ask about the application","get recruiting info"], verbs:["contact","reach","ask","message"], extras:["student inquiry form","recruiting help","application help"], misspell: MISS.recruiting.concat(MISS.application) }
      }
    ];

    // Build final FAQ bank with “excessive” phrase arrays
    const FAQ_BANK = RAW_FAQS.map(f => ({
      id: f.id,
      title: f.title,
      answerHTML: f.answerHTML,
      phrases: expandPhrases(f.basePhrases, f.gen),
      // keywords help “almost never miss” even if wording is novel
      keywords: tokens(f.title + " " + (f.basePhrases || []).join(" "))
    }));

    // ---------- Rendering ----------
    function renderAnswer(html){
      answerBox.innerHTML = html;
      show(answerBox);
    }

    function renderFallback(){
      renderAnswer(
        `I might not have that one in our FAQ bank yet — but we can route you fast.<div class="ocg-qa__ctaRow">
           <a class="ocg-qa__cta" href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>
           <a class="ocg-qa__cta" href="${STUDENT_INQUIRY_URL}">Student Inquiry</a>
         </div>`
      );
    }

    // ---------- Match function ----------
    // We score each FAQ by:
    // 1) max Dice similarity vs its phrase set
    // 2) token overlap vs its keywords (helps with totally new wording)
    function findBestMatch(userText){
      const qNorm = normalize(userText);
      const qTok = new Set(tokens(userText));
      let best = { score: 0, entry: null };

      for(const entry of FAQ_BANK){
        // phrase similarity
        let phraseBest = 0;
        for(const p of entry.phrases){
          const s = dice(qNorm, p);
          if (s > phraseBest) phraseBest = s;
          if (phraseBest >= 0.92) break; // early exit on near-perfect
        }

        // keyword overlap
        let keyHits = 0;
        for (const k of entry.keywords || []) if (qTok.has(k)) keyHits++;
        const keyScore = (entry.keywords && entry.keywords.length)
          ? (keyHits / Math.max(6, entry.keywords.length)) // normalize lightly
          : 0;

        // combined score (weights tuned to answer aggressively)
        const combined = (phraseBest * 0.78) + (keyScore * 0.22);

        if (combined > best.score) best = { score: combined, entry };
      }
      return best;
    }

    // ---------- Confirm flow ----------
    let pendingMatch = null;

    function showConfirm(best){
      pendingMatch = best;
      hide(answerBox);
      confirmText.innerHTML =
        `Did you mean <strong>${best.entry.title}</strong>?<br>
         <span style="color:#374151; font-size:12px;">(We’re guessing based on similar wording.)</span>`;
      show(confirmBox);
    }

    confirmYes.addEventListener("click", () => {
      if(pendingMatch?.entry?.answerHTML){
        hide(confirmBox);
        renderAnswer(pendingMatch.entry.answerHTML);
      } else {
        hide(confirmBox);
        renderFallback();
      }
      pendingMatch = null;
    });

    confirmNo.addEventListener("click", () => {
      hide(confirmBox);
      renderFallback();
      pendingMatch = null;
    });

    // ---------- Ask behavior ----------
    askBtn.addEventListener("click", () => {
      const raw = qInput.value.trim();
      if(!raw) return;

      hide(answerBox);
      hide(confirmBox);

      const best = findBestMatch(raw);

      if(best.entry && best.score >= THRESH_AUTO){
        renderAnswer(best.entry.answerHTML);
      } else if(best.entry && best.score >= THRESH_CONFIRM_MIN){
        showConfirm(best);
      } else {
        renderFallback();
      }
    });

  } catch (e) {
    console.warn("OCG widget error:", e);
  }
})();
</script>
