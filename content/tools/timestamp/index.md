---
title: "时间戳转换"
desc: "Unix 时间戳与日期时间的互相转换"
---

<div>
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:2rem;">
    <div>
      <h3 style="margin-top:0;font-size:1rem;">时间戳 → 日期</h3>
      <div class="tool-label">时间戳（秒或毫秒）</div>
      <div class="tool-row">
        <input id="tsInput" type="text" placeholder="如：1700000000" style="flex:1;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-size:0.9rem;outline:none;">
        <button class="tool-btn" onclick="ts2date()">转换</button>
        <button class="tool-btn tool-btn-secondary" onclick="useNow()">当前</button>
      </div>
      <div id="tsResult" style="margin-top:0.8rem;font-size:0.9rem;line-height:2;"></div>
    </div>

    <div>
      <h3 style="margin-top:0;font-size:1rem;">日期 → 时间戳</h3>
      <div class="tool-label">日期时间</div>
      <div class="tool-row">
        <input id="dateInput" type="datetime-local" style="flex:1;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-size:0.9rem;outline:none;">
        <button class="tool-btn" onclick="date2ts()">转换</button>
      </div>
      <div id="dateResult" style="margin-top:0.8rem;font-size:0.9rem;line-height:2;"></div>
    </div>
  </div>

  <div style="margin-top:2rem;padding:1rem;border:1px solid var(--border);border-radius:8px;background:var(--entry);">
    <div style="font-size:0.85rem;color:var(--secondary);margin-bottom:0.5rem;">当前时间</div>
    <div id="nowDisplay" style="font-size:1.1rem;font-family:monospace;"></div>
  </div>
</div>

<script>
function pad(n) { return String(n).padStart(2,'0'); }
function fmt(d) {
  return `${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())} ${pad(d.getHours())}:${pad(d.getMinutes())}:${pad(d.getSeconds())}`;
}
function ts2date() {
  let ts = document.getElementById('tsInput').value.trim();
  if (!ts) return;
  ts = parseInt(ts);
  if (ts < 1e10) ts *= 1000;
  const d = new Date(ts);
  if (isNaN(d)) { document.getElementById('tsResult').innerHTML = '<span style="color:#e53e3e">无效时间戳</span>'; return; }
  document.getElementById('tsResult').innerHTML =
    `本地时间：<strong>${fmt(d)}</strong><br>UTC 时间：<strong>${d.toUTCString()}</strong><br>ISO 8601：<strong>${d.toISOString()}</strong>`;
}
function date2ts() {
  const val = document.getElementById('dateInput').value;
  if (!val) return;
  const d = new Date(val);
  document.getElementById('dateResult').innerHTML =
    `秒级时间戳：<strong>${Math.floor(d.getTime()/1000)}</strong><br>毫秒时间戳：<strong>${d.getTime()}</strong>`;
}
function useNow() {
  document.getElementById('tsInput').value = Math.floor(Date.now()/1000);
  ts2date();
}
function updateNow() {
  const d = new Date();
  document.getElementById('nowDisplay').innerHTML =
    `${fmt(d)}&emsp;秒级：${Math.floor(d.getTime()/1000)}&emsp;毫秒：${d.getTime()}`;
}
updateNow();
setInterval(updateNow, 1000);

// 初始化 datetime-local 为当前时间
const now = new Date();
now.setSeconds(0,0);
document.getElementById('dateInput').value = now.toISOString().slice(0,16);
</script>
