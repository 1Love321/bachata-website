# Raffle share poster prototype (A2 Door ticket), whole spec

Source: Fable artifact https://claude.ai/artifact/6sag4KjF3FtW5AadqdGjdt, owner pick A2 (design registry row 95). Only the a2 poster (CSS `.tk`, `.tp`, `.rw`, `.pf`, `.bt`, `.ft`, `.ct`, `.nm`, `.won`, `.ti`, `.me`, `.url`, `.mk`, `.fly`, `.nf`, JS template `T.a2`) is spec; a1/a3/a4/a5 are NOT picked. Winner-page step mock is at the artifact lines for ".prev". Sample data: Maya, Sensual Thursdays, Thu 27 Nov 20:30, The Wardrobe, 148 dancers in the draw.

````html
<!doctype html><html><head><meta charset=utf8><meta name=viewport content="width=device-width,initial-scale=1,viewport-fit=cover"><style>:root{color-scheme:light;box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}html{scroll-padding-top:env(safe-area-inset-top,0px)}body{margin:0;padding:0;font:14px -apple-system,BlinkMacSystemFont,sans-serif;background:#fff;color:#000}img{max-width:100%}[hidden]:not([hidden=until-found i]){display:none!important}</style></head><body>
<title>Raffle Winner Poster Mockups</title>
<meta name="claude:viewport-fit-sides" content="cover">
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:wght@600;800&family=DM+Sans:wght@400;500;600;700&display=swap">
<style>
/* Layout: one column of option sections (poster pair each), then two rows of phone frames showing where the poster is offered. */
:root{
  --bg:#FFFFFF;--surf:#F7F3F0;--fg:#1C1917;--mut:#6B625C;--line:#E6DFD9;--acc:#C2410C;
  /* product colours: fixed in both themes, the app and the poster are white + orange */
  --pw:#FFFFFF;--po:#C2410C;--pi:#1C1917;--pm:#6B625C;--pl:#E6DFD9;--pt:#FDF1EA;
  --disp:"Bricolage Grotesque","Arial Narrow",system-ui,sans-serif;
  --body:"DM Sans",system-ui,-apple-system,"Segoe UI",sans-serif;
}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#161311;--surf:#211D1A;--fg:#F3EEEA;--mut:#A99F98;--line:#3A332E;--acc:#F28B5B;color-scheme:dark}}
:root[data-theme="dark"]{--bg:#161311;--surf:#211D1A;--fg:#F3EEEA;--mut:#A99F98;--line:#3A332E;--acc:#F28B5B;color-scheme:dark}
*{box-sizing:border-box}
body{background:var(--bg);color:var(--fg);font:16px/1.5 var(--body);padding-block:28px 56px;padding-left:max(16px,env(safe-area-inset-left,0px));padding-right:max(16px,env(safe-area-inset-right,0px))}
.wrap{max-width:860px;margin:0 auto;display:flex;flex-direction:column;gap:40px}
h1,h2,h3{font-family:var(--disp);margin:0;text-wrap:balance;line-height:1.1}
h1{font-size:clamp(28px,6vw,40px);font-weight:800}
h2{font-size:24px;font-weight:800}
h3{font-size:18px;font-weight:600}
p{margin:0;max-width:62ch}
.eye{font:600 12px var(--body);letter-spacing:.08em;text-transform:uppercase;color:var(--acc)}
.mut{color:var(--mut);font-size:15px}
.hd{display:flex;flex-direction:column;gap:10px}
.rec{background:var(--surf);border-left:4px solid var(--acc);padding:12px 14px;font-size:15px}
section{display:flex;flex-direction:column;gap:14px}
.tag{display:inline-block;font:700 11px var(--body);letter-spacing:.06em;color:var(--pw);background:var(--po);padding:2px 7px;border-radius:99px;vertical-align:middle}
.pair{display:grid;grid-template-columns:repeat(2,minmax(0,340px));gap:18px}
.frames{display:grid;grid-template-columns:repeat(2,minmax(0,390px));gap:22px;align-items:start}
@media (max-width:620px){.pair,.frames{grid-template-columns:minmax(0,390px)}}
figure{margin:0;display:flex;flex-direction:column;gap:8px}
figcaption{font-size:13px;color:var(--mut)}

/* ---------- poster (4:5, all sizes in cqw so it scales like an image) ---------- */
.p{aspect-ratio:4/5;max-width:100%;container-type:inline-size;position:relative;overflow:hidden;background:var(--pw);color:var(--pi);border:1px solid var(--pl);font-family:var(--body)}
.p .nm{font:800 14cqw/.92 var(--disp);color:var(--po);letter-spacing:-.02em}
.p .won{font:600 6.4cqw/1.1 var(--disp)}
.p .ti{font:800 5.4cqw/1.12 var(--disp)}
.p .ti span{font-weight:600}
.p .me{font:500 3.7cqw/1.35 var(--body);color:var(--pm)}
.p .url{font:700 3cqw/1.25 var(--body);color:var(--po)}
.p .mk{font:700 3cqw/1 var(--body);display:inline-flex;align-items:center;gap:1.2cqw;white-space:nowrap}
.p .mk i{width:2.6cqw;height:2.6cqw;border-radius:50%;background:var(--po)}
.p .ct{font:600 3.3cqw/1.2 var(--body)}
.p .ct b{font-weight:800}
.fly{background:linear-gradient(155deg,#231733,#7A2B4E 48%,#D98A3D);position:relative}
.fly::after{content:"event flyer";position:absolute;left:3cqw;top:3cqw;font:600 2.6cqw var(--body);letter-spacing:.08em;text-transform:uppercase;color:#fff;opacity:.8}
.fly.nf{background:var(--po) repeating-linear-gradient(135deg,rgba(255,255,255,.1) 0 2cqw,transparent 2cqw 6cqw);color:#fff;display:flex;align-items:flex-end;padding:5cqw;font:800 13cqw/.95 var(--disp);letter-spacing:-.02em}
.fly.nf::after{content:none}

/* A1 flyer first */
.a1{display:flex;flex-direction:column}
.a1 .fly{height:50%;flex:none}
.a1 .bd{flex:1;padding:5cqw 6cqw 4.5cqw;display:flex;flex-direction:column;gap:1.6cqw}
.a1 .ft{margin-top:auto;display:flex;justify-content:space-between;align-items:center;gap:3cqw}
.a1 .ct{position:absolute;right:5cqw;top:51cqw;width:23cqw;height:23cqw;border-radius:50%;background:var(--pw);color:var(--po);border:.8cqw solid var(--po);display:flex;flex-direction:column;align-items:center;justify-content:center;text-align:center;font-size:2.5cqw;line-height:1.1;padding:2cqw;transform:rotate(-8deg)}
.a1 .ct b{font:800 7.5cqw/1 var(--disp);display:block}

/* A2 ticket */
.a2{background:var(--po);padding:4cqw;border:0}
.a2 .tk{background:var(--pw);height:100%;border-radius:4cqw;display:flex;flex-direction:column}
.a2 .tp{padding:5cqw 5cqw 4.5cqw;display:flex;flex-direction:column;gap:4cqw}
.a2 .rw{display:flex;gap:4cqw;align-items:flex-end}
.a2 .fly{width:40cqw;height:50cqw;flex:none;border-radius:2cqw}
.a2 .fly.nf{font-size:5.6cqw;padding:3cqw}
.a2 .nm{font-size:12cqw;margin-bottom:1.5cqw}
.a2 .won{font-size:5.6cqw}
.a2 .pf{border-top:.6cqw dashed var(--po);margin:0 5cqw;position:relative}
.a2 .pf::before,.a2 .pf::after{content:"";position:absolute;top:-3.3cqw;width:6cqw;height:6cqw;border-radius:50%;background:var(--po)}
.a2 .pf::before{left:-8cqw}.a2 .pf::after{right:-8cqw}
.a2 .bt{flex:1;padding:4.5cqw 5cqw 5cqw;display:flex;flex-direction:column;gap:1.6cqw}
.a2 .ti{font-size:6.6cqw}
.a2 .ft{margin-top:auto;display:flex;justify-content:space-between;align-items:center;gap:3cqw}
.a2 .ct{border:.5cqw solid var(--po);color:var(--po);border-radius:99cqw;padding:1.4cqw 3cqw;white-space:nowrap}

/* A3 big name */
.a3{padding:6cqw}
.a3 .nm{font-size:27cqw;line-height:.85;margin-top:4cqw}
.a3 .won{font-size:7.6cqw;margin-top:2cqw}
.a3 .fly{position:absolute;right:7cqw;bottom:15cqw;width:40cqw;height:50cqw;border:1.6cqw solid var(--pw);box-shadow:0 2cqw 6cqw rgba(28,25,23,.28);transform:rotate(4deg)}
.a3 .fly.nf{font-size:5.6cqw;padding:3cqw}
.a3 .lf{position:absolute;left:6cqw;bottom:15cqw;width:38cqw;display:flex;flex-direction:column;gap:2cqw}
.a3 .ct{color:var(--po);border-top:.5cqw solid var(--po);padding-top:2cqw}
.a3 .ft{position:absolute;left:6cqw;right:6cqw;bottom:5cqw}

/* A4 split */
.a4{display:grid;grid-template-columns:52% 48%;background:var(--po);color:#fff;border:0}
.a4 .lf{padding:6cqw 5cqw 5cqw 6cqw;display:flex;flex-direction:column;gap:2cqw;min-width:0}
.a4 .nm,.a4 .me,.a4 .url{color:#fff}
.a4 .mk i{background:#fff}
.a4 .nm{margin-top:auto;font-size:13cqw}
.a4 .me{margin-bottom:auto;opacity:.92}
.a4 .ct{border-top:.5cqw solid #fff;padding-top:2cqw}
.a4 .fly.nf{border-left:.5cqw solid #fff;writing-mode:vertical-rl;align-items:center;justify-content:center;font-size:12cqw;padding:0}

/* A5 circle */
.a5{padding:6cqw;display:flex;flex-direction:column;align-items:center;text-align:center;gap:2cqw;background:var(--pw) radial-gradient(circle,var(--po) 0 1.3cqw,transparent 1.4cqw) 0 0/19cqw 23cqw}
.a5::before{content:"";position:absolute;inset:0;background:radial-gradient(ellipse at center,var(--pw) 0 52%,rgba(255,255,255,.72) 78%)}
.a5>*{position:relative}
.a5 .fly{width:50cqw;height:50cqw;border-radius:50%;border:1.2cqw solid var(--pw);box-shadow:0 0 0 1cqw var(--po);overflow:hidden;flex:none;margin-top:1cqw}
.a5 .fly::after{left:0;right:0;top:6cqw;text-align:center}
.a5 .fly.nf{font-size:7cqw;align-items:center;justify-content:center;padding:6cqw}
.a5 .nm{font-size:13cqw;margin-top:2.5cqw}
.a5 .ct{background:var(--po);color:#fff;border-radius:99cqw;padding:1.6cqw 4cqw}
.a5 .ft{margin-top:auto;display:flex;flex-direction:column;align-items:center;gap:1.6cqw}

/* ---------- phone frames (product look, fixed white + orange) ---------- */
.ph{width:100%;background:var(--pw);color:var(--pi);border:1px solid var(--line);border-radius:28px;padding:16px 16px 22px;display:flex;flex-direction:column;gap:14px;font:15px/1.4 var(--body);position:relative;overflow:hidden}
.ph .bar{display:flex;justify-content:space-between;align-items:center;font:700 13px var(--body);color:var(--pm)}
.ph h4{font:800 24px/1.1 var(--disp);margin:0}
.ph .sub{color:var(--pm);font-size:14px}
.ph .prev{width:68%;align-self:center;box-shadow:0 8px 22px rgba(28,25,23,.16)}
.b{min-height:48px;width:100%;border-radius:12px;font:600 16px var(--body);border:1.5px solid var(--po);background:var(--pw);color:var(--po);padding:0 14px;cursor:default}
.b.pri{background:var(--po);color:#fff}
.b.txt{border-color:transparent;text-decoration:underline;text-underline-offset:3px}
.b.sm{min-height:44px;font-size:14px;width:auto;flex:1}
.two{display:flex;gap:10px}
.qr{width:180px;height:180px;align-self:center;border:1px solid var(--pl);border-radius:12px;padding:10px}
.pill{align-self:center;background:var(--pt);color:var(--po);font:700 13px var(--body);padding:6px 14px;border-radius:99px}
.cen{text-align:center}
.row{border:1px solid var(--pl);border-radius:14px;padding:12px;display:flex;flex-direction:column;gap:10px}
.row.dim{opacity:.45}
.rt{display:flex;justify-content:space-between;gap:8px;align-items:baseline}
.rt b{font-size:16px}
.rt span{font-size:13px;color:var(--pm);text-align:right}
.chips{display:flex;gap:6px;flex-wrap:wrap}
.chip{font:600 12px var(--body);padding:3px 9px;border-radius:99px;background:var(--pt);color:var(--po)}
.chip.g{background:#EFEBE8;color:var(--pm)}
.new{position:relative;outline:2px dashed var(--po);outline-offset:3px}
.scrim{position:absolute;inset:0;background:rgba(28,25,23,.5)}
.sheet{position:relative;margin:60px -16px -22px;background:var(--pw);border-radius:22px 22px 0 0;padding:10px 16px 22px;display:flex;flex-direction:column;gap:12px}
.grab{width:40px;height:4px;border-radius:9px;background:var(--pl);align-self:center}
ul{margin:0;padding-left:20px;font-size:15px;color:var(--mut);max-width:62ch}
</style>

<div class="wrap">
  <div class="hd">
    <span class="eye">Raffle · Share poster · mockup round</span>
    <h1>Winner poster, five ways</h1>
    <p class="mut">Each poster says one thing: Maya won a free night at Sensual Thursdays. Same content on all five. Only the layout and tone change. Sample data; buttons do nothing.</p>
    <div class="rec"><b>Recommended: A1.</b> The flyer is what dancers already recognise from the listing, so it leads. The name and the win read at thumbnail size in a WhatsApp chat.</div>
  </div>

  <section><h2>A1 · Flyer first</h2><p class="mut">Flyer on top, win underneath, pool count as a sticker. Familiar and friendly.</p>
    <div class="pair"><figure><div data-p="a1" data-f="1"></div><figcaption>With flyer</figcaption></figure><figure><div data-p="a1"></div><figcaption>No flyer: orange block carries the title</figcaption></figure></div></section>

  <section><h2>A2 · Door ticket</h2><p class="mut">A ticket with a tear line. Playful, and hints that the win gets you in.</p>
    <div class="pair"><figure><div data-p="a2" data-f="1"></div><figcaption>With flyer</figcaption></figure><figure><div data-p="a2"></div><figcaption>No flyer</figcaption></figure></div></section>

  <section><h2>A3 · Big name</h2><p class="mut">The winner's name fills the top. Loud and personal; the flyer sits like a pinned photo.</p>
    <div class="pair"><figure><div data-p="a3" data-f="1"></div><figcaption>With flyer</figcaption></figure><figure><div data-p="a3"></div><figcaption>No flyer</figcaption></figure></div></section>

  <section><h2>A4 · Orange split</h2><p class="mut">Half orange, half flyer. The strongest brand colour of the five; calm and tidy.</p>
    <div class="pair"><figure><div data-p="a4" data-f="1"></div><figcaption>With flyer</figcaption></figure><figure><div data-p="a4"></div><figcaption>No flyer: title runs up the right half</figcaption></figure></div></section>

  <section><h2>A5 · Confetti badge</h2><p class="mut">Round flyer, centred text, orange dots. The most party-like of the five.</p>
    <div class="pair"><figure><div data-p="a5" data-f="1"></div><figcaption>With flyer</figcaption></figure><figure><div data-p="a5"></div><figcaption>No flyer</figcaption></figure></div></section>

  <section>
    <span class="eye">Where it is offered · shown with A1</span>
    <h2>1 · Winner's page</h2>
    <p class="mut">A new step after the winner confirms their night and before the QR screen. The dashed outline marks what is new.</p>
    <div class="frames">
      <figure>
        <div class="ph new">
          <div class="bar"><span>Bachata Calendar</span><span>Night confirmed</span></div>
          <h4>Share your win</h4>
          <div class="sub">Tell your friends you are going. Your QR code is on the next screen.</div>
          <div class="prev" data-p="a1" data-f="1"></div>
          <button type="button" class="b pri" id="w-share">Share</button>
          <button type="button" class="b" id="w-save">Save image</button>
          <button type="button" class="b txt" id="w-skip">Skip, show my QR</button>
        </div>
        <figcaption><span class="tag">NEW</span> Share your win step</figcaption>
      </figure>
      <figure>
        <div class="ph">
          <div class="bar"><span>Bachata Calendar</span><span>Your free night</span></div>
          <div class="cen"><h4>Sensual Thursdays</h4><div class="sub">Thu 27 Nov · 20:30<br>The Wardrobe</div></div>
          <svg class="qr" id="qr" viewBox="0 0 21 21" role="img" aria-label="QR code stand-in"></svg>
          <div class="pill">Starts in 3 days 4 hours</div>
          <div class="two"><button type="button" class="b sm" id="l-cal">Add to calendar</button><button type="button" class="b sm" id="l-dir">Directions</button></div>
          <button type="button" class="b txt" id="l-rel">Can't make it? Release my spot</button>
        </div>
        <figcaption>Then: the existing locked screen, unchanged</figcaption>
      </figure>
    </div>
  </section>

  <section>
    <h2>2 · Organiser admin, Raffles, Winners list</h2>
    <p class="mut">Each winner row gets one more action. Tapping it opens the preview sheet.</p>
    <div class="frames">
      <figure>
        <div class="ph">
          <div class="bar"><span>Raffles</span><span>Winners</span></div>
          <div class="row">
            <div class="rt"><b>Maya</b><span>Sensual Thursdays · Thu 27 Nov</span></div>
            <div class="chips"><span class="chip">To message</span><span class="chip g">Stale</span></div>
            <div class="two"><button type="button" class="b sm" id="a-att">Mark attended</button><button type="button" class="b sm new" id="a-share">Share poster</button></div>
          </div>
          <div class="row dim"><div class="rt"><b>Jordan</b><span>Sensual Thursdays · Thu 4 Dec</span></div><div class="chips"><span class="chip">To message</span></div></div>
        </div>
        <figcaption><span class="tag">NEW</span> Share poster on the winner row</figcaption>
      </figure>
      <figure>
        <div class="ph">
          <div class="bar"><span>Raffles</span><span>Winners</span></div>
          <div class="row"><div class="rt"><b>Maya</b><span>Sensual Thursdays · Thu 27 Nov</span></div></div>
          <div class="scrim"></div>
          <div class="sheet">
            <div class="grab"></div>
            <h4 style="font-size:20px">Maya's poster</h4>
            <div class="prev" data-p="a1" data-f="1" style="width:60%"></div>
            <button type="button" class="b pri" id="s-share">Share</button>
            <button type="button" class="b" id="s-save">Save image</button>
            <button type="button" class="b txt" id="s-close">Close</button>
          </div>
        </div>
        <figcaption><span class="tag">NEW</span> Preview sheet</figcaption>
      </figure>
    </div>
  </section>

  <section>
    <h3>Open points, not decided here</h3>
    <ul>
      <li>Long first names and long event titles need a shrink rule. A3 is the most sensitive to it, A1 and A2 the least.</li>
      <li>The winner's page wording ("Share your win", the helper line, "Night confirmed") is a draft.</li>
      <li>The flyer here is a colour stand-in. Real flyers are cropped to fill their frame.</li>
    </ul>
  </section>
</div>

<script>
(function(){
  var mk='<span class="mk"><i></i>Bachata Calendar</span>',
      url='<span class="url">bachatacalendar.co.uk/<wbr>sensual-thursdays</span>',
      ct='<div class="ct"><b>148</b> dancers in the draw</div>',
      nm='<div class="nm">Maya</div>', won='<div class="won">won a free night</div>',
      ti='<div class="ti"><span>at</span> Sensual Thursdays</div>',
      me='<div class="me">Thu 27 Nov · 20:30<br>The Wardrobe</div>';
  var T={
    a1:function(f){return f+ct+'<div class="bd">'+nm+won+ti+me+'<div class="ft">'+url+mk+'</div></div>'},
    a2:function(f){return '<div class="tk"><div class="tp">'+mk+'<div class="rw">'+f+'<div>'+nm+won+'</div></div></div><div class="pf"></div><div class="bt">'+ti+me+'<div class="ft">'+ct+'</div>'+url+'</div></div>'},
    a3:function(f){return mk+nm+won+f+'<div class="lf">'+ti+me+ct+'</div><div class="ft">'+url+'</div>'},
    a4:function(f){return '<div class="lf">'+mk+nm+won+ti+me+ct+url+'</div>'+f},
    a5:function(f){return f+nm+won+ti+me+ct+'<div class="ft">'+url+mk+'</div>'}
  };
  document.querySelectorAll('[data-p]').forEach(function(e){
    var k=e.getAttribute('data-p');
    var f=e.hasAttribute('data-f')?'<div class="fly"></div>':'<div class="fly nf">Sensual Thursdays</div>';
    e.classList.add('p',k); e.innerHTML=T[k](f);
  });
  // QR stand-in: fixed pattern, three finder squares
  var s='',n=21;
  function fin(x,y){return x>=0&&x<7&&y>=0&&y<7}
  function inF(x,y){return fin(x,y)||fin(x-14,y)||fin(x,y-14)}
  for(var y=0;y<n;y++)for(var x=0;x<n;x++){ if(!inF(x,y)&&((x*7+y*13+x*y*3)%5<2)) s+='<rect x="'+x+'" y="'+y+'" width="1" height="1" fill="#1C1917"/>'; }
  [[0,0],[14,0],[0,14]].forEach(function(p){
    s+='<rect x="'+p[0]+'" y="'+p[1]+'" width="7" height="7" fill="#1C1917"/><rect x="'+(p[0]+1)+'" y="'+(p[1]+1)+'" width="5" height="5" fill="#FFFFFF"/><rect x="'+(p[0]+2)+'" y="'+(p[1]+2)+'" width="3" height="3" fill="#1C1917"/>';
  });
  var q=document.getElementById('qr'); if(q) q.innerHTML=s;
})();
</script>

</body></html>
````
