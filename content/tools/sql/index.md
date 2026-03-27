---
title: "SQL 格式化"
desc: "SQL 语句格式化与压缩，支持常见 SQL 语法"
---

<div>
  <div class="tool-row">
    <button class="tool-btn" onclick="formatSQL()">格式化</button>
    <button class="tool-btn" onclick="compressSQL()">压缩</button>
    <button class="tool-btn tool-btn-secondary" onclick="copySQL()">复制结果</button>
    <button class="tool-btn tool-btn-secondary" onclick="clearSQL()">清空</button>
  </div>
  <div class="tool-cols" style="margin-top:0.8rem;">
    <div>
      <div class="tool-label">输入 SQL</div>
      <textarea id="sqlInput" rows="20" placeholder="SELECT * FROM users WHERE id = 1;"></textarea>
    </div>
    <div>
      <div class="tool-label">结果 <span id="sqlCopyTip" style="color:#38a169;font-size:0.8rem;"></span></div>
      <textarea id="sqlOutput" rows="20" readonly onclick="this.select()"></textarea>
    </div>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/sql-formatter/15.4.2/sql-formatter.min.js"></script>
<script>
function formatSQL() {
  const input = document.getElementById('sqlInput').value;
  try {
    document.getElementById('sqlOutput').value = sqlFormatter.format(input, { language: 'sql', tabWidth: 2 });
  } catch(e) {
    document.getElementById('sqlOutput').value = '格式化失败: ' + e.message;
  }
}
function compressSQL() {
  const input = document.getElementById('sqlInput').value;
  document.getElementById('sqlOutput').value = input.replace(/\s+/g, ' ').trim();
}
function clearSQL() {
  document.getElementById('sqlInput').value = '';
  document.getElementById('sqlOutput').value = '';
}
function copySQL() {
  const val = document.getElementById('sqlOutput').value;
  if (!val) return;
  navigator.clipboard.writeText(val).then(() => {
    const tip = document.getElementById('sqlCopyTip');
    tip.textContent = '已复制';
    setTimeout(() => tip.textContent = '', 2000);
  });
}
</script>
