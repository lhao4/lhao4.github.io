---
title: "JSON 格式化"
desc: "JSON 格式化、压缩、校验，支持语法高亮"
---

<div>
  <div class="tool-row">
    <button class="tool-btn" onclick="formatJSON()">格式化</button>
    <button class="tool-btn" onclick="compressJSON()">压缩</button>
    <button class="tool-btn tool-btn-secondary" onclick="copyJSON()">复制结果</button>
    <button class="tool-btn tool-btn-secondary" onclick="clearJSON()">清空</button>
    <span id="jsonStatus" style="font-size:0.85rem;"></span>
  </div>
  <div class="tool-cols" style="margin-top:0.8rem;">
    <div>
      <div class="tool-label">输入 JSON</div>
      <textarea id="jsonInput" rows="20" placeholder='{"key": "value"}'></textarea>
    </div>
    <div>
      <div class="tool-label">结果 <span id="jsonCopyTip" style="color:#38a169;font-size:0.8rem;"></span></div>
      <textarea id="jsonOutput" rows="20" readonly onclick="this.select()"></textarea>
    </div>
  </div>
</div>

<script>
function formatJSON() {
  const status = document.getElementById('jsonStatus');
  try {
    const obj = JSON.parse(document.getElementById('jsonInput').value);
    document.getElementById('jsonOutput').value = JSON.stringify(obj, null, 2);
    status.style.color = '#38a169';
    status.textContent = '✓ 合法 JSON';
  } catch(e) {
    status.style.color = '#e53e3e';
    status.textContent = '✗ ' + e.message;
    document.getElementById('jsonOutput').value = '';
  }
}
function compressJSON() {
  const status = document.getElementById('jsonStatus');
  try {
    const obj = JSON.parse(document.getElementById('jsonInput').value);
    document.getElementById('jsonOutput').value = JSON.stringify(obj);
    status.style.color = '#38a169';
    status.textContent = '✓ 压缩成功';
  } catch(e) {
    status.style.color = '#e53e3e';
    status.textContent = '✗ ' + e.message;
  }
}
function clearJSON() {
  document.getElementById('jsonInput').value = '';
  document.getElementById('jsonOutput').value = '';
  document.getElementById('jsonStatus').textContent = '';
}
function copyJSON() {
  const val = document.getElementById('jsonOutput').value;
  if (!val) return;
  navigator.clipboard.writeText(val).then(() => {
    const tip = document.getElementById('jsonCopyTip');
    tip.textContent = '已复制';
    setTimeout(() => tip.textContent = '', 2000);
  });
}
</script>
