[Пт 22.05.26 23:46] Shaxiy: <!DOCTYPE html>
<html lang="uz">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>SIGame — Intellektual Viktorina</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/7.23.2/babel.min.js"></script>
<style>
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:'Trebuchet MS','Segoe UI',sans-serif;background:#071028;min-height:100vh;}
.scroll-thin::-webkit-scrollbar{width:4px;}
.scroll-thin::-webkit-scrollbar-track{background:transparent;}
.scroll-thin::-webkit-scrollbar-thumb{background:rgba(255,255,255,0.15);border-radius:2px;}
.slider-blue{-webkit-appearance:none;appearance:none;height:4px;border-radius:2px;outline:none;cursor:pointer;width:100%;}
.slider-blue::-webkit-slider-thumb{-webkit-appearance:none;width:18px;height:18px;border-radius:50%;background:#3b82f6;border:2px solid #fff;cursor:pointer;box-shadow:0 0 8px rgba(59,130,246,0.7);}
.slider-blue::-moz-range-thumb{width:18px;height:18px;border-radius:50%;background:#3b82f6;border:2px solid #fff;cursor:pointer;}
@keyframes fadeUp{from{opacity:0;transform:translateY(16px);}to{opacity:1;transform:translateY(0);}}
@keyframes twinkle{0%,100%{opacity:0.15;}50%{opacity:0.6;}}
@keyframes pulseGlow{0%,100%{box-shadow:0 0 25px rgba(59,130,246,0.35);}50%{box-shadow:0 0 50px rgba(59,130,246,0.65);}}
@keyframes buzzPulse{0%,100%{box-shadow:0 0 50px rgba(239,68,68,0.7);}50%{box-shadow:0 0 80px rgba(239,68,68,1);}}
@keyframes sendPulse{0%,100%{box-shadow:0 0 20px rgba(16,185,129,0.5);}50%{box-shadow:0 0 40px rgba(16,185,129,0.85);}}
@keyframes neonFlash{0%,100%{border-color:rgba(239,68,68,0.4);box-shadow:0 0 20px rgba(239,68,68,0.2);}50%{border-color:rgba(249,115,22,1);box-shadow:0 0 40px rgba(249,115,22,0.6);}}
.btn-menu{transition:background 0.2s,box-shadow 0.2s,transform 0.2s;border:none;outline:none;}
.btn-menu:hover{background:rgba(255,255,255,0.16)!important;box-shadow:0 0 28px rgba(100,180,255,0.3)!important;transform:scale(1.03) translateX(4px)!important;}
</style>
</head>
<body>
<div id="root"></div>
<script type="text/babel">
const { useState, useEffect, useRef, useCallback } = React;

// ─── SVG Icons ───────────────────────────────────────────────
const MusicIcon = () => (
  <svg viewBox="0 0 24 24" fill="#fde047" style={{width:26,height:26}}>
    <path d="M9 17H5a2 2 0 0 0 0 4h2v2h2v-2a2 2 0 0 0 0-4zM19 3l-6 6.72V16a4 4 0 1 0 2 3.46V12.28L21 5.5 19 3z"/>
  </svg>
);
const CapIcon = () => (
  <svg viewBox="0 0 24 24" fill="#fde047" style={{width:26,height:26}}>
    <path d="M12 3L1 9l11 6 9-4.91V17h2V9L12 3zM5 13.18v4L12 21l7-3.82v-4L12 17l-7-3.82z"/>
  </svg>
);
const FolderIcon = () => (
  <svg viewBox="0 0 24 24" fill="none" stroke="#60a5fa" strokeWidth="2" style={{width:16,height:16}}>
    <path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"/>
  </svg>
);
const UserIcon = () => (
  <svg viewBox="0 0 24 24" fill="none" stroke="#60a5fa" strokeWidth="2" style={{width:16,height:16}}>
    <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/>
  </svg>
);
const PlayersIcon = () => (
  <svg viewBox="0 0 24 24" fill="none" stroke="#60a5fa" strokeWidth="2" style={{width:16,height:16}}>
    <path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/>
    <path d="M23 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/>
  </svg>
);
const ChevronDown = ({ open }) => (
  <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5"
    style={{width:16,height:16,flexShrink:0,transform:open?"rotate(180deg)":"rotate(0)",transition:"transform 0.25s"}}>
    <polyline points="6 9 12 15 18 9"/>
  </svg>
);
[Пт 22.05.26 23:46] Shaxiy: // ─── LocalStorage helpers ───────────────────────────────────
const STORAGE_KEY = "sigame_packages";
const DEFAULT_PACKAGE_NAME = "Standart savollar";
const DEFAULT_QUESTIONS = [
  {id:1,topic:"Tarix",q:"O'zbekiston qachon mustaqil bo'ldi?",a:"1991",price:100},
  {id:2,topic:"Geografiya",q:"O'zbekistonning poytaxti qaysi shahar?",a:"Toshkent",price:200},
  {id:3,topic:"Fan",q:"Suv qanday kimyoviy formulaga ega?",a:"H2O",price:100},
  {id:4,topic:"Adabiyot",q:"'O'tkan kunlar' romanini kim yozgan?",a:"Abdulla Qodiriy",price:300},
  {id:5,topic:"Matematika",q:"Kvadrat ildiz 144 dan necha?",a:"12",price:200},
  {id:6,topic:"Tarix",q:"Buyuk Ipak yo'li qaysi mamlakatdan boshlangan?",a:"Xitoy",price:300},
  {id:7,topic:"Geografiya",q:"Dunyodagi eng uzun daryo qaysi?",a:"Nil",price:400},
  {id:8,topic:"Adabiyot",q:"Alisher Navoiy qaysi asrda yashagan?",a:"XV asr",price:400},
  {id:9,topic:"Fan",q:"Yorug'lik tezligi taxminan qancha km/s?",a:"300000",price:500},
  {id:10,topic:"Matematika",q:"Pi soni taxminan qancha?",a:"3.14",price:500},
];
const DEFAULT_PACKAGE = {id:"default", name: DEFAULT_PACKAGE_NAME, questions: DEFAULT_QUESTIONS};

function loadPackages() {
  try {
    const s = localStorage.getItem(STORAGE_KEY);
    if (s) { const p = JSON.parse(s); if (p && p.length > 0) return p; }
  } catch(e) {}
  return [DEFAULT_PACKAGE];
}
function savePackages(pkgs) {
  try { localStorage.setItem(STORAGE_KEY, JSON.stringify(pkgs)); } catch(e) {}
}

function Logo() {
  return (
    <div style={{display:"flex",flexDirection:"column",alignItems:"center",gap:12}}>
      <div style={{display:"flex",alignItems:"flex-start",gap:8}}>
        <div style={{display:"flex",flexDirection:"column",alignItems:"center",gap:4}}>
          <div style={{alignSelf:"flex-start",paddingLeft:4}}><MusicIcon/></div>
          <div style={{display:"flex",gap:4}}>
            {["S","I"].map(l=>(
              <div key={l} style={{width:52,height:52,display:"flex",alignItems:"center",justifyContent:"center",
                fontWeight:900,fontSize:32,color:"#fff",
                border:"2px solid rgba(255,255,255,0.65)",background:"rgba(255,255,255,0.08)",backdropFilter:"blur(4px)"}}>
                {l}
              </div>
            ))}
          </div>
        </div>
        <div style={{width:8}}/>
        <div style={{display:"flex",flexDirection:"column",alignItems:"center",gap:4}}>
          <div style={{alignSelf:"flex-end",paddingRight:4}}><CapIcon/></div>
          <div style={{display:"flex",gap:4}}>
            {["G","A","M","E"].map(l=>(
              <div key={l} style={{width:40,height:40,display:"flex",alignItems:"center",justifyContent:"center",
                fontWeight:900,fontSize:22,color:"#fff",
                border:"2px solid rgba(255,255,255,0.65)",background:"rgba(255,255,255,0.08)",backdropFilter:"blur(4px)"}}>
                {l}
              </div>
            ))}
          </div>
        </div>
      </div>
      <p style={{color:"rgba(255,255,255,0.4)",fontSize:11,letterSpacing:"0.35em",textTransform:"uppercase"}}>
        Intellektual Viktorina
      </p>
    </div>
  );
}

function Stars() {
  const stars = Array.from({length:20},(_, i)=>({
    w:(Math.sin(i*37)*1+1.5),
    top:((i*53)%100),left:((i*71)%100),
    delay:(i*0.3),dur:(2+i%3)
  }));
  return (
    <div style={{position:"absolute",inset:0,pointerEvents:"none",overflow:"hidden"}}>
      <div style={{position:"absolute",top:-80,left:-80,width:280,height:280,borderRadius:"50%",
        background:"radial-gradient(circle, rgba(30,80,200,0.35) 0%, transparent 70%)",filter:"blur(50px)"}}/>
      <div style={{position:"absolute",bottom:-60,right:-60,width:300,height:300,borderRadius:"50%",
        background:"radial-gradient(circle, rgba(0,120,255,0.25) 0%, transparent 70%)",filter:"blur(60px)"}}/>
[Пт 22.05.26 23:46] Shaxiy: {stars.map((s,i)=>(
        <div key={i} style={{position:"absolute",borderRadius:"50%",background:"#fff",
          width:s.w,height:s.w,top:s.top+"%",left:s.left+"%",
          opacity:0.2,animation:twinkle ${s.dur}s ease-in-out infinite,animationDelay:s.delay+"s"}}/>
      ))}
    </div>
  );
}

const menuItems = [
  {id:1,label:"ONLAYN O'YIN",icon:"🌐",action:"online"},
  {id:2,label:"YAKKALIK O'YIN",icon:"🎮",action:"solo"},
  {id:3,label:"PIN-KOD ORQALI KIRISH",icon:"🔑",action:"pin"},
  {id:4,label:"QANDAY O'YNAYMAN?",icon:"❓",action:"help"},
  {id:5,label:"SAVOL MUHARRIRI",icon:"✏️",action:"editor"},
];

function MainMenu({ onAction }) {
  const [loaded,setLoaded]=useState(false);
  useEffect(()=>{const t=setTimeout(()=>setLoaded(true),80);return()=>clearTimeout(t);},[]);
  return (
    <div style={{minHeight:"100vh",width:"100%",display:"flex",flexDirection:"column",
      alignItems:"center",justifyContent:"center",position:"relative",overflow:"hidden",
      padding:"40px 16px",
      background:"linear-gradient(135deg,#0a0f2e 0%,#0d1b4b 40%,#0a2a6e 70%,#0c1f5a 100%)"}}>
      <Stars/>
      <div style={{position:"relative",zIndex:10,display:"flex",flexDirection:"column",
        alignItems:"center",width:"100%",maxWidth:480,gap:32,
        opacity:loaded?1:0,transform:loaded?"translateY(0)":"translateY(-24px)",transition:"all 0.7s ease"}}>
        <Logo/>
        <nav style={{display:"flex",flexDirection:"column",alignItems:"center",width:"100%",gap:10}}>
          {menuItems.map((item,idx)=>(
            <button key={item.id} className="btn-menu"
              onClick={()=>onAction(item.action)}
              style={{width:"100%",maxWidth:400,display:"flex",alignItems:"center",gap:16,
                padding:"14px 24px",textAlign:"left",
                background:"rgba(255,255,255,0.07)",border:"1px solid rgba(255,255,255,0.18)",
                borderRadius:12,backdropFilter:"blur(10px)",
                opacity:loaded?1:0,
                animation:loaded?fadeUp 0.5s ease ${idx*0.07}s both:"none",
                cursor:"pointer"}}>
              <span style={{fontSize:18}}>{item.icon}</span>
              <span style={{color:"#fff",fontWeight:700,letterSpacing:"0.13em",fontSize:13,flex:1}}>{item.label}</span>
              <span style={{color:"rgba(150,210,255,0.7)",fontSize:12}}>▶</span>
            </button>
          ))}
        </nav>
        <p style={{color:"rgba(255,255,255,0.2)",fontSize:11,letterSpacing:"0.2em"}}>v2.0.0 · SIGame Uzbek Edition</p>
      </div>
    </div>
  );
}

function Card({label,icon,hint,children}){
  return(
    <div style={{borderRadius:12,padding:16,background:"rgba(255,255,255,0.05)",border:"1px solid rgba(255,255,255,0.08)"}}>
      <div style={{display:"flex",alignItems:"center",gap:8,marginBottom:12}}>
        {icon}
        <span style={{color:"rgba(255,255,255,0.8)",fontSize:13,fontWeight:700}}>{label}</span>
        {hint&&<span style={{color:"rgba(255,255,255,0.22)",fontSize:11}}>— {hint}</span>}
      </div>
      {children}
    </div>
  );
}

function DDMenu({value,options,open,onToggle,onSelect,capitalize}){
  return(
    <div style={{position:"relative",width:"100%"}} data-dd>
      <button data-dd onClick={onToggle} style={{width:"100%",display:"flex",alignItems:"center",
        justifyContent:"space-between",padding:"10px 14px",borderRadius:10,fontSize:14,
        color:"#fff",fontWeight:600,cursor:"pointer",transition:"all 0.2s",
        background:open?"rgba(37,99,235,0.22)":"rgba(255,255,255,0.08)",
        border:open?"1px solid rgba(100,160,255,0.5)":"1px solid rgba(255,255,255,0.12)",
        textTransform:capitalize?"capitalize":"none"}}>
        <span>{value}</span><ChevronDown open={open}/>
      </button>
      {open&&(
        <div data-dd style={{position:"absolute",left:0,right:0,zIndex:100,top:"calc(100% + 4px)",
[Пт 22.05.26 23:46] Shaxiy: borderRadius:12,overflow:"hidden",
          background:"#0d1b4b",border:"1px solid rgba(100,160,255,0.3)",boxShadow:"0 16px 40px rgba(0,0,0,0.6)"}}>
          {options.map(opt=>(
            <button key={opt} data-dd onClick={()=>onSelect(opt)} style={{width:"100%",textAlign:"left",
              padding:"10px 14px",fontSize:13,cursor:"pointer",transition:"background 0.15s",
              color:opt===value?"#fff":"rgba(255,255,255,0.55)",
              background:opt===value?"rgba(37,99,235,0.3)":"transparent",
              borderBottom:"1px solid rgba(255,255,255,0.05)",
              textTransform:capitalize?"capitalize":"none"}}>
              {opt}
            </button>
          ))}
        </div>
      )}
    </div>
  );
}

function CBRow({checked,onChange,label,hint}){
  return(
    <div style={{display:"flex",alignItems:"flex-start",gap:12,cursor:"pointer"} } onClick={()=>onChange(!checked)}>
      <div style={{flexShrink:0,width:20,height:20,borderRadius:4,display:"flex",
        alignItems:"center",justifyContent:"center",marginTop:2,transition:"all 0.2s",
        background:checked?"#2563eb":"rgba(255,255,255,0.08)",
        border:checked?"1px solid #3b82f6":"1px solid rgba(255,255,255,0.2)",
        boxShadow:checked?"0 0 10px rgba(37,99,235,0.5)":"none"}}>
        {checked&&<svg viewBox="0 0 12 10" fill="none" style={{width:12,height:10}}>
          <polyline points="1,5 4.5,8.5 11,1" stroke="white" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"/>
        </svg>}
      </div>
      <div>
        <p style={{color:"rgba(255,255,255,0.8)",fontSize:13,fontWeight:500}}>{label}</p>
        {hint&&<p style={{color:"rgba(255,255,255,0.28)",fontSize:11,marginTop:2}}>{hint}</p>}
      </div>
    </div>
  );
}

const defaultSettings={
  questionPackage:"",myRole:"o'yinchi",playersCount:3,
  gameType:"Klassik",controlledGame:false,
  playAllQuestions:false,falseStarts:true,finalRound:false,
  timeQuestionSelect:30,timeTopicSelect:20,timePassQuestion:15,
  timePressButton:10,timeAnswerAfterPress:25,timeSpecialQuestion:40,
  timeAllAnswer:30,timeBidding:15,timeHostDecision:20,
};

function XonaTab({settings,update,openDD,setOpenDD}){
  const [allPkgs, setAllPkgs] = React.useState(()=>loadPackages());
  React.useEffect(()=>{ setAllPkgs(loadPackages()); },[openDD]);
  const pkgOptions = allPkgs.map(p=>p.name);
  const roles=["o'yinchi","boshlovchi","tomoshabin"];
  const pct=((settings.playersCount-2)/10)*100;
  return(
    <div style={{display:"flex",flexDirection:"column",gap:14}}>
      <Card label="Savollar paketi" icon={<FolderIcon/>} hint="Savol to'plamini tanlang">
        <DDMenu value={settings.questionPackage  allPkgs[0]?.name  "—"} options={pkgOptions}
          open={openDD==="pkg"} onToggle={()=>setOpenDD(p=>p==="pkg"?null:"pkg")}
          onSelect={v=>{update("questionPackage",v);setOpenDD(null);}}/>
        {allPkgs.length===1&&allPkgs[0].id==="default"&&(
          <p style={{marginTop:8,fontSize:11,color:"rgba(255,255,255,0.3)"}}>
            💡 Yangi paket qo'shish uchun → Savol Muharririga o'ting
          </p>
        )}
      </Card>
      <Card label="Mening rolim" icon={<UserIcon/>} hint="O'yindagi rolingiz">
        <DDMenu value={settings.myRole} options={roles} capitalize
          open={openDD==="role"} onToggle={()=>setOpenDD(p=>p==="role"?null:"role")}
          onSelect={v=>{update("myRole",v);setOpenDD(null);}}/>
      </Card>
      <Card label="O'yinchilar soni" icon={<PlayersIcon/>} hint="2 dan 12 gacha">
        <div style={{display:"flex",alignItems:"center",gap:14,width:"100%"}}>
          <div style={{flexShrink:0,width:48,height:44,display:"flex",alignItems:"center",
            justifyContent:"center",fontWeight:900,fontSize:20,color:"#fff",borderRadius:10,
            background:"linear-gradient(135deg,#1e40af,#2563eb)",boxShadow:"0 0 14px rgba(37,99,235,0.55)"}}>
[Пт 22.05.26 23:46] Shaxiy: {settings.playersCount}
          </div>
          <div style={{flex:1}}>
            <input type="range" min={2} max={12} value={settings.playersCount}
              onChange={e=>update("playersCount",Number(e.target.value))}
              className="slider-blue"
              style={{background:linear-gradient(to right,#2563eb 0%,#2563eb ${pct}%,rgba(255,255,255,0.15) ${pct}%,rgba(255,255,255,0.15) 100%)}}/>
            <div style={{display:"flex",justifyContent:"space-between",marginTop:4}}>
              <span style={{color:"rgba(255,255,255,0.3)",fontSize:11}}>2</span>
              <span style={{color:"rgba(255,255,255,0.3)",fontSize:11}}>12</span>
            </div>
          </div>
        </div>
      </Card>
    </div>
  );
}

function QoidalarTab({settings,update}){
  const types=["Klassik","Tez o'yin","Jamoa o'yini","Turnir"];
  const hints={"Klassik":"An'anaviy qoidalar.","Tez o'yin":"Qisqa vaqt, tez sur'at.","Jamoa o'yini":"Jamoalar birgalikda.","Turnir":"Bosqichli turnir."};
  const rules=[
    {key:"controlledGame",label:"Boshqariladigan o'yin",hint:"Boshlovchi har bosqichni nazorat qiladi"},
    {key:"playAllQuestions",label:"Barcha savollarni o'ynash",hint:"Hech bir savol o'tkazib yuborilmaydi"},
    {key:"falseStarts",label:"Falstartlar",hint:"Savol o'qilayotganda tugma bosish mumkin"},
    {key:"finalRound",label:"Yakuniy raund",hint:"Ohirgi raundda barcha qatnashadi"},
  ];
  return(
    <div style={{display:"flex",flexDirection:"column",gap:14}}>
      <Card label="O'yin turi" hint="Qoidalar to'plamini tanlang">
        <div style={{display:"flex",flexDirection:"column",gap:8}}>
          {types.map(t=>(
            <div key={t} onClick={()=>update("gameType",t)}
              style={{display:"flex",alignItems:"center",gap:12,padding:"10px 14px",borderRadius:10,cursor:"pointer",transition:"all 0.15s",
                background:settings.gameType===t?"rgba(37,99,235,0.2)":"rgba(255,255,255,0.04)",
                border:settings.gameType===t?"1px solid rgba(37,99,235,0.5)":"1px solid transparent"}}>
              <div style={{width:16,height:16,borderRadius:"50%",border:"2px solid",flexShrink:0,
                borderColor:settings.gameType===t?"#3b82f6":"rgba(255,255,255,0.3)",
                background:settings.gameType===t?"#2563eb":"transparent",transition:"all 0.15s"}}/>
              <div>
                <p style={{color:"rgba(255,255,255,0.85)",fontSize:13,fontWeight:600}}>{t}</p>
                <p style={{color:"rgba(255,255,255,0.35)",fontSize:11,marginTop:1}}>{hints[t]}</p>
              </div>
            </div>
          ))}
        </div>
      </Card>
      <Card label="Qoidalar" hint="Maxsus o'yin sozlamalari">
        <div style={{display:"flex",flexDirection:"column",gap:14}}>
          {rules.map(r=><CBRow key={r.key} checked={settings[r.key]} onChange={v=>update(r.key,v)} label={r.label} hint={r.hint}/>)}
        </div>
      </Card>
    </div>
  );
}

function VaqtTab({settings,update}){
  const sliders=[
    {key:"timeQuestionSelect",label:"Savol tanlash",max:60},
    {key:"timeTopicSelect",label:"Mavzu tanlash",max:60},
    {key:"timePassQuestion",label:"Savolni o'tkazib yuborish",max:30},
    {key:"timePressButton",label:"Tugma bosish",max:30},
    {key:"timeAnswerAfterPress",label:"Javob vaqti (bosish keyin)",max:60},
    {key:"timeSpecialQuestion",label:"Maxsus savol",max:90},
    {key:"timeAllAnswer",label:"Hammaning javobi",max:60},
    {key:"timeBidding",label:"Stavka vaqti",max:30},
    {key:"timeHostDecision",label:"Boshlovchi qarori",max:40},
  ];
  return(
    <div style={{display:"flex",flexDirection:"column",gap:10}}>
      {sliders.map(s=>{
        const pct=((settings[s.key]-5)/(s.max-5))*100;
        return(
          <div key={s.key} style={{borderRadius:12,padding:"12px 14px",background:"rgba(255,255,255,0.04)",border:"1px solid rgba(255,255,255,0.07)"}}>
[Пт 22.05.26 23:46] Shaxiy: <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:8}}>
              <span style={{fontSize:12,color:"rgba(255,255,255,0.7)",fontWeight:600}}>{s.label}</span>
              <span style={{fontSize:13,fontWeight:900,color:"#fde047",minWidth:40,textAlign:"right"}}>{settings[s.key]}s</span>
            </div>
            <input type="range" min={5} max={s.max} step={5} value={settings[s.key]}
              onChange={e=>update(s.key,Number(e.target.value))}
              className="slider-thumb"
              style={{background:linear-gradient(to right,#f59e0b 0%,#f59e0b ${pct}%,rgba(255,255,255,0.12) ${pct}%,rgba(255,255,255,0.12) 100%)}}/>
          </div>
        );
      })}
    </div>
  );
}

function RoomSettingsModal({onClose, onCreate}){
  const [tab,setTab]=useState("xona");
  const initPkgs = loadPackages();
  const [settings,setSettings]=useState({...defaultSettings, questionPackage: initPkgs[0]?.name || ""});
  const [openDD,setOpenDD]=useState(null);
  const update=(k,v)=>setSettings(p=>({...p,[k]:v}));
  const tabs=[{id:"xona",label:"Xona"},{id:"qoidalar",label:"Qoidalar"},{id:"vaqt",label:"Vaqt"}];
  useEffect(()=>{
    const h=e=>{if(!e.target.closest("[data-dd]"))setOpenDD(null);};
    document.addEventListener("mousedown",h);return()=>document.removeEventListener("mousedown",h);
  },[]);
  return(
    <div style={{position:"fixed",inset:0,background:"rgba(0,0,0,0.7)",backdropFilter:"blur(8px)",
      display:"flex",alignItems:"center",justifyContent:"center",zIndex:50,padding:16}}>
      <div className="scroll-thin" style={{width:"100%",maxWidth:500,borderRadius:24,
        border:"1px solid rgba(255,255,255,0.12)",background:"rgba(8,16,40,0.97)",
        padding:28,maxHeight:"90vh",overflowY:"auto",boxShadow:"0 0 60px rgba(37,99,235,0.25)"}}>
        <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:24}}>
          <h2 style={{color:"#fff",fontWeight:900,fontSize:18,letterSpacing:"0.08em"}}>⚙️ O'YIN SOZLAMALARI</h2>
          <button onClick={onClose} style={{background:"rgba(255,255,255,0.07)",border:"1px solid rgba(255,255,255,0.15)",
            borderRadius:8,padding:"6px 10px",cursor:"pointer",color:"rgba(255,255,255,0.6)",display:"flex",alignItems:"center",justifyContent:"center"}}>
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" style={{width:20,height:20}}><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
          </button>
        </div>
        <div style={{display:"flex",gap:4,marginBottom:20,background:"rgba(255,255,255,0.05)",borderRadius:12,padding:4}}>
          {tabs.map(t=>(
            <button key={t.id} onClick={()=>setTab(t.id)} style={{flex:1,padding:"8px 6px",borderRadius:9,fontSize:13,fontWeight:700,
              cursor:"pointer",border:"none",transition:"all 0.2s",letterSpacing:"0.06em",
              background:tab===t.id?"rgba(37,99,235,0.7)":"transparent",color:tab===t.id?"#fff":"rgba(255,255,255,0.45)"}}>
              {t.label}
            </button>
          ))}
        </div>
        {tab==="xona"&&<XonaTab settings={settings} update={update} openDD={openDD} setOpenDD={setOpenDD}/>}
        {tab==="qoidalar"&&<QoidalarTab settings={settings} update={update}/>}
        {tab==="vaqt"&&<VaqtTab settings={settings} update={update}/>}
        <div style={{marginTop:24,display:"flex",justifyContent:"center"}}>
          <button onClick={()=>onCreate(settings)}
            style={{padding:"12px 36px",borderRadius:12,fontWeight:900,fontSize:13,
              letterSpacing:"0.15em",textTransform:"uppercase",cursor:"pointer",
              background:"linear-gradient(135deg,#f59e0b,#d97706)",color:"#0a0f2e",border:"none",
              boxShadow:"0 6px 20px rgba(245,158,11,0.45)"}}>
            ✦ YARATISH
          </button>
        </div>
      </div>
    </div>
  );
}

const APPEAL_TIMER = 30;
[Пт 22.05.26 23:46] Shaxiy: function useCountdown(active, seconds, onExpire) {
  const [rem, setRem] = useState(seconds);
  const ref = useRef(null);
  useEffect(() => {
    if (!active) { setRem(seconds); return; }
    setRem(seconds);
    ref.current = setInterval(() => {
      setRem(p => {
        if (p <= 1) { clearInterval(ref.current); onExpire?.(); return 0; }
        return p - 1;
      });
    }, 1000);
    return () => clearInterval(ref.current);
  }, [active, seconds]);
  return rem;
}

function GameBoard({ onBack, selectedPackageName }) {
  const allPkgs = loadPackages();
  const activePkg = allPkgs.find(p=>p.name===selectedPackageName) || allPkgs[0];
  const allQuestions = activePkg ? activePkg.questions : DEFAULT_QUESTIONS;
  const [usedIds, setUsedIds] = useState([]);
  const [currentQ, setCurrentQ] = useState(null);
  const [players, setPlayers] = useState([
    {id:1,name:"ALI",score:1000},
    {id:2,name:"VALI",score:800},
    {id:3,name:"SAMI",score:600},
  ]);
  const [isHost] = useState(true);
  const [currentPlayer] = useState({id:1,name:"ALI"});
  const [qOpen, setQOpen] = useState(false);
  const [buzzed, setBuzzed] = useState(null);
  const [hasTurn, setHasTurn] = useState(false);
  const [answerText, setAnswerText] = useState("");
  const [submitted, setSubmitted] = useState(null);
  const [showAppeal, setShowAppeal] = useState(false);
  const [appealVote, setAppealVote] = useState(null);
  const [toast, setToast] = useState(null);
  const [voteYes, setVoteYes] = useState(0);
  const [voteNo, setVoteNo] = useState(0);
  const [timerActive, setTimerActive] = useState(false);
  const [myVoted, setMyVoted] = useState(false);
  const [history, setHistory] = useState([]);

  const onExpire = useCallback(() => {
    if (!myVoted && appealVote) { setVoteNo(v=>v+1); setMyVoted(true); }
    setTimerActive(false);
  }, [myVoted, appealVote]);

  const timerRem = useCountdown(timerActive, APPEAL_TIMER, onExpire);
  const timerColor = timerRem > 15 ? "green" : timerRem > 7 ? "yellow" : "red";
  const timerPct = (timerRem / APPEAL_TIMER) * 100;

  const openQ = () => {
    const remaining = allQuestions.filter(q=>!usedIds.includes(q.id));
    const pool = remaining.length > 0 ? remaining : allQuestions;
    const picked = pool[Math.floor(Math.random()*pool.length)];
    setCurrentQ(picked ? {text:picked.q, answer:picked.a, price:picked.price, topic:picked.topic, id:picked.id} : null);
    if (picked && remaining.length > 0) setUsedIds(p=>[...p, picked.id]);
    else if (remaining.length === 0) setUsedIds([picked?.id].filter(Boolean));
    setQOpen(true); setBuzzed(null); setHasTurn(false); setAnswerText(""); setSubmitted(null); setShowAppeal(false);
  };
  const closeQ = () => { setQOpen(false); setBuzzed(null); setHasTurn(false); setAnswerText(""); setSubmitted(null); setShowAppeal(false); };

  const pressBuzzer = () => {
    if (buzzed) return;
    setBuzzed(currentPlayer.id);
    setHasTurn(true);
    setAnswerText("");
  };
  const sendAnswer = () => {
    if (!answerText.trim() || submitted) return;
    setSubmitted({playerId:currentPlayer.id, playerName:currentPlayer.name, answer:answerText.trim()});
    setHasTurn(false);
  };

  const handleDecision = (correct) => {
    if (!submitted || !currentQ) return;
    setPlayers(prev=>prev.map(p=>p.id===submitted.playerId?{...p,score:p.score+(correct?currentQ.price:-currentQ.price)}:p));
    if (!correct && submitted.playerId===currentPlayer.id) {
      setShowAppeal(true);
    } else { closeQ(); }
  };

  const triggerAppeal = () => {
    setShowAppeal(false);
    setAppealVote({playerName:currentPlayer.name, answer:submitted.answer});
    setVoteYes(0); setVoteNo(0); setMyVoted(false); setTimerActive(true);
    setHistory(prev=>[{id:Date.now(),playerName:currentPlayer.name,answer:submitted.answer,status:"kutilmoqda",yes:0,no:0},...prev]);
  };

  const submitVote = (approve) => {
    setMyVoted(true);
    if (approve) setVoteYes(v=>v+1); else setVoteNo(v=>v+1);
  };
[Пт 22.05.26 23:46] Shaxiy: const resolveAppeal = (accepted) => {
    if (accepted && currentQ) {
      setPlayers(prev=>prev.map(p=>p.id===currentPlayer.id?{...p,score:p.score+currentQ.price}:p));
      setToast("⚠️ Shikoyat qabul ildindi! Ballar tiklandi.");
    } else {
      setToast("❌ Shikoyat rad etildi.");
    }
    setHistory(prev=>prev.map((h,i)=>i===0?{...h,status:accepted?"qabul":"rad",yes:voteYes,no:voteNo}:h));
    setAppealVote(null); setTimerActive(false);
    if (accepted) closeQ();
    setTimeout(()=>setToast(null),3500);
  };

  const timerBarColor = {green:"linear-gradient(90deg,#10b981,#34d399)",yellow:"linear-gradient(90deg,#f59e0b,#fde047)",red:"linear-gradient(90deg,#ef4444,#f87171)"};
  const timerNumColor = {green:"#34d399",yellow:"#fde047",red:"#f87171"};
  const buzzedName = buzzed ? (players.find(p=>p.id===buzzed)?.name ?? "—") : null;
  const totalVoters = players.length - 1;
  const waitingCount = Math.max(0, totalVoters - voteYes - voteNo);

  return (
    <div style={{minHeight:"100vh",padding:24,color:"#fff", background:"linear-gradient(135deg,#071028 0%,#0c1f4d 50%,#071028 100%)"}}>
      {toast&&<div style={{position:"fixed",top:30,left:"50%",transform:"translateX(-50%)", background:"rgba(16,185,129,0.95)",border:"2px solid #34d399", padding:"16px 36px",borderRadius:20,fontSize:17,fontWeight:900, color:"#fff",textAlign:"center",zIndex:100,boxShadow:"0 0 40px rgba(16,185,129,0.6)", letterSpacing:"0.05em"}}>{toast}</div>}
      <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:36,flexWrap:"wrap",gap:12}}>
        <div>
          <h1 style={{fontSize:42,fontWeight:900,letterSpacing:"0.25em",color:"#67e8f9"}}>SI GAME</h1>
          <p style={{color:"rgba(255,255,255,0.4)",marginTop:4,letterSpacing:"0.2em",fontSize:12}}>
            {selectedPackageName ? 📦 ${selectedPackageName} : "INTELLEKTUAL VIKTORINA"} · {allQuestions.length - usedIds.length}/{allQuestions.length} savol qoldi
          </p>
        </div>
        <div style={{display:"flex",gap:10}}>
          <button onClick={openQ} style={{padding:"12px 24px",background:"#2563eb",border:"none",borderRadius:12,color:"#fff",fontWeight:900,cursor:"pointer"}}>SAVOLNI OCHISH</button>
          <button onClick={onBack} style={{padding:"12px 20px",borderRadius:12,background:"rgba(255,255,255,0.07)", border:"1px solid rgba(255,255,255,0.2)",color:"rgba(255,255,255,0.6)", fontSize:13,fontWeight:700,cursor:"pointer"}}>← MENYU</button>
        </div>
      </div>
      <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(180px,1fr))",gap:18,marginBottom:32}}>
        {players.map(p=>(
          <div key={p.id} style={{borderRadius:22,padding:22, border:p.id===buzzed?"1px solid rgba(103,232,249,0.6)":"1px solid rgba(103,232,249,0.2)", background:"rgba(255,255,255,0.05)",backdropFilter:"blur(16px)", boxShadow:p.id===buzzed?"0 0 20px rgba(103,232,249,0.25)":"none",transition:"all 0.3s"}}>
            <div style={{fontSize:20,fontWeight:900,marginBottom:8}}>{p.name}</div>
            <div style={{fontSize:40,fontWeight:900,color:p.score<0?"#f87171":"#fde047"}}>{p.score}</div>
          </div>
        ))}
      </div>
      
      {qOpen && currentQ && (
        <div style={{position:"fixed",inset:0,background:"rgba(5,10,25,0.95)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:40,padding:20}}>
          <div style={{width:"100%",maxWidth:640,background:"#0c1535",border:"1px solid rgba(255,255,255,0.1)",borderRadius:28,padding:36,boxShadow:"0 20px 50px rgba(0,0,0,0.5)"}}>
            <div style={{display:"flex",justifyContent:"space-between",color:"#f97316",fontWeight:700,fontSize:14,marginBottom:16}}>
              <span>Mavzu: {currentQ.topic}</span>
              <span>Qiymati: {currentQ.price} ball</span>
[Пт 22.05.26 23:46] Shaxiy: </div>
            <div style={{fontSize:24,fontWeight:700,lineHeight:1.5,marginBottom:32,color:"#fff"}}>{currentQ.text}</div>
            
            {!buzzed && (
              <button onClick={pressBuzzer} style={{width:"100%",padding:20,background:"danger",borderRadius:16,background:"#ef4444",color:"#fff",fontSize:20,fontWeight:900,cursor:"pointer",boxShadow:"0 8px 24px rgba(239,68,68,0.4)"}}>TUGMANI BOSISH 🔴</button>
            )}
            
            {buzzed && (
              <div style={{background:"rgba(255,255,255,0.04)",borderRadius:16,padding:20,border:"1px solid rgba(255,255,255,0.08)"}}>
                <div style={{fontWeight:700,color:"#67e8f9",marginBottom:12}}>Javob beradi: {buzzedName}</div>
                {hasTurn ? (
                  <div style={{display:"flex",gap:10}}>
                    <input type="text" value={answerText} onChange={e=>setAnswerText(e.target.value)} placeholder="Javobingizni yozing..." style={{flex:1,padding:12,borderRadius:10,background:"#070d22",border:"1px solid rgba(255,255,255,0.2)",color:"#fff",fontSize:14}}/>
                    <button onClick={sendAnswer} style={{padding:"12px 24px",background:"#10b981",border:"none",borderRadius:10,color:"#fff",fontWeight:700,cursor:"pointer"}}>YUBORISH</button>
                  </div>
                ) : (
                  submitted && (
                    <div>
                      <div style={{fontSize:16,fontStyle:"italic",color:"#fde047",marginBottom:20}}>"{submitted.answer}"</div>
                      {isHost && (
                        <div style={{display:"flex",gap:12}}>
                          <button onClick={()=>handleDecision(true)} style={{flex:1,padding:12,background:"#10b981",border:"none",borderRadius:10,color:"#fff",fontWeight:700,cursor:"pointer"}}>✓ TO'G'RI</button>
                          <button onClick={()=>handleDecision(false)} style={{flex:1,padding:12,background:"#ef4444",border:"none",borderRadius:10,color:"#fff",fontWeight:700,cursor:"pointer"}}>✕ NOTO'G'RI</button>
                        </div>
                      )}
                    </div>
                  )
                )}
              </div>
            )}
            
            {showAppeal && (
              <div style={{marginTop:20,textAlign:"center",background:"rgba(239,68,68,0.1)",padding:16,borderRadius:12,border:"1px solid rgba(239,68,68,0.3)"}}>
                <p style={{color:"#f87171",fontSize:14,marginBottom:12}}>Javobingiz xato deb topildi. Apellyatsiya berasizmi?</p>
                <div style={{display:"flex",justifyContent:"center",gap:12}}>
                  <button onClick={triggerAppeal} style={{padding:"8px 20px",background:"#f59e0b",border:"none",borderRadius:8,color:"#000",fontWeight:700,cursor:"pointer"}}>HA, BERAMAN</button>
                  <button onClick={closeQ} style={{padding:"8px 20px",background:"rgba(255,255,255,0.1)",border:"none",borderRadius:8,color:"#fff",cursor:"pointer"}}>YO'Q, KEYINGI SAVOL</button>
                </div>
              </div>
            )}
          </div>
        </div>
      )}

      {appealVote && (
        <div style={{position:"fixed",inset:0,background:"rgba(5,10,25,0.92)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:45,padding:16}}>
          <div style={{width:"100%",maxWidth:500,background:"#0f172a",border:"2px solid #f59e0b",borderRadius:24,padding:28,boxShadow:"0 0 50px rgba(245,158,11,0.2)"}}>
            <h3 style={{color:"#f59e0b",fontSize:20,fontWeight:900,textAlign:"center",marginBottom:6}}>⚠️ SHIKOYAT KO'RIB CHIQILMOQDA</h3>
            <p style={{fontSize:13,color:"rgba(255,255,255,0.4)",textAlign:"center",marginBottom:24}}>Boshqa o'yinchilar ovoz berishmoqda</p>
            <div style={{background:"rgba(255,255,255,0.03)",borderRadius:16,padding:20,marginBottom:24,border:"1px solid rgba(255,255,255,0.06)"}}>
[Пт 22.05.26 23:46] Shaxiy: <div style={{fontSize:12,color:"rgba(255,255,255,0.4)",marginBottom:4}}>O'yinchi:</div>
              <div style={{fontSize:16,fontWeight:700,color:"#fff",marginBottom:12}}>{appealVote.playerName}</div>
              <div style={{fontSize:12,color:"rgba(255,255,255,0.4)",marginBottom:4}}>Bahsli javob:</div>
              <div style={{fontSize:16,fontWeight:700,color:"#fde047",fontStyle:"italic"}}>"{appealVote.answer}"</div>
            </div>
            
            <div style={{marginBottom:24}}>
              <div style={{display:"flex",justifyContent:"space-between",fontSize:13,marginBottom:6,fontWeight:700}}>
                <span style={{color:timerNumColor[timerColor]}}>Qolgan vaqt: {timerRem}s</span>
                <span style={{color:"rgba(255,255,255,0.5)"}}>{voteYes + voteNo}/{totalVoters} ovoz berildi</span>
              </div>
              <div style={{width:"100%",height:6,background:"rgba(255,255,255,0.1)",borderRadius:3,overflow:"hidden"}}>
                <div style={{width:${timerPct}%,height:"100%",background:timerBarColor[timerColor],transition:"width 1s linear"}}/>
              </div>
            </div>
            
            {!myVoted && appealVote.playerName !== currentPlayer.name ? (
              <div style={{display:"flex",gap:12,marginBottom:20}}>
                <button onClick={()=>submitVote(true)} style={{flex:1,padding:14,background:"#10b981",border:"none",borderRadius:12,color:"#fff",fontWeight:700,cursor:"pointer"}}>👍 JAVOB TO'G'RI</button>
                <button onClick={()=>submitVote(false)} style={{flex:1,padding:14,background:"#ef4444",border:"none",borderRadius:12,color:"#fff",fontWeight:700,cursor:"pointer"}}>👎 XATO JAVOB</button>
              </div>
            ) : (
              <div style={{textAlign:"center",padding:12,background:"rgba(255,255,255,0.05)",borderRadius:12,color:"rgba(255,255,255,0.5)",fontSize:13,marginBottom:20,border:"1px solid rgba(255,255,255,0.05)"}}>
                {appealVote.playerName === currentPlayer.name ? "Siz o'z ovozingiz natijasini kutyapsiz..." : "Ovozingiz qabul qilindi. Kutilmoqda..."}
              </div>
            )}
            
            {isHost && (
              <div style={{borderTop:"1px solid rgba(255,255,255,0.1)",paddingTop:20,display:"flex",gap:12}}>
                <button onClick={()=>resolveAppeal(true)} style={{flex:1,padding:10,background:"rgba(16,185,129,0.2)",border:"1px solid #10b981",borderRadius:10,color:"#34d399",fontWeight:700,cursor:"pointer"}}>MUDDATIDAN OLDIN QABUL QILISH</button>
                <button onClick={()=>resolveAppeal(false)} style={{flex:1,padding:10,background:"rgba(239,68,68,0.2)",border:"1px solid #ef4444",borderRadius:10,color:"#f87171",fontWeight:700,cursor:"pointer"}}>MUDDATIDAN OLDIN RAD ETISH</button>
              </div>
            )}
          </div>
        </div>
      )}
    </div>
  );
}

function SoloScreen({ onBack }) {
  return (
    <div style={{minHeight:"100vh",display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center",background:"#0a0f2e",color:"#fff",padding:24}}>
      <h2>🎮 YAKKALIK O'YIN BLOKI</h2>
      <p style={{color:"rgba(255,255,255,0.5)",marginTop:12,marginBottom:24}}>Bu bo'lim keyingi yangilanishda taqdim etiladi.</p>
      <button onClick={onBack} style={{padding:"10px 24px",background:"rgba(255,255,255,0.1)",border:"none",borderRadius:8,color:"#fff",cursor:"pointer"}}>ORQAGA</button>
    </div>
  );
}

function PinScreen({ onBack }) {
  const [pin, setPin] = useState("");
  return (
    <div style={{minHeight:"100vh",display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center",background:"#0a0f2e",color:"#fff",padding:24}}>
      <Logo/>
      <div style={{width:"100%",maxWidth:360,background:"rgba(255,255,255,0.04)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:20,padding:28,marginTop:32,display:"flex",flexDirection:"column",gap:20}}>
[Пт 22.05.26 23:46] Shaxiy: <h3 style={{textAlign:"center",fontWeight:900,fontSize:15,letterSpacing:"0.05em"}}>🔑 PIN-KOD ORQALI ULANISH</h3>
        <input type="text" maxLength={6} value={pin} onChange={e=>setPin(e.target.value.toUpperCase())} placeholder="000000" style={{width:"100%",padding:16,borderRadius:12,background:"#05091e",border:"1px solid rgba(255,255,255,0.2)",color:"#fff",fontSize:24,fontWeight:900,letterSpacing:"0.3em",textAlign:"center",outline:"none"}}/>
        <button style={{width:"100%",padding:14,background:"linear-gradient(135deg,#3b82f6,#1d4ed8)",border:"none",borderRadius:12,color:"#fff",fontWeight:900,cursor:"pointer"}}>KIRISH →</button>
        <button onClick={onBack} style={{width:"100%",padding:10,background:"transparent",border:"none",color:"rgba(255,255,255,0.4)",fontSize:13,cursor:"pointer"}}>Yopish</button>
      </div>
    </div>
  );
}

function HelpScreen({ onBack }) {
  return (
    <div style={{minHeight:"100vh",background:"#071028",color:"#fff",padding:40,display:"flex",flexDirection:"column",alignItems:"center"}}>
      <div style={{width:"100%",maxWidth:600}}>
        <h2 style={{fontSize:28,fontWeight:900,color:"#67e8f9",marginBottom:24,textAlign:"center"}}>❓ O'YIN QOIDALARI</h2>
        <div style={{display:"flex",flexDirection:"column",gap:16,lineHeight:1.6,color:"rgba(255,255,255,0.75)"}}>
          <p><strong>1. O'yin Maqsadi:</strong> Berilgan savollarga tez va to'g'ri javob berib, maksimal darajada yuqori ball to'plash.</p>
          <p><strong>2. Savol tanlash:</strong> Navbat kelgan o'yinchi doskadagi o'zi xohlagan mavzu va ball qiymatidagi savolni tanlaydi.</p>
          <p><strong>3. Tugmani bosish:</strong> Savol ekranda paydo bo'lgach, javob berishni istagan har qanday o'yinchi "TUGMANI BOSISH" tugmasini tezkor bosishi kerak. Kim birinchi bossa, o'sha odam javob berish huquqini qo'lga kiritadi.</p>
          <p><strong>4. Apellyatsiya tizimi:</strong> Agar o'yinchining javobi boshlovchi tomonidan xato deb topilsa va o'yinchi bu qarorga rozi bo'lmasa, u apellyatsiya berishi mumkin. Bunday holatda boshqa o'yinchilar 30 soniya ichida ovoz berishadi. Ko'pchilik ovoz bersa, ball tiklanadi.</p>
        </div>
        <button onClick={onBack} style={{marginTop:36,width:"100%",padding:12,background:"rgba(255,255,255,0.1)",border:"none",borderRadius:10,color:"#fff",fontWeight:700,cursor:"pointer"}}>TUSHUNDIM, ORQAGA</button>
      </div>
    </div>
  );
}

function EditorScreen({ onBack }) {
  const [packages, setPackages] = useState(() => loadPackages());
  const [selectedPkgId, setSelectedPkgId] = useState(packages[0]?.id || "default");
  
  const currentPkg = packages.find(p => p.id === selectedPkgId) || packages[0];
  
  const [topic, setTopic] = useState("");
  const [q, setQ] = useState("");
  const [a, setA] = useState("");
  const [price, setPrice] = useState(100);

  const saveAndRefresh = (updated) => {
    setPackages(updated);
    savePackages(updated);
  };

  const createPackage = () => {
    const name = prompt("Yangi paket nomini kiriting:");
    if (!name || !name.trim()) return;
    const newPkg = { id: "pkg_" + Date.now(), name: name.trim(), questions: [] };
    const updated = [...packages, newPkg];
    saveAndRefresh(updated);
    setSelectedPkgId(newPkg.id);
  };

  const deletePackage = (id) => {
    if (id === "default") { alert("Standart paketni o'chirib bo'lmaydi!"); return; }
    if (!confirm("Haqiqatan ham ushbu paketni va undagi barcha savollarni o'chirmoqchisiz?")) return;
    const updated = packages.filter(p => p.id !== id);
    saveAndRefresh(updated);
    setSelectedPkgId(packages[0]?.id || "default");
  };

  const addQuestion = (e) => {
    e.preventDefault();
    if (!topic.trim()  !q.trim()  !a.trim()) { alert("Barcha maydonlarni to'ldiring!"); return; }
    
    const newQuestion = {
      id: Date.now(),
      topic: topic.trim(),
      q: q.trim(),
      a: a.trim(),
      price: Number(price)
    };
[Пт 22.05.26 23:46] Shaxiy: const updated = packages.map(p => {
      if (p.id === selectedPkgId) {
        return { ...p, questions: [...p.questions, newQuestion] };
      }
      return p;
    });

    saveAndRefresh(updated);
    setQ(""); setA("");
    alert("✅ Savol muvaffaqiyatli qo'shildi!");
  };

  const removeQuestion = (qId) => {
    const updated = packages.map(p => {
      if (p.id === selectedPkgId) {
        return { ...p, questions: p.questions.filter(item => item.id !== qId) };
      }
      return p;
    });
    saveAndRefresh(updated);
  };

  // JSON fayl ko'rinishida yuklab olish (Export)
  const exportPackage = (pkg) => {
    const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(pkg, null, 2));
    const downloadAnchor = document.createElement('a');
    downloadAnchor.setAttribute("href", dataStr);
    downloadAnchor.setAttribute("download", ${pkg.name}_savollari.json);
    document.body.appendChild(downloadAnchor);
    downloadAnchor.click();
    downloadAnchor.remove();
  };

  // JSON faylni tizimga yuklash (Import)
  const handleJsonImport = (e) => {
    const fileReader = new FileReader();
    if (!e.target.files[0]) return;
    
    fileReader.readAsText(e.target.files[0], "UTF-8");
    fileReader.onload = (event) => {
      try {
        const parsed = JSON.parse(event.target.result);
        if (!parsed.name || !Array.isArray(parsed.questions)) {
          alert("Xato: JSON fayl formati noto'g'ri! Tarkibida 'name' va 'questions' massivi bo'lishi shart.");
          return;
        }
        const newPkg = {
          id: "pkg_" + Date.now(),
          name: parsed.name + " (Yuklangan)",
          questions: parsed.questions
        };
        const updated = [newPkg, ...packages];
        saveAndRefresh(updated);
        setSelectedPkgId(newPkg.id);
        alert("✅ Paket muvaffaqiyatli yuklandi!");
      } catch (err) {
        alert("JSON faylini o'qishda xatolik yuz berdi!");
      }
    };
    e.target.value = ""; // Inputni tozalash
  };

  return (
    <div style={{minHeight:"100vh",background:"#071028",color:"#fff",padding:32}}>
      <div style={{maxWidth:900,margin:"0 auto"}}>
        <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:24,flexWrap:"wrap",gap:12}}>
          <div>
            <h2 style={{fontSize:28,fontWeight:900,color:"#67e8f9"}}>✏️ SAVOLLAR MUHARRIRI</h2>
            <p style={{color:"rgba(255,255,255,0.4)",fontSize:13,marginTop:4}}>Savol to'plamlarini (paketlarni) boshqarish va tahrirlash</p>
          </div>
          <button onClick={onBack} style={{padding:"10px 20px",borderRadius:10,background:"rgba(255,255,255,0.08)",border:"1px solid rgba(255,255,255,0.15)",color:"#fff",cursor:"pointer",fontWeight:700}}>← MENYU</button>
        </div>

        <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(280px,1fr))",gap:24,alignItems:"start"}}>
          
          {/* Paketlarni tanlash va yuklash bo'limi */}
          <div style={{background:"rgba(255,255,255,0.03)",border:"1px solid rgba(255,255,255,0.08)",borderRadius:20,padding:24,display:"flex",flexDirection:"column",gap:16}}>
            <h3 style={{fontSize:16,color:"#fde047",fontWeight:800}}>📦 SAVOL PAKETLARI</h3>
            
            <div style={{display:"flex",flexDirection:"column",gap:6}}>
              {packages.map(p => (
                <div key={p.id} onClick={()=>setSelectedPkgId(p.id)} style={{display:"flex",alignItems:"center",justifyContent:"space-between",padding:"12px 14px",borderRadius:10,cursor:"pointer",background:p.id===selectedPkgId?"rgba(37,99,235,0.2)":"rgba(255,255,255,0.02)",border:p.id===selectedPkgId?"1px solid #2563eb":"1px solid rgba(255,255,255,0.06)"}}>
                  <span style={{fontSize:14,fontWeight:600,color:p.id===selectedPkgId?"#fff":"rgba(255,255,255,0.7)"}}>{p.name} ({p.questions.length})</span>
[Пт 22.05.26 23:46] Shaxiy: {p.id !== "default" && (
                    <button onClick={(e)=>{e.stopPropagation(); deletePackage(p.id);}} style={{background:"transparent",border:"none",color:"#ef4444",cursor:"pointer",fontSize:13}}>✕</button>
                  )}
                </div>
              ))}
            </div>

            <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:10,marginTop:8}}>
              <button onClick={createPackage} style={{padding:12,background:"#2563eb",border:"none",borderRadius:10,color:"#fff",fontWeight:700,cursor:"pointer",fontSize:12}}>➕ YANGI PAKET</button>
              <button onClick={() => exportPackage(currentPkg)} style={{padding:12,background:"#10b981",border:"none",borderRadius:10,color:"#fff",fontWeight:700,cursor:"pointer",fontSize:12}}>💾 JSON EXPORT</button>
            </div>

            {/* JSON IMPORT TUGMASI */}
            <div style={{borderTop:"1px solid rgba(255,255,255,0.1)",paddingTop:14,marginTop:4}}>
              <label style={{display:"block",textAlign:"center",padding:"12px",background:"rgba(245,158,11,0.15)",border:"1px dashed #f59e0b",borderRadius:10,color:"#fde047",fontWeight:700,cursor:"pointer",fontSize:12}}>
                📥 JSON FAYL YUKLASH (IMPORT)
                <input type="file" accept=".json" onChange={handleJsonImport} style={{display:"none"}}/>
              </label>
            </div>
          </div>

          {/* Savol qo'shish formasi */}
          <form onSubmit={addQuestion} style={{background:"rgba(255,255,255,0.03)",border:"1px solid rgba(255,255,255,0.08)",borderRadius:20,padding:24,display:"flex",flexDirection:"column",gap:14}}>
            <h3 style={{fontSize:16,color:"#67e8f9",fontWeight:800}}>➕ YANGI SAVOL QO'SHISH</h3>
            <p style={{fontSize:11,color:"rgba(255,255,255,0.4)"}}>Tanlangan paket: <span style={{color:"#fff",fontWeight:700}}>{currentPkg?.name}</span></p>
            
            <div style={{display:"flex",flexDirection:"column",gap:4}}>
              <label style={{fontSize:12,color:"rgba(255,255,255,0.5)"}}>Mavzu (Kategoriya)</label>
              <input type="text" required value={topic} onChange={e=>setTopic(e.target.value)} placeholder="Masalan: Geografiya" style={{padding:10,borderRadius:8,background:"#05091e",border:"1px solid rgba(255,255,255,0.15)",color:"#fff",fontSize:13}}/>
            </div>

            <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:10}}>
              <div style={{display:"flex",flexDirection:"column",gap:4}}>
                <label style={{fontSize:12,color:"rgba(255,255,255,0.5)"}}>Qiymati (Ball)</label>
                <select value={price} onChange={e=>setPrice(Number(e.target.value))} style={{padding:10,borderRadius:8,background:"#05091e",border:"1px solid rgba(255,255,255,0.15)",color:"#fff",fontSize:13}}>
                  {[100,200,300,400,500,600,700,800,900,1000].map(v=><option key={v} value={v}>{v}</option>)}
                </select>
              </div>
            </div>

            <div style={{display:"flex",flexDirection:"column",gap:4}}>
              <label style={{fontSize:12,color:"rgba(255,255,255,0.5)"}}>Savol matni</label>
              <textarea required rows={3} value={q} onChange={e=>setQ(e.target.value)} placeholder="Savolni batafsil yozing..." style={{padding:10,borderRadius:8,background:"#05091e",border:"1px solid rgba(255,255,255,0.15)",color:"#fff",fontSize:13,resize:"none"}}/>
            </div>

            <div style={{display:"flex",flexDirection:"column",gap:4}}>
              <label style={{fontSize:12,color:"rgba(255,255,255,0.5)"}}>To'g'ri javob</label>
              <input type="text" required value={a} onChange={e=>setA(e.target.value)} placeholder="Kutilayotgan qisqa javob" style={{padding:10,borderRadius:8,background:"#05091e",border:"1px solid rgba(255,255,255,0.15)",color:"#fff",fontSize:13}}/>
            </div>
[Пт 22.05.26 23:46] Shaxiy: <button type="submit" style={{padding:12,background:"linear-gradient(135deg,#f59e0b,#d97706)",border:"none",borderRadius:10,color:"#0a0f2e",fontWeight:900,cursor:"pointer",marginTop:6,letterSpacing:"0.05em"}}>KIRITISH ✦</button>
          </form>
        </div>

        {/* Mavjud savollar ro'yxati */}
        <div style={{marginTop:32,background:"rgba(255,255,255,0.02)",border:"1px solid rgba(255,255,255,0.06)",borderRadius:20,padding:24}}>
          <h3 style={{fontSize:16,fontWeight:800,marginBottom:16,color:"rgba(255,255,255,0.8)"}}>📋 TO'PLAMDAGI SAVOLLAR ({currentPkg?.questions.length || 0} ta)</h3>
          {(!currentPkg || currentPkg.questions.length === 0) ? (
            <p style={{color:"rgba(255,255,255,0.3)",fontSize:13,textAlign:"center",padding:"24px 0"}}>Ushbu paketga hali hech qanday savol kiritilmagan.</p>
          ) : (
            <div style={{display:"flex",flexDirection:"column",gap:10,maxHeight:400,overflowY:"auto",paddingRight:6}} className="scroll-thin">
              {currentPkg.questions.map((item, index) => (
                <div key={item.id} style={{display:"flex",alignItems:"center",justifyContent:"space-between",padding:14,background:"rgba(255,255,255,0.03)",borderRadius:12,border:"1px solid rgba(255,255,255,0.05)",gap:16}}>
                  <div style={{flex:1}}>
                    <div style={{display:"flex",alignItems:"center",gap:10,marginBottom:4}}>
                      <span style={{fontSize:11,background:"rgba(103,232,249,0.15)",color:"#67e8f9",padding:"2px 8px",borderRadius:4,fontWeight:700}}>{item.topic}</span>
                      <span style={{fontSize:11,background:"rgba(250,204,21,0.15)",color:"#facc15",padding:"2px 8px",borderRadius:4,fontWeight:700}}>{item.price} ball</span>
                    </div>
                    <p style={{fontSize:13,fontWeight:600,color:"#fff"}}>{index+1}. {item.q}</p>
                    <p style={{fontSize:12,color:"#10b981",marginTop:2,fontWeight:700}}>Javob: {item.a}</p>
                  </div>
                  <button onClick={() => removeQuestion(item.id)} style={{background:"rgba(239,68,68,0.1)",border:"1px solid rgba(239,68,68,0.2)",color:"#f87171",padding:"6px 12px",borderRadius:6,cursor:"pointer",fontSize:12,fontWeight:700}}>O'chirish</button>
                </div>
              ))}
            </div>
          )}
        </div>

      </div>
    </div>
  );
}

function MainApp() {
  const [screen, setScreen] = useState("menu");
  const [showModal, setShowModal] = useState(false);
  const [created, setCreated] = useState(null);
  const [selectedPackageName, setSelectedPackageName] = useState("");

  return (
    <div style={{width:"100%",minHeight:"100vh",position:"relative"}}>
      {screen === "menu" && (
        <MainMenu onAction={(act) => {
          if (act === "online") { setShowModal(true); }
          else if (act === "solo") { setScreen("solo"); }
          else if (act === "pin") { setScreen("pin"); }
          else if (act === "help") { setScreen("help"); }
          else if (act === "editor") { setScreen("editor"); }
        }}/>
      )}
      {screen === "game" && <GameBoard onBack={()=>setScreen("menu")} selectedPackageName={selectedPackageName}/>}
      {screen === "solo" && <SoloScreen onBack={()=>setScreen("menuexport")}/>}
      {screen === "pin" && <PinScreen onBack={()=>setScreen("menu")}/>}
      {screen === "help" && <HelpScreen onBack={()=>setScreen("menu")}/>}
      {screen === "editor" && <EditorScreen onBack={()=>setScreen("menu")}/>}

      {showModal && (
        <RoomSettingsModal
          onClose={()=>setShowModal(false)}
          onCreate={(s)=>{ 
            // 6 xonali tasodifiy PIN-kod generatsiya qilish (Masalan: 481953)
            const randomPin = Math.floor(100000 + Math.random() * 900000).toString();
            const roomData = { ...s, pin: randomPin };
            
            setCreated(roomData); 
            setSelectedPackageName(s.questionPackage); 
            setShowModal(false); 
            setScreen("game"); 
          }}
        />
      )}
[Пт 22.05.26 23:46] Shaxiy: {created && screen==="game" && (
        <div style={{position:"fixed",bottom:24,right:24,zIndex:50,
          padding:"12px 24px",borderRadius:14,color:"#fff",fontWeight:700,fontSize:13,
          background:"linear-gradient(135deg,#1d4ed8,#2563eb)",
          boxShadow:"0 8px 24px rgba(37,99,235,0.5)",
          animation:"fadeUp 0.4s ease",letterSpacing:"0.05em",display:"flex",alignItems:"center",gap:12}}>
          ✅ Xona yaratildi! PIN: <span style={{color:"#fbbf24",fontSize:16,fontWeight:900}}>{created.pin}</span> | Paket: <span style={{color:"#67e8f9"}}>{created.questionPackage}</span>
          <button onClick={()=>setCreated(null)} style={{background:"transparent",border:"none",color:"#fff",cursor:"pointer",fontWeight:900,marginLeft:8}}>✕</button>
        </div>
      )}
    </div>
  );
}

const root = AzadDom = ReactDOM.createRoot(document.getElementById("root"));
root.render(<MainApp/>);
</script>
</body>
</html>
