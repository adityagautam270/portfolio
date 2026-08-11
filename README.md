<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Portfolio</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;700&family=JetBrains+Mono:wght@400;500&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/7.23.5/babel.min.js"></script>
<style>
  :root{
    --paper:#F6F3EC; --ink:#1B1B18; --sub:#6B6558; --line:#DCD6C6;
    --card:#FFFDF8; --amber:#B9791C; --amber-deep:#7A4E10; --teal:#2F6F68; --teal-deep:#1D4640;
    --glow:rgba(185,121,28,0.25);
  }
  [data-theme="dark"]{
    --paper:#0A0B0D; --ink:#F0EDE4; --sub:#8B8A82; --line:#242629;
    --card:#14161A; --amber:#E8B563; --amber-deep:#F5D08A; --teal:#5FC4B6; --teal-deep:#8FE0D4;
    --glow:rgba(232,181,99,0.18);
  }
  *{box-sizing:border-box; margin:0; padding:0;}
  body{background:var(--paper); color:var(--ink); font-family:'Inter',sans-serif; transition:background .4s, color .4s; overflow-x:hidden; position:relative;}
  .mono{font-family:'JetBrains Mono',monospace;}
  .display{font-family:'Space Grotesk',sans-serif;}
  a{color:inherit;}
  ::selection{background:var(--amber); color:var(--paper);}
  .glow-card{transition: transform .25s ease, box-shadow .25s ease, border-color .25s ease;}
  .glow-card:hover{transform: translateY(-3px); box-shadow: 0 8px 30px var(--glow); }
  .reveal{opacity:0; transform:translateY(16px); transition: opacity .6s ease, transform .6s ease;}
  .reveal.in{opacity:1; transform:translateY(0);}
  @keyframes floaty{
    0%{transform: translate(0,0) rotate(0deg);}
    50%{transform: translate(8px,-18px) rotate(8deg);}
    100%{transform: translate(0,0) rotate(0deg);}
  }
  @keyframes drift{
    0%{transform: translateY(0) translateX(0);}
    100%{transform: translateY(-30px) translateX(12px);}
  }
  .emoji-particle{
    position:fixed; pointer-events:none; z-index:0; user-select:none;
    filter: drop-shadow(0 6px 10px rgba(0,0,0,0.35));
    animation-name: floaty; animation-timing-function: ease-in-out; animation-iteration-count: infinite;
  }
</style>
</head>
<body>
<div id="root"></div>
<script type="text/babel">
const {useState, useEffect, useRef} = React;

const PROJECTS = [
  {id:1, title:"Project Alpha", tag:"web", blurb:"About this... ", stack:["React","Node","Postgres"]},
  {id:2, title:"Project Beta", tag:"design", blurb:"About this... ", stack:["Figma","CSS"]},
  {id:3, title:"Project Gamma", tag:"web", blurb:"About this... ", stack:["Next.js","Tailwind"]},
  {id:4, title:"Project Delta", tag:"data", blurb:"About this... ", stack:["Python","Pandas"]},
];

const SKILLS = ["JavaScript","React","Node.js","Python","SQL","Figma","Git","Tailwind","REST APIs","CSS"];

function useTypewriter(text, speed=45, startDelay=300){
  const [out, setOut] = useState("");
  useEffect(()=>{
    let i=0; let timer;
    const startTimer = setTimeout(function tick(){
      timer = setInterval(()=>{
        i++;
        setOut(text.slice(0,i));
        if(i>=text.length) clearInterval(timer);
      }, speed);
    }, startDelay);
    return ()=>{clearTimeout(startTimer); clearInterval(timer);};
  }, [text]);
  return out;
}

function useReveal(){
  const ref = useRef(null);
  useEffect(()=>{
    const el = ref.current;
    if(!el) return;
    const obs = new IntersectionObserver(([entry])=>{
      if(entry.isIntersecting){ el.classList.add("in"); obs.disconnect(); }
    }, {threshold:0.15});
    obs.observe(el);
    return ()=>obs.disconnect();
  }, []);
  return ref;
}

