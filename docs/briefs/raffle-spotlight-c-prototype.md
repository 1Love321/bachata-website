# Raffles Spotlight C prototype (owner picked 2026-11-22; registry row 82)

Source of truth for look and behaviour. Standalone HTML, dark stage, orange. Published copy: https://claude.ai/artifact/FzTH9mqtmnqH2JBZzceCon

```html
<!doctype html><html><head><meta charset=utf8><meta name=viewport content="width=device-width,initial-scale=1,viewport-fit=cover"><style>:root{color-scheme:light;box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}html{scroll-padding-top:env(safe-area-inset-top,0px)}body{margin:0;padding:0;font:14px -apple-system,BlinkMacSystemFont,sans-serif;background:#fff;color:#000}img{max-width:100%}[hidden]:not([hidden=until-found i]){display:none!important}</style></head><body>
<title>Raffles look C</title>
<meta name="claude:viewport-fit-sides" content="cover">
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Anton&family=Fraunces:ital,opsz,wght@0,9..144,700;1,9..144,600;1,9..144,900&family=Manrope:wght@400;600;800&family=Playfair+Display:ital,wght@1,700&display=swap">
<style>
/* Layout: a dark stage. Two spotlights sweep the headline, counts glow as ringed orbs, the nights are one big swipeable poster carousel under the light, winners hang as medals below, and a glass bar sits at the bottom. */
:root{color-scheme:dark;
--bg:#050404;--surface:#130E09;--ink:#FBF4EA;--muted:#A99C88;--line:#32271A;
--accent:#FFA600;--accent-hot:#FFD58A;--accent-deep:#B86A00;--on-accent:#150B00;--bad:#FF7A6B;
--display:'Fraunces',Georgia,'Times New Roman',serif;--body:'Manrope',system-ui,-apple-system,'Segoe UI',sans-serif;
--sheet-bg:#16100A;--sheet-r:30px;--field:#050404;--h-style:italic;--h-weight:900;--btn-font:800 17px/1 var(--body);
--cw:min(40vw,170px)}
*,*::before,*::after{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--ink);font-family:var(--body);font-size:16px;line-height:1.4;-webkit-font-smoothing:antialiased;overflow-x:hidden}
button{font:inherit;color:inherit;border:0;background:none;padding:0;cursor:pointer;-webkit-tap-highlight-color:transparent}
a{color:inherit;-webkit-tap-highlight-color:transparent}
h1,h2,h3,p{margin:0}
h1,h2,h3{text-wrap:balance}
:focus-visible{outline:3px solid var(--ink);outline-offset:3px}
.wrap{max-width:720px;margin-inline:auto;padding-left:max(16px,env(safe-area-inset-left,0px));padding-right:max(16px,env(safe-area-inset-right,0px))}
.mono{font-family:var(--mono,ui-monospace,Menlo,monospace)}
body:not(.is-in) .in-only{display:none!important}
body.is-in .go-only{display:none!important}
.tick{flex:none;vertical-align:-3px}
.nudge{animation:boing .45s cubic-bezier(.3,1.5,.5,1)}
@keyframes boing{0%{scale:1}30%{scale:.9}62%{scale:1.07}100%{scale:1}}
@keyframes spin{to{rotate:360deg}}

/* posters */
.poster{position:relative;aspect-ratio:3/4;width:100%;max-width:100%;overflow:hidden;container-type:inline-size;background:linear-gradient(160deg,var(--p1),var(--p2));color:#fff;isolation:isolate}
.poster svg{position:absolute;inset:0;width:100%;height:100%;z-index:-1}
.poster__txt{position:absolute;inset:0;display:flex;flex-direction:column;justify-content:space-between;padding:8cqw;text-transform:uppercase;text-shadow:0 1px 10px rgba(0,0,0,.4)}
.poster__txt small{font:700 5.2cqw/1.1 var(--body);letter-spacing:.14em}
.poster__txt b{display:block;font:400 17cqw/.95 'Anton','Arial Narrow',Impact,sans-serif;letter-spacing:.01em;overflow-wrap:anywhere}
.poster__txt i{display:block;margin-top:3cqw;font:600 4.6cqw/1.2 var(--body);font-style:normal;letter-spacing:.1em;opacity:.92}
.poster--serif .poster__txt b{font:italic 700 14.5cqw/1 'Playfair Display',Georgia,serif;text-transform:none;letter-spacing:0}
.poster--init{display:grid;place-items:center}
.poster--init::before{content:"";position:absolute;inset:9cqw;border:1.2cqw solid var(--p3);opacity:.35;border-radius:50%;aspect-ratio:1;height:auto;top:50%;translate:0 -50%}
.poster--init span{font:400 40cqw/1 'Anton','Arial Narrow',Impact,sans-serif;color:var(--p3);letter-spacing:.02em}

/* confetti */
.confetti{position:fixed;inset:0;width:100%;height:100%;pointer-events:none;z-index:80}

/* entry sheet */
.sheet{position:fixed;inset:0;z-index:60;display:flex;align-items:flex-end;justify-content:center;visibility:hidden;pointer-events:none;transition:visibility 0s .36s}
.sheet.open{visibility:visible;pointer-events:auto;transition-delay:0s}
.sheet__scrim{position:absolute;inset:0;background:rgba(0,0,0,.74);opacity:0;transition:opacity .28s}
.sheet.open .sheet__scrim{opacity:1}
.sheet__panel{position:relative;width:100%;max-width:480px;max-height:100%;overflow:auto;background:var(--sheet-bg);color:var(--ink);border-radius:var(--sheet-r,24px) var(--sheet-r,24px) 0 0;padding-top:12px;padding-left:max(20px,env(safe-area-inset-left,0px));padding-right:max(20px,env(safe-area-inset-right,0px));padding-bottom:calc(24px + env(safe-area-inset-bottom,0px));transform:translateY(105%);transition:transform .36s cubic-bezier(.2,.85,.25,1)}
.sheet.open .sheet__panel{transform:none}
@media (min-width:600px){.sheet{align-items:center}.sheet__panel{border-radius:var(--sheet-r,24px);padding-bottom:28px}}
.sheet__grab{width:44px;height:5px;border-radius:9px;background:var(--line);margin:0 auto 16px}
.sheet__x{position:absolute;top:6px;right:8px;width:44px;height:44px;border-radius:50%;font-size:28px;line-height:1;color:var(--muted)}
.sheet__eyebrow{font:700 12px/1 var(--body);letter-spacing:.16em;text-transform:uppercase;color:var(--accent)}
.sheet h2{margin:8px 44px 6px 0;font:var(--h-style,normal) var(--h-weight,400) 30px/1.05 var(--display)}
.sheet__for{color:var(--muted);font-size:14px}
.sheet label{display:block;margin:16px 0 6px;font-weight:700;font-size:14px}
.sheet input{display:block;width:100%;height:58px;padding:0 16px;font:700 22px/1 var(--body);letter-spacing:.04em;font-variant-numeric:tabular-nums;color:var(--ink);background:var(--field);border:2px solid var(--line);border-radius:var(--field-r,14px)}
.sheet input::placeholder{color:var(--muted);opacity:.55}
.sheet input:focus{outline:none;border-color:var(--accent);box-shadow:0 0 0 4px rgba(255,166,0,.25)}
.sheet input[aria-invalid="true"]{border-color:var(--bad)}
.sheet__err{margin-top:8px;color:var(--bad);font-size:14px;font-weight:600}
.sheet__note{margin:12px 0 14px;color:var(--muted);font-size:14px;text-align:center}
.sheet__go{display:block;width:100%;min-height:56px;border-radius:var(--go-r,999px);background:var(--accent);color:var(--on-accent);font:var(--btn-font);transition:scale .12s}
.sheet__go:active{scale:.96}
.sheet__done{text-align:center;padding-top:6px;outline:none}
.sheet__done h2{margin:14px 0 6px}
.sheet__done p{color:var(--muted);margin-bottom:18px}
.sheet__tick{width:88px;height:88px;margin:0 auto;border-radius:50%;display:grid;place-items:center;background:var(--accent);color:var(--on-accent);box-shadow:0 0 0 10px rgba(255,166,0,.18),0 0 0 22px rgba(255,166,0,.08);animation:tickpop .7s cubic-bezier(.2,1.6,.4,1) both}
.sheet__tick .tick{width:46px;height:46px}
@keyframes tickpop{from{scale:0;rotate:-90deg}}
.shake{animation:shake .4s}
@keyframes shake{20%{translate:-8px 0}40%{translate:7px 0}60%{translate:-5px 0}80%{translate:3px 0}}

/* footer */
.foot{margin-top:56px;text-align:center;color:var(--muted);font-size:14px}
.foot nav{display:flex;flex-wrap:wrap;justify-content:center;gap:2px}
.foot a,.foot button{display:inline-flex;align-items:center;min-height:44px;padding:0 14px;text-decoration:underline;text-underline-offset:4px;text-decoration-color:var(--line)}
.foot__info{max-width:34ch;margin:6px auto 0;color:var(--ink)}

@media (prefers-reduced-motion:reduce){
  *,*::before,*::after{animation-duration:.01ms!important;animation-delay:0s!important;animation-iteration-count:1!important;transition-duration:.01ms!important;scroll-behavior:auto!important}
}

body{padding-bottom:calc(108px + env(safe-area-inset-bottom,0px))}
.hero{position:relative;overflow:hidden;text-align:center;margin-top:calc(-1*env(safe-area-inset-top,0px));padding-top:calc(env(safe-area-inset-top,0px) + 28px);min-height:420px;display:grid;place-items:center;padding-bottom:84px;background:radial-gradient(120% 70% at 50% 0,#2A1A05 0,transparent 70%)}
.beam{position:absolute;top:-6%;width:70%;height:118%;background:linear-gradient(180deg,rgba(255,176,40,.38),rgba(255,166,0,.08) 60%,transparent 88%);clip-path:polygon(46% 0,54% 0,100% 100%,0 100%);transform-origin:50% 0;filter:blur(7px);mix-blend-mode:screen;pointer-events:none}
.beam--l{left:8%;animation:xL 7s ease-in-out infinite alternate}
.beam--r{right:8%;animation:xR 8.5s ease-in-out infinite alternate}
@keyframes xL{from{rotate:-4deg}to{rotate:26deg}}@keyframes xR{from{rotate:4deg}to{rotate:-26deg}}
.pool{position:absolute;left:50%;bottom:90px;width:90%;height:90px;translate:-50% 0;border-radius:50%;background:radial-gradient(closest-side,rgba(255,166,0,.55),rgba(255,166,0,.12) 60%,transparent);pointer-events:none}
.bok{position:absolute;width:46px;height:46px;border-radius:50%;background:radial-gradient(closest-side,rgba(255,213,138,.5),transparent);animation:float 5s ease-in-out infinite;pointer-events:none}
@keyframes sweepL{from{rotate:-24deg}to{rotate:-2deg}}
@keyframes sweepR{from{rotate:22deg}to{rotate:0deg}}
.party .beam{animation-duration:1.1s;background:linear-gradient(180deg,rgba(255,213,138,.7),rgba(255,166,0,.2) 60%,transparent 90%)}
.embers{position:absolute;inset:0;pointer-events:none}
.embers i{position:absolute;bottom:-12px;border-radius:50%;background:var(--accent-hot);box-shadow:0 0 8px 2px rgba(255,166,0,.7);opacity:0;animation:rise linear infinite}
@keyframes rise{0%{translate:0 0;opacity:0}12%{opacity:.9}100%{translate:var(--dx) -560px;opacity:0}}
.hero .wrap{position:relative;width:100%}
.eyebrow{font-size:12px;font-weight:800;letter-spacing:.24em;text-transform:uppercase;color:var(--accent)}
h1{margin:0;font:700 clamp(34px,9.6vw,56px)/1.04 var(--display);letter-spacing:-.015em}
h1 em{display:block;font-style:italic;font-weight:900;background:linear-gradient(100deg,#FFA600 25%,#FFE9BD 42%,#FFA600 58%);background-size:230% 100%;-webkit-background-clip:text;background-clip:text;color:transparent;animation:sheen 3.6s linear infinite;padding-bottom:.08em}
@keyframes sheen{from{background-position:120% 0}to{background-position:-110% 0}}
.lede{max-width:30ch;margin-inline:auto;color:var(--muted)}

.under{position:relative;z-index:2;margin-top:-56px}
.orbs{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:10px;max-width:420px;margin:0 auto}
.orb{position:relative;aspect-ratio:1;max-width:100%;border-radius:50%;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:4px;background:radial-gradient(circle at 50% 30%,#2E1E08,#0B0805 72%);animation:float 4.5s ease-in-out infinite;animation-delay:calc(var(--n)*-1.5s)}
@keyframes float{50%{translate:0 -7px}}
.orb::before{content:"";position:absolute;inset:0;border-radius:50%;background:conic-gradient(var(--accent),transparent 28%,transparent 48%,var(--accent-hot) 62%,transparent 80%,var(--accent));-webkit-mask:radial-gradient(farthest-side,#0000 calc(100% - 3.5px),#000 calc(100% - 3px));mask:radial-gradient(farthest-side,#0000 calc(100% - 3.5px),#000 calc(100% - 3px));animation:spin 5s linear infinite;animation-delay:calc(var(--n)*-1.2s)}
.orb b{font:italic 900 clamp(22px,6.6vw,34px)/1 var(--display);color:var(--accent-hot);font-variant-numeric:tabular-nums;text-shadow:0 0 20px rgba(255,166,0,.6)}
.orb span{font-size:10px;font-weight:800;letter-spacing:.06em;text-transform:uppercase;color:var(--muted)}

.how{list-style:none;display:flex;flex-direction:column;align-items:center;gap:2px;max-width:420px;margin:24px auto 0;padding:0;font-size:14px;font-weight:700;color:var(--ink)}
.how li{display:flex;align-items:center;gap:6px}
.how li{justify-content:center;gap:10px;padding:4px 0}
.how b{flex:none;width:26px;height:26px;border-radius:50%;display:grid;place-items:center;background:var(--accent);color:var(--on-accent);font:italic 900 14px/1 var(--display)}

main{display:flex;flex-direction:column;gap:56px;margin-top:44px}
h2{font:italic 900 28px/1.1 var(--display)}
.head{display:flex;align-items:baseline;justify-content:space-between;gap:12px}
.count{font-size:14px;font-weight:600;color:var(--muted);font-variant-numeric:tabular-nums;white-space:nowrap}
.count b{font:italic 900 22px/1 var(--display);color:var(--accent)}

.reel{position:relative;display:flex;gap:14px;list-style:none;margin:0;padding:18px calc(50% - var(--cw)/2) 16px;overflow-x:auto;scroll-snap-type:x mandatory;scrollbar-width:none;-webkit-overflow-scrolling:touch;background:radial-gradient(60% 55% at 50% 42%,rgba(255,166,0,.2),transparent 70%)}
.reel::-webkit-scrollbar{display:none}
.slide{flex:0 0 var(--cw);min-width:0;scroll-snap-align:center;display:flex;flex-direction:column;gap:14px;text-align:center;will-change:transform}
.slide__art{position:relative}
.slide.cur .slide__art{animation:float 3.6s ease-in-out infinite}
.slide .poster{border-radius:22px;box-shadow:0 34px 60px -22px rgba(255,166,0,.6),0 0 0 1px rgba(255,255,255,.1)}
.slide h3{font:700 20px/1.18 var(--display);min-height:2.36em;display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden}
.enter{display:inline-flex;align-items:center;justify-content:center;gap:7px;width:100%;min-height:50px;border-radius:999px;background:var(--accent);color:var(--on-accent);font:800 16px/1 var(--body);box-shadow:0 10px 26px -8px rgba(255,166,0,.8);transition:scale .18s cubic-bezier(.3,1.6,.5,1)}
.enter:active{scale:.93}
.enter .in-only{display:inline-flex;align-items:center;gap:7px}
.is-in .enter{background:none;color:var(--accent-hot);box-shadow:inset 0 0 0 1.5px var(--accent)}
.seal{position:absolute;left:50%;top:50%;width:84px;height:84px;margin:-42px 0 0 -42px;border-radius:50%;display:grid;place-items:center;background:radial-gradient(circle at 35% 30%,#FFE9BD,#FFA600 58%,#B86A00);color:#2B1600;box-shadow:0 0 0 8px rgba(255,166,0,.25),0 14px 30px rgba(0,0,0,.6);animation:sealpop .7s cubic-bezier(.2,1.6,.4,1) both}
.seal .tick{width:44px;height:44px}
@keyframes sealpop{from{scale:0;rotate:-120deg}}
.nav{display:flex;align-items:center;gap:14px}
.arrow{flex:none;width:48px;height:48px;border-radius:50%;box-shadow:inset 0 0 0 1.5px var(--line);font-size:26px;line-height:1;color:var(--accent);transition:scale .15s}
.arrow:active{scale:.88}
.prog{flex:1;height:4px;border-radius:4px;background:var(--line);overflow:hidden}
.prog i{display:block;height:100%;width:7.7%;border-radius:4px;background:var(--accent);transition:width .3s}

.medals{list-style:none;margin:28px 0 0;padding:0;display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:44px 14px;text-align:center}
.medal{display:flex;flex-direction:column;align-items:center;gap:6px;min-width:0;color:inherit;text-decoration:none}
.medal-li{list-style:none}.medal-li.seen .medal{animation:drop .7s cubic-bezier(.2,1.4,.4,1) both;animation-delay:calc(var(--n)*80ms)}
.medal.seen{animation:drop .7s cubic-bezier(.2,1.4,.4,1) both;animation-delay:calc(var(--n)*80ms)}
@keyframes drop{from{translate:0 -26px;scale:.8;opacity:.2}}
.medal__go{margin-top:6px;font-size:13px;font-weight:700;color:var(--accent-hot);text-decoration:underline;text-underline-offset:4px;display:inline-flex;align-items:center;min-height:44px}
.medal__disc{position:relative;width:84px;height:84px;margin-bottom:18px;border-radius:50%;display:grid;place-items:center;background:radial-gradient(circle at 35% 30%,#FFE9BD,#FFA600 58%,#B86A00);color:#2B1600;box-shadow:0 0 30px rgba(255,166,0,.5)}
.medal__disc i{position:absolute;inset:-8px;border-radius:50%;background:conic-gradient(transparent 0 18%,#FFE9BD 30%,transparent 42% 68%,#FFA600 82%,transparent 94%);-webkit-mask:radial-gradient(farthest-side,#0000 calc(100% - 3.5px),#000 calc(100% - 3px));mask:radial-gradient(farthest-side,#0000 calc(100% - 3.5px),#000 calc(100% - 3px));animation:spin 3.4s linear infinite;animation-delay:calc(var(--n)*-.8s)}
.medal b{font:italic 900 22px/1.1 var(--display);background:linear-gradient(100deg,#FFA600 25%,#FFF1D0 42%,#FFA600 58%);background-size:230% 100%;-webkit-background-clip:text;background-clip:text;color:transparent;animation:sheen 3s linear infinite;animation-delay:calc(var(--n)*-.7s);padding:0 .1em .06em}
.medal span{font-size:13.5px;color:var(--muted);line-height:1.3}
.medal em{margin-top:8px;font-style:normal;font-size:10.5px;font-weight:800;letter-spacing:.12em;text-transform:uppercase;color:var(--accent)}

.bar{position:fixed;left:0;right:0;bottom:0;z-index:40;background:rgba(12,9,6,.8);-webkit-backdrop-filter:blur(16px);backdrop-filter:blur(16px);border-top:1px solid var(--line);padding-top:10px;padding-bottom:calc(10px + env(safe-area-inset-bottom,0px));padding-left:max(16px,env(safe-area-inset-left,0px));padding-right:max(16px,env(safe-area-inset-right,0px))}
.bar__in{display:flex;align-items:center;gap:12px;max-width:688px;margin-inline:auto;min-height:56px}
.bar__txt{flex:1;min-width:0;font-size:12.5px;line-height:1.3;color:var(--muted)}
.bar__txt b{display:block;font:italic 900 18px/1.1 var(--display);color:var(--ink)}
.bar__btn{position:relative;isolation:isolate;flex:none;min-height:56px;padding:0 22px;border-radius:999px;background:var(--accent);color:var(--on-accent);font:800 16px/1 var(--body);transition:scale .18s cubic-bezier(.3,1.6,.5,1)}
.bar__btn:active{scale:.93}
.bar__btn::before{content:"";position:absolute;z-index:-1;inset:-5px;border-radius:999px;background:conic-gradient(var(--accent-hot),transparent 30%,transparent 55%,var(--accent) 75%,var(--accent-hot));-webkit-mask:linear-gradient(#000 0 0) content-box,linear-gradient(#000 0 0);-webkit-mask-composite:xor;mask:linear-gradient(#000 0 0) content-box exclude,linear-gradient(#000 0 0);padding:2px;opacity:.9;animation:glow 2.2s ease-in-out infinite}
@keyframes glow{50%{opacity:.25;inset:-9px}}
.bar__done{display:flex;align-items:center;gap:12px;width:100%;animation:drop .5s cubic-bezier(.2,1.4,.4,1) both}
.bar__done>span:first-child{flex:none;width:44px;height:44px;border-radius:50%;display:grid;place-items:center;background:var(--accent);color:var(--on-accent)}
.bar__done b{flex:1;min-width:0;font:italic 900 18px/1.15 var(--display);color:var(--accent-hot)}
.bar__reset{flex:none;min-height:44px;padding:0 10px;font-size:14px;font-weight:600;color:var(--muted);text-decoration:underline;text-underline-offset:4px}

.sheet__panel{background:linear-gradient(180deg,#1E150A,#110C07);box-shadow:0 -1px 0 rgba(255,166,0,.5),0 -30px 80px -20px rgba(255,166,0,.35)}
.sheet__tick .tick path{stroke-dasharray:24;stroke-dashoffset:24;animation:draw .5s .35s ease-out forwards}
@keyframes draw{to{stroke-dashoffset:0}}

@media (min-width:700px){.medals{grid-template-columns:repeat(4,minmax(0,1fr))}}
@media (prefers-reduced-motion:reduce){.sheet__tick .tick path{stroke-dashoffset:0}}
</style>

<header class="hero">
 <div class="beam beam--l" aria-hidden="true"></div>
 <div class="beam beam--r" aria-hidden="true"></div>
 <i class="pool" aria-hidden="true"></i><i class="bok" style="left:12%;top:20%" aria-hidden="true"></i><i class="bok" style="right:10%;top:34%" aria-hidden="true"></i><i class="bok" style="left:20%;bottom:30%" aria-hidden="true"></i>
 <div class="embers" id="embers" aria-hidden="true"></div>
 <div class="wrap">
  <h1><em>Win a free night</em></h1>
 </div>
</header>
<div class="wrap under">
  <div class="orbs">
   <div class="orb" style="--n:0"><b data-count="1284" data-entries>1,284</b><span>Entries</span></div>
   <div class="orb" style="--n:1"><b data-count="13">13</b><span>Events open</span></div>
   <div class="orb" style="--n:2"><b data-count="96">96</b><span>Prizes won</span></div>
  </div>
  <ol class="how"><li><b>1</b>Pick a night</li><li><b>2</b>Add your phone</li><li><b>3</b>Win a free entry</li></ol>
</div>

<main>
 <section aria-labelledby="evH">
  <div class="wrap head"><h2 id="evH">Pick a night</h2><p class="count"><b id="pos">1</b> of 13</p></div>
  <ul class="reel" id="events" tabindex="0" aria-label="Nights you can win"></ul>
  <div class="wrap nav"><button type="button" class="arrow" id="prev" aria-label="Previous night">&lsaquo;</button><div class="prog" aria-hidden="true"><i id="progbar"></i></div><button type="button" class="arrow" id="next" aria-label="Next night">&rsaquo;</button></div>
 </section>

 <section class="wrap" aria-labelledby="winH">
  <h2 id="winH" style="text-align:center">This week's winners</h2>
  <ul class="medals">
   <li data-reveal style="--n:0" class="medal-li"><a class="medal" href="#profile" data-profile><span class="medal__disc"><i></i><svg viewBox="0 0 24 24" width="40" height="40" aria-hidden="true"><path fill="currentColor" d="M7 3h10v2h3v3a4 4 0 0 1-4 4h-.3a5 5 0 0 1-2.7 2.6V18h3v3H8v-3h3v-3.4A5 5 0 0 1 8.3 12H8a4 4 0 0 1-4-4V5h3V3zm0 4H6v1a2 2 0 0 0 1 1.7V7zm10 0v2.7A2 2 0 0 0 18 8V7h-1z"/></svg></span><b>Maya R.</b><span>Bachata Sensual Fridays</span><em>won a free entry</em><span class="medal__go">View profile &rsaquo;</span></a></li>
   <li data-reveal style="--n:1" class="medal-li"><a class="medal" href="#profile" data-profile><span class="medal__disc"><i></i><svg viewBox="0 0 24 24" width="40" height="40" aria-hidden="true"><path fill="currentColor" d="M7 3h10v2h3v3a4 4 0 0 1-4 4h-.3a5 5 0 0 1-2.7 2.6V18h3v3H8v-3h3v-3.4A5 5 0 0 1 8.3 12H8a4 4 0 0 1-4-4V5h3V3zm0 4H6v1a2 2 0 0 0 1 1.7V7zm10 0v2.7A2 2 0 0 0 18 8V7h-1z"/></svg></span><b>Tom K.</b><span>Luna Llena Bachata Party</span><em>won a free entry</em><span class="medal__go">View profile &rsaquo;</span></a></li>
   <li data-reveal style="--n:2" class="medal-li"><a class="medal" href="#profile" data-profile><span class="medal__disc"><i></i><svg viewBox="0 0 24 24" width="40" height="40" aria-hidden="true"><path fill="currentColor" d="M7 3h10v2h3v3a4 4 0 0 1-4 4h-.3a5 5 0 0 1-2.7 2.6V18h3v3H8v-3h3v-3.4A5 5 0 0 1 8.3 12H8a4 4 0 0 1-4-4V5h3V3zm0 4H6v1a2 2 0 0 0 1 1.7V7zm10 0v2.7A2 2 0 0 0 18 8V7h-1z"/></svg></span><b>Aisha B.</b><span>Sabor Latino Thursdays</span><em>won a free entry</em><span class="medal__go">View profile &rsaquo;</span></a></li>
   <li data-reveal style="--n:3" class="medal-li"><a class="medal" href="#profile" data-profile><span class="medal__disc"><i></i><svg viewBox="0 0 24 24" width="40" height="40" aria-hidden="true"><path fill="currentColor" d="M7 3h10v2h3v3a4 4 0 0 1-4 4h-.3a5 5 0 0 1-2.7 2.6V18h3v3H8v-3h3v-3.4A5 5 0 0 1 8.3 12H8a4 4 0 0 1-4-4V5h3V3zm0 4H6v1a2 2 0 0 0 1 1.7V7zm10 0v2.7A2 2 0 0 0 18 8V7h-1z"/></svg></span><b>Leo M.</b><span>Dominican Roots Night</span><em>won a free entry</em><span class="medal__go">View profile &rsaquo;</span></a></li>
  </ul>
 </section>
</main>

<footer class="foot wrap">
 <nav aria-label="Footer"><a href="#rules" data-info="rules">Rules</a><a href="#privacy" data-info="privacy">Privacy</a><a href="#contact" data-info="contact">Contact</a><button type="button" data-reset>Reset</button></nav>
 <p class="foot__info" id="info" aria-live="polite" hidden></p>
</footer>

<div class="bar">
 <div class="bar__in go-only"><p class="bar__txt"><b>Free</b>One number, every night</p><button type="button" class="bar__btn" data-enter>Enter the raffle</button></div>
 <div class="bar__in in-only"><div class="bar__done"><span><svg class="tick" viewBox="0 0 24 24" width="24" height="24" aria-hidden="true"><path d="M5 12.5l4.5 4.5L19 7.5" fill="none" stroke="currentColor" stroke-width="3.2" stroke-linecap="round" stroke-linejoin="round"/></svg></span><b>You're in for every night</b><button type="button" class="bar__reset" data-reset>Reset</button></div></div>
</div>

<script>
window.LOOK={
 colors:['#FFA600','#FFD58A','#FFFFFF','#FF7A00'],
 shapes:['spark','dot','spark'],
 card:(e,i,p,T)=>`<li class="slide" style="--i:${i}">
  <div class="slide__art">${p}<span class="seal in-only" aria-hidden="true">${T}</span></div>
  <h3>${e.name}</h3>
  <button type="button" class="enter" data-enter data-name="${e.name}"><span class="go-only">Enter</span><span class="in-only">${T}You're in</span></button></li>`,
 init(api){
  const reel=api.$('#events'),slides=api.$$('.slide',reel),pos=api.$('#pos'),bar=api.$('#progbar');let tk=0;
  function upd(){
   tk=0;const mid=reel.clientWidth/2;let best=0,bd=1e9;
   slides.forEach((s,i)=>{
    const w=s.offsetWidth,raw=(s.offsetLeft+w/2-reel.scrollLeft-mid)/w,d=Math.max(-1.2,Math.min(1.2,raw)),a=Math.min(Math.abs(d),1);
    if(Math.abs(raw)<bd){bd=Math.abs(raw);best=i}
    if(!api.RM){s.style.transform=`translateY(${(a*16).toFixed(1)}px) scale(${(1-a*.1).toFixed(3)}) rotate(${(d*2.5).toFixed(2)}deg)`;s.style.opacity=(1-a*.45).toFixed(2)}
   });
   slides.forEach((s,i)=>s.classList.toggle('cur',i===best));
   pos.textContent=best+1;bar.style.width=((best+1)/slides.length*100)+'%';
  }
  reel.addEventListener('scroll',()=>{if(!tk)tk=requestAnimationFrame(upd)},{passive:true});
  addEventListener('resize',upd);upd();
  const go=dir=>reel.scrollBy({left:dir*(slides[0].offsetWidth+14),behavior:api.RM?'auto':'smooth'});
  api.$('#prev').addEventListener('click',()=>go(-1));
  api.$('#next').addEventListener('click',()=>go(1));
  this.reel=reel;
  if(!api.RM){
   const box=api.$('#embers');let h='';
   for(let i=0;i<45;i++){const s=2+Math.random()*4;h+=`<i style="left:${(Math.random()*100).toFixed(1)}%;width:${s.toFixed(1)}px;height:${s.toFixed(1)}px;--dx:${(Math.random()*80-40).toFixed(0)}px;animation-duration:${(5+Math.random()*6).toFixed(1)}s;animation-delay:${(-Math.random()*10).toFixed(1)}s"></i>`}
   box.innerHTML=h;
  }
 },
 onEnter(){document.body.classList.add('party');setTimeout(()=>document.body.classList.remove('party'),4500)},
 onReset(){this.reel.scrollTo({left:0})},
 celebrate(api){
  const w=innerWidth,h=innerHeight;
  for(let k=0;k<7;k++)setTimeout(()=>api.burst(w*(.15+Math.random()*.7),h*(.12+Math.random()*.4),46,{spread:Math.PI*2,power:9+Math.random()*4}),k*260);
 }
};
</script>
<script>
(function(){
'use strict';
const LOOK=window.LOOK||{};
const RM=!!(window.matchMedia&&matchMedia('(prefers-reduced-motion: reduce)').matches);
const $=(s,r)=>(r||document).querySelector(s);
const $$=(s,r)=>Array.from((r||document).querySelectorAll(s));
const esc=s=>s.replace(/[&<>"]/g,c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;'}[c]));
const TICK='<svg class="tick" viewBox="0 0 24 24" width="18" height="18" aria-hidden="true"><path d="M5 12.5l4.5 4.5L19 7.5" fill="none" stroke="currentColor" stroke-width="3.2" stroke-linecap="round" stroke-linejoin="round"/></svg>';

const EVENTS=[
 {n:'Bachata Sensual Fridays',s:'Sensual Fridays',t:'Bachata social',f:'DJs · Class · Social',a:'rays',c:['#E4126F','#4A0D7A','#FFC94D']},
 {n:'Noche de Bachata at The Vaults',s:'Noche de Bachata',t:'The Vaults',f:'Live band',a:'moon',serif:1,c:['#12347F','#050E2B','#F2C14E']},
 {n:'Sunday Social: Bachata on the Rooftop',s:'Rooftop Social',t:'Open air',f:'Sunset session',a:'stripes',c:['#FF6B3D','#B5179E','#FFE08A']},
 {n:'Bachata Fusion Lab',init:'BF',c:['#FFA600','#B34700','#1C1000']},
 {n:'Dominican Roots Night',s:'Dominican Roots',t:'Tradicional',f:'Guest teachers',a:'waves',c:['#0B8A66','#06312B','#F9D65C']},
 {n:'Luna Llena Bachata Party',s:'Luna Llena',t:'Bachata party',f:'Dance till late',a:'moon',serif:1,c:['#3A1570','#0A0520','#EDE7FF']},
 {n:'Bachata & Chill Wednesdays',s:'Bachata & Chill',t:'Midweek social',f:'Beginners welcome',a:'dots',c:['#1FA2A8','#0B3C5D','#FFFFFF']},
 {n:'The Big London Bachata Weekender Opening Party',s:'The Big Weekender',t:'Opening party',f:'Three rooms',a:'rays',c:['#D7263D','#5C0A1E','#FFB703']},
 {n:'East London Bachata Beginners Social & Practice Night',init:'EL',c:['#2A1C08','#5A3A0A','#FFA600']},
 {n:'Corazón Social Club',s:'Corazón',t:'Social club',f:'Sensual · Moderna',a:'arcs',serif:1,c:['#C9184A','#590D22','#FFCCD5']},
 {n:'Midnight Bachata Sessions',s:'Midnight Sessions',t:'Late night',f:'Resident DJs',a:'disc',c:['#26265C','#05050F','#8A7DFF']},
 {n:'Ritmo Dorado',init:'RD',c:['#FF7A00','#7A2E00','#FFE2A8']},
 {n:'Sabor Latino Thursdays',s:'Sabor Latino',t:'Bachata room',f:'Class then party',a:'stripes',c:['#F29E00','#D9480F','#6A1B00']}
];

const P='fill="var(--p3)"',S='fill="none" stroke="var(--p3)"';
const ART={
 rays(){let s='';for(let i=0;i<12;i++){const a=i*30*Math.PI/180,b=(i*30+15)*Math.PI/180,f=v=>v.toFixed(1);s+=`<polygon points="60,70 ${f(60+230*Math.cos(a))},${f(70+230*Math.sin(a))} ${f(60+230*Math.cos(b))},${f(70+230*Math.sin(b))}" ${P} opacity=".26"/>`}return s+`<circle cx="60" cy="70" r="19" ${P} opacity=".95"/>`},
 moon(){return `<circle cx="80" cy="52" r="25" ${P}/><circle cx="80" cy="52" r="36" ${S} stroke-width="1.5" opacity=".45"/><circle cx="80" cy="52" r="48" ${S} stroke-width="1" opacity=".25"/><circle cx="22" cy="30" r="1.6" ${P}/><circle cx="38" cy="66" r="1.2" ${P}/><circle cx="16" cy="88" r="1.8" ${P}/><circle cx="104" cy="104" r="1.3" ${P}/>`},
 stripes(){let s='<g transform="rotate(-24 60 80)">';for(let i=0;i<7;i++)s+=`<rect x="-60" y="${-20+i*30}" width="240" height="13" ${P} opacity="${i%2?.38:.2}"/>`;return s+'</g>'},
 waves(){let s='';for(let i=0;i<7;i++){const y=26+i*13;s+=`<path d="M-5 ${y} Q25 ${y-12} 55 ${y} T125 ${y}" ${S} stroke-width="3" opacity="${(.25+i*.09).toFixed(2)}"/>`}return s},
 dots(){let s='';for(let r=0;r<5;r++)for(let c=0;c<5;c++)s+=`<circle cx="${16+c*22}" cy="${34+r*17}" r="${(1.5+((r*3+c*2)%5)*1.1).toFixed(1)}" ${P} opacity=".55"/>`;return s},
 arcs(){return `<circle cx="120" cy="0" r="40" ${S} stroke-width="7" opacity=".6"/><circle cx="120" cy="0" r="62" ${S} stroke-width="5" opacity=".42"/><circle cx="120" cy="0" r="84" ${S} stroke-width="3" opacity=".3"/><circle cx="120" cy="0" r="104" ${S} stroke-width="2" opacity=".2"/>`},
 disc(){return `<circle cx="60" cy="66" r="42" fill="#000" opacity=".55"/><circle cx="60" cy="66" r="34" ${S} stroke-width="1" opacity=".6"/><circle cx="60" cy="66" r="26" ${S} stroke-width="1" opacity=".5"/><circle cx="60" cy="66" r="18" ${S} stroke-width="1" opacity=".4"/><circle cx="60" cy="66" r="9" ${P}/>`}
};
function poster(e){
 const st=`style="--p1:${e.c[0]};--p2:${e.c[1]};--p3:${e.c[2]}"`;
 if(e.init)return `<div class="poster poster--init" ${st} aria-hidden="true"><span>${e.init}</span></div>`;
 return `<div class="poster${e.serif?' poster--serif':''}" ${st} aria-hidden="true"><svg viewBox="0 0 120 160" preserveAspectRatio="xMidYMid slice">${ART[e.a]()}</svg><div class="poster__txt"><small>${esc(e.t)}</small><div><b>${esc(e.s)}</b><i>${esc(e.f)}</i></div></div></div>`;
}

/* events */
const list=$('#events');
list.innerHTML=EVENTS.map((e,i)=>LOOK.card(Object.assign({name:esc(e.n)},e),i,poster(e),TICK)).join('');

/* sheet */
document.body.insertAdjacentHTML('beforeend',
`<div class="sheet" id="sheet" aria-hidden="true">
 <div class="sheet__scrim" data-close></div>
 <div class="sheet__panel" role="dialog" aria-modal="true" aria-labelledby="sheetTitle">
  <div class="sheet__grab" aria-hidden="true"></div>
  <button type="button" class="sheet__x" data-close aria-label="Close">&times;</button>
  <form id="entryForm" novalidate>
   <p class="sheet__eyebrow">Free raffle</p>
   <h2 id="sheetTitle">Enter the raffle</h2>
   <p class="sheet__for" id="sheetFor"></p>
   <label for="phone">Your mobile number</label>
   <input id="phone" name="phone" type="tel" inputmode="tel" autocomplete="tel" placeholder="07123 456789" aria-describedby="phoneErr phoneNote">
   <p class="sheet__err" id="phoneErr" role="alert" hidden></p>
   <p class="sheet__note" id="phoneNote">Free · one phone number covers every night</p>
   <button type="submit" class="sheet__go" id="sheetGo">Enter the raffle</button>
  </form>
  <div class="sheet__done" id="sheetDone" tabindex="-1" hidden>
   <div class="sheet__tick">${TICK}</div>
   <h2>You're in!</h2>
   <p>Your number is in the draw for every night. Winners are announced weekly.</p>
   <button type="button" class="sheet__go" data-close>Done</button>
  </div>
 </div>
</div><canvas class="confetti" aria-hidden="true"></canvas>`);
if(window.visualViewport){const vv=visualViewport,fitKb=()=>{const kb=Math.max(0,innerHeight-vv.height-vv.offsetTop);const s=document.getElementById('sheet');s.style.bottom=kb+'px';s.firstElementChild&&0;document.querySelector('.sheet__panel').style.maxHeight=(vv.height-12)+'px'};vv.addEventListener('resize',fitKb);vv.addEventListener('scroll',fitKb)}
const sheet=$('#sheet'),form=$('#entryForm'),done=$('#sheetDone'),phone=$('#phone'),err=$('#phoneErr'),sheetFor=$('#sheetFor');
let entered=false,lastFocus=null;

function openSheet(trigger){
 lastFocus=trigger||null;
 const nm=trigger&&trigger.dataset.name;
 sheetFor.textContent=nm?'You picked '+nm+'. Your entry covers every other night too.':'One entry puts you in the draw for all 13 nights.';
 form.hidden=false;done.hidden=true;err.hidden=true;phone.removeAttribute('aria-invalid');
 sheet.classList.add('open');sheet.setAttribute('aria-hidden','false');
 document.documentElement.style.overflow='hidden';
 try{phone.focus({preventScroll:true})}catch(e){phone.focus()}
}
function closeSheet(){
 if(!sheet.classList.contains('open'))return;
 sheet.classList.remove('open');sheet.setAttribute('aria-hidden','true');
 document.documentElement.style.overflow='';
 if(lastFocus&&document.contains(lastFocus)&&lastFocus.offsetParent)try{lastFocus.focus({preventScroll:true})}catch(e){}
}
function validPhone(v){
 const d=v.replace(/\(0\)/g,'').replace(/[\s\-().]/g,'');
 return /^(?:\+44|0044|0)\d{9,10}$/.test(d);
}
form.addEventListener('submit',ev=>{
 ev.preventDefault();
 if(!validPhone(phone.value)){
  err.textContent=phone.value.trim()?'That does not look like a UK phone number. Try it like 07123 456789.':'Add your mobile number to enter.';
  err.hidden=false;phone.setAttribute('aria-invalid','true');
  phone.classList.remove('shake');void phone.offsetWidth;phone.classList.add('shake');phone.focus();
  return;
 }
 entered=true;document.body.classList.add('is-in');
 form.hidden=true;done.hidden=false;done.focus();
 const en=$('[data-entries]');if(en){const n=+en.dataset.count;countTo(en,n,n+1,500)}
 (LOOK.celebrate||celebrate)(api);
 if(LOOK.onEnter)LOOK.onEnter(api);
});
phone.addEventListener('input',()=>{err.hidden=true;phone.removeAttribute('aria-invalid')});

function reset(){
 entered=false;document.body.classList.remove('is-in');
 form.reset();closeSheet();
 const info=$('#info');if(info)info.hidden=true;
 window.scrollTo(0,0);runCounts();
 if(LOOK.onReset)LOOK.onReset(api);
}
const INFO={
 rules:'One entry per phone number. One winner is drawn for each night. The prize is a free entry to that night.',
 privacy:'We use your number only to tell you if you win. We never share it.',
 contact:'Questions? Send us a message from the Contact page of this site.'
};
document.addEventListener('click',ev=>{
 const t=ev.target.closest('[data-enter],[data-close],[data-reset],[data-info]');if(!t)return;
 if(t.matches('[data-info]')){ev.preventDefault();const p=$('#info');p.textContent=INFO[t.dataset.info];p.hidden=false;return}
 if(t.matches('[data-reset]')){ev.preventDefault();reset();return}
 if(t.matches('[data-close]')){closeSheet();return}
 t.classList.remove('nudge');void t.offsetWidth;t.classList.add('nudge');
 if(entered){const r=t.getBoundingClientRect();burst(r.left+r.width/2,r.top+r.height/2,18,{spread:Math.PI*2,power:6});return}
 openSheet(t);
});
document.addEventListener('keydown',ev=>{
 if(!sheet.classList.contains('open'))return;
 if(ev.key==='Escape'){closeSheet();return}
 if(ev.key!=='Tab')return;
 const f=$$('button,input',sheet).filter(el=>el.offsetParent!==null);if(!f.length)return;
 const a=f[0],z=f[f.length-1];
 if(ev.shiftKey&&document.activeElement===a){ev.preventDefault();z.focus()}
 else if(!ev.shiftKey&&document.activeElement===z){ev.preventDefault();a.focus()}
});

/* counters */
const fmt=n=>n.toLocaleString('en-GB');
function countTo(el,from,to,dur){
 if(RM){el.textContent=fmt(to);return}
 const t0=performance.now();el.classList.add('counting');
 (function step(t){const k=Math.min(1,Math.max(0,(t-t0)/dur)),v=from+(to-from)*(1-Math.pow(1-k,3));
  el.textContent=fmt(Math.round(v));
  if(k<1)requestAnimationFrame(step);else el.classList.remove('counting')})(t0);
}
function runCounts(){$$('[data-count]').forEach((el,i)=>countTo(el,0,+el.dataset.count,1100+i*350))}

/* confetti */
const cv=$('.confetti'),cx=cv.getContext('2d');let parts=[],raf=0;
function fit(){const d=Math.min(window.devicePixelRatio||1,2);cv.width=innerWidth*d;cv.height=innerHeight*d;cx.setTransform(d,0,0,d,0,0)}
function burst(x,y,n,o){
 if(RM)return;o=o||{};
 const cols=LOOK.colors||['#FFA600','#FFD166','#FFF6E6','#FF7A00'],shapes=LOOK.shapes||['rect','dot'];
 for(let i=0;i<n;i++){
  const a=(o.angle==null?-Math.PI/2:o.angle)+(Math.random()-.5)*(o.spread==null?Math.PI*1.2:o.spread),v=(o.power||11)*(.4+Math.random()*.8);
  parts.push({x,y,vx:Math.cos(a)*v,vy:Math.sin(a)*v,g:.2+Math.random()*.14,s:5+Math.random()*7,r:Math.random()*6.28,vr:(Math.random()-.5)*.4,c:cols[(Math.random()*cols.length)|0],sh:shapes[(Math.random()*shapes.length)|0],life:0,max:80+Math.random()*80});
 }
 if(!raf){fit();raf=requestAnimationFrame(tick)}
}
function tick(){
 cx.clearRect(0,0,innerWidth,innerHeight);
 parts=parts.filter(p=>p.life<p.max&&p.y<innerHeight+40);
 for(const p of parts){
  p.life++;p.vx*=.985;p.vy=p.vy*.985+p.g;p.x+=p.vx;p.y+=p.vy;p.r+=p.vr;
  cx.save();cx.translate(p.x,p.y);cx.rotate(p.r);cx.globalAlpha=Math.min(1,(p.max-p.life)/22);cx.fillStyle=p.c;
  const w=Math.abs(Math.cos(p.life*.18))*.8+.2;
  if(p.sh==='dot'){cx.beginPath();cx.ellipse(0,0,p.s*.5,p.s*.5*w,0,0,6.3);cx.fill()}
  else if(p.sh==='ticket'){cx.fillRect(-p.s,-p.s*.45*w,p.s*2,p.s*.9*w)}
  else if(p.sh==='spark'){cx.fillRect(-p.s*.9,-1,p.s*1.8,2);cx.fillRect(-1,-p.s*.9,2,p.s*1.8)}
  else cx.fillRect(-p.s*.5,-p.s*.3*w,p.s,p.s*.6*w);
  cx.restore();
 }
 if(parts.length)raf=requestAnimationFrame(tick);else{raf=0;cx.clearRect(0,0,innerWidth,innerHeight)}
}
function celebrate(){
 const w=innerWidth,h=innerHeight;
 burst(w/2,h*.42,90,{spread:Math.PI*2,power:13});
 setTimeout(()=>burst(0,h*.72,55,{angle:-Math.PI/3,spread:.9,power:18}),160);
 setTimeout(()=>burst(w,h*.72,55,{angle:-Math.PI*2/3,spread:.9,power:18}),300);
}

/* reveal on scroll */
if(!RM&&'IntersectionObserver' in window){
 const io=new IntersectionObserver(es=>es.forEach(e=>{if(e.isIntersecting){e.target.classList.add('seen');io.unobserve(e.target)}}),{rootMargin:'0px 0px -6% 0px'});
 $$('[data-reveal]').forEach(el=>io.observe(el));
}

const api={$,$$,RM,burst,celebrate,EVENTS,isIn:()=>entered};
if(LOOK.init)LOOK.init(api);
runCounts();
})();

</script>

</body></html>
```
