# Ethical-Hacking
import React, { useEffect, useMemo, useState } from "react"; import { motion } from "framer-motion"; import { CheckCircle, Shield, Search, Filter, BookOpen, Target, ChevronRight, Github, ExternalLink, Sparkles, Layers } from "lucide-react";

/**

Hacker Skills Hub – single-file React site

Focus: legal, ethical, defensive cybersecurity learning.

Styling: Tailwind CSS (no extra setup needed in this preview).

Data is embedded; progress is saved to localStorage. */


const DOMAINS = [ { key: "fundamentals", label: "Fundamentals" }, { key: "web", label: "Web Security" }, { key: "network", label: "Network" }, { key: "osint", label: "OSINT" }, { key: "forensics", label: "Forensics" }, { key: "reverse", label: "Reverse" }, { key: "crypto", label: "Cryptography" }, { key: "cloud", label: "Cloud" }, { key: "blueteam", label: "Blue Team" }, ];

const LEVELS = ["Beginner", "Intermediate", "Advanced"];

const SKILLS = [ { id: "linux-basics", title: "Linux & CLI Basics", level: "Beginner", domain: "fundamentals", summary: "Navigating filesystems, permissions, processes, bash scripting—core skills for any security work.", objectives: [ "Use ls, cd, grep, find, sed, awk with confidence", "Manage users/groups and file permissions", "Write small bash scripts and cron jobs", ], safeLabs: [ { name: "OverTheWire: Bandit", url: "https://overthewire.org/wargames/bandit/" }, ], }, { id: "networking", title: "Networking Essentials", level: "Beginner", domain: "network", summary: "OSI/TCP-IP models, subnets, routing, DNS, HTTP—know how packets move.", objectives: [ "Explain OSI vs TCP/IP and key protocols", "Analyze traffic with tcpdump/Wireshark", "Map networks safely in a lab with nmap", ], safeLabs: [ { name: "Wireshark sample captures", url: "https://wiki.wireshark.org/SampleCaptures" }, ], }, { id: "web-owasp", title: "OWASP Top 10 (Defensive)", level: "Intermediate", domain: "web", summary: "Understand and mitigate common web risks: injection, auth flaws, XSS, SSRF, deserialization, etc.", objectives: [ "Model threats and abuse cases", "Write secure input validation & output encoding", "Harden auth/session and implement rate limits", ], safeLabs: [ { name: "OWASP Juice Shop (legal lab)", url: "https://owasp.org/www-project-juice-shop/" }, { name: "WebGoat", url: "https://owasp.org/www-project-webgoat/" }, ], }, { id: "osint-core", title: "OSINT Core", level: "Intermediate", domain: "osint", summary: "Ethical information gathering: search operators, metadata, mapping, and pivoting strategies.", objectives: [ "Use dorks and advanced queries", "Extract and sanitize metadata", "Correlate identities across platforms ethically", ], safeLabs: [ { name: "Maltego CE", url: "https://www.maltego.com/downloads/" }, { name: "theHarvester (docs)", url: "https://github.com/laramies/theHarvester" }, ], }, { id: "forensics-basics", title: "Forensics Basics", level: "Intermediate", domain: "forensics", summary: "Filesystems, memory acquisition, triage, and chain of custody—blue team essentials.", objectives: [ "Perform minimal-touch evidence collection", "Analyze logs and timelines", "Use Autopsy/Volatility in a lab", ], safeLabs: [ { name: "Volatility Foundation", url: "https://www.volatilityfoundation.org/releases" }, { name: "Autopsy", url: "https://www.autopsy.com/download/" }, ], }, { id: "reverse-intro", title: "Intro to Reverse Engineering", level: "Advanced", domain: "reverse", summary: "Disassemble, debug, and analyze binaries in a lawful environment (CTFs, owned VMs).", objectives: [ "Read assembly basics", "Use Ghidra/radare2 for static analysis", "Debug with x64dbg/gdb", ], safeLabs: [ { name: "Ghidra", url: "https://ghidra-sre.org/" }, { name: "radare2", url: "https://rada.re/n/" }, ], }, { id: "crypto-primer", title: "Applied Cryptography Primer", level: "Beginner", domain: "crypto", summary: "Hashes, MACs, symmetric/asymmetric crypto, TLS basics, key management.", objectives: [ "Pick correct primitives and modes", "Avoid home‑rolled crypto", "Use libsodium/openssl safely", ], safeLabs: [ { name: "CryptoPals", url: "https://cryptopals.com/" }, ], }, { id: "cloud-sec", title: "Cloud Security Fundamentals", level: "Intermediate", domain: "cloud", summary: "Shared responsibility, IAM least privilege, secrets, logging, and posture management.", objectives: [ "Design IAM with least privilege", "Set up logging/alerts", "Use IaC scanning (tfsec, Checkov)", ], safeLabs: [ { name: "tfsec", url: "https://aquasecurity.github.io/tfsec/" }, { name: "Checkov", url: "https://www.checkov.io/" }, ], }, { id: "blueteam-soc", title: "Blue Team & SOC", level: "Intermediate", domain: "blueteam", summary: "Detection engineering, SIEM basics, incident response, and threat hunting.", objectives: [ "Write basic detection rules", "Build timelines and hypotheses", "Practice IR playbooks in a lab", ], safeLabs: [ { name: "Sigma Rules", url: "https://sigmahq.io/" }, { name: "Security Onion", url: "https://securityonion.net/" }, ], }, ];