function FloatingEmojis(){
  const particles = [
    {emoji:"🚀", top:"8%", left:"6%", size:34, dur:"6s", delay:"0s", opacity:0.5},
    {emoji:"✨", top:"18%", left:"88%", size:26, dur:"5s", delay:"0.6s", opacity:0.6},
    {emoji:"💻", top:"55%", left:"3%", size:30, dur:"7s", delay:"1.2s", opacity:0.4},
    {emoji:"🎨", top:"72%", left:"92%", size:28, dur:"6.5s", delay:"0.3s", opacity:0.45},
    {emoji:"⚡", top:"38%", left:"95%", size:22, dur:"5.5s", delay:"1s", opacity:0.5},
    {emoji:"🌙", top:"85%", left:"12%", size:24, dur:"8s", delay:"0.8s", opacity:0.4},
  ];
  return (
    <div style={{position:"fixed", inset:0, overflow:"hidden", zIndex:0}} aria-hidden="true">
      {particles.map((p,i)=>(
        <span key={i} className="emoji-particle" style={{
          top:p.top, left:p.left, fontSize:p.size, opacity:p.opacity,
          animationDuration:p.dur, animationDelay:p.delay
        }}>{p.emoji}</span>
      ))}
    </div>
  );
}

function Nav({theme, toggleTheme}){
  const links = ["about","projects","skills","contact"];
  return (
    <nav style={{position:"sticky", top:0, zIndex:50, backdropFilter:"blur(10px)", background:"color-mix(in srgb, var(--paper) 82%, transparent)", borderBottom:"1px solid var(--line)"}}>
      <div style={{maxWidth:960, margin:"0 auto", padding:"14px 24px", display:"flex", justifyContent:"space-between", alignItems:"center"}}>
        <span className="mono" style={{fontSize:14, color:"var(--amber)"}}>~/portfolio</span>
        <div style={{display:"flex", gap:20, alignItems:"center"}}>
          {links.map(l=>(
            <a key={l} href={"#"+l} className="mono" style={{fontSize:13, color:"var(--sub)", textDecoration:"none"}}>./{l}</a>
          ))}
          <button onClick={toggleTheme} aria-label="Toggle theme" className="mono"
            style={{border:"1px solid var(--line)", background:"transparent", color:"var(--ink)", borderRadius:6, padding:"5px 10px", fontSize:12, cursor:"pointer"}}>
            {theme==="dark" ? "light" : "dark"}
          </button>
        </div>
      </div>
    </nav>
  );
}

function Hero(){
  const line1 = useTypewriter("whoami", 60, 300);
  const [showAnswer, setShowAnswer] = useState(false);
  useEffect(()=>{
    if(line1==="whoami"){
      const t = setTimeout(()=>setShowAnswer(true), 250);
      return ()=>clearTimeout(t);
    }
  }, [line1]);

  return (
    <header style={{maxWidth:960, margin:"0 auto", padding:"72px 24px 48px", position:"relative", zIndex:1}}>
      <div style={{background:"var(--card)", border:"1px solid var(--line)", borderRadius:10, padding:"20px 24px", boxShadow:"0 20px 60px var(--glow), 0 1px 0 var(--line)"}}>
        <div style={{display:"flex", gap:8, marginBottom:14}}>
          <span style={{width:10, height:10, borderRadius:"50%", background:"#E0736B"}}></span>
          <span style={{width:10, height:10, borderRadius:"50%", background:"#E0C46B"}}></span>
          <span style={{width:10, height:10, borderRadius:"50%", background:"#6BBE8A"}}></span>
        </div>
        <p className="mono" style={{fontSize:15, color:"var(--sub)"}}>
          <span style={{color:"var(--teal)"}}>Aditya's portfolio</span>:~$ {line1}
          <span style={{opacity: line1.length<6 ? 1:0}}>|</span>
        </p>
        {showAnswer && (
          <div style={{marginTop:14, animation:"fadein .4s ease"}}>
            <h1 className="display" style={{fontSize:38, fontWeight:700, lineHeight:1.15}}>Aditya Kumar Gautam</h1>
            <p className="mono" style={{fontSize:14, color:"var(--amber)", marginTop:6}}>Full-stack developer &amp; designer</p>
            <p style={{marginTop:14, color:"var(--sub)", fontSize:15, lineHeight:1.6, maxWidth:560}}>
               Currently exploring web development, programming, and modern technologies 
               while turning what I learn into real projects.
               I’m looking for opportunities to learn, collaborate,
               and grow as a developer....
            </p>
            <div style={{marginTop:20, display:"flex", gap:12}}>
              <a href="#projects" style={{background:"var(--amber)", color:"var(--paper)", padding:"10px 18px", borderRadius:8, textDecoration:"none", fontSize:14, fontWeight:600}}>View projects</a>
              <a href="#contact" style={{border:"1px solid var(--line)", padding:"10px 18px", borderRadius:8, textDecoration:"none", fontSize:14, fontWeight:600}}>Get in touch</a>
            </div>
          </div>
        )}
      </div>
      <style>{`@keyframes fadein{from{opacity:0; transform:translateY(4px)} to{opacity:1; transform:translateY(0)}}`}</style>
    </header>
  );
}

