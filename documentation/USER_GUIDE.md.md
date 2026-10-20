\# User & Operations Guide: COOPShield Pro (v1.0.0)

\> A comprehensive operations guide for implementing Cross-Origin Isolation (COOP, COEP, CORP) without breaking production applications.

\---

\#\# 1\. Quick Start

\#\#\# 1.1 Running the Studio  
Open \`source-code/index.html\` in any modern web browser (Chrome, Edge, Firefox, Safari).  
No installation, npm build, or external server is required.

\#\#\# 1.2 Selecting a Security Preset  
Use the \*\*Security Archetype Preset\*\* dropdown at the top of the interface:  
1\. \*\*High-Performance Wasm & Game Engine (Strict Isolation)\*\*: For multi-threaded WebAssembly, in-browser AI/LLMs, 3D engines, and digital audio workstations.  
2\. \*\*Modern Balanced SaaS (Credentialless & OAuth Compatible)\*\*: Recommended default for modern SaaS web applications using OAuth, Google Fonts, and third-party media.  
3\. \*\*Legacy Unisolated Web Application (High Spectre Risk)\*\*: Baseline demonstration of unhardened web stacks.

\---

\#\# 2\. Policy Configuration Guide

\#\#\# 2.1 Cross-Origin-Opener-Policy (COOP)  
\- \*\*\`same-origin\`\*\*: Highest security. Top-level window is assigned a dedicated browsing context group. Any window opened via \`window.open\` will have its \`window.opener\` severed.  
  \- \*Recommendation\*: Use for internal apps, gaming, or when OAuth is redirect-based.  
\- \*\*\`same-origin-allow-popups\`\*\*: Retains opener references for newly opened popups that do not set COOP or set \`unsafe-none\`.  
  \- \*Recommendation\*: Use when relying on third-party OAuth popup login SDKs.  
\- \*\*\`unsafe-none\`\*\*: Legacy default. Does not isolate browsing contexts.

\#\#\# 2.2 Cross-Origin-Embedder-Policy (COEP)  
\- \*\*\`credentialless\`\*\* \*(Recommended)\*: Supported in Chromium 96+ and all modern engines. Loads cross-origin subresources (images, scripts, fonts) without ambient credentials (cookies, HTTP basic auth). This allows public CDN assets to load without requiring explicit \`Cross-Origin-Resource-Policy\` headers.  
\- \*\*\`require-corp\`\*\*: Strict mode. Blocks \*all\* cross-origin subresources unless the responding server sends \`Cross-Origin-Resource-Policy: cross-origin\` or CORS headers.  
\- \*\*\`unsafe-none\`\*\*: Legacy default. Allows all subresources, but does NOT grant \`crossOriginIsolated\`.

\#\#\# 2.3 Cross-Origin-Resource-Policy (CORP)  
Add this header to your server's own API responses:  
\- \*\*\`same-origin\`\*\*: Protects your endpoints from being loaded by external sites in \`\<img\>\`, \`\<video\>\`, or \`\<iframe\>\` tags.  
\- \*\*\`same-site\`\*\*: Permits your related subdomains to embed the asset.  
\- \*\*\`cross-origin\`\*\*: Explicitly permits anyone to embed the asset (use for public CDNs).

\---

\#\# 3\. Safe Staging & Canary Rollout Strategy

Deploying Cross-Origin Isolation blindly in production can break third-party integrations. Follow this 3-phase rollout playbook:

\#\#\# Phase 1: Canary & Report-Only Monitoring  
Deploy report-only headers to observe violations without blocking user traffic:  
\`\`\`http  
Cross-Origin-Opener-Policy-Report-Only: same-origin; report-to="coop-endpoint"  
Cross-Origin-Embedder-Policy-Report-Only: credentialless; report-to="coep-endpoint"  
Reporting-Endpoints: coop-endpoint="https\://telemetry.yourdomain.com/coop", coep-endpoint="https\://telemetry.yourdomain.com/coep"  
\`\`\`  
Review telemetry beacons to identify any blocked fonts, images, or broken popup callbacks.

\#\#\# Phase 2: Remediating Third-Party Assets  
1\. \*\*Google Fonts\*\*: Add \`crossorigin="anonymous"\` to \`\<link\>\` tags:  
   \`\`\`html  
   \<link rel="preconnect" href="https\://fonts.gstatic.com" crossorigin="anonymous"\>  
   \<link rel="stylesheet" href="https\://fonts.googleapis.com/css2?family=Inter" crossorigin="anonymous"\>  
   \`\`\`  
2\. \*\*Third-Party CDN Images\*\*: Add \`crossorigin="anonymous"\` to \`\<img\>\` tags.  
3\. \*\*Embedded Iframes\*\*: Use the \`credentialless\` attribute:  
   \`\`\`html  
   \<iframe src="https\://partner-service.com/widget" credentialless\>\</iframe\>  
   \`\`\`

\#\#\# Phase 3: Enforcing Full Isolation  
Switch from \`-Report-Only\` headers to enforced headers. Check that \`window.crossOriginIsolated \=== true\` in the browser console.

\---

\#\# 4\. Standalone CLI & CI/CD Integration

Use \`coopshield-cli.py\` to audit configurations in automated workflows:

\#\#\# Basic Audit  
\`\`\`bash  
python3 build/coopshield-cli.py audit \--preset source-code/presets/credentialless-balanced-saas.json  
\`\`\`

\#\#\# Pre-Commit & GitHub Actions Gate  
Enforce that pull requests cannot introduce unhardened cross-origin policies:  
\`\`\`yaml  
name: Security Header Audit  
on: \[push, pull\_request\]

jobs:  
  audit-coop:  
    runs-on: ubuntu-latest  
    steps:  
      \- uses: actions/checkout@v3  
      \- name: Set up Python  
        uses: actions/setup-python@v4  
        with:  
          python-version: '3.10'  
      \- name: Run COOPShield Security Gate  
        run: |  
          python3 build/coopshield-cli.py audit \--preset source-code/presets/high-performance-wasm-strict.json \--ci  
\`\`\`  
If the policy is unhardened or scores below 80, the CLI exits with code \`1\`, failing the CI pipeline before vulnerable code is deployed.  
