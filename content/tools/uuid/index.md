---
title: "UUID 生成"
desc: "批量生成 UUID v4，支持自定义数量和格式"
---

<div>
  <div class="tool-row">
    <label style="font-size:0.9rem;">生成数量：</label>
    <input id="uuidCount" type="number" value="10" min="1" max="100" style="width:80px;padding:0.4rem 0.6rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;">
    <label style="font-size:0.9rem;margin-left:0.5rem;">
      <input type="checkbox" id="uuidUpper"> 大写
    </label>
    <label style="font-size:0.9rem;margin-left:0.5rem;">
      <input type="checkbox" id="uuidNoDash"> 去除连字符
    </label>
    <button class="tool-btn" onclick="genUUIDs()">生成</button>
    <button class="tool-btn tool-btn-secondary" onclick="copyAll()">复制全部</button>
  </div>

  <div class="tool-label" style="margin-top:0.8rem;">结果 <span id="uuidCopyTip" style="color:#38a169;font-size:0.8rem;"></span></div>
  <textarea id="uuidOutput" rows="16" readonly onclick="this.select()" style="font-family:monospace;font-size:0.85rem;"></textarea>
</div>

<script>
function uuidv4() {
  return ([1e7]+-1e3+-4e3+-8e3+-1e11).replace(/[018]/g, c =>
    (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16)
  );
}
function genUUIDs() {
  const count = Math.min(100, Math.max(1, parseInt(document.getElementById('uuidCount').value) || 1));
  const upper = document.getElementById('uuidUpper').checked;
  const noDash = document.getElementById('uuidNoDash').checked;
  const results = [];
  for (let i = 0; i < count; i++) {
    let id = uuidv4();
    if (noDash) id = id.replace(/-/g, '');
    if (upper) id = id.toUpperCase();
    results.push(id);
  }
  document.getElementById('uuidOutput').value = results.join('\n');
}
function copyAll() {
  const val = document.getElementById('uuidOutput').value;
  if (!val) return;
  navigator.clipboard.writeText(val).then(() => {
    const tip = document.getElementById('uuidCopyTip');
    tip.textContent = '已复制';
    setTimeout(() => tip.textContent = '', 2000);
  });
}
genUUIDs();
</script>
