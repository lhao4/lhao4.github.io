---
title: "JWT 解析"
desc: "解析 JWT Token 的 Header、Payload 信息，验证签名格式"
---

<div>
  <div class="tool-label">输入 JWT Token</div>
  <textarea id="jwtInput" rows="5" placeholder="粘贴 JWT Token..." oninput="parseJWT()"></textarea>

  <div style="margin-top:1.2rem;display:grid;grid-template-columns:1fr 1fr;gap:1rem;">
    <div>
      <div class="tool-label" style="color:#4299e1;">Header</div>
      <textarea id="jwtHeader" rows="8" readonly style="font-family:monospace;font-size:0.85rem;" onclick="this.select()"></textarea>
    </div>
    <div>
      <div class="tool-label" style="color:#9f7aea;">Payload</div>
      <textarea id="jwtPayload" rows="8" readonly style="font-family:monospace;font-size:0.85rem;" onclick="this.select()"></textarea>
    </div>
  </div>

  <div style="margin-top:1rem;">
    <div class="tool-label">Signature</div>
    <input id="jwtSig" type="text" readonly onclick="this.select()" style="width:100%;box-sizing:border-box;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-family:monospace;">
  </div>

  <div id="jwtInfo" style="margin-top:0.8rem;font-size:0.85rem;"></div>
  <div id="jwtErr" style="color:#e53e3e;font-size:0.85rem;margin-top:0.5rem;"></div>
</div>

<script>
function b64decode(str) {
  str = str.replace(/-/g, '+').replace(/_/g, '/');
  while (str.length % 4) str += '=';
  return decodeURIComponent(atob(str).split('').map(c =>
    '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
  ).join(''));
}
function parseJWT() {
  const token = document.getElementById('jwtInput').value.trim();
  const err = document.getElementById('jwtErr');
  const info = document.getElementById('jwtInfo');
  document.getElementById('jwtHeader').value = '';
  document.getElementById('jwtPayload').value = '';
  document.getElementById('jwtSig').value = '';
  err.textContent = ''; info.innerHTML = '';
  if (!token) return;
  const parts = token.split('.');
  if (parts.length !== 3) { err.textContent = '格式错误：JWT 应由三段组成（header.payload.signature）'; return; }
  try {
    const header = JSON.parse(b64decode(parts[0]));
    const payload = JSON.parse(b64decode(parts[1]));
    document.getElementById('jwtHeader').value = JSON.stringify(header, null, 2);
    document.getElementById('jwtPayload').value = JSON.stringify(payload, null, 2);
    document.getElementById('jwtSig').value = parts[2];

    const tips = [];
    if (payload.exp) {
      const exp = new Date(payload.exp * 1000);
      const expired = exp < new Date();
      tips.push(`<span style="color:${expired?'#e53e3e':'#38a169'}">${expired?'⚠ 已过期':'✓ 未过期'}（exp: ${exp.toLocaleString()}）</span>`);
    }
    if (payload.iat) tips.push(`签发时间：${new Date(payload.iat * 1000).toLocaleString()}`);
    if (payload.sub) tips.push(`Subject: ${payload.sub}`);
    if (header.alg) tips.push(`算法：${header.alg}`);
    info.innerHTML = tips.join('　');
  } catch(e) {
    err.textContent = '解析失败：' + e.message;
  }
}
</script>
