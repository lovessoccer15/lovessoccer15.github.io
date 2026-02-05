# lovessoccer15.github.io
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

<button class="ocg-launcher" id="ocgLauncher" aria-label="Open site helper">
  💬 <span>Help</span>
</button>

<script>
  (function () {
    const widget = document.getElementById("ocgWidget");
    const closeBtn = document.getElementById("ocgWidgetClose");
    const launcher = document.getElementById("ocgLauncher");

    // CONFIG
    const SHOW_DELAY_MS = 2500;          // delay before popping up
    const DISMISS_DAYS = 7;              // don't show again for X days after close
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

    // Close behavior
    closeBtn.addEventListener("click", closeWidget);
    launcher.addEventListener("click", openWidget);

    // Optional: close when pressing Escape
    document.addEventListener("keydown", (e) => {
      if (e.key === "Escape" && widget.classList.contains("is-open")) closeWidget();
    });

    // Auto-open logic
    const shouldShow = nowMs() > dismissedUntil();
    if (shouldShow) {
      setTimeout(openWidget, SHOW_DELAY_MS);
    } else {
      // show small launcher instead
      launcher.classList.add("is-visible");
    }
  })();
</script>
<!-- /OCG Concierge Widget -->
