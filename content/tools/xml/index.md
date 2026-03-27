---
title: "XML 格式化"
desc: "XML 格式化、压缩与校验"
---

<div>
  <div class="tool-row">
    <button class="tool-btn" onclick="formatXML()">格式化</button>
    <button class="tool-btn" onclick="compressXML()">压缩</button>
    <button class="tool-btn tool-btn-secondary" onclick="copyXML()">复制结果</button>
    <button class="tool-btn tool-btn-secondary" onclick="clearXML()">清空</button>
    <span id="xmlStatus" style="font-size:0.85rem;"></span>
  </div>
  <div class="tool-cols" style="margin-top:0.8rem;">
    <div>
      <div class="tool-label">输入 XML</div>
      <textarea id="xmlInput" rows="20" placeholder="<root><item>value</item></root>"></textarea>
    </div>
    <div>
      <div class="tool-label">结果 <span id="xmlCopyTip" style="color:#38a169;font-size:0.8rem;"></span></div>
      <textarea id="xmlOutput" rows="20" readonly onclick="this.select()"></textarea>
    </div>
  </div>
</div>

<script>
function formatXML() {
  const status = document.getElementById('xmlStatus');
  const input = document.getElementById('xmlInput').value.trim();
  try {
    const parser = new DOMParser();
    const doc = parser.parseFromString(input, 'application/xml');
    const err = doc.querySelector('parsererror');
    if (err) throw new Error(err.textContent.split('\n')[0]);
    document.getElementById('xmlOutput').value = formatXMLStr(input);
    status.style.color = '#38a169';
    status.textContent = '✓ 合法 XML';
  } catch(e) {
    status.style.color = '#e53e3e';
    status.textContent = '✗ ' + e.message;
    document.getElementById('xmlOutput').value = '';
  }
}
function formatXMLStr(xml) {
  let formatted = '', indent = '';
  xml.split(/>\s*</).forEach(node => {
    if (node.match(/^\/\w/)) indent = indent.slice(2);
    formatted += indent + '<' + node + '>\n';
    if (node.match(/^<?\w[^/]*[^/]$/) && !node.startsWith('?')) indent += '  ';
  });
  return formatted.slice(1, -2);
}
function compressXML() {
  const status = document.getElementById('xmlStatus');
  const input = document.getElementById('xmlInput').value.trim();
  try {
    const parser = new DOMParser();
    const doc = parser.parseFromString(input, 'application/xml');
    if (doc.querySelector('parsererror')) throw new Error('无效 XML');
    document.getElementById('xmlOutput').value = input.replace(/>\s+</g, '><').replace(/\s+/g, ' ').trim();
    status.style.color = '#38a169';
    status.textContent = '✓ 压缩成功';
  } catch(e) {
    status.style.color = '#e53e3e';
    status.textContent = '✗ ' + e.message;
  }
}
function clearXML() {
  document.getElementById('xmlInput').value = '';
  document.getElementById('xmlOutput').value = '';
  document.getElementById('xmlStatus').textContent = '';
}
function copyXML() {
  const val = document.getElementById('xmlOutput').value;
  if (!val) return;
  navigator.clipboard.writeText(val).then(() => {
    const tip = document.getElementById('xmlCopyTip');
    tip.textContent = '已复制';
    setTimeout(() => tip.textContent = '', 2000);
  });
}
</script>
