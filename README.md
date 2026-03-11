<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Auction Game — Fluxo Completo</title>
  <script src="https://unpkg.com/react@17/umd/react.production.min.js"></script>
  <script src="https://unpkg.com/react-dom@17/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500&family=Poppins:wght@700&display=swap" rel="stylesheet">
  <style>
    :root{
      --navy:#0b2540; --teal:#1fb2b9; --beige:#f4e5bf; --pink:#ff2a8a;
      --neon:#FF0082;
      --bg:var(--navy); --text:var(--beige); --accent:var(--neon);
      --border:#174066; --card:#0f2f50; --muted:#0c2a49; --shadow:0 6px 18px rgba(0,0,0,.25);
      --success:#20d38a; --danger:#ff4d8f; --warning:#ffb94d;
    }
    *{box-sizing:border-box}
    body{margin:0;background:var(--bg);color:var(--text);font-family:'Inter',system-ui,Segoe UI,Roboto,Arial,sans-serif}
    a{color:inherit;text-decoration:none}
    .app{min-height:100vh;display:flex;flex-direction:column}
    header.nav{position:sticky;top:0;z-index:20;background:rgba(15,47,80,.9);backdrop-filter:blur(6px);border-bottom:1px solid var(--border)}
    .nav-inner{max-width:1200px;margin:0 auto;padding:10px 20px;display:flex;align-items:center;justify-content:space-between;gap:12px;flex-wrap:wrap}
    .brand{display:flex;align-items:center;gap:10px;font-family:'Poppins',sans-serif;font-weight:800}
    .links{display:flex;gap:8px;flex-wrap:wrap;align-items:center}
    .link{padding:8px 12px;border:1px solid var(--border);border-radius:10px;background:#0c2a49}
    .link:hover{background:#10365d}
    .link.active{outline:2px solid var(--neon)}
    .credit{padding:8px 12px;border-radius:999px;background:#0c2a49;border:1px solid var(--border);font-weight:800;display:flex;align-items:center;gap:10px}
    .avatar{width:28px;height:28px;border-radius:50%;overflow:hidden;border:2px solid transparent;display:grid;place-items:center;background:#0b2540;color:#cfe3ff;font-weight:900;font-size:12px}
    .avatar img{width:100%;height:100%;object-fit:cover}
    .tier-bronze{ border-color:#cd7f32; box-shadow:0 0 0 2px rgba(205,127,50,.2) inset; }
    .tier-prata{  border-color:#c0c0c0; box-shadow:0 0 0 2px rgba(192,192,192,.2) inset; }
    .tier-ouro{    border-color:#ffd700; box-shadow:0 0 0 2px rgba(255,215,0,.22) inset; }
    .tier-esm{     border-color:#2ecc71; box-shadow:0 0 0 2px rgba(46,204,113,.22) inset; }
    .tier-diam{    border-color:#7fd8ff; box-shadow:0 0 0 2px rgba(127,216,255,.25) inset; }
    .level-chip{display:block;font-size:10px;line-height:1;margin-top:4px;color:#bfe1ff;text-align:center}
    .lang-selector{position:relative;display:inline-block}
    .lang-btn{background:transparent;border:1px solid var(--border);border-radius:8px;padding:6px 10px;cursor:pointer;font-size:18px;display:flex;align-items:center;gap:4px;color:var(--text)}
    .lang-btn:hover{background:#10365d}
    .lang-dropdown{position:absolute;top:100%;right:0;margin-top:4px;background:#0f2f50;border:1px solid var(--border);border-radius:8px;padding:4px;display:none;flex-direction:column;gap:2px;min-width:80px;box-shadow:var(--shadow);z-index:100}
    .lang-dropdown.open{display:flex}
    .lang-option{padding:8px 12px;cursor:pointer;border-radius:6px;font-size:13px;font-weight:600;text-align:center}
    .lang-option:hover{background:#1a4a6f}
    .lang-option.active{background:var(--neon);color:#091b2e}
    .preview{ width:100%; max-height:220px; border:1px dashed #2a5b8a; border-radius:10px; display:grid; place-items:center; overflow:hidden; background:#0b2540; color:#9bb8dd; }
    .preview img{ width:100%; height:100%; object-fit:cover; }
    .container{max-width:1200px;margin:0 auto;padding:24px 20px;width:100%}
    .hero{margin-top:18px;border:1px solid var(--border);border-radius:18px;background:linear-gradient(180deg,#10365d,#0b2540);padding:22px;box-shadow:var(--shadow)}
    .hero h1{margin:0 0 8px;font-family:'Poppins',sans-serif;color:var(--neon)}
    .hero p{margin:0 0 10px;color:#b6d4ff}
    .section-title{color:var(--neon)}
    .btn{border:none;border-radius:10px;padding:10px 14px;font-weight:800;cursor:pointer}
    .btn-primary{background:var(--accent);color:#091b2e}
    .btn-ghost{background:#0c2a49;color:var(--text);border:1px solid var(--border)}
    .btn-success{background:var(--success);color:#091b2e}
    .btn:disabled{opacity:.5;cursor:not-allowed}
    .grid{display:grid;gap:14px}
    .grid.cols-3{grid-template-columns:repeat(3,1fr)}
    .grid.cols-2{grid-template-columns:repeat(2,1fr)}
    @media(max-width:900px){.grid.cols-3{grid-template-columns:repeat(2,1fr)}}
    @media(max-width:640px){.grid.cols-3,.grid.cols-2{grid-template-columns:1fr}}
    .card{background:linear-gradient(180deg,#0f2f50,#0b2540);border:1px solid var(--border);border-radius:14px;padding:14px;box-shadow:var(--shadow)}
    .banner{height:120px;border-radius:10px;border:1px dashed #245a89;display:grid;place-items:center;font-size:42px}
    .meta{display:flex;justify-content:space-between;gap:10px;align-items:center;flex-wrap:wrap}
    .status{padding:4px 8px;border-radius:999px;font-size:12px;color:#091b2e;font-weight:800}
    .status.active{background:var(--success)} .status.pending{background:var(--warning)} .status.ended{background:var(--danger);color:#fff}
    .timer{color:#a9c6ff;font-variant-numeric:tabular-nums}
    .price{font-weight:800}
    .row{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
    .field{display:flex;flex-direction:column;gap:6px}
    input,select,textarea{background:#0b2540;color:var(--text);border:1px solid var(--border);border-radius:8px;padding:10px}
    .table{width:100%;border-collapse:collapse}
    .table th,.table td{border-bottom:1px dotted #1b4670;padding:8px;text-align:left}
    .muted{color:#9bb8dd}
    footer{margin-top:auto;padding:22px 20px;text-align:center;color:#9bb8dd}
    .winner-banner{background:linear-gradient(135deg,#20d38a,#1fb2b9);color:#091b2e;padding:16px;border-radius:12px;margin:12px 0;text-align:center;font-weight:800;box-shadow:0 4px 12px rgba(32,211,138,.3)}
    .winner-banner h3{margin:0 0 8px;font-size:20px}
    .exp-bar-container{width:100%;height:24px;background:rgba(0,0,0,.3);border-radius:12px;overflow:hidden;position:relative;margin-top:8px;border:1px solid var(--border)}
    .exp-bar{height:100%;background:linear-gradient(90deg,#20d38a,#1fb2b9);transition:width .5s ease;position:relative}
    .exp-text{position:absolute;width:100%;text-align:center;line-height:24px;font-weight:700;font-size:11px;text-shadow:1px 1px 2px rgba(0,0,0,.8);top:0;left:0}
  </style>
</head>
<body>
  <div id="root"></div>

  <script type="text/babel">
    const {useState,useEffect,useMemo,useRef} = React;

    const R = { HOME:'#/', PROD:'#/produtos', LOT:'#/leiloes', PAY:'#/pagamento', PROFILE:'#/perfil', CREDITS:'#/creditos', RANK:'#/rank', EVENT:'#/evento', FAQ:'#/duvidas', LOGIN:'#/login', ADMIN:'#/admin' };
    const LOGO = 'https://placehold.co/36x36/FF0082/FFFFFF?text=AG';

    const YEN='¥';
    const fmtJPY = n => `${YEN}${Number(n).toLocaleString('ja-JP')}`;
    const pad2 = n => String(n).padStart(2,'0');
    const clock = s => {const h=Math.floor(s/3600),m=Math.floor((s%3600)/60),sec=s%60; return (h?pad2(h)+':':'')+pad2(m)+':'+pad2(sec);};

    const LS = { USER:'ag_user_v3', CREDITS:'ag_credits_v4', LOTS:'ag_lots_v5', WINS:'ag_wins_v2', USERS:'ag_users_v2', PURCHASES:'ag_purchases_v2', LANG:'ag_lang_v1' };

    const PACKS = [
      {credits:1, price:110, label:'Starter'},
      {credits:11, price:1100, label:'Value'},
      {credits:58, price:5500, label:'Pro'},
      {credits:130, price:11000, label:'Mega'},
    ];

    const seedLots = [
      {id:'l1', titulo:'Console Retro', desc:'Console compacto com 200 jogos', lanceAtual:1, status:'active', endsIn:120, img:'🎮', lastBidder:'bot', defaultActiveSeconds:600},
      {id:'l2', titulo:'Headset Pro', desc:'Headset gamer com surround', lanceAtual:1, status:'active', endsIn:180, img:'🎧', lastBidder:'bot', defaultActiveSeconds:600},
      {id:'l3', titulo:'Coleção 8-bit', desc:'Pacote com jogos clássicos', lanceAtual:1, status:'pending', endsIn:210, img:'🕹️', lastBidder:'bot', defaultActiveSeconds:600},
    ];

    function useLS(key, init){
      const [v,setV] = useState(()=> {
        const raw = localStorage.getItem(key);
        return raw ? JSON.parse(raw) : init;
      });
      useEffect(()=> localStorage.setItem(key, JSON.stringify(v)), [key,v]);
      return [v,setV];
    }

    const T = {
      pt:{ home_title:'Bem-vindo ao Auction Game', home_desc:'Todos os produtos começam em ¥1. Cada lance aumenta exatamente ¥1. Para dar lances você precisa de créditos.',
           products:'Produtos', credits:'Créditos', rank:'Rank', event:'Evento', faq:'Dúvidas', profile:'Perfil', login:'Login', admin:'Admin',
           see_products:'Ver produtos', buy_credits:'Comprar créditos', active:'Ativo', pending:'Aguardando', ended:'Encerrado',
           bids_history:'Histórico de lances', bid:'Dar lance (+¥1)', pay_go:'Ir ao pagamento',
           page_title_products:'Produtos', page_title_credits:'Créditos', page_title_rank:'Rank', page_title_event:'Evento',
           page_title_faq:'Dúvidas e informações', page_title_profile:'Perfil', payment:'Pagamento',
           how_works:'Como funciona?', credits_sub:'Pacotes de bid para comprar. 1 crédito = 1 lance (+¥1).',
           level:'Nível', user:'Usuário', used_bids:'Lances usados', last_bidder:'Último lance',
           winner_title:'🎉 Parabéns! Você venceu este leilão!',
           winner_desc:'Você foi o último a dar lance. Finalize a compra agora!',
           buy_now:'Comprar agora por',
           exp_progress:'EXP', next_level:'Próximo nível', free_bid_reward:'+1 lance grátis'
      },
      ja:{ home_title:'Auction Game へようこそ', home_desc:'すべての商品は¥1から開始。入札ごとに¥1増加。入札にはクレジットが必要です。',
           products:'商品', credits:'クレジット', rank:'ランキング', event:'イベント', faq:'よくある質問', profile:'プロフィール', login:'ログイン', admin:'管理者',
           see_products:'商品を見る', buy_credits:'クレジットを購入', active:'開催中', pending:'準備中', ended:'終了',
           bids_history:'入札履歴', bid:'入札 (+¥1)', pay_go:'支払いへ',
           page_title_products:'商品', page_title_credits:'クレジット', page_title_rank:'ランキング', page_title_event:'イベント',
           page_title_faq:'情報・FAQ', page_title_profile:'プロフィール', payment:'支払い',
           how_works:'仕組み', credits_sub:'入札パック。1クレジット = 入札1回 (+¥1)。',
           level:'レベル', user:'ユーザー', used_bids:'使用入札数', last_bidder:'最後の入札者',
           winner_title:'🎉 おめでとうございます！オークション勝利！',
           winner_desc:'最後の入札者です。今すぐ購入を完了してください！',
           buy_now:'今すぐ購入',
           exp_progress:'経験値', next_level:'次のレベル', free_bid_reward:'+1無料入札'
      },
      en:{ home_title:'Welcome to Auction Game', home_desc:'All items start at ¥1. Each bid adds exactly ¥1. You need credits to bid.',
           products:'Products', credits:'Credits', rank:'Rank', event:'Event', faq:'FAQ', profile:'Profile', login:'Login', admin:'Admin',
           see_products:'View products', buy_credits:'Buy credits', active:'Active', pending:'Pending', ended:'Ended',
           bids_history:'Bidding history', bid:'Place bid (+¥1)', pay_go:'Go to payment',
           page_title_products:'Products', page_title_credits:'Credits', page_title_rank:'Rank', page_title_event:'Event',
           page_title_faq:'Questions & info', page_title_profile:'Profile', payment:'Payment',
           how_works:'How it works', credits_sub:'Bid packs. 1 credit = 1 bid (+¥1).',
           level:'Level', user:'User', used_bids:'Bids used', last_bidder:'Last bidder',
           winner_title:'🎉 Congratulations! You won this auction!',
           winner_desc:'You were the last bidder. Complete your purchase now!',
           buy_now:'Buy now for',
           exp_progress:'EXP', next_level:'Next level', free_bid_reward:'+1 free bid'
      },
    };

    const initials = (name='Você') => name.trim().split(/\s+/).map(s=>s[0]?.toUpperCase()).slice(0,2).join('') || 'VC';
    const calcLevel = (bids) => 1 + Math.floor((bids||0)/11);
    const calcExp = (bids) => (bids||0) % 11;
    function tierClass(level){
      if(level>=15) return 'tier-diam';
      if(level>=10) return 'tier-esm';
      if(level>=6)  return 'tier-ouro';
      if(level>=3)  return 'tier-prata';
      return 'tier-bronze';
    }

    function LangSelector({lang, setLang}){
      const [open, setOpen] = useState(false);
      const ref = useRef();

      useEffect(()=>{
        const handler = (e) => {
          if(ref.current && !ref.current.contains(e.target)) setOpen(false);
        };
        document.addEventListener('click', handler);
        return ()=> document.removeEventListener('click', handler);
      },[]);

      return (
        React.createElement('div', {className:'lang-selector', ref:ref},
          React.createElement('button', {className:'lang-btn', onClick:()=>setOpen(!open)},
            '🌐',
            React.createElement('span', {style:{fontSize:11,fontWeight:700}}, lang.toUpperCase())
          ),
          React.createElement('div', {className:`lang-dropdown ${open?'open':''}`},
            React.createElement('div', {className:`lang-option ${lang==='pt'?'active':''}`, onClick:()=>{setLang('pt');setOpen(false)}}, 'PT'),
            React.createElement('div', {className:`lang-option ${lang==='en'?'active':''}`, onClick:()=>{setLang('en');setOpen(false)}}, 'EN'),
            React.createElement('div', {className:`lang-option ${lang==='ja'?'active':''}`, onClick:()=>{setLang('ja');setOpen(false)}}, 'JP')
          )
        )
      );
    }

    function Nav({credits, user, usersStats, lang, setLang}){
      const bids = usersStats?.you?.bids || 0;
      const level = calcLevel(bids);
      const hasAvatar = Boolean(user?.avatar);
      const tClass = tierClass(level);

      function Link({href,label}){
        const active = location.hash.split('?')[0]===href;
        return React.createElement('a', {className:`link ${active?'active':''}`, href:href}, label);
      }

      return (
        React.createElement('header', {className:'nav'},
          React.createElement('div', {className:'nav-inner'},
            React.createElement('a', {className:'brand', href:R.HOME},
              React.createElement('img', {src:LOGO, alt:'logo', style:{width:36,height:36,borderRadius:8,border:'1px solid #174066'}}),
              'Auction Game'
            ),
            React.createElement('div', {className:'links'},
              React.createElement(Link, {href:R.PROD, label:T[lang].products}),
              React.createElement(Link, {href:R.CREDITS, label:T[lang].credits}),
              React.createElement(Link, {href:R.RANK, label:T[lang].rank}),
              React.createElement(Link, {href:R.EVENT, label:T[lang].event}),
              React.createElement(Link, {href:R.FAQ, label:T[lang].faq}),
              React.createElement(Link, {href:R.PROFILE, label:T[lang].profile}),
              React.createElement(Link, {href:R.LOGIN, label:T[lang].login}),
              user?.admin && React.createElement(Link, {href:R.ADMIN, label:T[lang].admin}),
              React.createElement(LangSelector, {lang:lang, setLang:setLang}),
              React.createElement('span', {className:'credit', style:{gap:8}},
                React.createElement('div', {style:{display:'flex',flexDirection:'column',alignItems:'center'}},
                  React.createElement('span', {className:`avatar ${tClass}`},
                    hasAvatar ? React.createElement('img', {src:user.avatar, alt:'avatar'}) : initials(user?.name)
                  ),
                  React.createElement('span', {className:'level-chip'}, `Nv ${level}`)
                ),
                `${T[lang].credits}: ${credits}`
              )
            )
          )
        )
      );
    }

    function Home({lang}){
      return (
        React.createElement('div', {className:'container'},
          React.createElement('div', {className:'hero'},
            React.createElement('h1', null, T[lang].home_title),
            React.createElement('p', null, T[lang].home_desc),
            React.createElement('div', {className:'row'},
              React.createElement('a', {className:'btn btn-primary', href:R.PROD}, T[lang].see_products),
              React.createElement('a', {className:'btn btn-ghost', href:R.CREDITS}, T[lang].buy_credits)
            )
          )
        )
      );
    }

    function Products({lots,lang}){
      const badge = s=> s==='active'?React.createElement('span',{className:'status active'},T[lang].active):s==='pending'?React.createElement('span',{className:'status pending'},T[lang].pending):React.createElement('span',{className:'status ended'},T[lang].ended);
      return (
        React.createElement('div', {className:'container'},
          React.createElement('h2', {className:'section-title'}, T[lang].page_title_products),
          React.createElement('div', {className:'grid cols-3'},
            lots.map(l=>(
              React.createElement('a', {key:l.id, className:'card', href:`${R.LOT}/${l.id}`},
                React.createElement('div', {className:'banner'}, typeof l.img==='string' && l.img.startsWith('data:') ? React.createElement('img', {src:l.img, alt:l.titulo, style:{width:'100%',height:'100%',objectFit:'cover'}}) : l.img),
                React.createElement('h3', {style:{margin:'8px 0', color:'var(--neon)'}}, l.titulo),
                React.createElement('p', {className:'muted', style:{minHeight:40}}, l.desc),
                React.createElement('div', {className:'meta'},
                  badge(l.status),
                  React.createElement('span', {className:'price'}, fmtJPY(l.lanceAtual)),
                  React.createElement('span', {className:'timer'}, l.status==='active'?clock(l.endsIn):(l.status==='pending'?T[lang].pending:T[lang].ended))
                ),
                React.createElement('div', {className:'muted', style:{marginTop:6, fontSize:12}}, `${T[lang].last_bidder}: `, React.createElement('strong', null, l.lastBidder==='you'?'Você':l.lastBidder))
              )
            ))
          )
        )
      );
    }

    function LotDetail({lot, hasCredits, spendCredit, updateLot, lang, currentUserName, addFreeBid}){
      const [state,setState] = useState({...lot, bids:[{who:'bot:seed', value:1, at:Date.now()}]});
      const tickRef = useRef(); const botRef = useRef(); const lastStatus = useRef(state.status);

      useEffect(()=> setState(prev=>({...prev, ...lot, bids:prev.bids||[]})), [lot.id]);

      useEffect(()=>{
        tickRef.current = setInterval(()=>{
          setState(prev=>{
            let n={...prev};
            if(n.status==='pending'){ n.endsIn=Math.max(0,n.endsIn-1); if(n.endsIn===0){ n.status='active'; n.endsIn=n.defaultActiveSeconds||600; } }
            else if(n.status==='active'){ n.endsIn=Math.max(0,n.endsIn-1); if(n.endsIn===0) n.status='ended'; }
            if(lastStatus.current!=='ended' && n.status==='ended'){ updateLot(n,true); }
            lastStatus.current=n.status; updateLot(n);
            return n;
          })
        },1000);
        return ()=> clearInterval(tickRef.current);
      },[updateLot]);

      useEffect(()=>{
        if(state.status!=='active'){ if(botRef.current) clearInterval(botRef.current); return; }
        botRef.current = setInterval(()=>{
          setState(prev=>{
            if(prev.status!=='active') return prev;
            const nv=prev.lanceAtual+1;
            const resetTime = prev.endsIn<300 ? 300 : prev.endsIn;
            const who=`bot:${['alpha','beta','gamma'][Math.floor(Math.random()*3)]}`;
            const next={...prev, lanceAtual:nv, lastBidder:who, endsIn:resetTime, bids:[...prev.bids,{who,value:nv,at:Date.now()}]};
            updateLot(next);
            return next;
          });
        },Math.floor(Math.random()*2500)+3000);
        return ()=> clearInterval(botRef.current);
      },[state.status, updateLot]);

      function bid(){
        if(state.status!=='active') return;
        if(!hasCredits() || !spendCredit(1)) return;
        const nv = state.lanceAtual+1;
        const resetTime = state.endsIn<300 ? 300 : state.endsIn;
        const who = currentUserName || 'you';
        const next={...state, lanceAtual:nv, lastBidder:who, endsIn:resetTime, bids:[...state.bids,{who,value:nv,at:Date.now()}]};
        setState(next); updateLot(next, false, true);
      }

      const isWinner = state.status==='ended' && (state.lastBidder==='you' || state.lastBidder===currentUserName);

      return (
        React.createElement('div', {className:'container'},
          React.createElement('a', {className:'link', href:R.PROD}, `← ${T[lang].products}`),
          React.createElement('div', {className:'grid cols-2', style:{marginTop:14}},
            React.createElement('div', {className:'card'},
              React.createElement('div', {className:'banner', style:{height:180}}, typeof state.img==='string' && state.img.startsWith('data:') ? React.createElement('img', {src:state.img, alt:state.titulo, style:{width:'100%',height:'100%',objectFit:'cover'}}) : state.img),
              React.createElement('h2', {style:{margin:'10px 0', color:'var(--neon)'}}, state.titulo),
              React.createElement('p', {className:'muted'}, state.desc),
              React.createElement('div', {className:'meta', style:{marginTop:10}},
                React.createElement('span', {className:`status ${state.status}`}, state.status==='active'?T[lang].active:state.status==='pending'?T[lang].pending:T[lang].ended),
                React.createElement('span', {className:'price'}, fmtJPY(state.lanceAtual)),
                React.createElement('span', {className:'timer'}, state.status==='active'?clock(state.endsIn):(state.status==='pending'?`${T[lang].pending} ${clock(state.endsIn)}`:T[lang].ended))
              ),
              React.createElement('p', {className:'muted', style:{marginTop:8}}, `${T[lang].last_bidder}: `, React.createElement('strong', null, state.lastBidder=== 'you' ? 'Você' : state.lastBidder)),
              isWinner && React.createElement('div', {className:'winner-banner'},
                React.createElement('h3', null, T[lang].winner_title),
                React.createElement('p', {style:{margin:'4px 0 12px',fontSize:14}}, T[lang].winner_desc),
                React.createElement('a', {className:'btn btn-success', href:`${R.PAY}?lot=${state.id}&winner=true`, style:{display:'inline-block'}}, `${T[lang].buy_now} ${fmtJPY(state.lanceAtual)}`)
              ),
              React.createElement('div', {className:'row', style:{marginTop:10}},
                React.createElement('button', {className:'btn btn-primary', disabled:state.status!=='active'||!hasCredits(), onClick:bid}, T[lang].bid),
                !hasCredits() && React.createElement('a', {className:'btn btn-ghost', href:R.CREDITS}, T[lang].buy_credits)
              )
            ),
            React.createElement('div', {className:'card'},
              React.createElement('h3', {className:'section-title'}, T[lang].bids_history),
              React.createElement('table', {className:'table'},
                React.createElement('thead', null, React.createElement('tr', null, React.createElement('th', null, T[lang].user), React.createElement('th', null, 'Valor'), React.createElement('th', null, 'Quando'))),
                React.createElement('tbody', null,
                  state.bids.slice().reverse().map((b,i)=>(
                    React.createElement('tr', {key:i},
                      React.createElement('td', null, b.who==='you'?'Você':b.who),
                      React.createElement('td', null, fmtJPY(b.value)),
                      React.createElement('td', null, new Date(b.at).toLocaleTimeString())
                    )
                  ))
                )
              )
            )
          )
        )
      );
    }

    function Credits({packs,lang}){
      return (
        React.createElement('div', {className:'container'},
          React.createElement('h2', {className:'section-title'}, T[lang].page_title_credits),
          React.createElement('p', {className:'muted'}, T[lang].credits_sub),
          React.createElement('div', {className:'grid cols-2'},
            packs.map((p,i)=>(
              React.createElement('div', {key:i, className:'card'},
                React.createElement('h3', {style:{margin:'0 0 4px', color:'var(--neon)'}}, p.label),
                React.createElement('div', {className:'muted'}, `${p.credits} ${p.credits>1?'lances':'lance'}`),
                React.createElement('div', {className:'row', style:{justifyContent:'space-between',marginTop:8}},
                  React.createElement('strong', null, fmtJPY(p.price)),
                  React.createElement('a', {className:'btn btn-primary', href:`${R.PAY}?pack=${p.credits}`}, 'Comprar')
                )
              )
            ))
          )
        )
      );
    }

    function Rank({users,wins,lang}){
      const spenders = useMemo(()=> Object.entries(users||{}).map(([u,s])=>({u, bids:s.bids||0})).sort((a,b)=>b.bids-a.bids).slice(0,10), [users]);
      const topWins = useMemo(()=> (wins||[]).slice().sort((a,b)=>b.price-a.price).slice(0,10), [wins]);
      return (
        React.createElement('div', {className:'container'},
          React.createElement('h2', {className:'section-title'}, T[lang].page_title_rank),
          React.createElement('div', {className:'grid cols-2'},
            React.createElement('div', {className:'card'},
              React.createElement('h3', {style:{color:'var(--neon)'}}, 'Top gastadores (lances)'),
              React.createElement('table', {className:'table'},
                React.createElement('thead', null, React.createElement('tr', null, React.createElement('th', null, '#'), React.createElement('th', null, 'Usuário'), React.createElement('th', null, 'Lances'))),
                React.createElement('tbody', null,
                  spenders.length===0 && React.createElement('tr', null, React.createElement('td', {colSpan:'3', className:'muted'}, 'Sem dados')),
                  spenders.map((s,i)=> React.createElement('tr', {key:i}, React.createElement('td', null, i+1), React.createElement('td', null, s.u==='you'?'Você':s.u), React.createElement('td', null, s.bids)))
                )
              )
            ),
            React.createElement('div', {className:'card'},
              React.createElement('h3', {style:{color:'var(--neon)'}}, 'Maiores arremates'),
              React.createElement('table', {className:'table'},
                React.createElement('thead', null, React.createElement('tr', null, React.createElement('th', null, '#'), React.createElement('th', null, 'Lote'), React.createElement('th', null, 'Vencedor'), React.createElement('th', null, 'Valor'))),
                React.createElement('tbody', null,
                  topWins.length===0 && React.createElement('tr', null, React.createElement('td', {colSpan:'4', className:'muted'}, 'Sem vencedores')),
                  topWins.map((w,i)=> React.createElement('tr', {key:i}, React.createElement('td', null, i+1), React.createElement('td', null, w.title), React.createElement('td', null, w.winner==='you'?'Você':w.winner), React.createElement('td', null, fmtJPY(w.price))))
                )
              )
            )
          )
        )
      );
    }

    function EventPage({lang}){
      return (
        React.createElement('div', {className:'container'},
          React.createElement('h2', {className:'section-title'}, T[lang].page_title_event),
          React.createElement('div', {className:'card'}, React.createElement('p', null, 'Evento semanal: a cada 20 lances, você ganha 5 créditos bônus (simulado).'))
        )
      );
    }

    function FAQ({lang}){
      return (
        React.createElement('div', {className:'container'},
          React.createElement('h2', {className:'section-title'}, T[lang].page_title_faq),
          React.createElement('div', {className:'card'},
            React.createElement('h3', {style:{color:'var(--neon)'}}, T[lang].how_works),
            React.createElement('ol', null,
              React.createElement('li', null, 'Cada lance consome 1 crédito e soma ¥1 ao preço.'),
              React.createElement('li', null, 'Se o tempo estiver abaixo de 5:00 e houver lance, o cronômetro volta para 5:00.'),
              React.createElement('li', null, 'Compre créditos na página Créditos.'),
              React.createElement('li', null, 'A cada 11 lances você sobe 1 nível e ganha +1 lance grátis!')
            )
          )
        )
      );
    }

    function Profile({user, setUser, credits, usersStats, wins, lang}){
      const bids = usersStats?.you?.bids || 0;
      const level = calcLevel(bids);
      const exp = calcExp(bids);
      const expPercent = (exp / 11) * 100;
      const myWins = (wins||[]).filter(w=> w.winner==='you' || w.winner===user?.name);
      const [avatar,setAvatar]=useState(user?.avatar||'');
      const tClass = tierClass(level);
      return (
        React.createElement('div', {className:'container'},
          React.createElement('h2', {className:'section-title'}, T[lang].page_title_profile),
          React.createElement('div', {className:'grid cols-2'},
            React.createElement('div', {className:'card'},
              React.createElement('h3', {style:{color:'var(--neon)'}}, 'Conta'),
              React.createElement('div', {className:'row', style:{alignItems:'center',gap:14}},
                React.createElement('div', {style:{display:'flex',flexDirection:'column',alignItems:'center'}},
                  React.createElement('span', {className:`avatar ${tClass}`, style:{width:64,height:64,fontSize:18}},
                    avatar ? React.createElement('img', {src:avatar, alt:'avatar'}) : initials(user?.name)
                  ),
                  React.createElement('span', {className:'level-chip', style:{fontSize:12}}, `${T[lang].level} ${level}`)
                ),
                React.createElement('div', null,
                  React.createElement('p', {style:{margin:'0 0 4px'}}, React.createElement('strong', null, user?.name || 'Você')),
                  React.createElement('p', {className:'muted', style:{margin:0}}, `${T[lang].used_bids}: `, React.createElement('strong', null, bids)),
                  React.createElement('p', {className:'muted', style:{margin:0}}, `${T[lang].credits}: `, React.createElement('strong', null, credits)),
                  user?.admin && React.createElement('p', {className:'muted', style:{margin:0,color:'var(--neon)'}}, 'Admin ✓')
                )
              ),
              React.createElement('div', {style:{marginTop:12}},
                React.createElement('div', {style:{display:'flex',justifyContent:'space-between',alignItems:'center',marginBottom:4}},
                  React.createElement('span', {style:{fontSize:12,fontWeight:700,color:'var(--neon)'}}, `${T[lang].exp_progress}: ${exp} / 11`),
                  React.createElement('span', {style:{fontSize:11,color:'#9bb8dd'}}, `${T[lang].next_level}: ${T[lang].free_bid_reward}`)
                ),
                React.createElement('div', {className:'exp-bar-container'},
                  React.createElement('div', {className:'exp-bar', style:{width:`${expPercent}%`}}),
                  React.createElement('div', {className:'exp-text'}, `${exp} / 11`)
                )
              ),
              React.createElement('div', {className:'field', style:{marginTop:12}},
                React.createElement('label', null, 'URL do avatar'),
                React.createElement('input', {value:avatar, onChange:e=>setAvatar(e.target.value), placeholder:'https://...png'}),
                React.createElement('div', {className:'row'},
                  React.createElement('button', {className:'btn btn-primary', onClick:()=>setUser({...user, avatar})}, 'Salvar avatar'),
                  React.createElement('button', {className:'btn btn-ghost', onClick:()=>{setAvatar(''); setUser({...user, avatar:''});}}, 'Remover')
                )
              )
            ),
            React.createElement('div', {className:'card'},
              React.createElement('h3', {style:{color:'var(--neon)'}}, 'Vitórias'),
              myWins.length===0 ? React.createElement('p', {className:'muted'}, 'Sem vitórias ainda.') :
                React.createElement('ul', null, myWins.map((w,i)=>React.createElement('li', {key:i}, `${w.title} — ${fmtJPY(w.price)}`)))
            )
          )
        )
      );
    }

    function Login({user,setUser,lang}){
      const [tab,setTab]=useState('entrar');
      const [name,setName]=useState(user?.name||'');
      return (
        React.createElement('div', {className:'container'},
          React.createElement('h2', {className:'section-title'}, T[lang].login),
          React.createElement('div', {className:'row'},
            React.createElement('button', {className:'btn btn-ghost', onClick:()=>setTab('entrar')}, 'Entrar'),
            React.createElement('button', {className:'btn btn-ghost', onClick:()=>setTab('criar')}, 'Criar conta'),
            React.createElement('button', {className:'btn btn-ghost', onClick:()=>setTab('esqueci')}, 'Esqueci a senha')
          ),
          React.createElement('div', {className:'card', style:{marginTop:12}},
            tab==='entrar' && (
              React.createElement('div', {className:'grid cols-2'},
                React.createElement('div', {className:'field'}, React.createElement('label', null, 'Usuário'), React.createElement('input', {value:name, onChange:e=>setName(e.target.value), placeholder:'Seu nome'})),
                React.createElement('div', {className:'field'}, React.createElement('label', null, 'Senha'), React.createElement('input', {type:'password', placeholder:'••••••'})),
                React.createElement('div', {className:'row'},
                  React.createElement('button', {className:'btn btn-primary', onClick:()=>setUser({...user, name:name||'Você'})}, 'Entrar'),
                  React.createElement('button', {className:'btn btn-ghost', onClick:()=>setUser({...user, name:name||'Você', admin:!user?.admin})},
                    user?.admin ? 'Remover admin' : 'Tornar admin'
                  )
                )
              )
            ),
            tab==='criar' && (
              React.createElement(React.Fragment, null,
                React.createElement('div', {className:'grid cols-2'},
                  React.createElement('div', {className:'field'}, React.createElement('label', null, 'Nome'), React.createElement('input', {value:name, onChange:e=>setName(e.target.value)})),
                  React.createElement('div', {className:'field'}, React.createElement('label', null, 'Email'), React.createElement('input', {type:'email', placeholder:'voce@email.com'})),
                  React.createElement('div', {className:'field'}, React.createElement('label', null, 'Senha'), React.createElement('input', {type:'password'}))
                ),
                React.createElement('button', {className:'btn btn-primary', onClick:()=>setUser({name:name||'Você', admin:false, avatar:''})}, 'Criar conta')
              )
            ),
            tab==='esqueci' && React.createElement('p', {className:'muted'}, 'Informe seu e‑mail e enviaremos instruções (simulado).')
          )
        )
      );
    }

    function Payment({setCredits, addPurchase, lots, lang}){
      const params = new URLSearchParams(location.hash.split('?')[1]||'');
      const pack = Number(params.get('pack'));
      const lotId = params.get('lot');
      const isWinner = params.get('winner')==='true';
    
      const selected = PACKS.find(p=>p.credits===pack);
      const lot = lots.find(l=>l.id===lotId);
    
      const total = selected ? selected.price : (lot ? lot.lanceAtual : 0);
    
      const confirmBuy = ()=>{
        if(selected){
          setCredits(c=>c+selected.credits);
          addPurchase({...selected, at:Date.now()});
          alert('Compra de créditos confirmada!');
          window.location.hash = R.CREDITS;
        } else if(lot && isWinner){
          alert(`Parabéns! Você comprou ${lot.titulo} por ${fmtJPY(lot.lanceAtual)}!`);
          window.location.hash = R.PROFILE;
        } else {
          alert('Pagamento ilustrativo.');
          window.location.hash = R.HOME;
        }
      };
    
      return (
        React.createElement('div', {className:'container'},
          React.createElement('h2', {className:'section-title'}, T[lang].payment),
          React.createElement('div', {className:'card'},
            selected ? (
              React.createElement(React.Fragment, null,
                React.createElement('p', null, 'Pacote: ', React.createElement('strong', null, selected.label)),
                React.createElement('p', null, 'Quantidade: ', React.createElement('strong', null, selected.credits), ' lances'),
                React.createElement('p', null, 'Total: ', React.createElement('strong', null, fmtJPY(total))),
                React.createElement('div', {className:'row'},
                  React.createElement('button', {className:'btn btn-primary', onClick:confirmBuy}, 'Pagar agora'),
                  React.createElement('a', {className:'btn btn-ghost', href:R.CREDITS}, 'Voltar')
                )
              )
            ) : lot && isWinner ? (
              React.createElement(React.Fragment, null,
                React.createElement('div', {className:'winner-banner', style:{marginBottom:16}},
                  React.createElement('h3', null, T[lang].winner_title)
                ),
                React.createElement('p', null, 'Produto: ', React.createElement('strong', null, lot.titulo)),
                React.createElement('p', null, 'Descrição: ', React.createElement('span', {className:'muted'}, lot.desc)),
                React.createElement('p', null, 'Valor final: ', React.createElement('strong', {style:{color:'var(--neon)',fontSize:20}}, fmtJPY(lot.lanceAtual))),
                React.createElement('div', {className:'row', style:{marginTop:16}},
                  React.createElement('button', {className:'btn btn-success', onClick:confirmBuy}, 'Confirmar compra'),
                  React.createElement('a', {className:'btn btn-ghost', href:`${R.LOT}/${lot.id}`}, 'Voltar')
                )
              )
            ) : (
              React.createElement(React.Fragment, null,
                React.createElement('p', {className:'muted'}, 'Sem item selecionado. Volte aos Créditos ou Produtos.'),
                React.createElement('div', {className:'row'},
                  React.createElement('a', {className:'btn btn-ghost', href:R.CREDITS}, 'Créditos'),
                  React.createElement('a', {className:'btn btn-ghost', href:R.PROD}, 'Produtos')
                )
              )
            )
          )
        )
      );
    }

    function Admin({lots,setLots, lang}){
      const [title,setTitle]=useState('');
      const [desc,setDesc]=useState('');
      const [imgUrl,setImgUrl]=useState('');
      const [fileData,setFileData]=useState('');
      const [mode,setMode]=useState('now');
      const [minutes,setMinutes]=useState(15);
      const [datetime,setDatetime]=useState('');
      const [activeDuration,setActiveDuration]=useState(10);

      function onFile(e){
        const f=e.target.files?.[0];
        if(!f) return;
        const reader=new FileReader();
        reader.onload=ev=> setFileData(ev.target.result);
        reader.readAsDataURL(f);
      }

      function addProduct(){
        if(!title.trim()) return alert('Informe o título');
        const id = 'l' + Math.random().toString(36).slice(2,8);
        const base = {
          id, titulo:title.trim(), desc:desc.trim()||'',
          lanceAtual:1, img:'', lastBidder:'bot'
        };
        const preview = fileData || imgUrl || '';
        let status='active', endsIn=activeDuration*60, pendingFor=0;

        if(mode==='now'){
          status='active';
          endsIn=activeDuration*60;
        } else if(mode==='minutes'){
          status='pending';
          pendingFor=Math.max(1, minutes)*60;
          endsIn=pendingFor;
        } else if(mode==='datetime'){
          const target = new Date(datetime).getTime();
          const now = Date.now();
          const diff = Math.max(60, Math.floor((target - now)/1000));
          status='pending';
          pendingFor=diff;
          endsIn=pendingFor;
        }

        const newLot = {
          ...base,
          status,
          endsIn,
          defaultActiveSeconds: activeDuration*60,
          img: preview || '🧩'
        };

        setLots(prev=> [newLot, ...prev]);
        setTitle(''); setDesc(''); setImgUrl(''); setFileData('');
        alert('Produto adicionado!');
        window.location.hash = R.PROD;
      }

      return (
        React.createElement('div', {className:'container'},
          React.createElement('h2', {className:'section-title'}, 'Admin — Novo Produto'),
          React.createElement('div', {className:'grid cols-2'},
            React.createElement('div', {className:'card'},
              React.createElement('div', {className:'field'}, React.createElement('label', null, 'Título'), React.createElement('input', {value:title, onChange:e=>setTitle(e.target.value), placeholder:'Nome do produto'})),
              React.createElement('div', {className:'field'}, React.createElement('label', null, 'Descrição'), React.createElement('textarea', {rows:'4', value:desc, onChange:e=>setDesc(e.target.value), placeholder:'Detalhes do produto'})),
              React.createElement('div', {className:'field'},
                React.createElement('label', null, 'Imagem (URL)'),
                React.createElement('input', {value:imgUrl, onChange:e=>setImgUrl(e.target.value), placeholder:'https://...png'})
              ),
              React.createElement('div', {className:'field'},
                React.createElement('label', null, 'Ou Upload'),
                React.createElement('input', {type:'file', accept:'image/*', onChange:onFile})
              ),
              React.createElement('div', {className:'preview', style:{marginTop:10}},
                fileData || imgUrl ? React.createElement('img', {src:fileData || imgUrl, alt:'preview'}) : React.createElement('span', null, 'Pré‑visualização')
              )
            ),
            React.createElement('div', {className:'card'},
              React.createElement('h3', {style:{color:'var(--neon)'}}, 'Agendamento'),
              React.createElement('div', {className:'field'},
                React.createElement('label', null, 'Quando entra no catálogo?'),
                React.createElement('select', {value:mode, onChange:e=>setMode(e.target.value)},
                  React.createElement('option', {value:'now'}, 'Ativar agora'),
                  React.createElement('option', {value:'minutes'}, 'Agendar em X minutos'),
                  React.createElement('option', {value:'datetime'}, 'Agendar por data/hora')
                )
              ),
              mode==='minutes' && (
                React.createElement('div', {className:'field'},
                  React.createElement('label', null, 'Em quantos minutos?'),
                  React.createElement('input', {type:'number', min:'1', value:minutes, onChange:e=>setMinutes(parseInt(e.target.value||'1'))})
                )
              ),
              mode==='datetime' && (
                React.createElement('div', {className:'field'},
                  React.createElement('label', null, 'Data/hora (local)'),
                  React.createElement('input', {type:'datetime-local', value:datetime, onChange:e=>setDatetime(e.target.value)})
                )
              ),
              React.createElement('div', {className:'field'},
                React.createElement('label', null, 'Duração ativa ao iniciar (minutos)'),
                React.createElement('input', {type:'number', min:'1', value:activeDuration, onChange:e=>setActiveDuration(parseInt(e.target.value||'10'))})
              ),
              React.createElement('div', {className:'row', style:{marginTop:10}},
                React.createElement('button', {className:'btn btn-primary', onClick:addProduct}, 'Adicionar produto'),
                React.createElement('a', {className:'btn btn-ghost', href:R.PROD}, 'Ver catálogo')
              )
            )
          ),
          React.createElement('div', {className:'card', style:{marginTop:16}},
            React.createElement('h3', {style:{color:'var(--neon)'}}, `Produtos atuais (${lots.length})`),
            React.createElement('table', {className:'table'},
              React.createElement('thead', null, React.createElement('tr', null, React.createElement('th', null, 'Título'), React.createElement('th', null, 'Status'), React.createElement('th', null, 'Tempo'), React.createElement('th', null, 'Preço'))),
              React.createElement('tbody', null,
                lots.map(l=>(
                  React.createElement('tr', {key:l.id},
                    React.createElement('td', null, l.titulo),
                    React.createElement('td', null, l.status),
                    React.createElement('td', null, l.status==='active' ? clock(l.endsIn) : l.status==='pending' ? `abre em ${clock(l.endsIn)}` : '—'),
                    React.createElement('td', null, fmtJPY(l.lanceAtual))
                  )
                ))
              )
            )
          )
        )
      );
    }

    function App(){
      const [user,setUser] = useLS(LS.USER, {name:'Você', avatar:'', admin:false});
      const [credits,setCredits] = useLS(LS.CREDITS, 0);
      const [lots,setLots] = useLS(LS.LOTS, seedLots);
      const [wins,setWins] = useLS(LS.WINS, []);
      const [users,setUsers] = useLS(LS.USERS, {});
      const [lang,setLang] = useLS(LS.LANG, 'pt');

      const [hash,setHash]=useState(location.hash||R.HOME);
      useEffect(()=>{ const h=()=>setHash(location.hash||R.HOME); window.addEventListener('hashchange',h); return ()=>window.removeEventListener('hashchange',h); },[]);

      useEffect(()=>{
        const t=setInterval(()=>{
          setLots(prev=> prev.map(l=>{
            if(l.status==='active'){
              const n={...l, endsIn:Math.max(0,l.endsIn-1)};
              if(n.endsIn===0){ n.status='ended'; finalizeWin(n); }
              return n;
            }
            if(l.status==='pending'){
              const n={...l, endsIn:Math.max(0,l.endsIn-1)};
              if(n.endsIn===0){ n.status='active'; n.endsIn = l.defaultActiveSeconds || 600; }
              return n;
            }
            return l;
          }))
        },1000);
        return ()=> clearInterval(t);
      },[]);

      const hasCredits = ()=> credits>0;
      function spendCredit(q){ let ok=false; setCredits(c=>{ if(c>=q){ ok=true; return c-q;} return c; }); return ok; }

      const getLot = id => lots.find(l=>l.id===id) || lots[0];
      function updateLot(updated, ended=false, userBid=false){
        setLots(prev=> prev.map(l=> l.id===updated.id ? {...l, ...updated} : l));
        if(ended) finalizeWin(updated);
        if(userBid) {
          setUsers(prev => {
            const s={...prev}; 
            const oldBids = s['you']?.bids || 0;
            const newBids = oldBids + 1;
            s['you']={bids:newBids};
          
            const oldLevel = calcLevel(oldBids);
            const newLevel = calcLevel(newBids);
          
            if(newLevel > oldLevel){
              setCredits(c => c + 1);
              setTimeout(()=> alert(`🎉 ${T[lang].level} ${newLevel}! ${T[lang].free_bid_reward}`), 100);
            }
          
            return s;
          });
        }
      }
      function finalizeWin(l){
        setWins(prev=>{
          if(prev.some(w=>w.id===l.id)) return prev;
          const rec={id:l.id,title:l.titulo,price:l.lanceAtual,winner:l.lastBidder||'bot',at:Date.now()};
          return [...prev, rec];
        });
      }

      let page=null;
      if(hash.startsWith(`${R.LOT}/`)){
        const id=hash.split('/')[2];
        page=React.createElement(LotDetail, {lot:getLot(id), hasCredits:hasCredits, spendCredit:spendCredit, updateLot:updateLot, lang:lang, currentUserName:user?.name||'you'});
      } else if(hash===R.PROD){
        page=React.createElement(Products, {lots:lots, lang:lang});
      } else if(hash===R.CREDITS){
        page=React.createElement(Credits, {packs:PACKS, lang:lang});
      } else if(hash===R.RANK){
        page=React.createElement(Rank, {users:users, wins:wins, lang:lang});
      } else if(hash===R.EVENT){
        page=React.createElement(EventPage, {lang:lang});
      } else if(hash===R.FAQ){
        page=React.createElement(FAQ, {lang:lang});
      } else if(hash===R.PROFILE){
        page=React.createElement(Profile, {user:user, setUser:setUser, credits:credits, usersStats:users, wins:wins, lang:lang});
      } else if(hash.startsWith(R.PAY)){
        page=React.createElement(Payment, {setCredits:setCredits, addPurchase:()=>{}, lots:lots, lang:lang});
      } else if(hash===R.LOGIN){
        page=React.createElement(Login, {user:user, setUser:setUser, lang:lang});
      } else if(hash===R.ADMIN){
        page = user?.admin ? React.createElement(Admin, {lots:lots, setLots:setLots, lang:lang}) : React.createElement('div', {className:'container'}, React.createElement('div', {className:'card'}, React.createElement('p', null, 'Acesso restrito. Faça login como admin.'), React.createElement('a', {className:'btn btn-ghost', href:R.LOGIN}, 'Ir para Login')));
      } else {
        page=React.createElement(Home, {lang:lang});
      }

      return (
        React.createElement('div', {className:'app'},
          React.createElement(Nav, {credits:credits, user:user, usersStats:users, lang:lang, setLang:setLang}),
          page,
          React.createElement('footer', null, `© ${new Date().getFullYear()} Auction Game — Sistema completo com Admin`)
        )
      );
    }

    ReactDOM.render(React.createElement(App), document.getElementById('root'));
  </script>
</body>
</html>
