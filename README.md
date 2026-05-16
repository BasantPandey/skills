# Skills

[![skills.sh](https://skills.sh/b/basantpandey/skills)](https://skills.sh/basantpandey/skills/quickui)

A collection of custom Claude Code skills for accelerating development workflows.

## Available Skills

### `/quickui` — Quick UI Design Variations

Generate 3 distinct HTML/CSS UI design variations from a plain English description. Useful for rapid POC design exploration without any setup.

**Usage:**
```
/quickui "a login page for a fintech app"
/quickui "sales dashboard with charts and a data table"
/quickui "e-commerce product listing page"
```

**What it generates:**

| Variation | Typography | Style |
|---|---|---|
| Clean & Minimal | Inter | White background, indigo accent, generous whitespace |
| Bold & Vibrant | Poppins | Gradient header, orange/pink palette, energetic layout |
| Dark & Sophisticated | Sora | Dark background, violet glow accent, glassmorphism cards |

Each design varies across: Typography · Color & Theme · Motion · Spatial Composition · Backgrounds & Visual Details.

**Output:**
- Saves 3 HTML files + a gallery to `./ui-designs/<timestamp>/`
- Opens a gallery page in your browser with inline iframe previews
- Click any design to open it fullscreen

## Installation

Each skill in `skills/` is a standalone Claude Code skill. To install, create a junction/symlink from your Claude skills directory to the skill folder:

**Windows:**
```powershell
cmd /c mklink /J "$env:USERPROFILE\.claude\skills\quickui" "C:\AIProjects\ProjectSkills\skills\quickui"
```

**macOS / Linux:**
```bash
ln -s /path/to/ProjectSkills/skills/quickui ~/.claude/skills/quickui
```
