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
    font-weight:850;
    line-height:1.2;
    color: var(--ocg-white);
  }
  /* subtitle removed per request */
  .ocg-widget__subtitle{ display:none; }

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
    font-weight:800;
    cursor:pointer;
  }
  .ocg-widget__btn:hover{ background: rgba(255,255,255,0.92); }
  .ocg-widget__btn small{
    display:block;
    font-weight:600;
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
    font-weight:850;
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
    font-weight:800;
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

  /* CTA base (not used for fallback; keep for any future CTA) */
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
    font-weight:850;
    font-size: 13px;
  }
  .ocg-qa__cta:hover{ filter: brightness(1.05); }

  /* White CTA variant (used for fallback) */
  .ocg-qa__cta--white{
    background:#ffffff;
    color:#111111;
    border:1px solid rgba(0,0,0,0.12);
  }
  .ocg-qa__cta--white:hover{
    background: rgba(255,255,255,0.92);
  }

  /* Launcher bubble (collapsed): GREEN + WHITE TEXT */
  .ocg-launcher{
    position: fixed;
    right: 18px;
    bottom: 18px;
    z-index: 999998;
    border: 1px solid rgba(255,255,255,0.25);
    background: var(--ocg-green);
    box-shadow: 0 10px 30px rgba(0,0,0,0.22);
    border-radius: 999px;
    padding: 10px 12px;
    cursor: pointer;
    display:none;
    align-items:center;
    gap:8px;
    color: #ffffff;
  }
  .ocg-launcher.is-visible{ display:inline-flex; }
  .ocg-launcher:hover{ filter: brightness(1.05); }
  .ocg-launcher span{ font-size:13px; font-weight:900; color:#ffffff; }
</style>

<!-- Widget HTML -->
<div class="ocg-widget" id="ocgWidget" role="dialog" aria-label="OCG site helper">
  <div class="ocg-widget__header">
    <div>
      <p class="ocg-widget__title">What are you here for?</p>
      <!-- subtitle removed -->
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
      <div>Other questions<small>Ask us anything!</small></div>
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

    // Aggressive matching so questions are almost always answered
    const THRESH_AUTO = 0.68;
    const THRESH_CONFIRM_MIN = 0.55;

    // Update these to your real Squarespace slugs/URLs:
    const CLIENT_INQUIRY_URL  = "/client-inquiry";
    const STUDENT_INQUIRY_URL = "/student-inquiry";
    const SERVICES_URL        = "/services";
    const PORTFOLIO_URL       = "/project-portfolio";
    const JOIN_URL            = "/join";

    // ---------- Elements ----------
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

    // ---------- Phrase generation (excessive) ----------
    function expandPhrases(base, opts){
      const stems = opts.stems || [];
      const nouns = opts.nouns || [];
      const verbs = opts.verbs || [];
      const extras = opts.extras || [];
      const miss = opts.misspell || [];
      const combos = new Set();

      for (const p of base) combos.add(normalize(p));

      for (const s of stems){
        for (const n of nouns){
          combos.add(normalize(`${s} ${n}`));
          combos.add(normalize(`${s} the ${n}`));
          combos.add(normalize(`${s} about ${n}`));
          combos.add(normalize(`${s}: ${n}`));
          combos.add(normalize(`${s} - ${n}`));
        }
        for (const v of verbs){
          combos.add(normalize(`${s} ${v}`));
          combos.add(normalize(`${s} to ${v}`));
          combos.add(normalize(`${s} how to ${v}`));
        }
      }

      for (const n of nouns){
        for (const v of verbs){
          combos.add(normalize(`${n} ${v}`));
          combos.add(normalize(`${v} ${n}`));
          combos.add(normalize(`${n} for ${v}`));
          combos.add(normalize(`${v} for ${n}`));
        }
        combos.add(normalize(`${n}?`));
        combos.add(normalize(`help with ${n}`));
        combos.add(normalize(`question about ${n}`));
      }

      for (const e of extras) combos.add(normalize(e));
      for (const m of miss) combos.add(normalize(m));

      // Add single-token fallbacks so short queries still match
      for (const n of nouns){
        const t = tokens(n);
        for (const tok of t) combos.add(normalize(tok));
      }

      return Array.from(combos).filter(Boolean);
    }

    const MISS = {
      ocg: ["orgn consulting group","oregon consultng group","oregon consuting group","oregon consulting groop","ocg info","ocg?","about ocg?"],
      services: ["servces","serivces","servics","servise","serices","servicez","servises"],
      application: ["aplication","applcation","applicaton","aply","aplly","applycation","applciation","aplication deadline","aplication process"],
      recruiting: ["recruting","recruitng","recruitingg","recruitment","recrutment","recruting cycle"],
      portfolio: ["protfolio","portoflio","porfolio","testimonals","testimonials","case study","case studies","past work","past projects"],
      confidentiality: ["confidenciality","confidentality","confidentiality nda","n da","ndaa","nda form","nda agreement"],
      pricing: ["prcing","prise","costs","how much $$","priceing","costing","fees","rate","rates"]
    };

    // ---------- FAQs (edit answers anytime) ----------
    const RAW_FAQS = [
      {
        id: "what_is_ocg",
        title: "What is OCG?",
        answerHTML: `The Oregon Consulting Group (OCG) is a student-run consulting organization housed in the Lundquist College of Business at the University of Oregon.`,
        basePhrases: ["what is ocg", "what is the oregon consulting group", "about ocg", "tell me about ocg", "who are you", "ocg"],
        gen: { stems:["what is","tell me about","info on","learn about","who is","explain","define"], nouns:["ocg","oregon consulting group","oregon consulting","your group","your organization","consulting group"], verbs:["ocg is","you are","this group is","you do"], extras:["ocg overview","ocg description","ocg details"], misspell: MISS.ocg }
      },
      {
        id: "client_types",
        title: "What types of clients do you work with?",
        answerHTML: `We work with a wide range of clients across industries (including nonprofits and larger companies). If you’re unsure fit-wise, submit a client inquiry: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["what types of clients do you work with","who do you work with","who are your clients","client types","do you work with nonprofits","do you work with startups","clients"],
        gen: { stems:["do you work with","can you work with","what kind of","which types of","who are your","are you open to"], nouns:["clients","organizations","companies","nonprofits","startups","small businesses","fortune companies","big companies"], verbs:["work with","help","support","take projects from","accept"], extras:["are you accepting clients","are you taking new clients","client eligibility","who can be a client"], misspell:["cleints","clinet","non profit","nonprofits?","orgs"] }
      },
      {
        id: "services",
        title: "What services do you offer?",
        answerHTML: `We offer services spanning business strategy, market analysis, and financial analysis (and custom projects). See details: <a href="${SERVICES_URL}">Services</a>.`,
        basePhrases: ["what services do you offer","services","service offerings","what do you do","what can you help with","capabilities"],
        gen: { stems:["what services","what do you","can you","do you","tell me"], nouns:["services","offerings","capabilities","deliverables","help","support","consulting services"], verbs:["offer","provide","do","support","help"], extras:["business strategy","market analysis","financial analysis","research","analysis","strategy work","market research","financial modeling"], misspell: MISS.services }
      },
      {
        id: "which_service",
        title: "Which service is best for what I need?",
        answerHTML: `If you’re not sure which service fits, that’s normal — we can scope it with you. Submit a client inquiry and we’ll recommend a path: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["which service is best","what service do i need","help me choose a service","not sure what i need","which offering","which option"],
        gen: { stems:["which","what","help me","can you help me","i need","i want"], nouns:["service","offering","package","option","type of help","best service"], verbs:["choose","pick","decide","select","figure out","recommend"], extras:["strategy vs market research","market research vs financial analysis","what should i do","i don't know what to ask for"], misspell:["wich service","servise","help me chuse","recomend"] }
      },
      {
        id: "engagement",
        title: "What does a typical engagement look like?",
        answerHTML: `Most engagements include kickoff + scope alignment, research/analysis, check-ins, and a final deliverable presentation with recommendations.`,
        basePhrases: ["typical engagement","what does an engagement look like","project process","kickoff to final deliverable","how does it work","steps"],
        gen: { stems:["what does","how does","walk me through","explain","what is","describe"], nouns:["an engagement","a project","the process","the workflow","kickoff","timeline"], verbs:["work","look like","run","happen","go"], extras:["from kickoff to final deliverable","what are the steps","how do projects run","what happens during a project"], misspell:["engagment","engagement proces","kikoff","delivarable"] }
      },
      {
        id: "timeline",
        title: "How long do projects take?",
        answerHTML: `Timeline depends on scope and academic-term scheduling. Submit an inquiry with your target date and we’ll confirm fit: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["how long do projects take","timeline","timeframe","project length","turnaround time","duration"],
        gen: { stems:["how long","what is the","do projects","typical","average"], nouns:["timeline","timeframe","turnaround","duration","project length","project timing"], verbs:["take","run","last","finish","be"], extras:["when would this be done","how quickly can you deliver","how fast can you do it"], misspell:["timline","time line","turn arond","duraton"] }
      },
      {
        id: "deliverables",
        title: "What do clients receive at the end of a project?",
        answerHTML: `Deliverables typically include a final presentation and written recommendations, often with supporting research/analysis. If you want a specific format, include it in your inquiry: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["what do clients receive","deliverables","final deliverable","what do i get","end of project","output","report"],
        gen: { stems:["what do","do we","will we","what is the","what are the"], nouns:["deliverable","deliverables","final presentation","report","recommendations","analysis","slide deck","deck","appendix"], verbs:["receive","get","deliver","provide","include"], extras:["do you give a slide deck","do we get a report","what outputs do you provide","what is included"], misspell:["deliverabels","delivarables","deliverbales","slidedeck","recomendations"] }
      },
      {
        id: "what_needed",
        title: "What do you need from me to get started?",
        answerHTML: `Usually we need your goal/problem, a point of contact, and any relevant context/data. Start with a client inquiry and we’ll guide you: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["what do you need from me","what do you need to get started","what should i prepare","what information do you need","getting started"],
        gen: { stems:["what do you need","what should i","how do i","what do we","do you need"], nouns:["to get started","before kickoff","for kickoff","to begin","to start","to move forward"], verbs:["prepare","send","provide","share","give"], extras:["do you need data","do you need access","what should i have ready","what do you need from us"], misspell:["geting started","preprare","beginn","provid"] }
      },
      {
        id: "become_client",
        title: "How do I become a client or submit a client inquiry?",
        answerHTML: `Submit a client inquiry here: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["how do i become a client","client inquiry","client interest form","work with ocg","hire ocg","client form","become client"],
        gen: { stems:["how do i","where do i","can i","i want to","looking to"], nouns:["become a client","submit an inquiry","start a project","work with ocg","hire ocg","contact you","get a proposal"], verbs:["apply","submit","start","begin","contact","inquire"], extras:["client intake","new client","client application","how to hire"], misspell:["clinet inquiry","client intrest","hireing","inqury"] }
      },
      {
        id: "pricing",
        title: "How much does a project cost? Nonprofit options?",
        answerHTML: `Pricing depends on scope and timing. If you’re a nonprofit, mention that in your inquiry and we’ll discuss options: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["how much does it cost","pricing","cost","price","how much do you charge","nonprofit pricing","discount","fees","rates"],
        gen: { stems:["how much","what is the","tell me the","do you have","what are your"], nouns:["pricing","cost","price","rate","fee","fees","rates","budget"], verbs:["charge","cost","run","be","cost"], extras:["do you discount for nonprofits","is there a nonprofit rate","what is the price","do you have pricing"], misspell: MISS.pricing }
      },
      {
        id: "confidentiality",
        title: "How do you handle confidentiality?",
        answerHTML: `If confidentiality is needed, we can discuss appropriate handling (including NDAs) during onboarding. Start here: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["confidentiality","nda","can you sign an nda","sensitive info","is it confidential","privacy","confidential"],
        gen: { stems:["how do you","can you","do you","is it"], nouns:["handle confidentiality","keep things confidential","sign an nda","protect data","protect information","data privacy","privacy"], verbs:["handle","protect","secure","keep","sign"], extras:["is my information safe","will you share our data","can we do an nda","nda agreement"], misspell: MISS.confidentiality }
      },
      {
        id: "scope_unknown",
        title: "Can OCG help even if I don’t know my scope yet?",
        answerHTML: `Yes — we can help you clarify goals and shape a scope on an initial call. Start here: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["i don't know my scope","dont know scope","not sure what i need","help define scope","unclear scope","scope help","not sure where to start"],
        gen: { stems:["i don't","im not","i am not","help me","can you"], nouns:["sure what i need","sure on scope","sure what to ask","sure where to start","sure what to do"], verbs:["define scope","figure out scope","shape scope","clarify scope","scope it"], extras:["i have a vague idea","i need direction","i have an idea but not sure","need help scoping"], misspell:["scop","scopping","dont no scope","shpae scope"] }
      },
      {
        id: "remote",
        title: "Do you work with organizations outside Oregon / remote projects?",
        answerHTML: `We can often work with organizations outside Oregon and support remote collaboration depending on fit and timing. Submit a client inquiry to confirm logistics: <a href="${CLIENT_INQUIRY_URL}">Client Inquiry</a>.`,
        basePhrases: ["outside oregon","remote projects","do you do remote work","out of state","virtual projects","remote","virtual"],
        gen: { stems:["do you","can you","are you able to","will you"], nouns:["work remotely","work outside oregon","work out of state","do virtual projects","do remote presentations","work online"], verbs:["work","support","take","accept","collaborate"], extras:["we are not in oregon","we are in another state","we are national","remote engagement"], misspell:["remtoe","ouitside oregon","virutal","outa state"] }
      },
      {
        id: "portfolio",
        title: "Where can I see examples of past projects or clients?",
        answerHTML: `You can browse examples here: <a href="${PORTFOLIO_URL}">Project Portfolio</a>.`,
        basePhrases: ["examples","past projects","portfolio","case studies","testimonials","previous clients","past work","work examples"],
        gen: { stems:["where can i","show me","do you have","can i see","link to"], nouns:["examples","past work","case studies","portfolio","previous projects","testimonials","past clients","projects"], verbs:["see","view","read","browse","look at","check out"], extras:["project examples","client examples","past deliverables","where is your portfolio"], misspell: MISS.portfolio }
      },
      {
        id: "who_contact_general",
        title: "Who should I contact if I’m not sure where my question fits?",
        answerHTML: `If you’re not sure where your question fits, please use our student inquiry form and we’ll route you: <a href="${STUDENT_INQUIRY_URL}">Student Inquiry</a>.`,
        basePhrases: ["who should i contact","who do i email","not sure where my question fits","general questions","contact","help"],
        gen: { stems:["who do i","where do i","i'm not sure","not sure"], nouns:["contact","reach out to","send this to","ask","message"], verbs:["contact","email","message","reach","ask"], extras:["wrong place","not sure who to ask","who handles this","who can help"], misspell:["contcat","emial","mesage"] }
      },
      {
        id: "join",
        title: "How do I join OCG?",
        answerHTML: `Recruiting details and how to apply are here: <a href="${JOIN_URL}">Join OCG</a>.`,
        basePhrases: ["how do i join","join ocg","apply to ocg","application","membership","recruiting","join"],
        gen: { stems:["how do i","where do i","can i","i want to","looking to"], nouns:["join ocg","apply","become a member","get involved","join the team","join the club"], verbs:["apply","join","get in","sign up","recruit"], extras:["membership application","recruiting cycles","how to get in","join application"], misspell: MISS.application.concat(MISS.recruiting) }
      },
      {
        id: "applications_process",
        title: "When do applications open/close and what does the process look like?",
        answerHTML: `Application timing and steps are on the Join page. If you still have questions, use the Student Inquiry form: <a href="${STUDENT_INQUIRY_URL}">Student Inquiry</a>.`,
        basePhrases: ["when do applications open","when do applications close","application deadline","application process","interview process","case interview","deadline","open applications"],
        gen: { stems:["when do","what is the","how does the","tell me the","where is the"], nouns:["application timeline","deadline","process","interview process","case interview","recruiting schedule","dates"], verbs:["open","close","work","look like","start","end"], extras:["recruiting schedule","application dates","what are the steps","when can i apply"], misspell: MISS.application.concat(MISS.recruiting) }
      },
      {
        id: "time_commitment",
        title: "What’s the weekly time commitment for members?",
        answerHTML: `Weekly time commitment varies by role and project. For the most accurate expectations, ask via Student Inquiry: <a href="${STUDENT_INQUIRY_URL}">Student Inquiry</a>.`,
        basePhrases: ["weekly time commitment","how many hours","hours per week","time commitment","workload","weekly hours"],
        gen: { stems:["how many","what is the","weekly","typical","average"], nouns:["hours","hours per week","time commitment","workload","weekly workload","weekly hours"], verbs:["is","are","do you expect","should i plan","do members spend"], extras:["weekly workload","time per week","commitment level","how busy is it"], misspell:["committment","weekley","hourss","work loadd"] }
      },
      {
        id: "experience",
        title: "Do I need prior consulting experience / what backgrounds are you looking for?",
        answerHTML: `Backgrounds vary and prior consulting experience isn’t always required. For recruiting-specific questions, use Student Inquiry: <a href="${STUDENT_INQUIRY_URL}">Student Inquiry</a>.`,
        basePhrases: ["do i need experience","consulting experience","backgrounds","what majors","requirements","qualifications","do i need to be business major"],
        gen: { stems:["do i need","what","are you","who should","can"], nouns:["experience","consulting experience","a business background","certain majors","requirements","qualifications","skills"], verbs:["need","look for","prefer","want","require"], extras:["is consulting experience required","can non business majors apply","what skills do you want","what are you looking for"], misspell:["experiance","requriements","qualifcations","majorss"] }
      },
      {
        id: "recruiting_contact",
        title: "Who can I contact with recruiting / student questions?",
        answerHTML: `Please use our student inquiry form and we’ll get back to you soon: <a href="${STUDENT_INQUIRY_URL}">Student Inquiry</a>.`,
        basePhrases: ["recruiting questions","student questions","who do i contact about recruiting","application questions","recruiting contact","who can i email"],
        gen: { stems:["who do i","where do i","how do i","can i"], nouns:["ask about recruiting","ask about joining","ask about the application","get recruiting info","ask a question"], verbs:["contact","reach","ask","message","email"], extras:["student inquiry form","recruiting help","application help","who to contact"], misspell: MISS.recruiting.concat(MISS.application) }
      }
    ];

    // Build final FAQ bank with HUGE phrase arrays
    const FAQ_BANK = RAW_FAQS.map(f => ({
      id: f.id,
      title: f.title,
      answerHTML: f.answerHTML,
      phrases: expandPhrases(f.basePhrases, f.gen),
      keywords: tokens(f.title + " " + (f.basePhrases || []).join(" "))
    }));

    // ---------- Rendering ----------
    function renderAnswer(html){
      answerBox.innerHTML = html;
      show(answerBox);
    }

    // Student-inquiry-only fallback (per request)
    function renderFallback(){
      renderAnswer(
        `I don't have an answer for you right now, but please ask using our student inquiry form and we'll get back to you soon.
         <div class="ocg-qa__ctaRow">
           <a class="ocg-qa__cta ocg-qa__cta--white" href="${STUDENT_INQUIRY_URL}">Student Inquiry</a>
         </div>`
      );
    }

    // ---------- Match function ----------
    function findBestMatch(userText){
      const qNorm = normalize(userText);
      const qTok = new Set(tokens(userText));
      let best = { score: 0, entry: null };

      for(const entry of FAQ_BANK){
        let phraseBest = 0;

        // Phrase similarity (scan many generated phrases)
        for(const p of entry.phrases){
          const s = dice(qNorm, p);
          if (s > phraseBest) phraseBest = s;
          if (phraseBest >= 0.92) break;
        }

        // Keyword overlap (helps with totally new wording)
        let keyHits = 0;
        for (const k of entry.keywords || []) if (qTok.has(k)) keyHits++;
        const keyScore = (entry.keywords && entry.keywords.length)
          ? (keyHits / Math.max(6, entry.keywords.length))
          : 0;

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
})();
</script>
