---
title: "IP 查询"
desc: "查询 IP 地址的归属地、运营商等信息"
---

<div>
  <div class="tool-row">
    <input id="ipInput" type="text" placeholder="输入 IP 地址，留空则查询本机 IP" style="flex:1;padding:0.5rem 0.8rem;border:1px solid var(--border);border-radius:6px;background:var(--code-bg);color:inherit;font-size:0.9rem;outline:none;">
    <button class="tool-btn" onclick="queryIP()">查询</button>
    <button class="tool-btn tool-btn-secondary" onclick="queryMyIP()">查询本机 IP</button>
  </div>

  <div id="ipResult" style="margin-top:1.2rem;"></div>
</div>

<style>
.ip-card { border:1px solid var(--border); border-radius:8px; padding:1.2rem; background:var(--entry); margin-top:0.8rem; }
.ip-row { display:flex; gap:1rem; padding:0.4rem 0; border-bottom:1px solid var(--border); font-size:0.9rem; }
.ip-row:last-child { border-bottom:none; }
.ip-key { color:var(--secondary); min-width:100px; }
.ip-val { font-weight:500; word-break:break-all; }
</style>

<script>
async function queryIP() {
  const ip = document.getElementById('ipInput').value.trim();
  const result = document.getElementById('ipResult');
  result.innerHTML = '<span style="color:var(--secondary)">查询中...</span>';
  try {
    const url = ip ? `https://ipapi.co/${ip}/json/` : 'https://ipapi.co/json/';
    const res = await fetch(url);
    const data = await res.json();
    if (data.error) throw new Error(data.reason || '查询失败');
    result.innerHTML = `<div class="ip-card">
      ${row('IP 地址', data.ip)}
      ${row('城市', data.city)}
      ${row('地区', data.region)}
      ${row('国家', data.country_name + (data.country ? ` (${data.country})` : ''))}
      ${row('运营商', data.org || data.asn || '-')}
      ${row('时区', data.timezone)}
      ${row('经纬度', data.latitude && data.longitude ? `${data.latitude}, ${data.longitude}` : '-')}
    </div>`;
  } catch(e) {
    result.innerHTML = `<span style="color:#e53e3e">查询失败：${e.message}</span>`;
  }
}
function queryMyIP() {
  document.getElementById('ipInput').value = '';
  queryIP();
}
function row(k, v) {
  return `<div class="ip-row"><span class="ip-key">${k}</span><span class="ip-val">${v || '-'}</span></div>`;
}
</script>
