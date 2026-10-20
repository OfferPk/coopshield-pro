/\* \==========================================================================  
   COOPShield Pro \- Cybersecurity Dark UI Stylesheet (v1.0.0)  
   Enterprise Cross-Origin Isolation (COOP / COEP / CORP) Studio  
   \========================================================================== \*/

:root {  
  \--bg-primary: \#0a0d14;  
  \--bg-secondary: \#101522;  
  \--bg-tertiary: \#171f32;  
  \--bg-card: \#131929;  
  \--border-color: \#232d45;  
  \--border-focus: \#3b82f6;

  \--text-main: \#f1f5f9;  
  \--text-muted: \#94a3b8;  
  \--text-dim: \#64748b;

  \--accent-emerald: \#10b981;  
  \--accent-emerald-glow: rgba(16, 185, 129, 0.25);  
  \--accent-cyan: \#06b6d4;  
  \--accent-blue: \#3b82f6;  
  \--accent-purple: \#8b5cf6;  
  \--accent-amber: \#f59e0b;  
  \--accent-rose: \#ef4444;  
  \--accent-rose-glow: rgba(239, 68, 68, 0.25);

  \--font-sans: \-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;  
  \--font-mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", monospace;

  \--radius-sm: 4px;  
  \--radius-md: 8px;  
  \--radius-lg: 12px;  
  \--shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.4);  
  \--shadow-md: 0 4px 6px \-1px rgba(0, 0, 0, 0.5), 0 2px 4px \-1px rgba(0, 0, 0, 0.4);  
  \--shadow-lg: 0 10px 15px \-3px rgba(0, 0, 0, 0.6), 0 4px 6px \-2px rgba(0, 0, 0, 0.4);  
}

\* {  
  box-sizing: border-box;  
  margin: 0;  
  padding: 0;  
}

body {  
  background-color: var(--bg-primary);  
  color: var(--text-main);  
  font-family: var(--font-sans);  
  line-height: 1.5;  
  min-height: 100vh;  
  padding: 24px;  
}

.container {  
  max-width: 1400px;  
  margin: 0 auto;  
}

/\* Header \*/  
.header-bar {  
  display: flex;  
  justify-content: space-between;  
  align-items: center;  
  padding: 16px 24px;  
  background-color: var(--bg-secondary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-lg);  
  margin-bottom: 24px;  
  box-shadow: var(--shadow-md);  
}

.brand-section {  
  display: flex;  
  align-items: center;  
  gap: 14px;  
}

.brand-logo {  
  width: 44px;  
  height: 44px;  
  background: linear-gradient(135deg, \#10b981, \#06b6d4);  
  border-radius: var(--radius-md);  
  display: flex;  
  align-items: center;  
  justify-content: center;  
  color: \#fff;  
  font-weight: 800;  
  font-size: 20px;  
  box-shadow: 0 0 16px var(--accent-emerald-glow);  
}

.brand-title h1 {  
  font-size: 1.4rem;  
  font-weight: 700;  
  letter-spacing: \-0.02em;  
  color: \#fff;  
}

.brand-title p {  
  font-size: 0.82rem;  
  color: var(--text-muted);  
}

.header-actions {  
  display: flex;  
  align-items: center;  
  gap: 12px;  
}

/\* Live Isolation Status Pill \*/  
.isolation-badge {  
  display: flex;  
  align-items: center;  
  gap: 8px;  
  padding: 8px 16px;  
  border-radius: 9999px;  
  font-family: var(--font-mono);  
  font-size: 0.84rem;  
  font-weight: 600;  
  border: 1px solid transparent;  
  transition: all 0.3s ease;  
}

.isolation-badge.active {  
  background-color: rgba(16, 185, 129, 0.12);  
  border-color: var(--accent-emerald);  
  color: \#34d399;  
  box-shadow: 0 0 12px var(--accent-emerald-glow);  
}

.isolation-badge.inactive {  
  background-color: rgba(239, 68, 68, 0.12);  
  border-color: var(--accent-rose);  
  color: \#f87171;  
  box-shadow: 0 0 12px var(--accent-rose-glow);  
}

.pulse-dot {  
  width: 8px;  
  height: 8px;  
  border-radius: 50%;  
  animation: pulse-animation 2s infinite;  
}

.isolation-badge.active .pulse-dot {  
  background-color: var(--accent-emerald);  
}

.isolation-badge.inactive .pulse-dot {  
  background-color: var(--accent-rose);  
}

@keyframes pulse-animation {  
  0% { transform: scale(0.95); opacity: 0.7; }  
  50% { transform: scale(1.3); opacity: 1; }  
  100% { transform: scale(0.95); opacity: 0.7; }  
}

/\* Layout Grid \*/  
.main-grid {  
  display: grid;  
  grid-template-columns: 1fr 1fr;  
  gap: 24px;  
  margin-bottom: 24px;  
}

@media (max-width: 1080px) {  
  .main-grid {  
    grid-template-columns: 1fr;  
  }  
}

/\* Cards \*/  
.card {  
  background-color: var(--bg-card);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-lg);  
  padding: 20px;  
  box-shadow: var(--shadow-sm);  
}

