---
title: "正则测试"
desc: "实时测试正则表达式，高亮显示匹配结果"
---

<div>
  <div style="display:grid;grid-template-columns:1fr auto auto;gap:0.6rem;align-items:center;margin-bottom:0.8rem;">
    <div>
      <div class="tool-label">正则表达式</div>
      <div style="display:flex;align-items:center;border:1px solid var(--border);border-radius:6px;overflow:hidden;background:var(--code-bg);">
        <span style="padding:0 0.5rem;color:var(--secondary);font-size:1.2rem;font-family:monospace;">/</span>
        <input id="regexPattern" type="text" placeholder="输入正则..." oninput="testRegex()" style="flex:1;border:none;background:transparent;color:inherit;padding:0.5rem 0;font-family:monospace;font-size:0.95rem;outline:none;">
        <span style="padding:0 0.5rem;color:var(--secondary);font-size:1.2rem;font-family:monospace;">/</span>
        <input id="regexFlags" type="text" value="g" placeholder="flags" oninput="testRegex()" style="width:60px;border:none;border-left:1px solid var(--border);background:transparent;color:inherit;padding:0.5rem;font-family:monospace;outline:none;">
      </div>
    </div>
  </div>

  <div class="tool-label">测试文本</div>
  <textarea id="regexText" rows="8" placeholder="输入要测试的文本..." oninput="testRegex()"></textarea>

  <div class="tool-row" style="margin-top:0.8rem;">
    <span id="regexStatus" style="font-size:0.9rem;"></span>
  </div>

  <div class="tool-label" style="margin-top:0.8rem;">匹配结果高亮</div>
  <div id="regexHighlight" style="min-height:80px;padding:0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);font-family:monospace;font-size:0.9rem;white-space:pre-wrap;word-break:break-all;line-height:1.6;"></div>

  <div class="tool-label" style="margin-top:0.8rem;">匹配详情</div>
  <div id="regexMatches" style="font-family:monospace;font-size:0.85rem;"></div>
</div>

<style>
.regex-match { background: #f6e05e; color: #1a1a1a; border-radius: 2px; }
.dark .regex-match, [data-theme=dark] .regex-match { background: #d69e2e; color: #1a1a1a; }
.match-item { padding:0.3rem 0.6rem; margin:0.2rem 0; border:1px solid var(--border); border-radius:4px; background:var(--entry); }
</style>

<script>
function testRegex() {
  const pattern = document.getElementById('regexPattern').value;
  const flags = document.getElementById('regexFlags').value;
  const text = document.getElementById('regexText').value;
  const status = document.getElementById('regexStatus');
  const highlight = document.getElementById('regexHighlight');
  const matchesDiv = document.getElementById('regexMatches');

  if (!pattern) {
    highlight.textContent = text;
    matchesDiv.innerHTML = '';
    status.textContent = '';
    return;
  }
  try {
    const regex = new RegExp(pattern, flags.replace(/[^gimsuy]/g, ''));
    const matches = [...text.matchAll(new RegExp(pattern, flags.includes('g') ? flags : flags + 'g'))];
    status.style.color = '#38a169';
    status.textContent = `✓ 共匹配 ${matches.length} 处`;

    // highlight
    let html = '', last = 0;
    for (const m of matches) {
      html += escHtml(text.slice(last, m.index));
      html += `<mark class="regex-match">${escHtml(m[0])}</mark>`;
      last = m.index + m[0].length;
    }
    html += escHtml(text.slice(last));
    highlight.innerHTML = html;

    // details
    matchesDiv.innerHTML = matches.slice(0, 50).map((m, i) =>
      `<div class="match-item">[${i+1}] 位置 ${m.index}：<strong>${escHtml(m[0])}</strong>${m.length > 1 ? '　分组：' + m.slice(1).map((g,j)=>`$${j+1}=${escHtml(g??'undefined')}`).join(', ') : ''}</div>`
    ).join('') + (matches.length > 50 ? `<div style="color:var(--secondary);font-size:0.8rem;">仅显示前 50 条</div>` : '');
  } catch(e) {
    status.style.color = '#e53e3e';
    status.textContent = '✗ ' + e.message;
    highlight.textContent = text;
    matchesDiv.innerHTML = '';
  }
}
function escHtml(s) {
  return (s||'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
}
</script>
