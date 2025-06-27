//# 2x2-ODE-System-Solver-cgpt
//Solving a system of two differential equations, including eigenvalues and eigenvectors, initial conditions, plots and both solutions, //html, css, javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>2x2 ODE System Solver</title>
  <style>
    body {
      font-family: sans-serif;
      background-color: #000;
      color: #0ff;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      height: 100vh;
      margin: 0;
      padding: 1rem;
    }
    input {
      width: 60px;
      margin: 0.2rem;
    }
    canvas {
      margin-top: 1rem;
      border: 1px solid #0ff;
      box-shadow: 0 0 20px #0ff;
      background-color: #111;
    }
    #controls {
      margin: 1rem 0;
    }
    button {
      background-color: #111;
      border: 1px solid #0ff;
      color: #0ff;
      padding: 0.5rem;
      margin: 0.3rem;
      cursor: pointer;
      box-shadow: 0 0 10px #0ff;
    }
    #output {
      white-space: pre-wrap;
      max-width: 700px;
      margin-top: 1rem;
      border: 1px solid #0ff;
      padding: 1rem;
      background: #111;
      box-shadow: 0 0 10px #0ff inset;
    }
  </style>
</head>
<body>
  <h1>2x2 ODE System Solver</h1>
  <div id="controls">
    <label>A = <input id="A" type="number" value="-1" /></label>
    <label>B = <input id="B" type="number" value="2" /></label>
    <label>C = <input id="C" type="number" value="1" /></label>
    <label>D = <input id="D" type="number" value="-3" /></label>
    <button onclick="initializeSystem()">Initialize</button>
    <button id="stepBtn" onclick="nextStep()" disabled>Next Step</button>
  </div>
  <canvas id="solutionCanvas" width="700" height="400"></canvas>
  <div id="output">Enter coefficients and click Initialize.</div>

  <script>
    let step = 0;
    let A, B, C, D;
    let eigenvalues = [], eigenvectors = [];
    let x0 = [100, 1];

    function initializeSystem() {
      A = parseFloat(document.getElementById('A').value);
      B = parseFloat(document.getElementById('B').value);
      C = parseFloat(document.getElementById('C').value);
      D = parseFloat(document.getElementById('D').value);
      step = 1;
      document.getElementById('stepBtn').disabled = false;
      nextStep();
    }

    function nextStep() {
      const output = document.getElementById('output');
      const ctx = document.getElementById('solutionCanvas').getContext('2d');
      switch (step) {
        case 1:
          output.textContent = `Step 1: Matrix form\n\n[dx1/dt] = [${A} ${B}] [x1]\n[dx2/dt]   [${C} ${D}] [x2]`;
          break;
        case 2:
          const tr = A + D;
          const det = A * D - B * C;
          const disc = Math.sqrt(tr * tr - 4 * det);
          const lambda1 = (tr + disc) / 2;
          const lambda2 = (tr - disc) / 2;
          eigenvalues = [lambda1, lambda2];
          output.textContent = `Step 2: Eigenvalues\n\nTrace = ${tr}\nDeterminant = ${det}\nDiscriminant = ${disc}\n\nCharacteristic equation:\nλ² - (${tr})λ + (${det}) = 0\n\nSolutions:\nλ₁ = ${lambda1}\nλ₂ = ${lambda2}`;
          break;
        case 3:
          eigenvectors = eigenvalues.map(lambda => {
            let v1 = 1;
            let v2 = (lambda - A) / B;
            return [v1, v2];
          });
          output.textContent = `Step 3: Eigenvectors\n\nFor λ₁ = ${eigenvalues[0]} → [1, ${(eigenvalues[0] - A) / B}]\nFor λ₂ = ${eigenvalues[1]} → [1, ${(eigenvalues[1] - A) / B}]`;
          break;
        case 4:
          output.textContent = `Step 4: Homogeneous solution\n\nThe general solution to the homogeneous system is:\n\nx(t) = c₁ * v₁ * e^(λ₁ t) + c₂ * v₂ * e^(λ₂ t)\nwhere v₁ and v₂ are the eigenvectors.`;
          break;
        case 5:
          output.textContent = `Step 5: Particular solution\n\nSince the system is homogeneous (no forcing term),\nthe particular solution is zero:\n\nx_p(t) = 0`;
          break;
        case 6:
          if (!eigenvectors.length || !eigenvalues.length) return;
          output.textContent = `Step 6: General solution\n\nx₁(t) = c₁ * ${eigenvectors[0][0]} * e^(${eigenvalues[0]} t) + c₂ * ${eigenvectors[1][0]} * e^(${eigenvalues[1]} t)\nx₂(t) = c₁ * ${eigenvectors[0][1]} * e^(${eigenvalues[0]} t) + c₂ * ${eigenvectors[1][1]} * e^(${eigenvalues[1]} t)`;
          break;
        case 7:
          if (!eigenvectors.length || !eigenvalues.length) return;
          const [[v1x, v1y], [v2x, v2y]] = eigenvectors;
          const [[x1_0], [x2_0]] = [[x0[0]], [x0[1]]];
          const detM = v1x * v2y - v1y * v2x;
          const inv = [
            [ v2y / detM, -v2x / detM],
            [-v1y / detM,  v1x / detM]
          ];
          const c1 = inv[0][0] * x1_0 + inv[0][1] * x2_0;
          const c2 = inv[1][0] * x1_0 + inv[1][1] * x2_0;
          window._c1 = c1;
          window._c2 = c2;
          output.textContent = `Step 7: Apply Initial Conditions\n\nSolving x(0) = c₁ * v₁ + c₂ * v₂:\n\nc₁ = ${c1}\nc₂ = ${c2}`;
          break;
        case 8:
          if (!eigenvectors.length || !eigenvalues.length || window._c1 === undefined) return;
          output.textContent = `Step 8: Final Solution\n\nx₁(t) = ${window._c1} * ${eigenvectors[0][0]} * e^(${eigenvalues[0]} t) + ${window._c2} * ${eigenvectors[1][0]} * e^(${eigenvalues[1]} t)\nx₂(t) = ${window._c1} * ${eigenvectors[0][1]} * e^(${eigenvalues[0]} t) + ${window._c2} * ${eigenvectors[1][1]} * e^(${eigenvalues[1]} t)`;
          break;
        case 9:
          if (!eigenvectors.length || !eigenvalues.length || window._c1 === undefined) return;
          let result = '';
          for (let t = 0; t <= 10; t += 1) {
            const x1 = window._c1 * eigenvectors[0][0] * Math.exp(eigenvalues[0] * t) +
                       window._c2 * eigenvectors[1][0] * Math.exp(eigenvalues[1] * t);
            const x2 = window._c1 * eigenvectors[0][1] * Math.exp(eigenvalues[0] * t) +
                       window._c2 * eigenvectors[1][1] * Math.exp(eigenvalues[1] * t);
            result += `t=${t}: x1=${x1.toFixed(2)}, x2=${x2.toFixed(2)}\n`;
          }
          output.textContent = `Step 9: Numerical Solution at Specific Times\n\n${result}`;
          break;
        case 10:
          if (!eigenvectors.length || !eigenvalues.length || window._c1 === undefined) return;
          output.textContent = 'Step 10: Plotting x1 (left axis) and x2 (right axis)';
          plotSolutions(ctx);
          break;
        case 11:
          if (!eigenvectors.length || !eigenvalues.length || window._c1 === undefined) return;
          output.textContent = 'Step 11: Phase plot x2 vs x1';
          plotPhase(ctx);
          break;
        case 12:
          output.textContent = `Step 12: Comment\n\nSystem with matrix [${A}, ${B}; ${C}, ${D}] and ICs x1(0)=${x0[0]}, x2(0)=${x0[1]}\nSolutions behave based on eigenvalues: stability and direction.`;
          document.getElementById('stepBtn').disabled = true;
          break;
      }
      step++;
    }

    function plotSolutions(ctx) {
      ctx.clearRect(0, 0, 700, 400);
      ctx.beginPath();
      ctx.strokeStyle = '#0f0';
      let scaleX = 70;
      let scaleY1 = 100/ Math.max(...Array.from({length: 11}, (_, t) => Math.abs(window._c1 * eigenvectors[0][0] * Math.exp(eigenvalues[0] * t) + window._c2 * eigenvectors[1][0] * Math.exp(eigenvalues[1] * t))));
      let scaleY2 = 100 / Math.max(...Array.from({length: 11}, (_, t) => Math.abs(window._c1 * eigenvectors[0][1] * Math.exp(eigenvalues[0] * t) + window._c2 * eigenvectors[1][1] * Math.exp(eigenvalues[1] * t))));
      for (let t = 0; t <= 10; t += 0.1) {
        let x = t * scaleX;
        let y1 = 200 - (window._c1 * eigenvectors[0][0] * Math.exp(eigenvalues[0] * t) + window._c2 * eigenvectors[1][0] * Math.exp(eigenvalues[1] * t)) * scaleY1;
        let y2 = 200 - (window._c1 * eigenvectors[0][1] * Math.exp(eigenvalues[0] * t) + window._c2 * eigenvectors[1][1] * Math.exp(eigenvalues[1] * t)) * scaleY2;
        ctx.fillStyle = '#0f0';
        ctx.fillRect(x, y1, 2, 2);
        ctx.fillStyle = '#f0f';
        ctx.fillRect(x, y2, 2, 2);
      }
    }

    function plotPhase(ctx) {
      ctx.clearRect(0, 0, 700, 400);
      ctx.beginPath();
      ctx.strokeStyle = '#0ff';
      for (let t = 0; t <= 10; t += 0.1) {
        let x1 = window._c1 * eigenvectors[0][0] * Math.exp(eigenvalues[0] * t) + window._c2 * eigenvectors[1][0] * Math.exp(eigenvalues[1] * t);
        let x2 = window._c1 * eigenvectors[0][1] * Math.exp(eigenvalues[0] * t) + window._c2 * eigenvectors[1][1] * Math.exp(eigenvalues[1] * t);
        ctx.lineTo(350 + x1, 200 - x2);
      }
      ctx.stroke();
    }
  </script>
</body>
</html>