.card-title {  
  display: flex;  
  justify-content: space-between;  
  align-items: center;  
  margin-bottom: 16px;  
  padding-bottom: 12px;  
  border-bottom: 1px solid var(--border-color);  
}

.card-title h2 {  
  font-size: 1.1rem;  
  font-weight: 600;  
  color: \#fff;  
  display: flex;  
  align-items: center;  
  gap: 8px;  
}

.badge-counter {  
  background-color: var(--bg-tertiary);  
  color: var(--accent-cyan);  
  padding: 2px 8px;  
  border-radius: 9999px;  
  font-size: 0.75rem;  
  font-family: var(--font-mono);  
}

/\* Preset Selector Bar \*/  
.preset-bar {  
  display: flex;  
  align-items: center;  
  gap: 10px;  
  margin-bottom: 20px;  
  background-color: var(--bg-secondary);  
  padding: 12px 16px;  
  border-radius: var(--radius-md);  
  border: 1px solid var(--border-color);  
}

.preset-bar label {  
  font-size: 0.85rem;  
  font-weight: 600;  
  color: var(--text-muted);  
  white-space: nowrap;  
}

.preset-select {  
  flex: 1;  
  background-color: var(--bg-primary);  
  border: 1px solid var(--border-color);  
  color: var(--text-main);  
  padding: 8px 12px;  
  border-radius: var(--radius-sm);  
  font-size: 0.88rem;  
  outline: none;  
}

.preset-select:focus {  
  border-color: var(--border-focus);  
}

/\* Policy Form Groups \*/  
.policy-group {  
  margin-bottom: 20px;  
}

.policy-group-header {  
  display: flex;  
  justify-content: space-between;  
  align-items: baseline;  
  margin-bottom: 8px;  
}

.policy-label {  
  font-size: 0.92rem;  
  font-weight: 600;  
  color: \#fff;  
  font-family: var(--font-mono);  
}

.policy-desc {  
  font-size: 0.78rem;  
  color: var(--text-muted);  
  margin-bottom: 10px;  
}

/\* Segmented Button Toggles \*/  
.segment-container {  
  display: flex;  
  background-color: var(--bg-primary);  
  padding: 3px;  
  border-radius: var(--radius-md);  
  border: 1px solid var(--border-color);  
  gap: 2px;  
}

.segment-btn {  
  flex: 1;  
  background: transparent;  
  border: none;  
  color: var(--text-muted);  
  padding: 8px 12px;  
  font-size: 0.82rem;  
  font-weight: 500;  
  border-radius: var(--radius-sm);  
  cursor: pointer;  
  transition: all 0.2s ease;  
  font-family: var(--font-mono);  
  text-align: center;  
}

.segment-btn:hover {  
  color: \#fff;  
  background-color: rgba(255, 255, 255, 0.05);  
}

.segment-btn.active {  
  background-color: var(--bg-tertiary);  
  color: \#fff;  
  font-weight: 600;  
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.4);  
}

.segment-btn.active.primary {  
  background-color: var(--accent-blue);  
  color: \#fff;  
}

.segment-btn.active.emerald {  
  background-color: var(--accent-emerald);  
  color: \#fff;  
}

.segment-btn.active.amber {  
  background-color: var(--accent-amber);  
  color: \#000;  
}

.segment-btn.active.rose {  
  background-color: var(--accent-rose);  
  color: \#fff;  
}

/\* Checkboxes & Inputs \*/  
.toggle-row {  
  display: flex;  
  align-items: center;  
  justify-content: space-between;  
  padding: 10px 14px;  
  background-color: var(--bg-secondary);  
  border-radius: var(--radius-sm);  
  margin-bottom: 12px;  
  border: 1px solid var(--border-color);  
}

.toggle-row label {  
  font-size: 0.86rem;  
  color: var(--text-main);  
  cursor: pointer;  
}

.input-text {  
  width: 100%;  
  background-color: var(--bg-primary);  
  border: 1px solid var(--border-color);  
  color: var(--text-main);  
  padding: 8px 12px;  
  border-radius: var(--radius-sm);  
  font-family: var(--font-mono);  
  font-size: 0.85rem;  
  outline: none;  
}

.input-text:focus {  
  border-color: var(--border-focus);  
}

