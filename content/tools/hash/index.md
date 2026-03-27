---
title: "MD5 / SHA 加密"
desc: "在线计算字符串的 MD5、SHA-1、SHA-256、SHA-512 哈希值"
---

<div>
  <div class="tool-label">输入文本</div>
  <textarea id="hashInput" rows="6" placeholder="输入需要计算哈希的文本..."></textarea>
  <div class="tool-row">
    <button class="tool-btn" onclick="calcAll()">计算</button>
    <button class="tool-btn tool-btn-secondary" onclick="document.getElementById('hashInput').value='';clearResults()">清空</button>
  </div>

  <div style="margin-top:1.2rem;display:flex;flex-direction:column;gap:0.8rem;">
    <div>
      <div class="tool-label">MD5</div>
      <input id="outMD5" type="text" readonly onclick="this.select()" style="width:100%;box-sizing:border-box;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-family:monospace;">
    </div>
    <div>
      <div class="tool-label">SHA-1</div>
      <input id="outSHA1" type="text" readonly onclick="this.select()" style="width:100%;box-sizing:border-box;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-family:monospace;">
    </div>
    <div>
      <div class="tool-label">SHA-256</div>
      <input id="outSHA256" type="text" readonly onclick="this.select()" style="width:100%;box-sizing:border-box;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-family:monospace;">
    </div>
    <div>
      <div class="tool-label">SHA-512</div>
      <input id="outSHA512" type="text" readonly onclick="this.select()" style="width:100%;box-sizing:border-box;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-family:monospace;word-break:break-all;">
    </div>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.2.0/crypto-js.min.js"></script>
<script>
function clearResults() {
  ['outMD5','outSHA1','outSHA256','outSHA512'].forEach(id => document.getElementById(id).value = '');
}
function calcAll() {
  const text = document.getElementById('hashInput').value;
  document.getElementById('outMD5').value    = CryptoJS.MD5(text).toString();
  document.getElementById('outSHA1').value   = CryptoJS.SHA1(text).toString();
  document.getElementById('outSHA256').value = CryptoJS.SHA256(text).toString();
  document.getElementById('outSHA512').value = CryptoJS.SHA512(text).toString();
}
</script>