const ROADMAP = [ { step: 1, title: "Ethics & Legal", detail: "Understand lawful scope: only test systems you own or have written permission to test. Document consent.", }, { step: 2, title: "Computing Basics", detail: "Linux, networking, programming (Python/JS), Git, regex." }, { step: 3, title: "Web & Network", detail: "HTTP, DNS, TLS, nmap, Wireshark, secure coding." }, { step: 4, title: "Specialize", detail: "Pick tracks: WebSec, OSINT, Forensics, Cloud, Blue Team, Reverse." }, { step: 5, title: "Projects & Labs", detail: "Build a homelab, do CTFs, write reports, contribute to open-source." }, { step: 6, title: "Certs & Portfolio", detail: "eJPT/PNPT/OSCP (optional), blog, GitHub, write-ups, responsible disclosure." }, ];

// Local storage helpers const STORAGE_KEY = "hacker-skills-progress-v1"; const getInitialProgress = () => { try { const raw = localStorage.getItem(STORAGE_KEY); return raw ? JSON.parse(raw) : {}; } catch (e) { return {}; } };

export default function HackerSkillsHub() { const [q, setQ] = useState(""); const [domain, setDomain] = useState("all"); const [level, setLevel] = useState("all"); const [progress, setProgress] = useState(getInitialProgress());

useEffect(() => { localStorage.setItem(STORAGE_KEY, JSON.stringify(progress)); }, [progress]);

const filtered = useMemo(() => { return SKILLS.filter((s) => (domain === "all" || s.domain === domain) && (level === "all" || s.level === level) && (q.trim() === "" || (s.title + s.summary + s.objectives.join(" ")).toLowerCase().includes(q.toLowerCase())) ); }, [q, domain, level]);

const toggle = (id) => { setProgress((p) => ({ ...p, [id]: !p[id] })); };

const completion = Math.round((Object.values(progress).filter(Boolean).length / SKILLS.length) * 100);

return ( <div className="min-h-screen bg-slate-950 text-slate-100"> <header className="sticky top-0 z-40 backdrop-blur supports-[backdrop-filter]:bg-slate-950/70 bg-slate-950/80 border-b border-slate-800"> <div className="max-w-7xl mx-auto px-4 py-3 flex items-center gap-4"> <Sparkles className="w-6 h-6 text-indigo-400" /> <h1 className="text-lg md:text-xl font-semibold tracking-tight">Hacker Skills Hub</h1> <span className="ml-auto hidden md:flex items-center text-xs text-slate-300 gap-2"><Shield className="w-4 h-4"/> Ethical • Defensive • Legal only</span> </div> </header>

<main className="max-w-7xl mx-auto px-4 py-8 space-y-10">
    {/* Hero */}
    <section className="grid md:grid-cols-2 gap-6 items-center">
      <motion.div initial={{ opacity: 0, y: 8 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.4 }}>
        <h2 className="text-3xl md:text-4xl font-bold leading-tight">
          Kuasai skill keamanan siber <span className="text-indigo-400">dengan aman & legal</span>
        </h2>
        <p className="mt-3 text-slate-300">
          Website ini menyajikan roadmap, modul skill, dan lab yang <strong>aman untuk dipraktikkan</strong> di lingkungan uji milik sendiri.
          Tidak ada panduan menyusup ke sistem tanpa izin.
        </p>
        <div className="mt-5 flex flex-wrap gap-3">
          <a className="inline-flex items-center gap-2 px-4 py-2 rounded-2xl bg-indigo-600 hover:bg-indigo-500 transition shadow-lg shadow-indigo-800/20" href="#skills">
            <Target className="w-4 h-4" /> Mulai belajar
          </a>
          <a className="inline-flex items-center gap-2 px-4 py-2 rounded-2xl bg-slate-800 hover:bg-slate-700 transition" href="#roadmap">
            <Layers className="w-4 h-4"/> Lihat roadmap
          </a>
        </div>
      </motion.div>
      <motion.div initial={{ opacity: 0, y: 8 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.5 }} className="p-5 rounded-3xl bg-gradient-to-br from-slate-900 to-slate-800 border border-slate-800 shadow-xl">
        <div className="text-sm text-slate-300">Progress kamu</div>
        <div className="mt-2 w-full bg-slate-900/60 rounded-xl h-3 overflow-hidden border border-slate-700">
          <div className="h-3 bg-indigo-500" style={{ width: `${completion}%` }} />
        </div>
        <div className="mt-2 text-2xl font-semibold">{completion}% selesai</div>
        <p className="mt-1 text-slate-400 text-sm">Centang modul yang telah kamu kuasai. Tersimpan otomatis di browser.</p>
      </motion.div>
    </section>

    {/* Filters */}
    <section className="rounded-3xl border border-slate-800 bg-slate-900/40 p-4">
      <div className="flex flex-wrap gap-3 items-center">
        <div className="relative flex-1 min-w-[220px]">
          <Search className="w-4 h-4 absolute left-3 top-1/2 -translate-y-1/2 text-slate-400"/>
          <input
            value={q}
            onChange={(e) => setQ(e.target.value)}
            placeholder="Cari skill, contoh: OSINT, Wireshark, OWASP..."
            className="w-full pl-10 pr-3 py-2 rounded-xl bg-slate-950 border border-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500"
          />
        </div>
        <div className="flex items-center gap-2">
          <Filter className="w-4 h-4 text-slate-400"/>
          <select value={domain} onChange={(e) => setDomain(e.target.value)} className="px-3 py-2 rounded-xl bg-slate-950 border border-slate-800">
            <option value="all">Semua Domain</option>
            {DOMAINS.map(d => <option key={d.key} value={d.key}>{d.label}</option>)}
          </select>
          <select value={level} onChange={(e) => setLevel(e.target.value)} className="px-3 py-2 rounded-xl bg-slate-950 border border-slate-800">
            <option value="all">Semua Level</option>
            {LEVELS.map(l => <option key={l} value={l}>{l}</option>)}
          </select>
        </div>
      </div>
    </section>

    {/* Skills Grid */}
    <section id="skills" className="space-y-4">
      <div className="flex items-center gap-2">
        <BookOpen className="w-5 h-5 text-indigo-400"/>
        <h3 className="text-xl font-semibold">Modul Skill</h3>
      </div>

      <div className="grid sm:grid-cols-2 lg:grid-cols-3 gap-4">
        {filtered.map((s) => (
          <motion.article
            key={s.id}
            initial={{ opacity: 0, y: 8 }}
            animate={{ opacity: 1, y: 0 }}
            transition={{ duration: 0.25 }}
            className="group rounded-3xl border border-slate-800 bg-slate-900/40 p-5 hover:border-indigo-600/60 hover:bg-slate-900/60 transition"
          >
            <div className="flex items-start gap-3">
              <input
                type="checkbox"
                checked={!!progress[s.id]}
                onChange={() => toggle(s.id)}
                className="mt-1 w-5 h-5 rounded-md border-slate-700 bg-slate-950 text-indigo-600 focus:ring-indigo-500"
                aria-label={`Tandai ${s.title} selesai`}
              />
              <div>
                <h4 className="text-lg font-semibold leading-snug">{s.title}</h4>
                <div className="text-xs text-slate-400 mt-1">{s.level} • {DOMAINS.find(d=>d.key===s.domain)?.label}</div>
              </div>
            </div>
            <p className="mt-3 text-slate-300 text-sm">{s.summary}</p>
            <ul className="mt-3 space-y-1 text-sm text-slate-300 list-disc list-inside">
              {s.objectives.map((o, i) => (
                <li key={i}>{o}</li>
              ))}
            </ul>
            <div className="mt-4 flex flex-wrap gap-2">
              {s.safeLabs.map((lab, i) => (
                <a key={i} href={lab.url} target="_blank" rel="noreferrer" className="inline-flex items-center gap-1 text-xs px-2 py-1 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700">
                  <ExternalLink className="w-3 h-3"/> {lab.name}
                </a>
              ))}
            </div>
          </motion.article>
        ))}
      </div>

      {filtered.length === 0 && (
        <div className="text-sm text-slate-400">Tidak ada hasil. Coba ubah filter/pencarian.</div>
      )}
    </section>

    {/* Roadmap */}
    <section id="roadmap" className="space-y-4">
      <div className="flex items-center gap-2">
        <Target className="w-5 h-5 text-indigo-400"/>
        <h3 className="text-xl font-semibold">Roadmap Pembelajaran</h3>
      </div>
      <ol className="relative border-s border-slate-800 pl-6 space-y-5">
        {ROADMAP.map((r) => (
          <li key={r.step} className="relative">
            <div className="absolute -left-3 top-1.5 w-6 h-6 rounded-full bg-indigo-600 ring-2 ring-slate-900 flex items-center justify-center text-xs font-bold">{r.step}</div>
            <div className="rounded-2xl p-4 bg-slate-900/40 border border-slate-800">
              <div className="font-semibold">{r.title}</div>
              <div className="text-sm text-slate-300 mt-1">{r.detail}</div>
            </div>
          </li>
        ))}
      </ol>
    </section>

    {/* Ethics Card */}
    <section className="rounded-3xl border border-slate-800 bg-slate-900/50 p-5">
      <div className="flex items-start gap-3">
        <Shield className="w-6 h-6 text-emerald-400"/>
        <div>
          <h3 className="text-lg font-semibold">Etika & Legal</h3>
          <p className="text-slate-300 text-sm mt-1">
            Belajar keamanan siber harus <strong>bertanggung jawab</strong>. Uji hanya di lab milik sendiri atau dengan <em>izin tertulis</em>. Hindari akses tanpa otorisasi—itu melanggar hukum. Fokus pada pertahanan, audit, dan perbaikan.
          </p>
        </div>
      </div>
    </section>

    {/* Resources */}
    <section className="space-y-3">
      <div className="flex items-center gap-2">
        <BookOpen className="w-5 h-5 text-indigo-400"/>
        <h3 className="text-xl font-semibold">Sumber Belajar Aman</h3>
      </div>
      <div className="grid md:grid-cols-2 gap-3">
        <ResourceLink title="OWASP" href="https://owasp.org/"/>
        <ResourceLink title="TryHackMe (legal labs)" href="https://tryhackme.com/"/>
        <ResourceLink title="Hack The Box Academy" href="https://academy.hackthebox.com/"/>
        <ResourceLink title="OverTheWire" href="https://overthewire.org/"/>
        <ResourceLink title="MITRE ATT&CK" href="https://attack.mitre.org/"/>
        <ResourceLink title="NIST Cybersecurity Framework" href="https://www.nist.gov/cyberframework"/>
      </div>
    </section>
  </main>

  <footer className="max-w-7xl mx-auto px-4 py-10 text-sm text-slate-400">
    <div className="flex flex-wrap items-center gap-3">
      <CheckCircle className="w-4 h-4"/> Dibuat untuk tujuan edukasi & pertahanan.
      <span className="ml-auto flex items-center gap-2"><Github className="w-4 h-4"/> Kembangkan versi kamu sendiri.</span>
    </div>
  </footer>
</div>

); }

function ResourceLink({ title, href }) { return ( <a target="_blank" rel="noreferrer" href={href} className="flex items-center justify-between rounded-2xl border border-slate-800 bg-slate-900/40 px-4 py-3 hover:bg-slate-900/60"> <span>{title}</span> <ChevronRight className="w-4 h-4"/> </a> ); }

