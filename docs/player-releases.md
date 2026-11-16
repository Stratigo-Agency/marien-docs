# Player Releases

Every Marien Player version, newest first. Install the one marked **newest**, as in [Set Up Your Device](setting-up-your-device.md#android-player). Older versions stay here in case you need one.

<div id="releases" markdown>
<p><em>Loading the list…</em></p>
</div>

<noscript>
<p>Your browser has scripts turned off. <a href="https://api.marien.co.id/player/versions">See the list here instead.</a></p>
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
  function date(iso) { try { return new Date(iso).toLocaleDateString(undefined, { day: 'numeric', month: 'short', year: 'numeric' }); } catch (e) { return iso; } }
  fetch(API + '/player/versions.json')
    .then(function (r) { if (!r.ok) throw new Error(r.status); return r.json(); })
    .then(function (list) {
      if (!list.length) { box.innerHTML = '<p>No player build has been published yet.</p>'; return; }
      var newest = newestOf(list);
      var rows = list.map(function (r) {
        return '<tr><td><b>' + esc(r.version) + '</b>' + (r.version === newest ? ' <span style="color:#7d7d7d">· newest</span>' : '') + '</td>' +
          '<td>' + esc(date(r.published_at)) + '</td>' +
          '<td>' + (r.size_bytes / 1048576).toFixed(1) + ' MB</td>' +
          '<td><a class="md-button md-button--primary" href="' + API + esc(r.download_url) + '" download>Download</a></td></tr>';
      }).join('');
      box.innerHTML = '<table><thead><tr><th>Version</th><th>Published</th><th>Size</th><th></th></tr></thead><tbody>' + rows + '</tbody></table>';
    })
    .catch(function () {
      box.innerHTML = '<p>Could not load the list right now. <a href="' + API + '/player/versions">See it here instead.</a></p>';
    });
})();
</script>
