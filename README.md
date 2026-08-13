import React, { useState, useMemo, useEffect } from "react";
import {
  Shield,
  Truck,
  Lock,
  Video,
  MapPin,
  DoorOpen,
  DoorClosed,
  Car,
  ShieldAlert,
  ChevronDown,
  Check,
  ArrowRight,
  CheckCircle2,
  Gauge,
  Phone,
  Star,
} from "lucide-react";

/* ------------------------------------------------------------------ */
/* VanSecure brand tokens                                              */
/*  bg: #FFFFFF   panel: #F4F6F9   panel-2: #EAEEF3  border: #DCE3EC   */
/*  navy (primary): #0F2C54   blue (accent): #1F5FA8                   */
/*  gold (Sold Secure GOLD tier / CTA): #C79A3D                        */
/*  text: #14213D   text-dim: #5B6B82   success: #1F8A5C                */
/* ------------------------------------------------------------------ */

const FONT_STYLE = `
  @import url('https://fonts.googleapis.com/css2?family=Oswald:wght@500;600;700&family=Inter:wght@400;500;600&family=IBM+Plex+Mono:wght@400;500;600&display=swap');
  .axm-display { font-family: 'Oswald', sans-serif; }
  .axm-body { font-family: 'Inter', sans-serif; }
  .axm-mono { font-family: 'IBM Plex Mono', monospace; }
`;

/* ------------------------------------------------------------------ */
/* Data: hardware catalogue                                            */
/* ------------------------------------------------------------------ */

