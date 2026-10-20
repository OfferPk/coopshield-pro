/\*\*  
 \* COOPShield Pro \- Server & Middleware Synthesizer (v1.0.0)  
 \* Generates production-ready configurations for Nginx, Apache,  
 \* Cloudflare, Caddy, Next.js/Express, and HTML subresource tags.  
 \*/

(function (root, factory) {  
  if (typeof module \=== 'object' && module.exports) {  
    module.exports \= factory();  
  } else {  
    root.ServerSynthesizer \= factory();  
  }  
}(typeof self \!== 'undefined' ? self : this, function () {  
  'use strict';

  function buildHeaderPairs(config) {  
    var coop \= config.coop || 'unsafe-none';  
    var coep \= config.coep || 'unsafe-none';  
    var corp \= config.corp || 'none';  
    var isReportOnly \= \!\!config.reportOnly;  
    var reportingEndpoint \= config.reportingEndpoint ? config.reportingEndpoint.trim() : '';

    var headers \= \[\];

    // COOP Header  
    var coopName \= isReportOnly ? 'Cross-Origin-Opener-Policy-Report-Only' : 'Cross-Origin-Opener-Policy';  
    var coopVal \= coop;  
    if (reportingEndpoint) {  
      coopVal \+= '; report-to="coop-endpoint"';  
    }  
    headers.push({ name: coopName, value: coopVal });

    // COEP Header  
    var coepName \= isReportOnly ? 'Cross-Origin-Embedder-Policy-Report-Only' : 'Cross-Origin-Embedder-Policy';  
    var coepVal \= coep;  
    if (reportingEndpoint) {  
      coepVal \+= '; report-to="coep-endpoint"';  
    }  
    headers.push({ name: coepName, value: coepVal });

    // CORP Header  
    if (corp && corp \!== 'none') {  
      headers.push({ name: 'Cross-Origin-Resource-Policy', value: corp });  
    }

    // Reporting Endpoints (W3C standard)  
    if (reportingEndpoint) {  
      headers.push({  
        name: 'Reporting-Endpoints',  
        value: 'coop-endpoint="' \+ reportingEndpoint \+ '", coep-endpoint="' \+ reportingEndpoint \+ '"'  
      });  
    }

    return headers;  
  }

  function toNginx(config) {  
    var pairs \= buildHeaderPairs(config);  
    var lines \= \['\# \================================================================'\];  
    lines.push('\# COOPShield Pro \- Nginx Cross-Origin Isolation Configuration');  
    lines.push('\# Ensures crossOriginIsolated \=== true & mitigates Spectre side-channels');  
    lines.push('\# \================================================================');  
    lines.push('');  
    pairs.forEach(function (h) {  
      lines.push('add\_header ' \+ h.name \+ ' "' \+ h.value \+ '" always;');  
    });  
    return lines.join('\\n');  
  }

  function toApache(config) {  
    var pairs \= buildHeaderPairs(config);  
    var lines \= \['\# \================================================================'\];  
    lines.push('\# COOPShield Pro \- Apache 2.4+ (.htaccess / httpd.conf)');  
    lines.push('\# Requires: mod\_headers (LoadModule headers\_module modules/mod\_headers.so)');  
    lines.push('\# \================================================================');  
    lines.push('\<IfModule mod\_headers.c\>');  
    pairs.forEach(function (h) {  
      lines.push('    Header always set ' \+ h.name \+ ' "' \+ h.value \+ '"');  
    });  
    lines.push('\</IfModule\>');  
    return lines.join('\\n');  
  }

  function toCloudflarePages(config) {  
    var pairs \= buildHeaderPairs(config);  
    var lines \= \['\# \================================================================'\];  
    lines.push('\# COOPShield Pro \- Cloudflare Pages (\_headers file)');  
    lines.push('\# Place in the root of your published output directory');  
    lines.push('\# \================================================================');  
    lines.push('/\*');  
    pairs.forEach(function (h) {  
      lines.push('  ' \+ h.name \+ ': ' \+ h.value);  
    });  
    return lines.join('\\n');  
  }

  function toCloudflareWorker(config) {  
    var pairs \= buildHeaderPairs(config);  
    var lines \= \['/\*\*'\];  
    lines.push(' \* COOPShield Pro \- Cloudflare Worker / Edge Middleware');  
    lines.push(' \*/');  
    lines.push('export default {');  
    lines.push('  async fetch(request, env, ctx) {');  
    lines.push('    const response \= await fetch(request);');  
    lines.push('    const newHeaders \= new Headers(response.headers);');  
    lines.push('');  
    pairs.forEach(function (h) {  
      lines.push('    newHeaders.set("' \+ h.name \+ '", "' \+ h.value \+ '");');  
    });  
    lines.push('');  
    lines.push('    return new Response(response.body, {');  
    lines.push('      status: response.status,');  
    lines.push('      statusText: response.statusText,');  
    lines.push('      headers: newHeaders');  
    lines.push('    });');  
    lines.push('  }');  
    lines.push('};');  
    return lines.join('\\n');  
  }

  function toCaddy(config) {  
    var pairs \= buildHeaderPairs(config);  
    var lines \= \['\# \================================================================'\];  
    lines.push('\# COOPShield Pro \- Caddy v2 Caddyfile');  
    lines.push('\# \================================================================');  
    lines.push('example.com {');  
    lines.push('    header {');  
    pairs.forEach(function (h) {  
      lines.push('        ' \+ h.name \+ ' "' \+ h.value \+ '"');  
    });  
    lines.push('    }');  
    lines.push('    reverse\_proxy localhost:3000');  
    lines.push('}');  
    return lines.join('\\n');  
  }

  function toNextJs(config) {  
    var pairs \= buildHeaderPairs(config);  
    var lines \= \['// \================================================================'\];  
    lines.push('// COOPShield Pro \- Next.js next.config.js');  
    lines.push('// \================================================================');  
    lines.push('module.exports \= {');  
    lines.push('  async headers() {');  
    lines.push('    return \[');  
    lines.push('      {');  
    lines.push('        source: "/:path\*",');  
    lines.push('        headers: \[');  
    pairs.forEach(function (h, idx) {  
      var comma \= (idx \< pairs.length \- 1\) ? ',' : '';  
      lines.push('          { key: "' \+ h.name \+ '", value: "' \+ h.value \+ '" }' \+ comma);  
    });  
    lines.push('        \]');  
    lines.push('      }');  
    lines.push('    \];');  
    lines.push('  }');  
    lines.push('};');  
    return lines.join('\\n');  
  }

  function toHtmlSubresources(config) {  
    var lines \= \['\<\!-- \================================================================ \--\>'\];  
    lines.push('\<\!-- COOPShield Pro \- Subresource & Iframe Integration Examples       \--\>');  
    lines.push('\<\!-- Use these patterns to avoid CORB/COEP asset blocking in browsers   \--\>');  
    lines.push('\<\!-- \================================================================ \--\>');  
    lines.push('');  
    lines.push('\<\!-- 1\. External Image from Public CDN (with CORS) \--\>');  
    lines.push('\<img src="https\://cdn.example.com/hero.jpg" crossorigin="anonymous" alt="Optimized Hero"\>');  
    lines.push('');  
    lines.push('\<\!-- 2\. External Font / Stylesheet (with CORS) \--\>');  
    lines.push('\<link rel="stylesheet" href="https\://fonts.googleapis.com/css2?family=Inter" crossorigin="anonymous"\>');  
    lines.push('');  
    lines.push('\<\!-- 3\. Third-Party Iframe Embed with Credentialless (Chrome 110+) \--\>');  
    lines.push('\<iframe src="https\://widget.vendor.com/embed" credentialless width="600" height="400"\>\</iframe\>');  
    lines.push('');  
    lines.push('\<\!-- 4\. WebAssembly Multi-Threaded Worker Script (Requires crossOriginIsolated) \--\>');  
    lines.push('\<script\>');  
    lines.push('  if (window.crossOriginIsolated) {');  
    lines.push('    console.log("Cross-Origin Isolation active\! Initializing SharedArrayBuffer...");');  
    lines.push('    const sharedBuf \= new SharedArrayBuffer(1024 \* 1024);');  
    lines.push('    const worker \= new Worker("wasm-worker.js");');  
    lines.push('    worker.postMessage({ buffer: sharedBuf });');  
    lines.push('  } else {');  
    lines.push('    console.warn("Cross-Origin Isolation missing. Falling back to single-threaded mode.");');  
    lines.push('  }');  
    lines.push('\</script\>');  
    return lines.join('\\n');  
  }

  return {  
    buildHeaderPairs: buildHeaderPairs,  
    toNginx: toNginx,  
    toApache: toApache,  
    toCloudflarePages: toCloudflarePages,  
    toCloudflareWorker: toCloudflareWorker,  
    toCaddy: toCaddy,  
    toNextJs: toNextJs,  
    toHtmlSubresources: toHtmlSubresources  
  };  
}));  
