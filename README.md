# lovessoccer15.github.io
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>OCG Concierge Widget</title>

  <style>
    :root{
      --ocg-bg:#ffffff;
      --ocg-border:rgba(0,0,0,.12);
      --ocg-shadow:0 18px 50px rgba(0,0,0,.18);
      --ocg-text:#111827;
      --ocg-muted:#6b7280;
      --ocg-btn:#f3f4f6;
      --ocg-btn-hover:#e5e7eb;
      --ocg-radius:16px;
    }

    /* --- Widget --- */
    .ocg-widget{
      position: fixed;
      right: 18px;
      bottom: 18px;
      width: min(360px, calc(100vw - 36px));
      background: var(--ocg-bg);
      color: var(--ocg-text);
      border: 1px solid var(--ocg-border);
      border-radius: var(--ocg-radius);
      box-shadow: var(--ocg-shadow);
      z-index: 99999;
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
      border-bottom:1px solid rgba(0,0,0,0.08);
    }
    .ocg-widget__title{
      margin:0;
      font-size:14px;
      font-weight:650;
      line-height:1.2;
    }
    .ocg-widget__subtitle{
      margin:6px 0 0 0;
      font-size:12px;
      color:var(--ocg-muted);
      line-height:1.35;
    }
    .ocg-widget__close{
      border:0;
      background:transparent;
      cursor:pointer;
      padding:6px;
      border-radius:10px;
      line-height:0;
    }
    .ocg-widget__close:hover{ background: rgba(0,0,0,.06); }

    .ocg-widget__body{
      padding:12px 14px 14px 14px;
      display:grid;
      gap:10px;
    }

    .ocg-widget__btn{
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:10px;
      text-decoration:none;
      padding:12px 12px;
      border-radius:12px;
      background:var(--ocg-btn);
      color:var(--ocg-text);
      border:1px solid rgba(0,0,0,.08);
      font-size:14px;
      font-weight:560;
      cursor:pointer;
    }
    .ocg-widget__btn:hover{ background:var(--ocg-btn-hover); }
    .ocg-widget__btn small{
      display:block;
      font-weight:500;
      font-size:12px;
      color:var(--ocg-muted);
      margin-top:2px;
    }
    .ocg-widget__arrow{ opacity:.6; font-size:16px; flex: 0 0 auto; }

    /* Other questions block */
    .ocg-qa{
      display:none;
      margin-top: 2px;
      padding-top: 6px;
      border-top: 1px dashed rgba(0,0,0,0.12);
    }
    .ocg-qa textarea{
      width:100%;
      resize:vertical;
      padding:10px 12px;
      border-radius:12px;
      border:1px solid rgba(0,0,0,0.12);
      font:inherit;
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
      background:#111827;
      color:#fff;
      font:inherit;
      cursor:pointer;
    }
    .ocg-qa__secondary{
      padding:10px 12px;
      border-radius:12px;
      border:1px solid rgba(0,0,0,0.12);
      background:#fff;
      font:inherit;
      cursor:pointer;
    }
    .ocg-qa__box{
      display:none;
      padding:12px;
      border-radius:12px;
      border:1px solid rgba(0,0,0,0.10);
      background:#fafafa;
      margin-top: 10px;
      font-size: 14px;
      line-height: 1.35;
    }
    .ocg-qa__box a{ color:#111827; }

    /* Launcher bubble when widget is closed */
    .ocg-launcher{
      position: fixed;
      right: 18px;
      bottom: 18px;
      z-index: 99998;
      border: 1px solid var(--ocg-border);
      background: var(--ocg-bg);
      box-shadow: 0 10px 30px rgba(0,0,0,0.14);
      border-radius: 999px;
      padding: 10px 12px;
      cursor: pointer;
      display:none;
      align-items:center;
      gap:8px;
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
    }
    .ocg-launcher.is-visible{ display:inline-flex; }
    .ocg-launcher:hover{ background:#f9fafb; }
    .ocg-launcher span{ font-size:13px; font-weight:650; }
  </style>
</head>

<body>
  <!-- =========================
       Widget Markup (HTML)
       ========================= -->
  <div class="ocg-widget" id="ocgWidget" role="dialog" aria-label="OCG site helper">
    <div class="ocg-widget__header">
      <div>
        <p class="ocg-widget__title">What are you here for?</p>
        <p class="ocg-widget__subtitle">Pick a quick path — we’ll take you there.</p>
      </div>
      <button class="ocg-widget__close" id="ocgWidgetClose" aria-label="Close">✕</button>
    </div>

    <div class="ocg-widget__body">
      <!-- URL buttons -->
      <a class="ocg-widget__btn" href="/join">
        <div>Apply to OCG<small>Applications, info sessions, recruiting</small></div>
        <span class="ocg-widget__arrow">›</span>
      </a>

      <a class="ocg-widget__btn" href="/contact#client">
        <div>Work with OCG<small>Client interest form & next steps</small></div>
        <span class="ocg-widget__arrow">›</span>
      </a>

      <a class="ocg-widget__btn" href="/services">
        <div>Services<small>Strategy, market research, financial analysis</small></div>
        <span class="ocg-widget__arrow">›</span>
      </a>

      <a class="ocg-widget__btn" href="/project-portfolio">
        <div>Past projects<small>Examples & outcomes</small></div>
        <span class="ocg-widget__arrow">›</span>
      </a>

      <a class="ocg-widget__btn" href="/team-structures">
        <div>About OCG<small>Who we are & how we’re structured</small></div>
        <span class="ocg-widget__arrow">›</span>
      </a>

      <a class="ocg-widget__btn" href="/contact">
        <div>Contact<small>Email + general questions</small></div>
        <span class="ocg-widget__arrow">›</span>
      </a>

      <!-- Other questions toggle -->
      <button class="ocg-widget__btn" type="button" id="ocgOtherBtn">
        <div>Other questions<small>Type a question — we’ll search our FAQ bank</small></div>
        <span class="ocg-widget__arrow">›</span>
      </button>

      <!-- Other questions content -->
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

  <!-- =========================
       Script (JS)
       ========================= -->
  <script>
    (function () {
      // ---------- Config ----------
      const SHOW_DELAY_MS = 2500;   // auto-open delay on homepage
      const DISMISS_DAYS = 7;       // after closing, don't auto-open for X days
      const STORAGE_KEY = "ocg_concierge_dismissed_until";

      // Fuzzy-match thresholds:
      const THRESH_AUTO = 0.80;        // auto-answer above this
      const THRESH_CONFIRM_MIN = 0.70; // ask "Did you mean...?" between confirmMin and auto

      // Optional logging endpoint (Google Form / Formspree / Netlify forms).
      // Leave blank to disable logging.
      const FALLBACK_FORM_ACTION = "";
      const FALLBACK_FORM_ENTRY  = "";

      // ---------- FAQ Bank (hardcoded) ----------
      // Add as many as you want. Put common misspellings in "phrases".
      const FAQ_BANK = [
        {
          id: "services",
          title: "Services",
          answerHTML: `We offer business strategy, market analysis, and financial analysis. See details here: <a href="/services">Services</a>.`,
          phrases: [
            "what services do you offer",
            "services",
            "what do you do",
            "service offerings",
            "servces",
            "serivces",
            "what can ocg help with"
          ]
        },
        {
          id: "apply",
          title: "Applying / Joining",
          answerHTML: `You can apply here: <a href="/join">Join OCG</a>. You’ll also find timelines and recruiting info there.`,
          phrases: [
            "how do i apply",
            "application",
            "join ocg",
            "recruiting",
            "apply",
            "aplly",
            "aply",
            "aplication",
            "when do applications open"
          ]
        },
        {
          id: "client",
          title: "Becoming a Client",
          answerHTML: `Interested in working together? Start here: <a href="/contact#client">Client Inquiry</a>.`,
          phrases: [
            "become a client",
            "work with ocg",
            "client interest form",
            "hire ocg",
            "clinet",
            "cleint",
            "client inquiry",
            "how do we start a project"
          ]
        },
        {
          id: "portfolio",
          title: "Past Projects",
          answerHTML: `See examples of past work here: <a href="/project-portfolio">Project Portfolio</a>.`,
          phrases: [
            "past projects",
            "testimonials",
            "case studies",
            "portfolio",
            "project portfolio",
            "examples of work",
            "prevous projects",
            "previous projects"
          ]
        },
        {
          id: "timeline",
          title: "Project Timeline",
          answerHTML: `Project timing depends on scope, but we can usually align around academic terms. Start here and we’ll follow up: <a href="/contact#client">Client Inquiry</a>.`,
          phrases: [
            "how long do projects take",
            "timeline",
            "project timeline",
            "how fast can you do this",
            "timline",
            "time line"
          ]
        }
        {
    id: "about_ocg",
    title: "What is OCG?",
    answerHTML: `OCG is a student-led consulting organization at the University of Oregon that supports organizations with research and analysis. Learn more here: <a href="/team-structures">About OCG</a>.`,
    phrases: [
      "what is ocg",
      "what is the oregon consulting group",
      "what is oregon consulting group",
      "who are you",
      "about ocg",
      "tell me about ocg",
      "what do you guys do",
      "oregon consultng group",
      "oregon consuting group",
      "oregon consulting groop"
    ]
  },
  {
    id: "client_types",
    title: "Client Types",
    answerHTML: `We work with a range of organizations (often startups, small businesses, and nonprofits). If you’re not sure fit-wise, submit an inquiry and we’ll route you: <a href="/contact#client">Client Inquiry</a>.`,
    phrases: [
      "what types of clients do you work with",
      "who do you work with",
      "who are your clients",
      "do you work with nonprofits",
      "do you work with startups",
      "do you work with small businesses",
      "client types",
      "what kind of orgs",
      "what kind of organizations",
      "clinet types",
      "cleints"
    ]
  },
  {
    id: "services",
    title: "Services",
    answerHTML: `We typically support projects in strategy, market research, and financial analysis. See the full breakdown here: <a href="/services">Services</a>.`,
    phrases: [
      "what services do you offer",
      "services",
      "service offerings",
      "what do you offer",
      "what can you help with",
      "what can ocg do",
      "servces",
      "serivces",
      "servics"
    ]
  },
  {
    id: "which_service",
    title: "Which service should I choose?",
    answerHTML: `If you want direction/priority → strategy. If you need customer/competitor insights → market research. If you need numbers/models/forecasting → financial analysis. More detail: <a href="/services">Services</a>.`,
    phrases: [
      "which service is best",
      "what service do i need",
      "what should i pick",
      "strategy vs market research",
      "market analysis vs strategy",
      "financial analysis vs strategy",
      "help me choose a service",
      "which offering",
      "wich service",
      "best service for me"
    ]
  },
  {
    id: "engagement_process",
    title: "Engagement Process",
    answerHTML: `A typical engagement includes kickoff + scope alignment, research/analysis, periodic check-ins, and a final deliverable presentation with recommendations. Start here: <a href="/contact#client">Client Inquiry</a>.`,
    phrases: [
      "what does an engagement look like",
      "typical engagement",
      "how does it work",
      "process from kickoff to deliverable",
      "project process",
      "how do projects work",
      "what are the steps",
      "engagment process",
      "engagement proces"
    ]
  },
  {
    id: "timeline",
    title: "Project Timeline",
    answerHTML: `Timelines depend on scope and academic-term scheduling, but we’ll align expectations during kickoff. If you share your target date, we’ll advise fit: <a href="/contact#client">Client Inquiry</a>.`,
    phrases: [
      "how long do projects take",
      "timeline",
      "how long is a project",
      "project length",
      "how fast can you do this",
      "timeframe",
      "turnaround time",
      "timline",
      "time line"
    ]
  },
  {
    id: "deliverables",
    title: "End-of-Project Deliverables",
    answerHTML: `Most projects end with a final presentation and a set of recommendations (often with supporting analysis/appendix). Details vary by scope — ask here: <a href="/contact#client">Client Inquiry</a>.`,
    phrases: [
      "what do clients receive",
      "deliverables",
      "final deliverable",
      "what do i get at the end",
      "what do you deliver",
      "do we get a presentation",
      "do we get a report",
      "deliverabels",
      "delivarables"
    ]
  },
  {
    id: "what_you_need",
    title: "What we need from you to start",
    answerHTML: `To start, we typically need a clear problem statement, a point-of-contact, and any relevant context/data you can share. We’ll confirm the rest at kickoff: <a href="/contact#client">Client Inquiry</a>.`,
    phrases: [
      "what do you need from me",
      "what do you need to get started",
      "what information do you need",
      "what should i prepare",
      "client responsibilities",
      "what do i have to provide",
      "getting started requirements",
      "what do you need frm me"
    ]
  },
  {
    id: "become_client",
    title: "Become a Client / Submit Inquiry",
    answerHTML: `You can submit a client inquiry here: <a href="/contact#client">Client Inquiry</a>. If you include your goals + timeline, we can route you faster.`,
    phrases: [
      "how do i become a client",
      "submit a client inquiry",
      "client inquiry",
      "client interest form",
      "work with ocg",
      "hire ocg",
      "be a client",
      "client form",
      "clinet inquiry",
      "client intrest"
    ]
  },
  {
    id: "pricing",
    title: "Project Cost / Nonprofit Options",
    answerHTML: `Pricing depends on scope and timing. If you’re a nonprofit/startup, mention that in your inquiry and we’ll discuss options: <a href="/contact#client">Client Inquiry</a>.`,
    phrases: [
      "how much does a project cost",
      "pricing",
      "cost",
      "price",
      "how much do you charge",
      "is it affordable",
      "nonprofit pricing",
      "pro bono",
      "prcing",
      "how much is it"
    ]
  },
  {
    id: "confidentiality",
    title: "Confidentiality",
    answerHTML: `We take confidentiality seriously. If your project involves sensitive information, mention it in your inquiry and we’ll discuss appropriate handling during kickoff: <a href="/contact#client">Client Inquiry</a>.`,
    phrases: [
      "confidentiality",
      "how do you handle confidentiality",
      "nda",
      "can you sign an nda",
      "sensitive data",
      "is my info private",
      "confidenciality",
      "confidentality"
    ]
  },
  {
    id: "unknown_scope",
    title: "Not sure on scope yet",
    answerHTML: `Yes — if you’re still scoping the problem, we can help clarify objectives and define a reasonable project scope on the first call. Start here: <a href="/contact#client">Client Inquiry</a>.`,
    phrases: [
      "i don't know my scope yet",
      "dont know scope",
      "not sure what i need",
      "can you help define scope",
      "can you help me scope this",
      "help me figure out my project",
      "unclear scope",
      "scope help",
      "scoping"
    ]
  },
  {
    id: "remote_outside_oregon",
    title: "Remote / Outside Oregon",
    answerHTML: `We can often work with organizations outside Oregon and/or collaborate remotely depending on fit and timing. Submit an inquiry and we’ll confirm logistics: <a href="/contact#client">Client Inquiry</a>.`,
    phrases: [
      "do you work outside oregon",
      "out of state clients",
      "remote projects",
      "do you do remote work",
      "virtual projects",
      "non oregon clients",
      "can you work with us remotely",
      "outside of oregon",
      "remotely",
      "remote"
    ]
  },
  {
    id: "portfolio_examples",
    title: "Past Projects / Examples",
    answerHTML: `You can browse examples of past work and clients here: <a href="/project-portfolio">Project Portfolio</a>.`,
    phrases: [
      "examples of past projects",
      "past projects",
      "portfolio",
      "project portfolio",
      "testimonials",
      "case studies",
      "previous clients",
      "who have you worked with",
      "past work",
      "portoflio",
      "protfolio"
    ]
  },
  {
    id: "who_to_contact_general",
    title: "Who should I contact?",
    answerHTML: `If you’re not sure where your question fits, the best move is to use the contact page and we’ll route you: <a href="/contact">Contact</a>.`,
    phrases: [
      "who should i contact",
      "who do i email",
      "not sure who to contact",
      "general questions",
      "who can help me",
      "contact person",
      "best person to reach",
      "who to reach out to",
      "who to talk to"
    ]
  },
  {
    id: "join_ocg",
    title: "Joining OCG",
    answerHTML: `To join, start here: <a href="/join">Join OCG</a>. That page should have the application and recruiting details.`,
    phrases: [
      "how do i join ocg",
      "join",
      "join ocg",
      "apply to ocg",
      "recruiting",
      "membership",
      "become a member",
      "how to join",
      "how do i get in",
      "joinning ocg",
      "aplly to ocg"
    ]
  },
  {
    id: "applications_process",
    title: "Application timeline & process",
    answerHTML: `Recruiting timelines and the application process live here: <a href="/join">Join OCG</a>. If you don’t see dates, use <a href="/contact">Contact</a> and we’ll point you to the latest info.`,
    phrases: [
      "when do applications open",
      "when do applications close",
      "application deadline",
      "recruiting timeline",
      "application process",
      "how does the process work",
      "steps to apply",
      "interview process",
      "aplication timeline",
      "deadlines"
    ]
  },
  {
    id: "time_commitment",
    title: "Weekly time commitment",
    answerHTML: `Weekly commitment varies by role and project load. If you’re applying, check details here: <a href="/join">Join OCG</a> (or ask via <a href="/contact">Contact</a>).`,
    phrases: [
      "weekly time commitment",
      "how many hours",
      "hours per week",
      "time commitment",
      "how much time is ocg",
      "workload",
      "weekly hours",
      "weekley time",
      "time committment"
    ]
  },
  {
    id: "experience_background",
    title: "Experience & backgrounds",
    answerHTML: `You don’t need prior consulting experience to apply — we look for motivated students with strong problem-solving and communication. Details: <a href="/join">Join OCG</a>.`,
    phrases: [
      "do i need prior consulting experience",
      "do i need experience",
      "what backgrounds are you looking for",
      "what majors",
      "who should apply",
      "requirements",
      "qualifications",
      "do i have to be a business major",
      "experience needed",
      "experiance"
    ]
  },
  {
    id: "recruiting_contact",
    title: "Recruiting / student questions contact",
    answerHTML: `For recruiting or student questions, use: <a href="/contact">Contact</a> and mention “recruiting” in your message so it routes correctly.`,
    phrases: [
      "who can i contact with recruiting questions",
      "recruiting contact",
      "student questions",
      "who do i email about joining",
      "application questions",
      "contact for joining",
      "recruting",
      "recruiting email",
      "student contact"
    ]
  }
];
      ];

      // ---------- Elements ----------
      const widget     = document.getElementById("ocgWidget");
      const closeBtn   = document.getElementById("ocgWidgetClose");
      const launcher   = document.getElementById("ocgLauncher");

      const otherBtn   = document.getElementById("ocgOtherBtn");
      const qaBox      = document.getElementById("ocgQABox");
      const qInput     = document.getElementById("ocgQuestionInput");
      const askBtn     = document.getElementById("ocgAskBtn");
      const cancelBtn  = document.getElementById("ocgCancelAskBtn");

      const answerBox  = document.getElementById("ocgAnswerBox");
      const confirmBox = document.getElementById("ocgConfirmBox");
      const confirmText= document.getElementById("ocgConfirmText");
      const confirmYes = document.getElementById("ocgConfirmYes");
      const confirmNo  = document.getElementById("ocgConfirmNo");

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
      const isHome = (window.location.pathname === "/" || window.location.pathname === "/index.html");
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

      function renderAnswer(html){
        answerBox.innerHTML = html;
        show(answerBox);
      }

      // ---------- Fuzzy match (Dice coefficient on bigrams) ----------
      function normalize(s){
        return (s || "")
          .toLowerCase()
          .replace(/[\u2019']/g, "'")
          .replace(/[^a-z0-9\s]/g, " ")
          .replace(/\s+/g, " ")
          .trim();
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
          if(c>0){
            freq.set(y, c-1);
            matches++;
          }
        }
        return (2*matches)/(A.length+B.length);
      }

      function findBestMatch(userText){
        const q = normalize(userText);
        let best = { score: 0, entry: null, phrase: "" };

        for(const entry of FAQ_BANK){
          const phrases = entry.phrases || [];
          for(const p of phrases){
            const score = dice(q, normalize(p));
            if(score > best.score) best = { score, entry, phrase: p };
          }
        }
        return best;
      }

      // ---------- Logging unmatched questions ----------
      async function logUnmatchedQuestion(questionText){
        if(!FALLBACK_FORM_ACTION || !FALLBACK_FORM_ENTRY) return;
        const fd = new FormData();
        fd.append(FALLBACK_FORM_ENTRY, questionText);
        await fetch(FALLBACK_FORM_ACTION, { method:"POST", mode:"no-cors", body: fd });
      }

      // ---------- Confirm flow ----------
      let pendingMatch = null;
      let pendingQuestion = "";

      function showConfirm(best, originalQuestion){
        pendingMatch = best;
        pendingQuestion = originalQuestion;

        hide(answerBox);
        confirmText.innerHTML =
          `Did you mean <strong>${best.entry.title || "this topic"}</strong>?<br>
           <span style="color:#6b7280; font-size:12px;">(We’re guessing based on similar wording.)</span>`;
        show(confirmBox);
      }

      confirmYes.addEventListener("click", () => {
        if(pendingMatch?.entry?.answerHTML){
          hide(confirmBox);
          renderAnswer(pendingMatch.entry.answerHTML);
        }
        pendingMatch = null;
        pendingQuestion = "";
      });

      confirmNo.addEventListener("click", async () => {
        hide(confirmBox);
        renderAnswer(
          `Got it — we’ll get back to you. We saved your question for our team to review.<br><br>
           <span style="color:#6b7280; font-size:12px;">If it’s urgent, use <a href="/contact">Contact</a>.</span>`
        );
        try{ await logUnmatchedQuestion(pendingQuestion); }catch(_){}
        pendingMatch = null;
        pendingQuestion = "";
      });

      // ---------- Ask button ----------
      askBtn.addEventListener("click", async () => {
        const raw = qInput.value.trim();
        if(!raw) return;

        hide(answerBox);
        hide(confirmBox);

        const best = findBestMatch(raw);

        if(best.entry && best.score >= THRESH_AUTO){
          renderAnswer(best.entry.answerHTML);
        }else if(best.entry && best.score >= THRESH_CONFIRM_MIN){
          showConfirm(best, raw);
        }else{
          renderAnswer(
            `Thanks — we’ll get back to you. We saved your question for our team to review.<br><br>
             <span style="color:#6b7280; font-size:12px;">If it’s urgent, use <a href="/contact">Contact</a>.</span>`
          );
          try{ await logUnmatchedQuestion(raw); }catch(_){}
        }
      });

    })();
  </script>
</body>
</html>