const SECTIONS = [
  {
    id: "ns_door",
    title: "NS Sliding Door",
    sub: "Nearside",
    icon: DoorOpen,
    type: "multi",
    groups: [
      {
        label: "Deadlocks",
        options: [
          { id: "ns_dl_gold_high", name: "Sold Secure GOLD Hook (High)", weight: 10 },
          { id: "ns_dl_gold_low", name: "Sold Secure GOLD Hook (Low)", weight: 10 },
          { id: "ns_dl_stronghold", name: "STRONGHOLD Reinforced Hook (High)", weight: 10 },
        ],
      },
      {
        label: "Shields",
        options: [
          { id: "ns_sh_silver", name: "External Latch (Silver)", weight: 6 },
          { id: "ns_sh_black", name: "External Latch (Black)", weight: 6 },
          { id: "ns_sh_painted", name: "External Latch (Painted)", weight: 6 },
          { id: "ns_sh_internal", name: "Internal Latch Shield", weight: 6 },
        ],
      },
      {
        label: "Extras",
        options: [{ id: "ns_ex_antipeel", name: "Anti Peel Bracket", weight: 6 }],
      },
    ],
  },
  {
    id: "os_door",
    title: "OS Sliding Door",
    sub: "Offside",
    icon: DoorOpen,
    type: "multi",
    groups: [
      {
        label: "Deadlocks",
        options: [
          { id: "os_dl_gold_high", name: "Sold Secure GOLD Hook (High)", weight: 10 },
          { id: "os_dl_gold_low", name: "Sold Secure GOLD Hook (Low)", weight: 10 },
          { id: "os_dl_stronghold", name: "STRONGHOLD Reinforced Hook (High)", weight: 10 },
        ],
      },
      {
        label: "Shields",
        options: [
          { id: "os_sh_silver", name: "External Latch (Silver)", weight: 6 },
          { id: "os_sh_black", name: "External Latch (Black)", weight: 6 },
          { id: "os_sh_painted", name: "External Latch (Painted)", weight: 6 },
          { id: "os_sh_internal", name: "Internal Latch Shield", weight: 6 },
        ],
      },
      {
        label: "Extras",
        options: [{ id: "os_ex_antipeel", name: "Anti Peel Bracket", weight: 6 }],
      },
    ],
  },
  {
    id: "rear_doors",
    title: "Rear Barn Doors",
    sub: "Load compartment",
    icon: DoorClosed,
    type: "multi",
    groups: [
      {
        label: "Deadlocks",
        options: [
          { id: "rd_dl_gold_high", name: "Sold Secure GOLD Hook (High)", weight: 10 },
          { id: "rd_dl_gold_low", name: "Sold Secure GOLD Hook (Low)", weight: 10 },
          { id: "rd_dl_stronghold", name: "STRONGHOLD Reinforced Hook (High)", weight: 10 },
        ],
      },
      {
        label: "Shields",
        options: [
          { id: "rd_sh_silver", name: "External Latch (Silver)", weight: 6 },
          { id: "rd_sh_black", name: "External Latch (Black)", weight: 6 },
          { id: "rd_sh_painted", name: "External Latch (Painted)", weight: 6 },
          { id: "rd_sh_internal", name: "Internal Latch Shield", weight: 6 },
        ],
      },
    ],
  },
  {
    id: "drivers_cab",
    title: "Driver's Cab Door",
    sub: "Front, nearside/offside",
    icon: Car,
    type: "multi",
    groups: [
      {
        label: "Deadlocks",
        options: [
          { id: "dc_dl_straight", name: "Sold Secure GOLD Straight Deadlock", weight: 10 },
          { id: "dc_dl_hook", name: "Sold Secure GOLD Hook Deadlock", weight: 10 },
        ],
      },
      {
        label: "Extras",
        options: [
          { id: "dc_ex_loomguard", name: "Loom Guard", weight: 5 },
          { id: "dc_ex_replock", name: "Replock (Drivers side only)", weight: 8 },
        ],
      },
    ],
  },
  {
    id: "passenger_cab",
    title: "Passenger Cab Door",
    sub: "Front",
    icon: Car,
    type: "multi",
    groups: [
      {
        label: "Deadlocks",
        options: [
          { id: "pc_dl_straight", name: "Sold Secure GOLD Straight Deadlock", weight: 10 },
          { id: "pc_dl_hook", name: "Sold Secure GOLD Hook Deadlock", weight: 10 },
        ],
      },
      {
        label: "Extras",
        options: [{ id: "pc_ex_loomguard", name: "Loom Guard", weight: 5 }],
      },
    ],
  },
  {
    id: "tailgate",
    title: "Tailgate",
    sub: "Rear lift-up access",
    icon: Lock,
    type: "multi",
    groups: [
      {
        label: "Deadlocks",
        options: [
          { id: "tg_dl_pair", name: "Sold Secure GOLD Approved Hook Deadlocks x2", weight: 14 },
        ],
      },
    ],
  },
  {
    id: "dashcams",
    title: "Dash Cams",
    sub: "Choose one",
    icon: Video,
    type: "single",
    groups: [
      {
        label: "Dash Cams",
        options: [
          { id: "dcam_ra_front", name: "Road Angel — Front Only", weight: 10 },
          { id: "dcam_ra_fr", name: "Road Angel — Front and Rear", weight: 14 },
          { id: "dcam_vw_front", name: "VehicleWatch — Front + Subscription", weight: 10 },
          { id: "dcam_vw_fr", name: "VehicleWatch — Front + Rear + Subscription", weight: 14 },
        ],
      },
    ],
  },
  {
    id: "immobiliser",
    title: "Immobiliser",
    sub: "Choose one",
    icon: ShieldAlert,
    type: "single",
    groups: [
      {
        label: "Immobiliser",
        options: [{ id: "immo_scorpion", name: "Scorpion X", weight: 22 }],
      },
    ],
  },
  {
    id: "trackers",
    title: "Trackers",
    sub: "Choose one",
    icon: MapPin,
    type: "single",
    groups: [
      {
        label: "Trackers",
        options: [
          { id: "trk_vehicle", name: "Vehicle Only", weight: 12 },
          { id: "trk_v5", name: "Vehicle + 5 Tool Locators", weight: 18 },
          { id: "trk_v10", name: "Vehicle + 10 Tool Locators", weight: 24 },
        ],
      },
    ],
  },
];

const ALL_OPTIONS = SECTIONS.flatMap((section) =>
  section.groups.flatMap((group) =>
    group.options.map((opt) => ({
      ...opt,
      sectionId: section.id,
      sectionTitle: section.title,
      sectionType: section.type,
      groupLabel: group.label,
    }))
  )
);

const OPTION_BY_ID = Object.fromEntries(ALL_OPTIONS.map((o) => [o.id, o]));

