/\*\*  
 \* COOPShield Pro \- Studio UI Controller & State Manager (v1.0.0)  
 \*/

(function () {  
  'use strict';

  var PRESETS \= {  
    'high-performance-wasm-strict': {  
      name: 'High-Performance Wasm & Game Engine (Strict Isolation)',  
      coop: 'same-origin',  
      coep: 'require-corp',  
      corp: 'same-origin',  
      reportOnly: false,  
      reportingEndpoint: 'https\://telemetry.coopshield.io/reports',  
      handlesOAuth: true,  
      usesCdnAssets: false  
    },  
    'credentialless-balanced-saas': {  
      name: 'Modern Balanced SaaS (Credentialless & OAuth Compatible)',  
      coop: 'same-origin',  
      coep: 'credentialless',  
      corp: 'same-origin',  
      reportOnly: false,  
      reportingEndpoint: 'https\://reports.saasplatform.com/coop',  
      handlesOAuth: true,  
      usesCdnAssets: true  
    },  
    'vulnerable-unisolated-legacy': {  
      name: 'Legacy Unisolated Web Application (High Spectre Risk)',  
      coop: 'unsafe-none',  
      coep: 'unsafe-none',  
      corp: 'none',  
      reportOnly: false,  
      reportingEndpoint: '',  
      handlesOAuth: false,  
      usesCdnAssets: true  
    }  
  };

  var state \= {  
    coop: 'same-origin',  
    coep: 'credentialless',  
    corp: 'same-origin',  
    reportOnly: false,  
    handlesOAuth: true,  
    usesCdnAssets: true,  
    reportingEndpoint: 'https\://reports.saasplatform.com/coop',  
    activeTab: 'nginx',  
    activeScenario: 'coopshield\_zero\_trust'  
  };

  // DOM Elements  
  var statusPill \= document.getElementById('isolation-status-pill');  
  var statusText \= document.getElementById('isolation-status-text');  
  var presetSelect \= document.getElementById('preset-select');  
  var reportOnlyToggle \= document.getElementById('report-only-toggle');  
  var oauthToggle \= document.getElementById('oauth-toggle');  
  var cdnToggle \= document.getElementById('cdn-toggle');  
  var reportingInput \= document.getElementById('reporting-input');  
  var capabilityGrid \= document.getElementById('capability-grid');  
  var unlockedCount \= document.getElementById('unlocked-count');  
  var auditScoreCircle \= document.getElementById('audit-score-circle');  
  var auditGrade \= document.getElementById('audit-grade');  
  var auditScore \= document.getElementById('audit-score');  
  var auditHeadline \= document.getElementById('audit-headline');  
  var auditSummaryText \= document.getElementById('audit-summary-text');  
  var auditList \= document.getElementById('audit-list');  
  var scenarioTimeline \= document.getElementById('scenario-timeline');  
  var serverCodeBlock \= document.getElementById('server-code-block');  
  var copyBtn \= document.getElementById('copy-code-btn');

  function init() {  
    setupEventListeners();  
    applyStateToUI();  
    renderAll();  
  }

  function setupEventListeners() {  
    // Preset dropdown  
    if (presetSelect) {  
      presetSelect.addEventListener('change', function () {  
        var p \= PRESETS\[presetSelect.value\];  
        if (p) {  
          state.coop \= p.coop;  
          state.coep \= p.coep;  
          state.corp \= p.corp;  
          state.reportOnly \= p.reportOnly;  
          state.handlesOAuth \= p.handlesOAuth;  
          state.usesCdnAssets \= p.usesCdnAssets;  
          state.reportingEndpoint \= p.reportingEndpoint;  
          applyStateToUI();  
          renderAll();  
        }  
      });  
    }

    // Segment buttons  
    setupSegmentGroup('coop-segments', 'coop', function (val) {  
      state.coop \= val;  
      renderAll();  
    });

    setupSegmentGroup('coep-segments', 'coep', function (val) {  
      state.coep \= val;  
      renderAll();  
    });

    setupSegmentGroup('corp-segments', 'corp', function (val) {  
      state.corp \= val;  
      renderAll();  
    });

    // Checkboxes  
    if (reportOnlyToggle) {  
      reportOnlyToggle.addEventListener('change', function () {  
        state.reportOnly \= reportOnlyToggle.checked;  
        renderAll();  
      });  
    }

    if (oauthToggle) {  
      oauthToggle.addEventListener('change', function () {  
        state.handlesOAuth \= oauthToggle.checked;  
        renderAll();  
      });  
    }

    if (cdnToggle) {  
      cdnToggle.addEventListener('change', function () {  
        state.usesCdnAssets \= cdnToggle.checked;  
        renderAll();  
      });  
    }

    if (reportingInput) {  
      reportingInput.addEventListener('input', function () {  
        state.reportingEndpoint \= reportingInput.value;  
        renderAll();  
      });  
    }

    // Scenario Pills  
    var scenarioPills \= document.querySelectorAll('.scenario-pill');  
    scenarioPills.forEach(function (pill) {  
      pill.addEventListener('click', function () {  
        scenarioPills.forEach(function (btn) { btn.classList.remove('active'); });  
        pill.classList.add('active');  
        state.activeScenario \= pill.getAttribute('data-scenario');  
        renderSimulator();  
      });  
    });

    // Server Tabs  
    var serverTabs \= document.querySelectorAll('.server-tab');  
    serverTabs.forEach(function (tab) {  
      tab.addEventListener('click', function () {  
        serverTabs.forEach(function (btn) { btn.classList.remove('active'); });  
        tab.classList.add('active');  
        state.activeTab \= tab.getAttribute('data-tab');  
        renderServerCode();  
      });  
    });

    // Copy Button  
    if (copyBtn) {  
      copyBtn.addEventListener('click', function () {  
        var text \= serverCodeBlock.textContent;  
        navigator.clipboard.writeText(text).then(function () {  
          copyBtn.textContent \= 'Copied\!';  
          setTimeout(function () {  
            copyBtn.textContent \= 'Copy Config';  
          }, 2000);  
        });  
      });  
    }  
  }

  function setupSegmentGroup(containerId, stateKey, onSelect) {  
    var container \= document.getElementById(containerId);  
    if (\!container) return;  
    var buttons \= container.querySelectorAll('.segment-btn');  
    buttons.forEach(function (btn) {  
      btn.addEventListener('click', function () {  
        buttons.forEach(function (b) {  
          b.className \= 'segment-btn';  
        });  
        var val \= btn.getAttribute('data-val');  
        btn.classList.add('active');  
        if (val \=== 'same-origin' || val \=== 'credentialless') {  
          btn.classList.add('emerald');  
        } else if (val \=== 'same-origin-allow-popups' || val \=== 'require-corp') {  
          btn.classList.add('amber');  
        } else if (val \=== 'unsafe-none' || val \=== 'none') {  
          btn.classList.add('rose');  
        } else {  
          btn.classList.add('primary');  
        }  
        onSelect(val);  
      });  
    });  
  }

  function applyStateToUI() {  
    updateSegmentActive('coop-segments', state.coop);  
    updateSegmentActive('coep-segments', state.coep);  
    updateSegmentActive('corp-segments', state.corp);

    if (reportOnlyToggle) reportOnlyToggle.checked \= state.reportOnly;  
    if (oauthToggle) oauthToggle.checked \= state.handlesOAuth;  
    if (cdnToggle) cdnToggle.checked \= state.usesCdnAssets;  
    if (reportingInput) reportingInput.value \= state.reportingEndpoint;  
  }

  function updateSegmentActive(containerId, activeVal) {  
    var container \= document.getElementById(containerId);  
    if (\!container) return;  
    var buttons \= container.querySelectorAll('.segment-btn');  
    buttons.forEach(function (btn) {  
      var val \= btn.getAttribute('data-val');  
      btn.className \= 'segment-btn';  
      if (val \=== activeVal) {  
        btn.classList.add('active');  
        if (val \=== 'same-origin' || val \=== 'credentialless') {  
          btn.classList.add('emerald');  
        } else if (val \=== 'same-origin-allow-popups' || val \=== 'require-corp') {  
          btn.classList.add('amber');  
        } else if (val \=== 'unsafe-none' || val \=== 'none') {  
          btn.classList.add('rose');  
        } else {  
          btn.classList.add('primary');  
        }  
      }  
    });  
  }

  function renderAll() {  
    renderIsolationStatus();  
    renderCapabilities();  
    renderAuditor();  
    renderSimulator();  
    renderServerCode();  
  }

  function renderIsolationStatus() {  
    var evalResult \= window.IsolationEngine.evaluateIsolation(state.coop, state.coep, state.reportOnly);  
    if (evalResult.crossOriginIsolated) {  
      statusPill.className \= 'isolation-badge active';  
      statusText.textContent \= 'crossOriginIsolated: true';  
    } else {  
      statusPill.className \= 'isolation-badge inactive';  
      if (evalResult.wouldBeIsolatedInEnforce && state.reportOnly) {  
        statusText.textContent \= 'Report-Only (Not Isolated)';  
      } else {  
        statusText.textContent \= 'crossOriginIsolated: false';  
      }  
    }  
  }

  function renderCapabilities() {  
    var evalResult \= window.IsolationEngine.evaluateIsolation(state.coop, state.coep, state.reportOnly);  
    var unlocked \= evalResult.unlockedCapabilities;  
    var locked \= evalResult.lockedCapabilities;

    unlockedCount.textContent \= unlocked.length \+ ' / ' \+ (unlocked.length \+ locked.length) \+ ' Unlocked';

    var allCaps \= window.IsolationEngine.UNLOCKED\_CAPABILITIES;  
    var html \= '';

    allCaps.forEach(function (cap) {  
      var isUnlocked \= evalResult.crossOriginIsolated;  
      var statusClass \= isUnlocked ? 'unlocked' : 'locked';  
      var badgeText \= isUnlocked ? 'ACTIVE' : 'BLOCKED';

      html \+= '\<div class="capability-card ' \+ statusClass \+ '"\>';  
      html \+= '  \<div class="capability-title"\>';  
      html \+= '    \<span\>' \+ escapeHtml(cap.name) \+ '\</span\>';  
      html \+= '    \<span class="cap-badge ' \+ statusClass \+ '"\>' \+ badgeText \+ '\</span\>';  
      html \+= '  \</div\>';  
      html \+= '  \<p class="capability-desc"\>' \+ escapeHtml(cap.description) \+ '\</p\>';  
      html \+= '\</div\>';  
    });

    capabilityGrid.innerHTML \= html;  
  }

  function renderAuditor() {  
    var audit \= window.SpectreAuditor.auditPolicy({  
      coop: state.coop,  
      coep: state.coep,  
      corp: state.corp,  
      reportOnly: state.reportOnly,  
      reportingEndpoint: state.reportingEndpoint,  
      handlesOAuth: state.handlesOAuth,  
      usesCdnAssets: state.usesCdnAssets  
    });

    auditScore.textContent \= audit.score \+ '/100';  
    auditGrade.textContent \= audit.grade;

    var gradeClass \= 'grade-F';  
    if (audit.grade \=== 'A+' || audit.grade \=== 'A') gradeClass \= 'grade-A';  
    else if (audit.grade \=== 'B') gradeClass \= 'grade-B';  
    else if (audit.grade \=== 'C') gradeClass \= 'grade-C';  
    else if (audit.grade \=== 'D') gradeClass \= 'grade-D';

    auditScoreCircle.className \= 'score-badge-circle ' \+ gradeClass;

    if (audit.score \>= 95\) {  
      auditHeadline.textContent \= 'Fortified Zero-Trust Posture';  
      auditSummaryText.textContent \= 'Full cross-origin isolation enabled with zero Spectre attack surface.';  
    } else if (audit.score \>= 85\) {  
      auditHeadline.textContent \= 'Balanced Production Posture';  
      auditSummaryText.textContent \= 'Secure cross-origin isolation active with minor friction optimizations.';  
    } else if (audit.score \>= 70\) {  
      auditHeadline.textContent \= 'Moderate Security Stance';  
      auditSummaryText.textContent \= 'Partial isolation configured; review compatibility and reporting telemetry.';  
    } else {  
      auditHeadline.textContent \= 'Vulnerable Unisolated State';  
      auditSummaryText.textContent \= 'Critical exposure to Spectre speculative side-channel attacks and reverse tabnabbing.';  
    }

    var listHtml \= '';  
    audit.checks.forEach(function (c) {  
      listHtml \+= '\<div class="audit-item status-' \+ c.status \+ '"\>';  
      listHtml \+= '  \<div class="audit-item-top"\>';  
      listHtml \+= '    \<span class="audit-item-title"\>' \+ escapeHtml(c.title) \+ '\</span\>';  
      listHtml \+= '    \<span class="audit-item-badge ' \+ c.status \+ '"\>' \+ c.status \+ ' (' \+ c.score \+ '/' \+ c.weight \+ ')\</span\>';  
      listHtml \+= '  \</div\>';  
      listHtml \+= '  \<p class="audit-item-detail"\>' \+ escapeHtml(c.detail) \+ '\</p\>';  
      listHtml \+= '\</div\>';  
    });

    auditList.innerHTML \= listHtml;  
  }

  function renderSimulator() {  
    var scenario \= window.SpectreSimulator.SCENARIOS\[state.activeScenario\];  
    if (\!scenario) return;

    var html \= '';  
    scenario.steps.forEach(function (step) {  
      html \+= '\<div class="timeline-step"\>';  
      html \+= '  \<div\>';  
      html \+= '    \<div class="timeline-phase"\>' \+ escapeHtml(step.phase) \+ '\</div\>';  
      html \+= '    \<div class="timeline-actor"\>' \+ escapeHtml(step.actor) \+ '\</div\>';  
      html \+= '  \</div\>';  
      html \+= '  \<div\>';  
      html \+= '    \<div class="timeline-action"\>' \+ escapeHtml(step.action) \+ '\</div\>';  
      html \+= '    \<div class="timeline-detail"\>' \+ escapeHtml(step.detail) \+ '\</div\>';  
      html \+= '  \</div\>';  
      html \+= '  \<div class="timeline-badge"\>';  
      html \+= '    \<span class="' \+ step.badgeClass \+ '"\>' \+ escapeHtml(step.badge) \+ '\</span\>';  
      html \+= '  \</div\>';  
      html \+= '\</div\>';  
    });

    scenarioTimeline.innerHTML \= html;  
  }

  function renderServerCode() {  
    var cfg \= {  
      coop: state.coop,  
      coep: state.coep,  
      corp: state.corp,  
      reportOnly: state.reportOnly,  
      reportingEndpoint: state.reportingEndpoint  
    };

    var code \= '';  
    switch (state.activeTab) {  
      case 'nginx':  
        code \= window.ServerSynthesizer.toNginx(cfg);  
        break;  
      case 'apache':  
        code \= window.ServerSynthesizer.toApache(cfg);  
        break;  
      case 'cf-pages':  
        code \= window.ServerSynthesizer.toCloudflarePages(cfg);  
        break;  
      case 'cf-worker':  
        code \= window.ServerSynthesizer.toCloudflareWorker(cfg);  
        break;  
      case 'caddy':  
        code \= window.ServerSynthesizer.toCaddy(cfg);  
        break;  
      case 'nextjs':  
        code \= window.ServerSynthesizer.toNextJs(cfg);  
        break;  
      case 'html':  
        code \= window.ServerSynthesizer.toHtmlSubresources(cfg);  
        break;  
      default:  
        code \= window.ServerSynthesizer.toNginx(cfg);  
    }

    serverCodeBlock.textContent \= code;  
  }

  function escapeHtml(str) {  
    if (\!str) return '';  
    return String(str)  
      .replace(/&/g, '\&amp;')  
      .replace(/\</g, '\&lt;')  
      .replace(/\>/g, '\&gt;')  
      .replace(/"/g, '\&quot;')  
      .replace(/'/g, '&\#039;');  
  }

  // Initialize on DOM load  
  if (document.readyState \=== 'loading') {  
    document.addEventListener('DOMContentLoaded', init);  
  } else {  
    init();  
  }  
})();  
