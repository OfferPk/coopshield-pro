/\*\*  
 \* COOPShield Pro \- Spectre Side-Channel & Isolation Simulator (v1.0.0)  
 \* Visualizes hardware cache-timing attacks, OAuth popup failures,  
 \* CDN asset breakage, and zero-trust cross-origin isolation.  
 \*/

(function (root, factory) {  
  if (typeof module \=== 'object' && module.exports) {  
    module.exports \= factory();  
  } else {  
    root.SpectreSimulator \= factory();  
  }  
}(typeof self \!== 'undefined' ? self : this, function () {  
  'use strict';

  var SCENARIOS \= {  
    spectre\_unisolated: {  
      id: 'spectre\_unisolated',  
      title: 'Spectre Side-Channel Speculative Execution Attack',  
      threatType: 'Microarchitectural Hardware Vulnerability',  
      victimDomain: 'https\://bank.secure-portal.com',  
      attackerDomain: 'https\://cdn-evil-tracker.io',  
      steps: \[  
        {  
          phase: '1. Process Sharing',  
          actor: 'Browser Process Scheduler',  
          action: 'Top-level document operates under COOP: unsafe-none and COEP: unsafe-none.',  
          detail: 'Browser allocates victim banking session and untrusted third-party iframe into the SAME underlying OS renderer process.',  
          badge: 'SECURITY HOLE',  
          badgeClass: 'badge-danger'  
        },  
        {  
          phase: '2. Branch Target Injection',  
          actor: 'Attacker Script',  
          action: 'Executes speculative execution training loop over array bounds.',  
          detail: 'Manipulates CPU Branch Target Buffer (BTB) to speculatively access victim memory addresses out-of-bounds.',  
          badge: 'SPECULATIVE BYPASS',  
          badgeClass: 'badge-danger'  
        },  
        {  
          phase: '3. Cache Line Probing',  
          actor: 'Attacker Script',  
          action: 'Probes L1/L3 CPU Data Cache lines using high-precision timer loop.',  
          detail: 'Measures memory access latency: Cache Hit (\~10ns) vs. Cache Miss (\~200ns) reveals byte value of victim auth token.',  
          badge: 'CACHE EXTRAPOLATION',  
          badgeClass: 'badge-danger'  
        },  
        {  
          phase: '4. Secret Exfiltration',  
          actor: 'Attacker Beacon',  
          action: 'Exfiltrates recovered private session token: "sk\_live\_9a8f4c2e1b".',  
          detail: 'Spectre timing attack succeeds without triggering any CORS, CSP, or traditional browser security warnings.',  
          badge: 'COMPROMISED',  
          badgeClass: 'badge-danger'  
        }  
      \]  
    },

    broken\_oauth\_popup: {  
      id: 'broken\_oauth\_popup',  
      title: 'Severed OAuth Pop-up Window Handshake',  
      threatType: 'Deployment Misconfiguration / UX Breakage',  
      victimDomain: 'https\://app.saas-platform.com',  
      attackerDomain: 'https\://accounts.google.com/oauth',  
      steps: \[  
        {  
          phase: '1. User Sign-In Trigger',  
          actor: 'User / Web App',  
          action: 'User clicks "Sign in with Google". Application executes window.open(authUrl).',  
          detail: 'A new pop-up window opens pointing to external identity provider.',  
          badge: 'POPUP INITIATED',  
          badgeClass: 'badge-info'  
        },  
        {  
          phase: '2. Strict COOP Enforcement',  
          actor: 'Browser Security Engine',  
          action: 'Host document sets Cross-Origin-Opener-Policy: same-origin.',  
          detail: 'Because opener is https\://app.saas-platform.com and popup is https\://accounts.google.com, browser SEVERS window context.',  
          badge: 'CONTEXT ISOLATED',  
          badgeClass: 'badge-warning'  
        },  
        {  
          phase: '3. Opener Disconnect',  
          actor: 'Popup Window',  
          action: 'OAuth popup authenticates user and attempts window.opener.postMessage({ token: "..." }).',  
          detail: 'Browser evaluates window.opener as null. Property access fails with TypeError.',  
          badge: 'DESERIALIZATION ERROR',  
          badgeClass: 'badge-danger'  
        },  
        {  
          phase: '4. Infinite Authentication Hang',  
          actor: 'Client Application',  
          action: 'Parent application waits indefinitely for login event; popup remains orphaned.',  
          detail: 'Resolution: Use redirect flow or configure COOP: same-origin-allow-popups for OAuth endpoints.',  
          badge: 'BROKEN LOGIN',  
          badgeClass: 'badge-danger'  
        }  
      \]  
    },

    blocked\_cdn\_assets: {  
      id: 'blocked\_cdn\_assets',  
      title: 'Strict COEP Subresource CDN Breakage',  
      threatType: 'Cross-Origin Resource Blocking (CORB / COEP)',  
      victimDomain: 'https\://studio.video-editor.io',  
      attackerDomain: 'https\://fonts.gstatic.com / https\://cdn.images.net',  
      steps: \[  
        {  
          phase: '1. High-Performance Isolation Activation',  
          actor: 'Server Response',  
          action: 'Server sends Cross-Origin-Embedder-Policy: require-corp to enable SharedArrayBuffer.',  
          detail: 'Browser puts document into strict embedder mode.',  
          badge: 'REQUIRE-CORP ACTIVE',  
          badgeClass: 'badge-info'  
        },  
        {  
          phase: '2. Subresource Fetch',  
          actor: 'DOM Parser',  
          action: 'Document parses \<link rel="stylesheet" href="https\://fonts.googleapis.com/css2"\> and \<img\> from public CDN.',  
          detail: 'Standard HTTP GET requests sent to external CDN hosts.',  
          badge: 'FETCH DISPATCHED',  
          badgeClass: 'badge-info'  
        },  
        {  
          phase: '3. Missing CORP Validation',  
          actor: 'Browser Network Stack',  
          action: 'CDN response arrives without Cross-Origin-Resource-Policy: cross-origin header.',  
          detail: 'Browser inspects response headers. Lacking explicit CORP or CORS authorization, browser terminates response.',  
          badge: 'POLICY VIOLATION',  
          badgeClass: 'badge-danger'  
        },  
        {  
          phase: '4. Asset Crash in DOM',  
          actor: 'Browser Console',  
          action: 'net::ERR\_BLOCKED\_BY\_RESPONSE.NotSameOriginAfterDefaultedToSameOriginByCoep.',  
          detail: 'Fonts fail to render, images show broken icons, layout shifts. Fix: Use COEP: credentialless or add crossorigin attribute.',  
          badge: 'ASSET BLOCKED',  
          badgeClass: 'badge-danger'  
        }  
      \]  
    },

    coopshield\_zero\_trust: {  
      id: 'coopshield\_zero\_trust',  
      title: 'COOPShield Pro Zero-Trust Hardened Isolation',  
      threatType: 'Complete Cross-Origin Isolation Defense',  
      victimDomain: 'https\://enterprise.fintech-core.com',  
      attackerDomain: 'https\://untrusted-external-domain.org',  
      steps: \[  
        {  
          phase: '1. Strict Process Partitioning',  
          actor: 'COOP: same-origin',  
          action: 'Top-level document is assigned to a dedicated, isolated OS process space.',  
          detail: 'Zero browsing context sharing with any external window, link, or tab.',  
          badge: 'PROCESS SANDBOXED',  
          badgeClass: 'badge-success'  
        },  
        {  
          phase: '2. Modern Credentialless Embedding',  
          actor: 'COEP: credentialless',  
          action: 'Subresources load in credentialless mode (no ambient cookies/credentials passed).',  
          detail: 'Public CDN fonts and media load seamlessly without CORB/COEP blocking errors.',  
          badge: 'FRICTION-FREE ASSETS',  
          badgeClass: 'badge-success'  
        },  
        {  
          phase: '3. Data Leak Prevention',  
          actor: 'CORP: same-origin',  
          action: 'All proprietary API endpoints serve Cross-Origin-Resource-Policy: same-origin.',  
          detail: 'Malicious external sites cannot embed or probe confidential company endpoints.',  
          badge: 'CORP LOCKED',  
          badgeClass: 'badge-success'  
        },  
        {  
          phase: '4. Capability Unlock',  
          actor: 'Browser Runtime Engine',  
          action: 'window.crossOriginIsolated evaluates to TRUE.',  
          detail: 'SharedArrayBuffer, Atomics.wait(), and high-performance multi-threaded WebAssembly execute safely with zero Spectre vulnerability.',  
          badge: 'FULL ISOLATION UNLOCKED',  
          badgeClass: 'badge-success'  
        }  
      \]  
    }  
  };

  return {  
    SCENARIOS: SCENARIOS  
  };  
}));  