const MAX_POINTS = SECTIONS.reduce((total, section) => {
  const opts = section.groups.flatMap((g) => g.options);
  if (section.type === "multi") {
    return total + opts.reduce((s, o) => s + o.weight, 0);
  }
  return total + Math.max(...opts.map((o) => o.weight));
}, 0);

const VEHICLE_SPECS = [
  { id: "highRoof", label: "High roof van" },
  { id: "existingLocks", label: "Existing aftermarket locks or plates fitted" },
  { id: "hasWindows", label: "Windows fitted in side or back doors" },
  { id: "hasRacking", label: "Racking or equipment fixed inside the sliding door" },
];

/* ------------------------------------------------------------------ */
/* Coverage gauge                                                       */
/* ------------------------------------------------------------------ */

function polarPoint(cx, cy, r, angleDeg) {
  const rad = (angleDeg * Math.PI) / 180;
  return { x: cx + r * Math.cos(rad), y: cy - r * Math.sin(rad) };
}

function CoverageGauge({ score }) {
  const cx = 110;
  const cy = 108;
  const r = 88;
  const strokeWidth = 14;

  const angleForPercent = (p) => 180 - (p / 100) * 180;
  const circumference = Math.PI * r;
  const dashOffset = circumference * (1 - score / 100);

  const ticks = [0, 25, 50, 75, 100];
  const needleAngle = angleForPercent(score);
  const needleTip = polarPoint(cx, cy, r - 22, needleAngle);

  const zoneColor = score < 35 ? "#C0483A" : score < 70 ? "#C79A3D" : "#1F8A5C";

  return (
    <div className="flex flex-col items-center">
      <svg viewBox="0 0 220 130" className="w-full max-w-[280px]">
        <path
          d={`M ${cx - r} ${cy} A ${r} ${r} 0 0 1 ${cx + r} ${cy}`}
          fill="none"
          stroke="#E2E7EE"
          strokeWidth={strokeWidth}
          strokeLinecap="round"
        />
        <path
          d={`M ${cx - r} ${cy} A ${r} ${r} 0 0 1 ${cx + r} ${cy}`}
          fill="none"
          stroke={zoneColor}
          strokeWidth={strokeWidth}
          strokeLinecap="round"
          strokeDasharray={circumference}
          strokeDashoffset={dashOffset}
          style={{ transition: "stroke-dashoffset 500ms ease, stroke 500ms ease" }}
        />
        {ticks.map((t) => {
          const a = angleForPercent(t);
          const inner = polarPoint(cx, cy, r - strokeWidth / 2 - 4, a);
          const outer = polarPoint(cx, cy, r + strokeWidth / 2 + 4, a);
          return (
            <line
              key={t}
              x1={inner.x}
              y1={inner.y}
              x2={outer.x}
              y2={outer.y}
              stroke="#B9C3D1"
              strokeWidth="2"
            />
          );
        })}
        <line
          x1={cx}
          y1={cy}
          x2={needleTip.x}
          y2={needleTip.y}
          stroke="#0F2C54"
          strokeWidth="2.5"
          strokeLinecap="round"
          style={{ transition: "all 500ms ease" }}
        />
        <circle cx={cx} cy={cy} r="5" fill="#0F2C54" />
      </svg>
      <div className="-mt-6 flex flex-col items-center">
        <span
          className="axm-mono text-4xl font-semibold tracking-tight"
          style={{ color: zoneColor }}
        >
          {score}
          <span className="text-lg align-top">%</span>
        </span>
        <span className="axm-mono text-[10px] tracking-[0.25em] text-[#5B6B82] mt-1">
          COVERAGE SCORE
        </span>
      </div>
    </div>
  );
}

/* ------------------------------------------------------------------ */
/* Small building blocks                                               */
/* ------------------------------------------------------------------ */

