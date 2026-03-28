---
layout: default
title: Lingjie | Data Science & Statistics
---

# Hi — I'm Ling 👋

I'm a senior data scientist who loves Bayesian thinking, practical ML, and solving real business problems.

- **PhD in Statistics & Quantitative Economics**
- **Expertise:** Marketing analytics, Retail modeling, and Variational Inference
- **Interests:** Bayesian methods and Applied ML

Say hi: [LinkedIn](https://www.linkedin.com/in/wang-lingjie/)

---

### Interactive Residual Analysis

_A quick demonstration of OLS residuals._

<div id="residual-plot" style="width:100%;max-width:900px;height:500px;margin:20px auto;border-radius:8px;overflow:hidden;"></div>

<script src="https://cdn.plot.ly/plotly-2.27.0.min.js"></script>
<script>
(function(){
    const n = 200;
    const xs = Array.from({length:n}, (_,i) => i/10 + (Math.random()-0.5)*2);
    const trueSlope = 0.8, trueIntercept = 1.5;
    const ys = xs.map(x => trueIntercept + trueSlope*x + (Math.random()-0.5)*4);

    const mean = arr => arr.reduce((a,b)=>a+b,0)/arr.length;
    const xm = mean(xs), ym = mean(ys);
    
    // OLS Estimator: beta_1 = sum((x-xm)(y-ym)) / sum((x-xm)^2)
    const num = xs.reduce((s,xi,i)=> s + (xi-xm)*(ys[i]-ym), 0);
    const den = xs.reduce((s,xi)=> s + (xi-xm)*(xi-xm), 0) || 1;
    const slope = num/den;
    const intercept = ym - slope*xm;

    const fitted = xs.map(x => slope*x + intercept);
    const residuals = ys.map((y,i) => y - fitted[i]);

    const trace = {
        x: fitted,
        y: residuals,
        mode: 'markers',
        type: 'scatter',
        marker: {
            color: '#88C0D0',
            size: 7,
            opacity: 0.8,
            line: { color: '#2E3440', width: 0.5 }
        },
        hovertemplate: 'Fitted: %{x:.2f}<br>Residual: %{y:.2f}<extra></extra>'
    };

    const layout = {
        title: { 
            text: 'Residuals vs Fitted (n=200)', 
            font: { color: '#ECEFF4', size: 18 } 
        },
        xaxis: { title: 'Fitted Values (ŷ)', gridcolor: '#3B4252', color: '#D8DEE9' },
        yaxis: { title: 'Residuals (e)', gridcolor: '#3B4252', color: '#D8DEE9' },
        plot_bgcolor: '#2E3440',
        paper_bgcolor: '#2E3440',
        font: { color: '#D8DEE9' },
        margin: { t: 80, l: 60, r: 30, b: 60 },
        // The "Senior" touch: Horizontal Zero Line
        shapes: [{
            type: 'line',
            x0: Math.min(...fitted),
            y0: 0,
            x1: Math.max(...fitted),
            y1: 0,
            line: { color: '#BF616A', width: 2, dash: 'dash' }
        }]
    };

    Plotly.newPlot('residual-plot', [trace], layout, {responsive: true, displayModeBar: false});
})();
</script>
