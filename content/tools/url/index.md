---
title: "URL 编解码"
desc: "对 URL 进行编码或解码，支持 encodeURIComponent / decodeURIComponent"
---

<div class="tool-cols">
  <div>
    <div class="tool-label">输入</div>
    <textarea id="urlInput" rows="10" placeholder="输入需要编码或解码的内容..."></textarea>
    <div class="tool-row">
      <button class="tool-btn" onclick="urlEncode()">编码</button>
      <button class="tool-btn" onclick="urlDecode()">解码</button>
      <button class="tool-btn tool-btn-secondary" onclick="urlSwap()">⇄ 互换</button>
      <button class="tool-btn tool-btn-secondary" onclick="urlClear()">清空</button>
    </div>
  </div>
  <div>
    <div class="tool-label">结果 <span id="urlCopyTip" style="color:#38a169;font-size:0.8rem;"></span></div>
    <textarea id="urlOutput" rows="10" placeholder="结果将显示在这里..." readonly onclick="this.select()"></textarea>
    <div class="tool-row">
      <button class="tool-btn tool-btn-secondary" onclick="copyUrl()">复制结果</button>
    </div>
  </div>
</div>
<div id="urlErr" style="color:#e53e3e;font-size:0.85rem;margin-top:0.5rem;"></div>

<script>
function urlEncode() {
  document.getElementById('urlErr').textContent = '';
  document.getElementById('urlOutput').value = encodeURIComponent(document.getElementById('urlInput').value);
}
function urlDecode() {
  try {
    document.getElementById('urlErr').textContent = '';
    document.getElementById('urlOutput').value = decodeURIComponent(document.getElementById('urlInput').value);
  } catch(e) {
    document.getElementById('urlErr').textContent = '解码失败，请检查输入是否为有效的编码字符串';
  }
}
function urlSwap() {
  const a = document.getElementById('urlInput'), b = document.getElementById('urlOutput');
  [a.value, b.value] = [b.value, a.value];
}
function urlClear() {
  document.getElementById('urlInput').value = '';
  document.getElementById('urlOutput').value = '';
  document.getElementById('urlErr').textContent = '';
}
function copyUrl() {
  const val = document.getElementById('urlOutput').value;
  if (!val) return;
  navigator.clipboard.writeText(val).then(() => {
    const tip = document.getElementById('urlCopyTip');
    tip.textContent = '已复制';
    setTimeout(() => tip.textContent = '', 2000);
  });
}
</script>