function OptionCard({ option, selected, onToggle, mode }) {
  return (
    <button
      type="button"
      onClick={() => onToggle(option)}
      className={`w-full text-left rounded-md border px-3 py-2.5 flex items-center gap-3 transition-all
        ${
          selected
            ? "bg-[#EAF1FA] border-[#1F5FA8] shadow-[0_0_0_1px_rgba(31,95,168,0.25)]"
            : "bg-white border-[#DCE3EC] hover:border-[#B9C3D1]"
        }`}
    >
      <span
        className={`flex-shrink-0 w-4 h-4 flex items-center justify-center border transition-colors
          ${mode === "single" ? "rounded-full" : "rounded"}
          ${selected ? "bg-[#1F5FA8] border-[#1F5FA8]" : "border-[#B9C3D1]"}`}
      >
        {selected && <Check size={11} strokeWidth={3} className="text-white" />}
      </span>
      <span
        className={`axm-body text-sm ${
          selected ? "text-[#0F2C54] font-medium" : "text-[#3B4A61]"
        }`}
      >
        {option.name}
      </span>
    </button>
  );
}

function AccordionSection({ section, isOpen, onOpenToggle, selectedOptions, onOptionToggle }) {
  const Icon = section.icon;
  const selectedCount = section.groups
    .flatMap((g) => g.options)
    .filter((o) => selectedOptions.has(o.id)).length;

  return (
    <div className="border border-[#DCE3EC] rounded-lg overflow-hidden bg-white shadow-[0_1px_2px_rgba(15,44,84,0.04)]">
      <button
        type="button"
        onClick={onOpenToggle}
        className="w-full flex items-center justify-between px-4 py-3.5 bg-[#F4F6F9] hover:bg-[#EAEEF3] transition-colors"
      >
        <div className="flex items-center gap-3">
          <span className="flex-shrink-0 w-8 h-8 rounded-md bg-white border border-[#DCE3EC] flex items-center justify-center text-[#1F5FA8]">
            <Icon size={16} />
          </span>
          <div className="text-left">
            <div className="axm-display text-[#0F2C54] text-[15px] tracking-wide uppercase">
              {section.title}
            </div>
            <div className="axm-mono text-[10px] text-[#5B6B82] tracking-wider">
              {section.sub}
            </div>
          </div>
        </div>
        <div className="flex items-center gap-3">
          {selectedCount > 0 && (
            <span className="axm-mono text-[11px] px-2 py-0.5 rounded-full bg-[#1F5FA8]/10 text-[#1F5FA8] border border-[#1F5FA8]/25">
              {selectedCount} selected
            </span>
          )}
          <ChevronDown
            size={16}
            className={`text-[#5B6B82] transition-transform ${isOpen ? "rotate-180" : ""}`}
          />
        </div>
      </button>

      {isOpen && (
        <div className="px-4 py-4 space-y-4">
          {section.groups.map((group) => (
            <div key={group.label}>
              {section.groups.length > 1 && (
                <div className="axm-mono text-[10px] tracking-[0.2em] text-[#1F5FA8] mb-2 uppercase">
                  {group.label}
                </div>
              )}
              <div className="grid grid-cols-1 sm:grid-cols-2 gap-2">
                {group.options.map((opt) => (
                  <OptionCard
                    key={opt.id}
                    option={opt}
                    mode={section.type}
                    selected={selectedOptions.has(opt.id)}
                    onToggle={(o) => onOptionToggle(o, section)}
                  />
                ))}
              </div>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}

/* ------------------------------------------------------------------ */
/* Main component                                                      */
/* ------------------------------------------------------------------ */

export default function VanSecurityConfigurator() {
  const [vehicleSpec, setVehicleSpec] = useState({
    highRoof: false,
    existingLocks: false,
    hasWindows: false,
    hasRacking: false,
  });

  const [selectedOptions, setSelectedOptions] = useState(new Set());
  const [openSection, setOpenSection] = useState(SECTIONS[0].id);
  const [otherWork, setOtherWork] = useState("");
  const [lead, setLead] = useState({ name: "", email: "", phone: "" });
  const [submitted, setSubmitted] = useState(false);

  useEffect(() => {
    if (typeof document === "undefined") return;
    const styleTag = document.createElement("style");
    styleTag.innerHTML = FONT_STYLE;
    document.head.appendChild(styleTag);
    return () => document.head.removeChild(styleTag);
  }, []);

  const handleOptionToggle = (option, section) => {
    setSelectedOptions((prev) => {
      const next = new Set(prev);
      if (section.type === "single") {
        section.groups
          .flatMap((g) => g.options)
          .forEach((o) => next.delete(o.id));
        if (!prev.has(option.id)) next.add(option.id);
      } else {
        if (next.has(option.id)) next.delete(option.id);
        else next.add(option.id);
      }
      return next;
    });
  };

  const score = useMemo(() => {
    const points = Array.from(selectedOptions).reduce(
      (sum, id) => sum + (OPTION_BY_ID[id]?.weight || 0),
      0
    );
    return Math.min(100, Math.round((points / MAX_POINTS) * 100));
  }, [selectedOptions]);

  const summaryBySection = useMemo(() => {
    const map = {};
    Array.from(selectedOptions).forEach((id) => {
      const opt = OPTION_BY_ID[id];
      if (!opt) return;
      if (!map[opt.sectionTitle]) map[opt.sectionTitle] = [];
      map[opt.sectionTitle].push(opt.name);
    });
    return map;
  }, [selectedOptions]);

  const hasAnySelection = selectedOptions.size > 0;
  const canSubmit = lead.name.trim() && lead.email.trim() && hasAnySelection;

  function handleSubmit(e) {
    e.preventDefault();
    if (!canSubmit) return;

    const payload = {
      vehicleSpec,
      selections: Array.from(selectedOptions).map((id) => {
        const o = OPTION_BY_ID[id];
        return { id: o.id, name: o.name, section: o.sectionTitle, weight: o.weight };
      }),
      otherWork,
      coverageScore: score,
      lead,
      submittedAt: new Date().toISOString(),
    };

    // eslint-disable-next-line no-console
    console.log("VanSecure configurator submission:", JSON.stringify(payload, null, 2));
    setSubmitted(true);

    // Hand off to WhatsApp with the build pre-filled, so the lead lands
    // in chat with full context instead of a blank enquiry.
    if (typeof window !== "undefined") {
      const lines = [
        `Hi VanSecure, I've just built a security spec online — ${score}% coverage.`,
        "",
        `Name: ${lead.name}`,
        lead.phone ? `Phone: ${lead.phone}` : null,
        lead.email ? `Email: ${lead.email}` : null,
        "",
        "Selected hardware:",
        ...Object.entries(summaryBySection).flatMap(([section, items]) => [
          `${section}:`,
          ...items.map((name) => `- ${name}`),
        ]),
        otherWork.trim() ? "" : null,
        otherWork.trim() ? `Other work: ${otherWork.trim()}` : null,
      ].filter(Boolean);

      const message = lines.join("\n");
      const whatsappUrl = `https://wa.me/+447858192831?text=${encodeURIComponent(message)}`;
      window.location.href = whatsappUrl;
    }
  }

  return (
    <div className="min-h-screen bg-white text-[#14213D] axm-body">
      {/* Header — mirrors vansecure.co.uk: white bar, navy wordmark, phone CTA */}
      <header className="border-b border-[#DCE3EC] bg-white">
        <div className="max-w-6xl mx-auto px-5 py-4 flex items-center gap-3">
          <div className="w-9 h-9 rounded-md bg-[#0F2C54] flex items-center justify-center text-white">
            <Shield size={18} />
          </div>
          <div>
            <div className="axm-display text-lg tracking-wide uppercase text-[#0F2C54]">
              VanSecure Configurator
            </div>
            <div className="axm-mono text-[11px] text-[#5B6B82] tracking-wide">
              Build your fit-out. Get a custom Price &amp; Risk Report.
            </div>
          </div>
          <div className="ml-auto hidden sm:flex items-center gap-4">
            <div className="flex items-center gap-1 text-[#C79A3D]">
              {Array.from({ length: 5 }).map((_, i) => (
                <Star key={i} size={13} fill="#C79A3D" strokeWidth={0} />
              ))}
              <span className="axm-mono text-[11px] text-[#5B6B82] ml-1">Google Reviews</span>
            </div>
            <div className="h-5 w-px bg-[#DCE3EC]" />
            <div className="flex items-center gap-1.5 text-[#0F2C54] font-medium text-sm">
              <Phone size={14} />
              020 4636 8841
            </div>
          </div>
        </div>
      </header>

      <main className="max-w-6xl mx-auto px-5 py-8 grid grid-cols-1 lg:grid-cols-[1fr_400px] gap-6">
        {/* ---------------- Left column: configurator ---------------- */}
        <div className="space-y-6">
          <section className="border border-[#DCE3EC] rounded-lg bg-white p-4">
            <h2 className="axm-display uppercase tracking-wide text-sm text-[#1F5FA8] mb-3">
              Van Details
            </h2>
            <div className="grid grid-cols-1 sm:grid-cols-2 gap-2">
              {VEHICLE_SPECS.map((spec) => (
                <button
                  key={spec.id}
                  type="button"
                  onClick={() =>
                    setVehicleSpec((prev) => ({ ...prev, [spec.id]: !prev[spec.id] }))
                  }
                  className={`text-left rounded-md border px-3 py-2.5 flex items-center gap-3 transition-all
                    ${
                      vehicleSpec[spec.id]
                        ? "bg-[#EAF1FA] border-[#1F5FA8]"
                        : "bg-[#F4F6F9] border-[#DCE3EC] hover:border-[#B9C3D1]"
                    }`}
                >
                  <span
                    className={`flex-shrink-0 w-4 h-4 rounded border flex items-center justify-center
                      ${
                        vehicleSpec[spec.id]
                          ? "bg-[#1F5FA8] border-[#1F5FA8]"
                          : "border-[#B9C3D1]"
                      }`}
                  >
                    {vehicleSpec[spec.id] && (
                      <Check size={11} strokeWidth={3} className="text-white" />
                    )}
                  </span>
                  <span className="text-sm text-[#3B4A61]">{spec.label}</span>
                </button>
              ))}
            </div>
          </section>

          <section>
            <h2 className="axm-display uppercase tracking-wide text-sm text-[#1F5FA8] mb-3">
              Security Hardware
            </h2>
            <div className="space-y-2.5">
              {SECTIONS.map((section) => (
                <AccordionSection
                  key={section.id}
                  section={section}
                  isOpen={openSection === section.id}
                  onOpenToggle={() =>
                    setOpenSection((prev) => (prev === section.id ? null : section.id))
                  }
                  selectedOptions={selectedOptions}
                  onOptionToggle={handleOptionToggle}
                />
              ))}
            </div>
          </section>

          <section className="border border-[#DCE3EC] rounded-lg bg-white p-4">
            <h2 className="axm-display uppercase tracking-wide text-sm text-[#1F5FA8] mb-3">
              Other Work Required
            </h2>
            <textarea
              value={otherWork}
              onChange={(e) => setOtherWork(e.target.value)}
              placeholder="Anything else you'd like fitted or assessed..."
              rows={4}
              className="w-full bg-[#F4F6F9] border border-[#DCE3EC] rounded-md px-3 py-2.5 text-sm text-[#14213D] placeholder-[#8593A8] focus:outline-none focus:border-[#1F5FA8] resize-none"
            />
          </section>
        </div>

        {/* ---------------- Right column: summary & lead form ---------------- */}
        <div className="lg:sticky lg:top-6 self-start space-y-4">
          {!submitted ? (
            <>
              <div className="border border-[#DCE3EC] rounded-lg bg-white p-5">
                <CoverageGauge score={score} />
              </div>

              <div className="border border-[#DCE3EC] rounded-lg bg-white p-4">
                <h3 className="axm-display uppercase tracking-wide text-sm text-[#1F5FA8] mb-3">
                  Build Summary
                </h3>
                {!hasAnySelection ? (
                  <p className="text-sm text-[#8593A8] axm-mono">
                    No hardware selected yet.
                  </p>
                ) : (
                  <div className="space-y-3 max-h-64 overflow-y-auto pr-1">
                    {Object.entries(summaryBySection).map(([title, items]) => (
                      <div key={title}>
                        <div className="axm-mono text-[10px] tracking-[0.2em] text-[#1F5FA8] uppercase mb-1">
                          {title}
                        </div>
                        <ul className="space-y-1">
                          {items.map((name) => (
                            <li
                              key={name}
                              className="text-[13px] text-[#3B4A61] flex items-start gap-2"
                            >
                              <span className="text-[#1F8A5C] mt-0.5">
                                <Check size={12} strokeWidth={3} />
                              </span>
                              {name}
                            </li>
                          ))}
                        </ul>
                      </div>
                    ))}
                  </div>
                )}
              </div>

              <form
                onSubmit={handleSubmit}
                className="border border-[#DCE3EC] rounded-lg bg-white p-4 space-y-3"
              >
                <h3 className="axm-display uppercase tracking-wide text-sm text-[#1F5FA8] mb-1">
                  Request Custom Report
                </h3>
                <div>
                  <label className="axm-mono text-[10px] tracking-wider text-[#5B6B82] uppercase">
                    Name
                  </label>
                  <input
                    type="text"
                    required
                    value={lead.name}
                    onChange={(e) => setLead((p) => ({ ...p, name: e.target.value }))}
                    className="w-full mt-1 bg-[#F4F6F9] border border-[#DCE3EC] rounded-md px-3 py-2 text-sm text-[#14213D] focus:outline-none focus:border-[#1F5FA8]"
                    placeholder="Your name"
                  />
                </div>
                <div>
                  <label className="axm-mono text-[10px] tracking-wider text-[#5B6B82] uppercase">
                    Email
                  </label>
                  <input
                    type="email"
                    required
                    value={lead.email}
                    onChange={(e) => setLead((p) => ({ ...p, email: e.target.value }))}
                    className="w-full mt-1 bg-[#F4F6F9] border border-[#DCE3EC] rounded-md px-3 py-2 text-sm text-[#14213D] focus:outline-none focus:border-[#1F5FA8]"
                    placeholder="you@example.com"
                  />
                </div>
                <div>
                  <label className="axm-mono text-[10px] tracking-wider text-[#5B6B82] uppercase">
                    Phone Number
                  </label>
                  <input
                    type="tel"
                    value={lead.phone}
                    onChange={(e) => setLead((p) => ({ ...p, phone: e.target.value }))}
                    className="w-full mt-1 bg-[#F4F6F9] border border-[#DCE3EC] rounded-md px-3 py-2 text-sm text-[#14213D] focus:outline-none focus:border-[#1F5FA8]"
                    placeholder="07000 000000"
                  />
                </div>

                <button
                  type="submit"
                  disabled={!canSubmit}
                  className={`w-full mt-2 rounded-md py-3 flex items-center justify-center gap-2 axm-display uppercase tracking-wide text-sm transition-all
                    ${
                      canSubmit
                        ? "bg-[#C79A3D] text-white hover:bg-[#B78A2F]"
                        : "bg-[#EAEEF3] text-[#8593A8] cursor-not-allowed"
                    }`}
                >
                  Send Build to WhatsApp
                  <ArrowRight size={16} />
                </button>
                {!hasAnySelection && (
                  <p className="axm-mono text-[10px] text-[#8593A8] text-center">
                    Select at least one item of hardware to continue.
                  </p>
                )}
              </form>
            </>
          ) : (
            <div className="border border-[#DCE3EC] rounded-lg bg-white p-8 flex flex-col items-center text-center">
              <div className="w-14 h-14 rounded-full bg-[#1F8A5C]/10 border border-[#1F8A5C]/30 flex items-center justify-center text-[#1F8A5C] mb-4">
                <CheckCircle2 size={28} />
              </div>
              <h3 className="axm-display uppercase tracking-wide text-lg mb-2 text-[#0F2C54]">
                Request Received
              </h3>
              <p className="text-sm text-[#3B4A61] mb-4">
                Thanks{lead.name ? `, ${lead.name}` : ""}. Your build is logged at a{" "}
                <span className="text-[#C79A3D] axm-mono font-semibold">{score}%</span> coverage score.
                Opening WhatsApp with your build pre-filled — send it across and the team will follow up with your custom Price &amp; Risk Report.
              </p>
              <div className="flex items-center gap-2 axm-mono text-[11px] text-[#1F5FA8]">
                <Gauge size={14} />
                Full build also logged to console
              </div>
            </div>
          )}
        </div>
      </main>
    </div>
  );
}
