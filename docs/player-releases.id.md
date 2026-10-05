# Versi Player

Semua versi Marien Player, dari yang terbaru. Versi bertanda **current** adalah yang saat ini diberikan ke layar. Cara pasangnya ada di [Siapkan Perangkat](setting-up-your-device.md#android-player).

<div id="releases" markdown>
<p><em>Memuat daftar…</em></p>
</div>

<noscript>
<p>Script di browsermu sedang dimatikan. <a href="https://api.marien.co.id/player/versions">Lihat daftarnya di sini.</a></p>
</noscript>

<script>
(function () {
  var API = 'https://api.marien.co.id';
  var box = document.getElementById('releases');
  function esc(s) { return String(s).replace(/[&<>"]/g, function (c) { return { '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;' }[c]; }); }
  function date(iso) { try { return new Date(iso).toLocaleDateString('id-ID', { day: 'numeric', month: 'short', year: 'numeric' }); } catch (e) { return iso; } }
  fetch(API + '/player/versions.json')
    .then(function (r) { if (!r.ok) throw new Error(r.status); return r.json(); })
    .then(function (list) {
      if (!list.length) { box.innerHTML = '<p>Belum ada versi player yang dipublikasikan.</p>'; return; }
      var rows = list.map(function (r) {
        return '<tr><td><b>' + esc(r.version) + '</b>' + (r.is_current ? ' <span style="color:#7d7d7d">· current</span>' : '') + '</td>' +
          '<td>' + esc(date(r.published_at)) + '</td>' +
          '<td>' + (r.size_bytes / 1048576).toFixed(1) + ' MB</td>' +
          '<td><a class="md-button md-button--primary" href="' + API + esc(r.download_url) + '" download>Download</a></td></tr>';
      }).join('');
      box.innerHTML = '<table><thead><tr><th>Versi</th><th>Dipublikasikan</th><th>Ukuran</th><th></th></tr></thead><tbody>' + rows + '</tbody></table>';
    })
    .catch(function () {
      box.innerHTML = '<p>Daftar belum bisa dimuat sekarang. <a href="' + API + '/player/versions">Lihat di sini.</a></p>';
    });
})();
</script>