function Section({id, eyebrow, title, children}){
  const ref = useReveal();
  return (
    <section id={id} ref={ref} className="reveal" style={{maxWidth:960, margin:"0 auto", padding:"56px 24px", position:"relative", zIndex:1}}>
      <p className="mono" style={{fontSize:12, color:"var(--amber)", letterSpacing:1}}>{eyebrow}</p>
      <h2 className="display" style={{fontSize:26, fontWeight:700, marginTop:6, marginBottom:24}}>{title}</h2>
      {children}
    </section>
  );
}

function Skills(){
  return (
    <Section id="skills"  title="What I work with">
      <div style={{display:"flex", flexWrap:"wrap", gap:10}}>
        {SKILLS.map(s=>(
          <span key={s} className="mono" style={{border:"1px solid var(--line)", borderRadius:6, padding:"6px 12px", fontSize:13, color:"var(--sub)", background:"var(--card)"}}>{s}</span>
        ))}
      </div>
    </Section>
  );
}

function Projects(){
  const [filter, setFilter] = useState("all");
  const [openId, setOpenId] = useState(null);
  const tags = ["all", ...Array.from(new Set(PROJECTS.map(p=>p.tag)))];
  const visible = filter==="all" ? PROJECTS : PROJECTS.filter(p=>p.tag===filter);

  return (
    <Section id="projects"  title="Selected work">
      <div style={{display:"flex", gap:8, marginBottom:20}}>
        {tags.map(t=>(
          <button key={t} onClick={()=>setFilter(t)} className="mono"
            style={{
              fontSize:12, padding:"6px 12px", borderRadius:6, cursor:"pointer",
              border: filter===t ? "1px solid var(--amber)" : "1px solid var(--line)",
              background: filter===t ? "var(--amber)" : "transparent",
              color: filter===t ? "var(--paper)" : "var(--sub)"
            }}>{t}</button>
        ))}
      </div>
      <div style={{display:"grid", gridTemplateColumns:"repeat(auto-fit, minmax(260px, 1fr))", gap:16}}>
        {visible.map(p=>{
          const open = openId===p.id;
          return (
            <div key={p.id} onClick={()=>setOpenId(open?null:p.id)} className="glow-card"
              style={{
                background:"var(--card)", border:"1px solid var(--line)", borderRadius:10, padding:18,
                cursor:"pointer"
              }}
              onMouseEnter={e=>e.currentTarget.style.borderColor="var(--teal)"}
              onMouseLeave={e=>e.currentTarget.style.borderColor="var(--line)"}>
              <div style={{display:"flex", justifyContent:"space-between", alignItems:"baseline"}}>
                <h3 className="display" style={{fontSize:17, fontWeight:700}}>{p.title}</h3>
                <span className="mono" style={{fontSize:11, color:"var(--teal)"}}>{p.tag}</span>
              </div>
              <p style={{fontSize:13, color:"var(--sub)", marginTop:8, lineHeight:1.5}}>
                {open ? p.blurb : p.blurb.slice(0,54)+"…"}
              </p>
              {open && (
                <div style={{marginTop:12, display:"flex", flexWrap:"wrap", gap:6}}>
                  {p.stack.map(s=>(
                    <span key={s} className="mono" style={{fontSize:11, border:"1px solid var(--line)", borderRadius:4, padding:"3px 8px", color:"var(--sub)"}}>{s}</span>
                  ))}
                </div>
              )}
              <p className="mono" style={{fontSize:11, color:"var(--amber)", marginTop:10}}>{open ? "click to collapse" : "click to expand"}</p>
            </div>
          );
        })}
      </div>
    </Section>
  );
}

