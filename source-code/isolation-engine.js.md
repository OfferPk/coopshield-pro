/\*\*  
 \* COOPShield Pro \- Cross-Origin Isolation Engine (v1.0.0)  
 \* Evaluates W3C COOP, COEP, and CORP specifications, determines  
 \* window.crossOriginIsolated status, and checks API availability.  
 \*/

(function (root, factory) {  
  if (typeof module \=== 'object' && module.exports) {  
    module.exports \= factory();  
  } else {  
    root.IsolationEngine \= factory();  
  }  
}(typeof self \!== 'undefined' ? self : this, function () {  
  'use strict';

  var COOP\_OPTIONS \= {  
    'same-origin': {  
      label: 'same-origin',  
      description: 'Isolates browsing context strictly to same-origin. Disconnects window.opener for cross-origin popups/openers.',  
      isolationWeight: 50,  
      spectreSafe: true,  
      popupFriendly: false  
    },  
    'same-origin-allow-popups': {  
      label: 'same-origin-allow-popups',  
      description: 'Retains window references for newly opened popups that do not set COOP or set unsafe-none. Useful for OAuth.',  
      isolationWeight: 30,  
      spectreSafe: false, // Standard spec requires same-origin for full crossOriginIsolated  
      popupFriendly: true  
    },  
    'unsafe-none': {  
      label: 'unsafe-none',  
      description: 'Default browser behavior. Allows cross-origin openers and popups to retain window references without isolation.',  
      isolationWeight: 0,  
      spectreSafe: false,  
      popupFriendly: true  
    }  
  };

  var COEP\_OPTIONS \= {  
    'require-corp': {  
      label: 'require-corp',  
      description: 'Strict. Blocks all cross-origin subresources unless explicitly permitted by CORP or CORS.',  
      isolationWeight: 50,  
      spectreSafe: true,  
      breakageRisk: 'High'  
    },  
    'credentialless': {  
      label: 'credentialless',  
      description: 'Modern (Chrome 96+). Loads cross-origin subresources without credentials (cookies, certs), avoiding CORP errors.',  
      isolationWeight: 45,  
      spectreSafe: true,  
      breakageRisk: 'Low'  
    },  
    'unsafe-none': {  
      label: 'unsafe-none',  
      description: 'Default browser behavior. Cross-origin subresources loaded with ambient credentials; no isolation.',  
      isolationWeight: 0,  
      spectreSafe: false,  
      breakageRisk: 'None'  
    }  
  };

  var CORP\_OPTIONS \= {  
    'same-origin': {  
      label: 'same-origin',  
      description: 'Only resources from the exact same origin (scheme, host, port) can read this resource.'  
    },  
    'same-site': {  
      label: 'same-site',  
      description: 'Only resources from the same registered domain (e.g., \*.example.com) can read this resource.'  
    },  
    'cross-origin': {  
      label: 'cross-origin',  
      description: 'Resources from any origin can read this resource. Crucial for public CDNs loaded in require-corp environments.'  
    },  
    'none': {  
      label: 'none (header omitted)',  
      description: 'No CORP header sent. Subresources will fail under strict COEP: require-corp.'  
    }  
  };

  var UNLOCKED\_CAPABILITIES \= \[  
    {  
      id: 'shared-array-buffer',  
      name: 'SharedArrayBuffer',  
      description: 'Shared memory between web workers and main thread for multi-threaded performance.',  
      requiresIsolation: true,  
      impact: 'Critical for high-performance WebAssembly, audio workstations, and local AI/LLM models.'  
    },  
    {  
      id: 'atomics-wait',  
      name: 'Atomics.wait()',  
      description: 'High-performance thread synchronization primitives in Dedicated Web Workers.',  
      requiresIsolation: true,  
      impact: 'Allows efficient worker synchronization without spinlocks.'  
    },  
    {  
      id: 'high-res-timers',  
      name: 'High-Resolution Timers (\<5µs)',  
      description: 'Unclamped performance.now() timers without jitter or artificial precision coarsening.',  
      requiresIsolation: true,  
      impact: 'Essential for precise audio synthesis, 120fps physics simulation, and benchmarking.'  
    },  
    {  
      id: 'measure-memory',  
      name: 'performance.measureUserAgentSpecificMemory()',  
      description: 'Quantifies exact breakdown of memory usage per browsing context and worker.',  
      requiresIsolation: true,  
      impact: 'Enterprise memory leak diagnosis and profiling.'  
    },  
    {  
      id: 'audio-worklet-ring-buffer',  
      name: 'AudioWorklet Zero-Latency Ring Buffers',  
      description: 'Direct sample stream transfer between audio thread and UI without garbage collection pauses.',  
      requiresIsolation: true,  
      impact: 'Professional DAW web apps and low-latency VoIP conferencing.'  
    }  
  \];

  function evaluateIsolation(coop, coep, isReportOnly) {  
    coop \= coop || 'unsafe-none';  
    coep \= coep || 'unsafe-none';  
    isReportOnly \= \!\!isReportOnly;

    // Standard W3C rule: crossOriginIsolated requires COOP: same-origin AND COEP: require-corp OR credentialless  
    var coopIsolated \= (coop \=== 'same-origin');  
    var coepIsolated \= (coep \=== 'require-corp' || coep \=== 'credentialless');

    var isIsolated \= coopIsolated && coepIsolated && \!isReportOnly;  
    var wouldBeIsolatedInEnforce \= coopIsolated && coepIsolated;

    var unlocked \= \[\];  
    var locked \= \[\];

    UNLOCKED\_CAPABILITIES.forEach(function (cap) {  
      if (isIsolated) {  
        unlocked.push(cap);  
      } else {  
        locked.push(cap);  
      }  
    });

    return {  
      crossOriginIsolated: isIsolated,  
      reportOnly: isReportOnly,  
      wouldBeIsolatedInEnforce: wouldBeIsolatedInEnforce,  
      coop: coop,  
      coep: coep,  
      unlockedCapabilities: unlocked,  
      lockedCapabilities: locked,  
      coopSpec: COOP\_OPTIONS\[coop\] || COOP\_OPTIONS\['unsafe-none'\],  
      coepSpec: COEP\_OPTIONS\[coep\] || COEP\_OPTIONS\['unsafe-none'\]  
    };  
  }

  function parseHeaderString(headerStr) {  
    if (\!headerStr || typeof headerStr \!== 'string') return {};  
    var result \= {  
      coop: 'unsafe-none',  
      coep: 'unsafe-none',  
      corp: 'none',  
      coopReportOnly: false,  
      coepReportOnly: false,  
      reportTo: '',  
      reportingEndpoints: ''  
    };

    var lines \= headerStr.split(/\\r?\\n/);  
    lines.forEach(function (line) {  
      var parts \= line.split(':');  
      if (parts.length \>= 2\) {  
        var name \= parts\[0\].trim().toLowerCase();  
        var val \= parts.slice(1).join(':').trim();

        if (name \=== 'cross-origin-opener-policy') {  
          result.coop \= extractDirectiveVal(val);  
          result.coopReportOnly \= false;  
        } else if (name \=== 'cross-origin-opener-policy-report-only') {  
          result.coop \= extractDirectiveVal(val);  
          result.coopReportOnly \= true;  
        } else if (name \=== 'cross-origin-embedder-policy') {  
          result.coep \= extractDirectiveVal(val);  
          result.coepReportOnly \= false;  
        } else if (name \=== 'cross-origin-embedder-policy-report-only') {  
          result.coep \= extractDirectiveVal(val);  
          result.coepReportOnly \= true;  
        } else if (name \=== 'cross-origin-resource-policy') {  
          result.corp \= extractDirectiveVal(val);  
        } else if (name \=== 'reporting-endpoints') {  
          result.reportingEndpoints \= val;  
        }  
      }  
    });

    return result;  
  }

  function extractDirectiveVal(raw) {  
    var clean \= raw.split(';')\[0\].trim().replace(/^\["'\]|\["'\]\$/g, '');  
    return clean.toLowerCase();  
  }

  return {  
    COOP\_OPTIONS: COOP\_OPTIONS,  
    COEP\_OPTIONS: COEP\_OPTIONS,  
    CORP\_OPTIONS: CORP\_OPTIONS,  
    UNLOCKED\_CAPABILITIES: UNLOCKED\_CAPABILITIES,  
    evaluateIsolation: evaluateIsolation,  
    parseHeaderString: parseHeaderString  
  };  
}));  
