---
title: "进制转换"
desc: "支持二进制、八进制、十进制、十六进制之间的相互转换"
---

<div>
  <div class="tool-label">输入数值</div>
  <div class="tool-row">
    <input id="inputVal" type="text" placeholder="输入数字..." style="flex:1;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-size:1rem;outline:none;" oninput="convert(this.value, document.getElementById('fromBase').value)">
    <select id="fromBase" onchange="convert(document.getElementById('inputVal').value, this.value)" style="padding:0.5rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;">
      <option value="2">二进制</option>
      <option value="8">八进制</option>
      <option value="10" selected>十进制</option>
      <option value="16">十六进制</option>
    </select>
  </div>

  <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:1rem;margin-top:1rem;">
    <div>
      <div class="tool-label">二进制 (Base 2)</div>
      <input id="out2" type="text" readonly style="width:100%;box-sizing:border-box;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-family:monospace;" onclick="this.select()">
    </div>
    <div>
      <div class="tool-label">八进制 (Base 8)</div>
      <input id="out8" type="text" readonly style="width:100%;box-sizing:border-box;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-family:monospace;" onclick="this.select()">
    </div>
    <div>
      <div class="tool-label">十进制 (Base 10)</div>
      <input id="out10" type="text" readonly style="width:100%;box-sizing:border-box;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-family:monospace;" onclick="this.select()">
    </div>
    <div>
      <div class="tool-label">十六进制 (Base 16)</div>
      <input id="out16" type="text" readonly style="width:100%;box-sizing:border-box;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-family:monospace;" onclick="this.select()">
    </div>
  </div>

  <div id="errMsg" style="color:#e53e3e;font-size:0.85rem;margin-top:0.5rem;"></div>
</div>

<script>
function convert(val, base) {
  const err = document.getElementById('errMsg');
  ['out2','out8','out10','out16'].forEach(id => document.getElementById(id).value = '');
  if (!val.trim()) { err.textContent = ''; return; }
  try {
    const decimal = parseInt(val.trim(), parseInt(base));
    if (isNaN(decimal)) throw new Error('无效输入');
    err.textContent = '';
    document.getElementById('out2').value = decimal.toString(2);
    document.getElementById('out8').value = decimal.toString(8);
    document.getElementById('out10').value = decimal.toString(10);
    document.getElementById('out16').value = decimal.toString(16).toUpperCase();
  } catch(e) {
    err.textContent = '输入格式有误，请检查';
  }
}
</script>
