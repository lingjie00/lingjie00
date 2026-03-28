---
layout: default
title: Lingjie | Data Science & Statistics
---

<style>
  /* Iron Man HUD Aesthetic */
  #bayesian-calculator {
    box-shadow: 0 0 40px rgba(136, 192, 208, 0.2);
    border: 1px solid rgba(136, 192, 208, 0.1);
    background: #1A1C23;
  }

  /* Glassmorphism for Tooltips */
  .hovertext path {
    fill: rgba(46, 52, 64, 0.85) !important;
    stroke: rgba(136, 192, 208, 0.5) !important;
    backdrop-filter: blur(8px);
  }
  
  input:focus {
    outline: none;
    border-color: #88C0D0 !important;
    box-shadow: 0 0 10px rgba(136, 192, 208, 0.5);
  }
</style>

# Hi — I'm Ling 👋

I'm a senior data scientist who loves Bayesian thinking, practical ML, and solving real business problems.

- **PhD in Statistics & Quantitative Economics**
- **Expertise:** Marketing analytics, Retail modeling, and Variational Inference
- **Interests:** Bayesian methods and Applied ML

Say hi! <a href="https://www.linkedin.com/in/wang-lingjie/" target="_blank" rel="noopener noreferrer" style="text-decoration:none;color:inherit;display:inline-flex;align-items:center;gap:8px"><svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" aria-hidden="true" focusable="false" style="vertical-align:middle;"><path fill="#0A66C2" d="M19 0h-14c-2.76 0-5 2.24-5 5v14c0 2.76 2.24 5 5 5h14c2.76 0 5-2.24 5-5v-14c0-2.76-2.24-5-5-5zm-11.75 20h-3v-11h3v11zm-1.5-12.5c-.97 0-1.75-.79-1.75-1.75s.78-1.75 1.75-1.75 1.75.79 1.75 1.75-.78 1.75-1.75 1.75zm13.25 12.5h-3v-5.5c0-1.32-.03-3.01-1.84-3.01-1.84 0-2.12 1.44-2.12 2.92v5.59h-3v-11h2.88v1.5h.04c.4-.76 1.37-1.56 2.82-1.56 3.02 0 3.58 1.99 3.58 4.58v6.48z"/></svg><span style="vertical-align:middle">LinkedIn</span></a>

---

### Bayesian Posterior for Beta-Binomial

Compute the conjugate update for a Beta-Binomial model: $$P(\theta \mid y) \propto \text{Beta}(\alpha + y, \beta + n - y)$$.

<div id="bayesian-calculator" style="background:#1A1C23; padding:20px; border-radius:12px; margin:20px auto; max-width:900px; font-family:monospace; color:#D8DEE9;">
  <div style="margin-bottom:15px; font-size:0.8em; color:#4C566A;">// SYSTEM_RESOLVER_V2.0</div>
  <div style="display:grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr)); gap:15px; margin-bottom:20px;">
    <div>
      <label style="font-size:0.7em;">PRIOR_ALPHA (α)</label><br>
      <input type="number" id="alpha" value="2" step="0.1" style="background:#2E3440; border:1px solid #4C566A; color:#88C0D0; padding:8px; width:100%; border-radius:4px;">
    </div>
    <div>
      <label style="font-size:0.7em;">PRIOR_BETA (β)</label><br>
      <input type="number" id="beta" value="2" step="0.1" style="background:#2E3440; border:1px solid #4C566A; color:#88C0D0; padding:8px; width:100%; border-radius:4px;">
    </div>
    <div>
      <label style="font-size:0.7em;">DATA_SUCCESS (y)</label><br>
      <input type="number" id="success" value="8" style="background:#2E3440; border:1px solid #4C566A; color:#A3BE8C; padding:8px; width:100%; border-radius:4px;">
    </div>
    <div>
      <label style="font-size:0.7em;">DATA_TRIALS (n)</label><br>
      <input type="number" id="trials" value="10" style="background:#2E3440; border:1px solid #4C566A; color:#A3BE8C; padding:8px; width:100%; border-radius:4px;">
    </div>
  </div>
  <div id="bayes-plot" style="width:100%; height:350px;"></div>
</div>

<script src="https://cdn.plot.ly/plotly-2.27.0.min.js"></script>

<script>
(function(){
    function gamma(n) {
        const g = 7;
        const p = [0.99999999999980993, 676.5203681218851, -1259.1392167224028, 771.32342877765313, -176.61502916214059, 12.507343278686905, -0.13857109526572012, 9.9843695780195716e-6, 1.5056327351493116e-7];
        if (n < 0.5) return Math.PI / (Math.sin(Math.PI * n) * gamma(1 - n));
        n -= 1;
        let x = p[0];
        for (let i = 1; i < g + 2; i++) x += p[i] / (n + i);
        let t = n + g + 0.5;
        return Math.sqrt(2 * Math.PI) * Math.pow(t, n + 0.5) * Math.exp(-t) * x;
    }

    function betaPdf(x, a, b) {
        if (x <= 0 || x >= 1) return 0;
        const bFunc = (gamma(a) * gamma(b)) / gamma(a + b);
        return (Math.pow(x, a - 1) * Math.pow(1 - x, b - 1)) / bFunc;
    }

    function updatePlot() {
        const a_prior = parseFloat(document.getElementById('alpha').value) || 0.1;
        const b_prior = parseFloat(document.getElementById('beta').value) || 0.1;
        const y = parseFloat(document.getElementById('success').value) || 0;
        const n = parseFloat(document.getElementById('trials').value) || 0;
        
        const a_post = a_prior + y;
        const b_post = b_prior + n - y;

        const x_vals = Array.from({length: 100}, (_, i) => i / 99);
        const y_prior = x_vals.map(x => betaPdf(x, a_prior, b_prior));
        const y_post = x_vals.map(x => betaPdf(x, a_post, b_post));

        const tracePrior = {
            x: x_vals, y: y_prior, name: 'PRIOR', type: 'scatter', mode: 'lines',
            line: { color: '#4C566A', width: 2, dash: 'dot' },
            fill: 'tozeroy', fillcolor: 'rgba(76, 86, 106, 0.1)'
        };

        const tracePost = {
            x: x_vals, y: y_post, name: 'POSTERIOR', type: 'scatter', mode: 'lines',
            line: { color: '#88C0D0', width: 3 },
            fill: 'tozeroy', fillcolor: 'rgba(136, 192, 208, 0.2)'
        };

        const layout = {
            paper_bgcolor: '#1A1C23', plot_bgcolor: '#1A1C23',
            font: { color: '#D8DEE9', family: 'monospace' },
            margin: { t: 20, b: 40, l: 40, r: 20 },
            xaxis: { gridcolor: '#2E3440', title: 'θ (Probability Parameter)', zeroline: false },
            yaxis: { gridcolor: '#2E3440', showticklabels: false, zeroline: false },
            legend: { orientation: 'h', y: 1.1, x: 0.5, xanchor: 'center' }
        };

        Plotly.newPlot('bayes-plot', [tracePrior, tracePost], layout, {responsive: true, displayModeBar: false});
    }

    ['alpha', 'beta', 'success', 'trials'].forEach(id => {
        document.getElementById(id).addEventListener('input', updatePlot);
    });

    updatePlot();
})();
</script>
