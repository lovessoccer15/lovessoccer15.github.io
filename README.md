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

  /* Widget container */
  .ocg-widget{
    position: fixed;
    right: 18px;
    bottom: 18px;
    width: min(380px, calc(100vw - 36px));
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
    font-weight:700;
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

  /* White buttons + black text */
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
    font-weight:650;
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
    font-weight:700;
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
    font-weight:650;
  }
  .ocg-qa__secondary:hover{ background: rgba(255,255,255,0.14); }

  /* Answer / confirm boxes: white for readability */
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

  /* Launcher bubble (collapsed state) */
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
    font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
    color: #ffffff;               /* white text */
  }
  .ocg-launcher.is-visible{ display:inline-flex; }
  .ocg-launcher:hover{ filter: brightness(1.05); }
  .ocg-launcher span{ font-size:13px; font-weight:750; color:#ffffff; }
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
    <!-- Update these hrefs if your Squarespace slugs differ -->
    <a class="ocg-widget__btn" href="/join">
      <div>Join OCG<small>Recruiting cycles + application info</small></div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/client-inquiry">
      <div>Become a client<small>Submit a client inquiry</small></div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/services">
      <div>Services<small>Strategy, market analysis, financial analysis</small></div>
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
      <div>Other questions<small>Ask something — we’ll match our FAQ bank</small></div>
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
    const SHOW_DELAY_MS = 2500; // auto-open delay on homepage
    const DISMISS_DAYS = 7;     // don't auto-open again for X days after close
    const STORAGE_KEY = "ocg_concierge_dismissed_until";

    // Fuzzy match thresholds
    const THRESH_AUTO = 0.80;        // auto-answer above this
    const THRESH_CONFIRM_MIN = 0.70; // confirm between this and auto

    // OPTIONAL: log unmatched questions somewhere (Google Form / endpoint).
    // Leave blank to disable.
    const FALLBACK_FORM_ACTION = "";
    const FALLBACK_FORM_ENTRY  = "";

    // ---------- Links you might want to update ----------
    // If your actual pages differ, change these in ONE place:
    const LINKS = {
      clientInquiry: "/client-inquiry",
      joinPage: "/join",
      services: "/services",
      portfolio: "/project-portfolio",
      studentInquiry: "https://www.oregonconsultinggroup.com/student-inquiry",
      recruitingInfo: "https://www.oregonconsultinggroup.com/join",
      handshakeJob: "https://uoregon.joinhandshake.com/jobs/10704215"
    };

    // ---------- FAQ Bank (hardcoded; based on your draft answers) ----------
    // Source: your draft PDF. :contentReference[oaicite:1]{index=1}
    const FAQ_BANK = [
      {
        id: "what_is_ocg",
        title: "What is OCG?",
        answerHTML: `The Oregon Consulting Group (OCG) is a professionally managed, student-run consulting organization housed in the Lundquist College of Business at the University of Oregon.`,
        phrases: [
          "what is ocg", "what is the oregon consulting group", "what is oregon consulting group",
          "tell me about ocg", "about ocg", "who are you", "oregon consultng group", "oregon consuting group"
        ]
      },
      {
        id: "types_of_clients",
        title: "What types of clients do you work with?",
        answerHTML: `OCG works with all types of clients from many different industries, ranging from non-profits to Fortune 150 companies.`,
        phrases: [
          "what types of clients do you work with", "who do you work with", "who are your clients",
          "do you work with nonprofits", "fortune 150", "client types", "cleints", "clinet"
        ]
      },
      {
        id: "services_offered",
        title: "What services do you offer?",
        answerHTML: `We offer services ranging from business strategy, to market analysis, to financial analysis, and almost anything in between. Have a specific service in mind? Send us a client inquiry to set up an initial meeting: <a href="${LINKS.clientInquiry}">Client Inquiry</a>.`,
        phrases: [
          "what services do you offer", "services", "service offerings", "what do you do",
          "business strategy", "market analysis", "financial analysis", "servces", "serivces"
        ]
      },
      {
        id: "which_service_best",
        title: "Which service is best for what I need?",
        answerHTML: `Every organization faces unique challenges. In addition to our standard service offerings, we offer customized consulting solutions tailored to your specific needs. Send us a client inquiry to set up an initial meeting: <a href="${LINKS.clientInquiry}">Client Inquiry</a>.`,
        phrases: [
          "which service is best", "what service do i need", "help me choose", "not sure what i need",
          "custom consulting", "tailored solution", "wich service"
        ]
      },
      {
        id: "typical_engagement",
        title: "What does a typical engagement look like?",
        answerHTML: `Most projects follow a similar pattern: an introductory meeting and secondary research (background research + case studies), then primary research (often a survey and focus groups/expert interviews), and then the team derives insights and creates a final deliverable to present to the client.`,
        phrases: [
          "typical engagement", "what does a typical engagement look like", "project process",
          "kickoff to deliverable", "how does it work", "engagement process", "engagment"
        ]
      },
      {
        id: "project_length",
        title: "How long do projects take?",
        answerHTML: `Projects run on 10-week schedules.`,
        phrases: [
          "how long do projects take", "project length", "timeline", "timeframe",
          "turnaround time", "timline"
        ]
      },
      {
        id: "end_of_project",
        title: "What do clients receive at the end of a project?",
        answerHTML: `Clients receive a final presentation including all recommendations made by the team and an appendix containing insights from the primary and secondary research. Clients may also receive supplementary deliverables (e.g., a one-pager or marketing mock-ups).`,
        phrases: [
          "what do clients receive", "deliverables", "final presentation", "appendix",
          "what do i get", "end of project", "deliverabels"
        ]
      },
      {
        id: "what_needed_to_start",
        title: "What do you need from me to get started?",
        // This one was missing as a dedicated item in the draft; kept it simple & consistent.
        answerHTML: `To get started, we typically need your goal/problem, a point of contact, and any relevant context or data you can share. The kickoff meeting is where we align scope, timeline, and expectations. Start here: <a href="${LINKS.clientInquiry}">Client Inquiry</a>.`,
        phrases: [
          "what do you need from me", "what do you need to get started", "what should i prepare",
          "what info do you need", "getting started", "requirements to start"
        ]
      },
      {
        id: "become_client",
        title: "How do I become a client?",
        answerHTML: `Submit a client inquiry here: <a href="${LINKS.clientInquiry}">Client Inquiry</a>.`,
        phrases: [
          "how do i become a client", "client inquiry", "be a client", "work with ocg",
          "hire ocg", "client interest form", "clinet inquiry"
        ]
      },
      {
        id: "cost_nonprofit",
        title: "How much does a project cost? Nonprofit options?",
        answerHTML: `For a for-profit company, an OCG project costs $6,000 (plus any professional survey fees if desired). For non-profits, we offer a 50% discount ($3,000).`,
        phrases: [
          "how much does a project cost", "pricing", "cost", "price", "nonprofit pricing",
          "do you discount for nonprofits", "6000", "3000", "pro bono", "prcing"
        ]
      },
      {
        id: "confidentiality",
        title: "How do you handle confidentiality?",
        answerHTML: `If confidentiality is necessary, OCG (through the University of Oregon) can have the project team sign an NDA relating to the project.`,
        phrases: [
          "confidentiality", "nda", "can you sign an nda", "is this confidential",
          "sensitive information", "confidenciality", "confidentality"
        ]
      },
      {
        id: "scope_unknown",
        title: "Can OCG help if I don’t know my scope yet?",
        // Not explicitly listed in the draft as its own Q (you had it in the earlier list),
        // so this is a consistent “yes + route to inquiry”.
        answerHTML: `Yes — that’s common. We can help clarify goals and shape a project scope during an initial conversation. Start here: <a href="${LINKS.clientInquiry}">Client Inquiry</a>.`,
        phrases: [
          "i don't know my scope yet", "dont know scope", "not sure on scope",
          "can you help define scope", "help me scope", "unclear scope", "scope help"
        ]
      },
      {
        id: "remote_outside_oregon",
        title: "Remote / outside Oregon?",
        answerHTML: `While many past clients have been Oregon-based, we’re open to working with organizations across the country. We’ve also done many final presentations remotely.`,
        phrases: [
          "outside oregon", "remote projects", "do you do remote work",
          "out of state", "virtual", "remote presentation", "remotely"
        ]
      },
      {
        id: "examples",
        title: "Where can I see examples of past projects or clients?",
        answerHTML: `You can view examples here: <a href="${LINKS.portfolio}">Project Portfolio</a>.`,
        phrases: [
          "examples of past projects", "past projects", "portfolio", "testimonials",
          "case studies", "previous clients", "project portfolio", "protfolio", "portoflio"
        ]
      },
      {
        id: "where_my_question_fits",
        title: "Who should I contact if I’m not sure where my question fits?",
        answerHTML: `General inquiries: Ben Gilmour (President) at <a href="mailto:bgilmou2@uoregon.edu">bgilmou2@uoregon.edu</a>. Client relations inquiries: Connor Lowery-North (VP of Client Relations) at <a href="mailto:cloweryn@uoregon.edu">cloweryn@uoregon.edu</a>.`,
        phrases: [
          "who should i contact", "who do i contact", "not sure where my question fits",
          "general inquiry", "client relations", "ben gilmour", "connor lowery", "email"
        ]
      },
      {
        id: "join_ocg",
        title: "How do I join OCG?",
        answerHTML: `Fill out our application during our fall, winter, or spring recruiting cycles here: <a href="${LINKS.handshakeJob}" target="_blank" rel="noopener">Handshake Application</a>.`,
        phrases: [
          "how do i join ocg", "join ocg", "apply to ocg", "application", "handshake",
          "recruiting cycles", "joinning ocg", "aplly"
        ]
      },
      {
        id: "applications_open_close",
        title: "When do applications open/close and what’s the process?",
        answerHTML: `Recruiting cycle info is here: <a href="${LINKS.recruitingInfo}">Recruiting Info</a>. The process is: application → (if selected) interviews including a behavioral interview and a case interview.`,
        phrases: [
          "when do applications open", "when do applications close", "application deadline",
          "recruiting timeline", "what is the process", "interview process", "case interview"
        ]
      },
      {
        id: "weekly_time",
        title: "Weekly time commitment?",
        answerHTML: `Members typically spend 10–15 hours per week on their projects.`,
        phrases: [
          "weekly time commitment", "how many hours", "hours per week",
          "time commitment", "workload", "10-15 hours", "10 15 hours"
        ]
      },
      {
        id: "experience_needed",
        title: "Do I need prior consulting experience?",
        answerHTML: `No — you do not need prior consulting experience to be in OCG. We encourage people not from a business background to apply.`,
        phrases: [
          "do i need consulting experience", "experience needed", "what backgrounds",
          "do i need prior experience", "non business major", "requirements", "experiance"
        ]
      },
      {
        id: "recruiting_questions_contact",
        title: "Who can I contact with recruiting / student questions?",
        answerHTML: `Fill out our student inquiry form here: <a href="${LINKS.studentInquiry}" target="_blank" rel="noopener">Student Inquiry</a>.`,
        phrases: [
          "recruiting questions", "student questions", "who do i contact about recruiting",
          "student inquiry", "application questions", "recruting"
        ]
      }
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

    // If any are missing, bail safely
    if (!widget || !closeBtn || !launcher || !otherBtn || !qaBox || !qInput || !askBtn || !cancelBtn ||
        !answerBox || !confirmBox || !confirmText || !confirmYes || !confirmNo) {
      return;
    }

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
      document.body.classList.contains("collection-type-index"); // Squarespace homepages

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
      let best = { score: 0, entry: null };

      for(const entry of FAQ_BANK){
        for(const p of (entry.phrases || [])){
          const score = dice(q, normalize(p));
          if(score > best.score) best = { score, entry };
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
         <span style="color:#374151; font-size:12px;">(We’re guessing based on similar wording.)</span>`;
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
         <span style="color:#374151; font-size:12px;">If it’s urgent, use <a href="${LINKS.clientInquiry}">Client Inquiry</a>.</span>`
      );
      try{ await logUnmatchedQuestion(pendingQuestion); }catch(_){}
      pendingMatch = null;
      pendingQuestion = "";
    });

    // ---------- Ask behavior ----------
    askBtn.addEventListener("click", async () => {
      const raw = qInput.value.trim();
      if(!raw) return;

      hide(answerBox);
      hide(confirmBox);

      const best = findBestMatch(raw);

      if(best.entry && best.score >= THRESH_AUTO){
        renderAnswer(best.entry.answerHTML);
      } else if(best.entry && best.score >= THRESH_CONFIRM_MIN){
        showConfirm(best, raw);
      } else {
        renderAnswer(
          `Thanks — we’ll get back to you. We saved your question for our team to review.<br><br>
           <span style="color:#374151; font-size:12px;">If it’s urgent, use <a href="${LINKS.clientInquiry}">Client Inquiry</a>.</span>`
        );
        try{ await logUnmatchedQuestion(raw); }catch(_){}
      }
    });

  } catch (e) {
    console.warn("OCG widget error:", e);
  }
})();
</script>
