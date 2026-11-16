# Versi Player

Semua versi Marien Player, dari yang terbaru. Pasang versi bertanda **terbaru**, caranya ada di [Siapkan Perangkat](setting-up-your-device.md#android-player). Versi lama tetap ada di sini kalau kamu butuh.

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
  // The build to install: the API marks it is_newest; an older API doesn't, so then the
  // highest version number is taken here instead.
  function num(v) { return String(v).split('.').map(function (p) { return parseInt(p, 10) || 0; }); }
  function higher(a, b) { var x = num(a), y = num(b); for (var i = 0; i < Math.max(x.length, y.length); i++) { if ((x[i] || 0) !== (y[i] || 0)) return (x[i] || 0) > (y[i] || 0); } return false; }
  function newestOf(list) {
    var marked = list.filter(function (r) { return r.is_newest; });
    if (marked.length) return marked[0].version;
    return list.reduce(function (best, r) { return best === null || higher(r.version, best) ? r.version : best; }, null);
  }
  function date(iso) { try { return new Date(iso).toLocaleDateString('id-ID', { day: 'numeric', month: 'short', year: 'numeric' }); } catch (e) { return iso; } }
  fetch(API + '/player/versions.json')
    .then(function (r) { if (!r.ok) throw new Error(r.status); return r.json(); })
    .then(function (list) {
      if (!list.length) { box.innerHTML = '<p>Belum ada versi player yang dipublikasikan.</p>'; return; }
      var newest = newestOf(list);
      var rows = list.map(function (r) {
        return '<tr><td><b>' + esc(r.version) + '</b>' + (r.version === newest ? ' <span style="color:#7d7d7d">· terbaru</span>' : '') + '</td>' +
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
