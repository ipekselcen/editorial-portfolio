---
title: Projects
layout: single
permalink: /projects/
classes: wide
---

More of my work is hosted here:  
[Visit my Bioinformatics Projects →](https://ipekselcen.github.io/bioinformatics-projects/)

<div id="projects-list" style="margin-top:1rem;">Loading projects…</div>

<script>
(async function () {
  const container = document.getElementById('projects-list');
  const BASE = 'https://ipekselcen.github.io/bioinformatics-projects';

  function item(href, title, dateStr) {
    const li = document.createElement('li'); li.style.margin='0 0 .85rem 0';
    const a = document.createElement('a'); a.href=href; a.textContent=title||href; a.rel='noopener'; a.style.fontWeight='600';
    li.appendChild(a);
    if (dateStr) { const m=document.createElement('div'); m.style.fontSize='.9rem'; m.style.opacity='.75'; m.textContent=dateStr; li.appendChild(m); }
    return li;
  }

  async function fetchText(u){ const r=await fetch(u,{mode:'cors'}); if(!r.ok) throw new Error(u+': '+r.status); return r.text(); }

  try {
    // Try feed, then sitemap
    let xml = null;
    for (const u of [`${BASE}/feed.xml`, `${BASE}/atom.xml`, `${BASE}/sitemap.xml`]) {
      try { xml = new DOMParser().parseFromString(await fetchText(u), 'application/xml'); break; } catch (_) {}
    }
    if (!xml) throw new Error('no feed/sitemap');

    const ul = document.createElement('ul'); ul.style.listStyle='none'; ul.style.paddingLeft='0';

    const entries = Array.from(xml.querySelectorAll('entry, item'));
    if (entries.length) {
      entries.slice(0,50).forEach(e=>{
        const t=(e.querySelector('title')?.textContent||'Untitled').trim();
        const href = e.querySelector('link[rel="alternate"]')?.getAttribute('href')
                  || e.querySelector('link[href]')?.getAttribute('href')
                  || e.querySelector('guid')?.textContent || '#';
        const d=e.querySelector('updated, pubDate, published')?.textContent||'';
        const nice=d? (isNaN(new Date(d))? d : new Date(d).toLocaleDateString()):'';
        ul.appendChild(item(href,t,nice));
      });
      container.innerHTML=''; container.appendChild(ul); return;
    }

    const urls = Array.from(xml.querySelectorAll('url > loc')).map(n=>n.textContent.trim())
      .filter(u=>u.startsWith(`${BASE}/`))
      .filter(u=>!/\/(assets|page\/\d+|tags|categories|feed\.xml|atom\.xml|sitemap\.xml|index\.html)$/.test(u));

    for (const href of urls) {
      try {
        const doc = new DOMParser().parseFromString(await fetchText(href),'text/html');
        const t = doc.querySelector('h1, .page__title')?.textContent?.trim()
              || decodeURIComponent(href.split('/').filter(Boolean).pop()).replace(/[-_]/g,' ');
        const lastmod = Array.from(xml.querySelectorAll('url'))
          .find(u=>u.querySelector('loc')?.textContent.trim()===href)?.querySelector('lastmod')?.textContent;
        ul.appendChild(item(href, t, lastmod ? new Date(lastmod).toLocaleDateString() : ''));
      } catch { ul.appendChild(item(href, null, '')); }
    }
    container.innerHTML=''; container.appendChild(ul);

  } catch (e) {
    console.error(e);
    container.innerHTML = `
      <p>Couldn’t load the project list. You can view everything directly on the bioinformatics site:</p>
      <p><a href="${BASE}/">Go to Bioinformatics Projects →</a></p>`;
  }
})();
</script>