function Contact(){
  const [form, setForm] = useState({name:"", email:"", message:""});
  const [sent, setSent] = useState(false);
  const [errors, setErrors] = useState({});

 const handleSubmit = async (e)=>{
  e.preventDefault();

  const errs = {};

  if(!form.name.trim()) errs.name = "Enter your name";

  if(!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) {
    errs.email = "Enter a valid email";
  }

  if(!form.message.trim()) errs.message = "Say something";

  setErrors(errs);

  if(Object.keys(errs).length === 0){

    const response = await fetch("https://formspree.io/f/xeajgreq", {
      method: "POST",
      headers: {
        "Accept": "application/json",
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        name: form.name,
        email: form.email,
        message: form.message
      })
    });

    if(response.ok){
      setSent(true);
    } else {
      alert("Message could not be sent. Please try again.");
    }
  }
};
    e.preventDefault();
    const errs = {};
    if(!form.name.trim()) errs.name = "Enter your name";
    if(!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) errs.email = "Enter a valid email";
    if(!form.message.trim()) errs.message = "Say something";
    setErrors(errs);
    if(Object.keys(errs).length===0){
      setSent(true);
      // Connect to a real backend (e.g. Formspree, EmailJS) to actually deliver messages.
    }
  };

  const fieldStyle = {
    width:"100%", padding:"10px 12px", borderRadius:8, border:"1px solid var(--line)",
    background:"var(--paper)", color:"var(--ink)", fontSize:14, fontFamily:"inherit", marginTop:6
  };

  return (
    <Section id="contact"  title="Get in touch">
      <p style={{color:"var(--sub)", fontSize:14, marginBottom:18}}>
  Email:{" "}
  <a
    href="mailto:gautamaditya270@gmail.com"
    style={{color:"var(--amber)", textDecoration:"none"}}
  >
    gautamaditya270@gmail.com
  </a>
</p>
      {sent ? (
        <div style={{background:"var(--card)", border:"1px solid var(--teal)", borderRadius:10, padding:20}}>
          <p className="mono" style={{color:"var(--teal)", fontSize:14}}>Message queued.</p>
          <p style={{color:"var(--sub)", fontSize:13, marginTop:6}}>This form is a placeholder — connect it to Formspree, EmailJS, or your own backend to actually send messages.</p>
        </div>
      ) : (
        <form onSubmit={handleSubmit} style={{maxWidth:480}}>
          <label className="mono" style={{fontSize:12, color:"var(--sub)"}}>Name</label>
          <input style={fieldStyle} value={form.name} onChange={e=>setForm({...form, name:e.target.value})} placeholder="Ada Lovelace" />
          {errors.name && <p style={{color:"#C0554F", fontSize:12, marginTop:4}}>{errors.name}</p>}

          <label className="mono" style={{fontSize:12, color:"var(--sub)", display:"block", marginTop:14}}>Email</label>
          <input style={fieldStyle} value={form.email} onChange={e=>setForm({...form, email:e.target.value})} placeholder="ada@example.com" />
          {errors.email && <p style={{color:"#C0554F", fontSize:12, marginTop:4}}>{errors.email}</p>}

          <label className="mono" style={{fontSize:12, color:"var(--sub)", display:"block", marginTop:14}}>Message</label>
          <textarea style={{...fieldStyle, minHeight:100, resize:"vertical"}} value={form.message} onChange={e=>setForm({...form, message:e.target.value})} placeholder="What's on your mind?" />
          {errors.message && <p style={{color:"#C0554F", fontSize:12, marginTop:4}}>{errors.message}</p>}

          <button type="submit" style={{marginTop:16, background:"var(--amber)", color:"var(--paper)", border:"none", padding:"10px 20px", borderRadius:8, fontWeight:600, fontSize:14, cursor:"pointer"}}>
            Send message
          </button>
        </form>
      )}
    </Section>
  );
}

function Footer(){
  return (
    <footer style={{borderTop:"1px solid var(--line)", padding:"24px", textAlign:"center", position:"relative", zIndex:1, background:"var(--paper)"}}>
      <p className="mono" style={{fontSize:12, color:"var(--sub)"}}>Built by Aditya · @2026  </p>
    </footer>
  );
}

function App(){
  const [theme, setTheme] = useState("dark");
  useEffect(()=>{
    document.documentElement.setAttribute("data-theme", theme);
  }, [theme]);

  return (
    <React.Fragment>
      <FloatingEmojis />
      <Nav theme={theme} toggleTheme={()=>setTheme(t=>t==="dark"?"light":"dark")} />
      <Hero />
      <About/>
      <Skills />
      <Projects />
      <Contact />
      <Footer />
    </React.Fragment>
  );
}

function About(){
  return (
    <Section id="about" title="A little more about me">
      <p style={{color:"var(--sub)", fontSize:15, lineHeight:1.7, maxWidth:600}}>
        Student and aspiring developer who enjoys turning ideas into clean, interactive, 
        and meaningful digital experiences. I love experimenting with code, exploring
        new technologies, and bringing creative concepts to life through development. 
        Currently focused on strengthening my skills in HTML, CSS, C, and JavaScript
         while building projects that reflect my curiosity, creativity, and growth.
      </p>
    </Section>
  );
}

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<App />);
</script>
</body>
</html>