/\* Auditor Score Header \*/  
.audit-overview {  
  display: flex;  
  align-items: center;  
  gap: 20px;  
  padding: 16px;  
  background-color: var(--bg-secondary);  
  border-radius: var(--radius-md);  
  margin-bottom: 18px;  
  border: 1px solid var(--border-color);  
}

.score-badge-circle {  
  width: 72px;  
  height: 72px;  
  border-radius: 50%;  
  display: flex;  
  flex-direction: column;  
  align-items: center;  
  justify-content: center;  
  font-weight: 800;  
  border: 3px solid;  
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.4);  
}

.score-badge-circle .grade {  
  font-size: 1.5rem;  
  line-height: 1;  
}

.score-badge-circle .score-num {  
  font-size: 0.72rem;  
  font-family: var(--font-mono);  
  opacity: 0.9;  
}

.score-badge-circle.grade-A { border-color: var(--accent-emerald); color: var(--accent-emerald); background-color: rgba(16, 185, 129, 0.1); }  
.score-badge-circle.grade-B { border-color: var(--accent-cyan); color: var(--accent-cyan); background-color: rgba(6, 182, 212, 0.1); }  
.score-badge-circle.grade-C { border-color: var(--accent-amber); color: var(--accent-amber); background-color: rgba(245, 158, 11, 0.1); }  
.score-badge-circle.grade-D { border-color: \#f97316; color: \#f97316; background-color: rgba(249, 115, 22, 0.1); }  
.score-badge-circle.grade-F { border-color: var(--accent-rose); color: var(--accent-rose); background-color: rgba(239, 68, 68, 0.1); }

.audit-meta h3 {  
  font-size: 1.1rem;  
  font-weight: 700;  
  margin-bottom: 4px;  
}

.audit-meta p {  
  font-size: 0.82rem;  
  color: var(--text-muted);  
}

/\* Auditor Vector List \*/  
.audit-list {  
  display: flex;  
  flex-direction: column;  
  gap: 10px;  
  max-height: 480px;  
  overflow-y: auto;  
  padding-right: 4px;  
}

.audit-item {  
  background-color: var(--bg-secondary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-sm);  
  padding: 12px 14px;  
  border-left: 4px solid var(--border-color);  
}

.audit-item.status-PASS { border-left-color: var(--accent-emerald); }  
.audit-item.status-WARN { border-left-color: var(--accent-amber); }  
.audit-item.status-FAIL { border-left-color: var(--accent-rose); }  
.audit-item.status-INFO { border-left-color: var(--accent-cyan); }

.audit-item-top {  
  display: flex;  
  justify-content: space-between;  
  align-items: center;  
  margin-bottom: 4px;  
}

.audit-item-title {  
  font-size: 0.88rem;  
  font-weight: 600;  
  color: \#fff;  
}

.audit-item-badge {  
  font-size: 0.72rem;  
  font-family: var(--font-mono);  
  font-weight: 700;  
  padding: 2px 6px;  
  border-radius: 4px;  
}

.audit-item-badge.PASS { background-color: rgba(16, 185, 129, 0.2); color: \#34d399; }  
.audit-item-badge.WARN { background-color: rgba(245, 158, 11, 0.2); color: \#fbbf24; }  
.audit-item-badge.FAIL { background-color: rgba(239, 68, 68, 0.2); color: \#f87171; }  
.audit-item-badge.INFO { background-color: rgba(6, 182, 212, 0.2); color: \#38bdf8; }

.audit-item-detail {  
  font-size: 0.8rem;  
  color: var(--text-muted);  
  line-height: 1.4;  
}

/\* Capability Unlock Grid \*/  
.capability-grid {  
  display: grid;  
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));  
  gap: 12px;  
  margin-top: 14px;  
}

.capability-card {  
  padding: 12px 14px;  
  background-color: var(--bg-secondary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-sm);  
  display: flex;  
  flex-direction: column;  
  justify-content: space-between;  
}

.capability-card.unlocked {  
  border-color: rgba(16, 185, 129, 0.4);  
  background-color: rgba(16, 185, 129, 0.04);  
}

.capability-card.locked {  
  border-color: rgba(239, 68, 68, 0.3);  
  opacity: 0.75;  
}

.capability-title {  
  font-size: 0.88rem;  
  font-weight: 600;  
  color: \#fff;  
  display: flex;  
  align-items: center;  
  justify-content: space-between;  
  margin-bottom: 6px;  
}

.cap-badge {  
  font-size: 0.7rem;  
  font-family: var(--font-mono);  
  padding: 2px 6px;  
  border-radius: 4px;  
}

.cap-badge.unlocked { background-color: var(--accent-emerald); color: \#fff; }  
.cap-badge.locked { background-color: var(--accent-rose); color: \#fff; }

.capability-desc {  
  font-size: 0.76rem;  
  color: var(--text-muted);  
}

/\* Simulator Section \*/  
.simulator-card {  
  margin-top: 24px;  
}

.scenario-pills {  
  display: flex;  
  gap: 8px;  
  margin-bottom: 16px;  
  overflow-x: auto;  
  padding-bottom: 4px;  
}

.scenario-pill {  
  padding: 8px 14px;  
  background-color: var(--bg-secondary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-sm);  
  color: var(--text-muted);  
  font-size: 0.82rem;  
  font-weight: 600;  
  cursor: pointer;  
  white-space: nowrap;  
  transition: all 0.2s;  
}

.scenario-pill:hover {  
  color: \#fff;  
  border-color: var(--text-dim);  
}

.scenario-pill.active {  
  background-color: var(--bg-tertiary);  
  color: \#fff;  
  border-color: var(--accent-cyan);  
  box-shadow: 0 0 10px rgba(6, 182, 212, 0.2);  
}

.timeline-container {  
  display: flex;  
  flex-direction: column;  
  gap: 12px;  
  margin-top: 14px;  
}

.timeline-step {  
  display: grid;  
  grid-template-columns: 160px 1fr 140px;  
  align-items: center;  
  padding: 12px 16px;  
  background-color: var(--bg-secondary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-sm);  
  gap: 16px;  
}

@media (max-width: 768px) {  
  .timeline-step {  
    grid-template-columns: 1fr;  
    gap: 8px;  
  }  
}

.timeline-phase {  
  font-weight: 700;  
  font-size: 0.85rem;  
  color: \#fff;  
}

.timeline-actor {  
  font-size: 0.75rem;  
  color: var(--accent-cyan);  
  font-family: var(--font-mono);  
}

.timeline-action {  
  font-size: 0.82rem;  
  color: var(--text-main);  
}

.timeline-detail {  
  font-size: 0.75rem;  
  color: var(--text-muted);  
  margin-top: 2px;  
}

.timeline-badge {  
  text-align: right;  
}

.timeline-badge span {  
  font-size: 0.72rem;  
  font-family: var(--font-mono);  
  font-weight: 700;  
  padding: 3px 8px;  
  border-radius: 4px;  
}

.timeline-badge .badge-danger { background-color: rgba(239, 68, 68, 0.2); color: \#f87171; border: 1px solid var(--accent-rose); }  
.timeline-badge .badge-warning { background-color: rgba(245, 158, 11, 0.2); color: \#fbbf24; border: 1px solid var(--accent-amber); }  
.timeline-badge .badge-info { background-color: rgba(6, 182, 212, 0.2); color: \#38bdf8; border: 1px solid var(--accent-cyan); }  
.timeline-badge .badge-success { background-color: rgba(16, 185, 129, 0.2); color: \#34d399; border: 1px solid var(--accent-emerald); }

/\* Server Synthesizer Section \*/  
.server-tabs {  
  display: flex;  
  border-bottom: 1px solid var(--border-color);  
  margin-bottom: 14px;  
  gap: 4px;  
}

.server-tab {  
  background: transparent;  
  border: none;  
  border-bottom: 2px solid transparent;  
  color: var(--text-muted);  
  padding: 8px 14px;  
  font-size: 0.84rem;  
  font-weight: 600;  
  cursor: pointer;  
  transition: all 0.2s;  
}

.server-tab:hover {  
  color: \#fff;  
}

.server-tab.active {  
  color: var(--accent-cyan);  
  border-bottom-color: var(--accent-cyan);  
}

.code-container {  
  position: relative;  
  background-color: var(--bg-primary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-md);  
  padding: 16px;  
  font-family: var(--font-mono);  
  font-size: 0.82rem;  
  color: \#e2e8f0;  
  white-space: pre-wrap;  
  word-break: break-all;  
  overflow-x: auto;  
  max-height: 320px;  
}

.copy-btn {  
  position: absolute;  
  top: 10px;  
  right: 10px;  
  background-color: var(--bg-tertiary);  
  border: 1px solid var(--border-color);  
  color: var(--text-main);  
  padding: 5px 10px;  
  border-radius: var(--radius-sm);  
  font-size: 0.75rem;  
  cursor: pointer;  
  transition: all 0.2s;  
}

.copy-btn:hover {  
  background-color: var(--border-color);  
  color: \#fff;  
}

/\* Footer \*/  
footer {  
  margin-top: 36px;  
  text-align: center;  
  font-size: 0.8rem;  
  color: var(--text-dim);  
  padding: 16px;  
  border-top: 1px solid var(--border-color);  
}  
