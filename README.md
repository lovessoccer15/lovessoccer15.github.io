# lovessoccer15.github.io
{
  "thresholds": {
    "autoAnswer": 0.80,
    "confirmMin": 0.70
  },
  "faqs": [
    {
      "id": "services",
      "title": "Services",
      "answerHTML": "We offer business strategy, market analysis, and financial analysis. See details here: <a href=\"/services\">Services</a>.",
      "phrases": [
        "what services do you offer",
        "services",
        "what do you do",
        "service offerings",
        "servces",
        "serivces"
      ]
    },
    {
      "id": "apply",
      "title": "Applying / Joining",
      "answerHTML": "You can apply here: <a href=\"/join\">Join OCG</a>. You’ll also find timelines and recruiting info there.",
      "phrases": [
        "how do i apply",
        "application",
        "join ocg",
        "recruiting",
        "apply",
        "aplly",
        "aply",
        "aplication"
      ]
    },
    {
      "id": "client",
      "title": "Becoming a Client",
      "answerHTML": "Interested in working together? Start here: <a href=\"/contact#client\">Client Inquiry</a>.",
      "phrases": [
        "become a client",
        "work with ocg",
        "client interest form",
        "hire ocg",
        "clinet",
        "cleint"
      ]
    }
  ]
}
<!-- OCG Concierge Widget (no vendor, no AI) -->
<style>
  :root {
    --ocg-widget-bg: #ffffff;
    --ocg-widget-border: rgba(0,0,0,0.12);
    --ocg-widget-shadow: 0 18px 50px rgba(0,0,0,0.18);
    --ocg-widget-text: #111827;
    --ocg-widget-muted: #6b7280;
    --ocg-widget-btn-bg: #f3f4f6;
    --ocg-widget-btn-hover: #e5e7eb;
    --ocg-widget-radius: 16px;
  }

  .ocg-widget {
    position: fixed;
    right: 18px;
    bottom: 18px;
    width: min(360px, calc(100vw - 36px));
    background: var(--ocg-widget-bg);
    color: var(--ocg-widget-text);
    border: 1px solid var(--ocg-widget-border);
    border-radius: var(--ocg-widget-radius);
    box-shadow: var(--ocg-widget-shadow);
    z-index: 99999;
    overflow: hidden;
    transform: translateY(12px);
    opacity: 0;
    pointer-events: none;
    transition: opacity 220ms ease, transform 220ms ease;
    font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
  }
  .ocg-widget.is-open {
    opacity: 1;
    transform: translateY(0);
    pointer-events: auto;
  }

  .ocg-widget__header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 10px;
    padding: 14px 14px 10px 14px;
    border-bottom: 1px solid rgba(0,0,0,0.08);
  }
  .ocg-widget__title {
    margin: 0;
    font-size: 14px;
    font-weight: 650;
    line-height: 1.2;
  }
  .ocg-widget__subtitle {
    margin: 6px 0 0 0;
    font-size: 12px;
    color: var(--ocg-widget-muted);
    line-height: 1.35;
  }
  .ocg-widget__close {
    border: 0;
    background: transparent;
    cursor: pointer;
    padding: 6px;
    border-radius: 10px;
    line-height: 0;
  }
  .ocg-widget__close:hover {
    background: rgba(0,0,0,0.06);
  }

  .ocg-widget__body {
    padding: 12px 14px 14px 14px;
    display: grid;
    gap: 10px;
  }

  .ocg-widget__btn {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10px;
    text-decoration: none;
    padding: 12px 12px;
    border-radius: 12px;
    background: var(--ocg-widget-btn-bg);
    color: var(--ocg-widget-text);
    border: 1px solid rgba(0,0,0,0.08);
    font-size: 14px;
    font-weight: 560;
  }
  .ocg-widget__btn:hover {
    background: var(--ocg-widget-btn-hover);
  }
  .ocg-widget__btn small {
    display: block;
    font-weight: 500;
    font-size: 12px;
    color: var(--ocg-widget-muted);
    margin-top: 2px;
  }
  .ocg-widget__arrow {
    opacity: 0.6;
    font-size: 16px;
  }

  /* Optional: little launcher button when widget is closed */
  .ocg-launcher {
    position: fixed;
    right: 18px;
    bottom: 18px;
    z-index: 99998;
    border: 1px solid var(--ocg-widget-border);
    background: var(--ocg-widget-bg);
    box-shadow: 0 10px 30px rgba(0,0,0,0.14);
    border-radius: 999px;
    padding: 10px 12px;
    cursor: pointer;
    font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
    display: none;
  }
  .ocg-launcher.is-visible { display: inline-flex; align-items: center; gap: 8px; }
  .ocg-launcher:hover { background: #f9fafb; }
  .ocg-launcher span { font-size: 13px; font-weight: 650; }
</style>

<div class="ocg-widget" id="ocgWidget" role="dialog" aria-label="OCG site helper">
  <div class="ocg-widget__header">
    <div>
      <p class="ocg-widget__title">What are you here for?</p>
      <p class="ocg-widget__subtitle">Pick a quick path — we’ll take you there.</p>
    </div>
    <button class="ocg-widget__close" id="ocgWidgetClose" aria-label="Close">
      ✕
    </button>
  </div>

  <div class="ocg-widget__body">
    <a class="ocg-widget__btn" href="/join">
      <div>
        Apply to OCG
        <small>Applications, info sessions, recruiting</small>
      </div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/contact#client">
      <div>
        Work with OCG
        <small>Client interest form & next steps</small>
      </div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/services">
      <div>
        Services
        <small>Strategy, market research, financial analysis</small>
      </div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/project-portfolio">
      <div>
        Past projects
        <small>Examples & outcomes</small>
      </div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/team-structures">
      <div>
        About OCG
        <small>How we’re structured & what we do</small>
      </div>
      <span class="ocg-widget__arrow">›</span>
    </a>

    <a class="ocg-widget__btn" href="/contact">
      <div>
        Contact
        <small>Email + general questions</small>
      </div>
      <span class="ocg-widget__arrow">›</span>
    </a>
  </div>
</div>
<button class="ocg-widget__btn" type="button" id="ocgOtherBtn">
  <div>
    Other questions
    <small>Ask something quick — we’ll point you to an answer</small>
  </div>
  <span class="ocg-widget__arrow">›</span>
</button>

<div id="ocgQABox" style="display:none; margin-top: 6px;">
  <div style="display:grid; gap:10px;">
    <textarea id="ocgQuestionInput" rows="3" placeholder="Type your question…"
      style="width:100%; resize:vertical; padding:10px 12px; border-radius:12px; border:1px solid rgba(0,0,0,0.12); font:inherit;"></textarea>

    <div style="display:flex; gap:10px;">
      <button id="ocgAskBtn" type="button"
        style="flex:1; padding:10px 12px; border-radius:12px; border:1px solid rgba(0,0,0,0.12); background:#111827; color:#fff; font:inherit; cursor:pointer;">
        Ask
      </button>
      <button id="ocgCancelAskBtn" type="button"
        style="padding:10px 12px; border-radius:12px; border:1px solid rgba(0,0,0,0.12); background:#fff; font:inherit; cursor:pointer;">
        Cancel
      </button>
    </div>

    <div id="ocgAnswerBox"
      style="display:none; padding:12px; border-radius:12px; border:1px solid rgba(0,0,0,0.10); background:#fafafa;">
    </div>

    <div id="ocgConfirmBox"
      style="display:none; padding:12px; border-radius:12px; border:1px solid rgba(0,0,0,0.10); background:#fafafa;">
      <div id="ocgConfirmText" style="margin-bottom:10px;"></div>
      <div style="display:flex; gap:10px;">
        <button id="ocgConfirmYes" type="button"
          style="flex:1; padding:10px 12px; border-radius:12px; border:1px solid rgba(0,0,0,0.12); background:#111827; color:#fff; font:inherit; cursor:pointer;">
          Yes
        </button>
        <button id="ocgConfirmNo" type="button"
          style="flex:1; padding:10px 12px; border-radius:12px; border:1px solid rgba(0,0,0,0.12); background:#fff; font:inherit; cursor:pointer;">
          No
        </button>
      </div>
    </div>
  </div>
</div>
<button class="ocg-launcher" id="ocgLauncher" aria-label="Open site helper">
  💬 <span>Help</span>
</button>

<script>
  (function () {
    // ---------------------------
    // Existing widget open/close
    // ---------------------------
    const widget = document.getElementById("ocgWidget");
    const closeBtn = document.getElementById("ocgWidgetClose");
    const launcher = document.getElementById("ocgLauncher");

    const SHOW_DELAY_MS = 2500;
    const DISMISS_DAYS = 7;
    const STORAGE_KEY = "ocg_concierge_dismissed_until";

    function nowMs() { return Date.now(); }
    function dismissedUntil() {
      const v = localStorage.getItem(STORAGE_KEY);
      return v ? Number(v) : 0;
    }
    function setDismissed(days) {
      const ms = days * 24 * 60 * 60 * 1000;
      localStorage.setItem(STORAGE_KEY, String(nowMs() + ms));
    }
    function openWidget() {
      widget.classList.add("is-open");
      launcher.classList.remove("is-visible");
    }
    function closeWidget() {
      widget.classList.remove("is-open");
      launcher.classList.add("is-visible");
      setDismissed(DISMISS_DAYS);
    }

    closeBtn.addEventListener("click", closeWidget);
    launcher.addEventListener("click", openWidget);
    document.addEventListener("keydown", (e) => {
      if (e.key === "Escape" && widget.classList.contains("is-open")) closeWidget();
    });

    // Only auto-open on homepage
    const isHome = window.location.pathname === "/" || window.location.pathname === "/index.html";
    const shouldShow = isHome && nowMs() > dismissedUntil();
    if (shouldShow) setTimeout(openWidget, SHOW_DELAY_MS);
    else launcher.classList.add("is-visible");

    // ---------------------------
    // "Other questions" UI
    // ---------------------------
    const otherBtn = document.getElementById("ocgOtherBtn");
    const qaBox = document.getElementById("ocgQABox");
    const qInput = document.getElementById("ocgQuestionInput");
    const askBtn = document.getElementById("ocgAskBtn");
    const cancelBtn = document.getElementById("ocgCancelAskBtn");

    const answerBox = document.getElementById("ocgAnswerBox");

    const confirmBox = document.getElementById("ocgConfirmBox");
    const confirmText = document.getElementById("ocgConfirmText");
    const confirmYes = document.getElementById("ocgConfirmYes");
    const confirmNo = document.getElementById("ocgConfirmNo");

    function show(el) { el.style.display = ""; }
    function hide(el) { el.style.display = "none"; }

    otherBtn.addEventListener("click", () => {
      if (qaBox.style.display === "none") {
        show(qaBox);
        hide(answerBox);
        hide(confirmBox);
        qInput.value = "";
        setTimeout(() => qInput.focus(), 50);
      } else {
        hide(qaBox);
      }
    });

    cancelBtn.addEventListener("click", () => {
      hide(qaBox);
      hide(answerBox);
      hide(confirmBox);
      qInput.value = "";
    });

    function renderAnswer(html) {
      answerBox.innerHTML = html;
      show(answerBox);
    }

    // ---------------------------
    // Load FAQ bank from JSON
    // ---------------------------
    let FAQ_BANK = [];
    let THRESH_AUTO = 0.80;
    let THRESH_CONFIRM_MIN = 0.70;

    async function loadFAQBank() {
      try {
        const res = await fetch("/faq_bank.json", { cache: "no-cache" });
        if (!res.ok) throw new Error("faq_bank.json not found");
        const data = await res.json();

        FAQ_BANK = Array.isArray(data.faqs) ? data.faqs : [];
        THRESH_AUTO = data.thresholds?.autoAnswer ?? THRESH_AUTO;
        THRESH_CONFIRM_MIN = data.thresholds?.confirmMin ?? THRESH_CONFIRM_MIN;
      } catch (e) {
        // Fallback: bank not loaded (still allow logging)
        FAQ_BANK = [];
      }
    }

    // start loading immediately
    loadFAQBank();

    // ---------------------------
    // Matching utilities (typo-tolerant)
    // ---------------------------
    function normalize(s) {
      return (s || "")
        .toLowerCase()
        .replace(/[\u2019']/g, "'")
        .replace(/[^a-z0-9\s]/g, " ")
        .replace(/\s+/g, " ")
        .trim();
    }

    function bigrams(s) {
      const str = " " + s + " ";
      const out = [];
      for (let i = 0; i < str.length - 1; i++) out.push(str.slice(i, i + 2));
      return out;
    }

    function dice(a, b) {
      if (!a || !b) return 0;
      if (a === b) return 1;

      const A = bigrams(a);
      const B = bigrams(b);

      const freq = new Map();
      for (const x of A) freq.set(x, (freq.get(x) || 0) + 1);

      let matches = 0;
      for (const y of B) {
        const c = freq.get(y) || 0;
        if (c > 0) {
          freq.set(y, c - 1);
          matches++;
        }
      }
      return (2 * matches) / (A.length + B.length);
    }

    function findBestMatch(userText) {
      const q = normalize(userText);
      let best = { score: 0, entry: null, phrase: "" };

      for (const entry of FAQ_BANK) {
        for (const p of (entry.phrases || [])) {
          const score = dice(q, normalize(p));
          if (score > best.score) best = { score, entry, phrase: p };
        }
      }
      return best;
    }

    // ---------------------------
    // Fallback logging (paste your form endpoint)
    // ---------------------------
    const FALLBACK_FORM_ACTION = ""; // e.g. "https://docs.google.com/forms/d/e/XXXXX/formResponse"
    const FALLBACK_FORM_ENTRY = "";  // e.g. "entry.1234567890"

    async function logUnmatchedQuestion(questionText) {
      if (!FALLBACK_FORM_ACTION || !FALLBACK_FORM_ENTRY) return;
      const fd = new FormData();
      fd.append(FALLBACK_FORM_ENTRY, questionText);
      await fetch(FALLBACK_FORM_ACTION, { method: "POST", mode: "no-cors", body: fd });
    }

    // ---------------------------
    // Confirmation flow
    // ---------------------------
    let pendingMatch = null;
    let pendingQuestion = "";

    function showConfirm(best, originalQuestion) {
      pendingMatch = best;
      pendingQuestion = originalQuestion;

      // hide other boxes
      hide(answerBox);

      confirmText.innerHTML =
        `Did you mean <strong>${best.entry.title || "this"}</strong>?<br>
         <span style="color:#6b7280; font-size:12px;">(We’re guessing based on similar wording.)</span>`;
      show(confirmBox);
    }

    confirmYes.addEventListener("click", () => {
      if (pendingMatch?.entry?.answerHTML) {
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
         <span style="color:#6b7280; font-size:12px;">(If it’s urgent, use <a href="/contact">Contact</a>.)</span>`
      );
      try { await logUnmatchedQuestion(pendingQuestion); } catch (_) {}
      pendingMatch = null;
      pendingQuestion = "";
    });

    // ---------------------------
    // Ask button behavior
    // ---------------------------
    askBtn.addEventListener("click", async () => {
      const raw = qInput.value.trim();
      if (!raw) return;

      hide(answerBox);
      hide(confirmBox);

      // Ensure the bank has had a chance to load (if it hasn't, load again)
      if (!FAQ_BANK.length) await loadFAQBank();

      const best = findBestMatch(raw);

      if (best.entry && best.score >= THRESH_AUTO) {
        renderAnswer(best.entry.answerHTML);
      } else if (best.entry && best.score >= THRESH_CONFIRM_MIN) {
        showConfirm(best, raw);
      } else {
        renderAnswer(
          `Thanks — we’ll get back to you. We saved your question for our team to review.<br><br>
           <span style="color:#6b7280; font-size:12px;">(If it’s urgent, use <a href="/contact">Contact</a>.)</span>`
        );
        try { await logUnmatchedQuestion(raw); } catch (_) {}
      }
    });
  })();
</script>
<!-- /OCG Concierge Widget -->
