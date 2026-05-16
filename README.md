# Skills

[![skills.sh](https://skills.sh/b/basantpandey/skills)](https://skills.sh/basantpandey/skills/quickui)

A collection of custom Claude Code skills for accelerating development workflows.

## Available Skills

### `/linkedin` — LinkedIn Content Kit

Generate a full LinkedIn content kit from a single topic or story. Produces 5 pieces in one shot and opens a LinkedIn-branded HTML preview with copy buttons.

**Usage:**
```
/linkedin "I just shipped a feature that reduced API latency by 40%"
/linkedin "I got promoted after 3 years of being overlooked" [tone: raw] [audience: mid-career engineers]
```

**Optional tags:**
- `[tone: raw | bold | mentor | story]` — writing voice (default: confident practitioner)
- `[audience: x]` — target reader (default: professionals in the field)

**What it generates:**

**1. Hook Trio** — 3 opening lines using different angles: curiosity gap, bold claim, story opener

**2. Full Post** — Complete ~250-word LinkedIn post, plain text formatted (no markdown, no tables — LinkedIn doesn't render them)

**3. Carousel Script** — 6-slide script for a PDF carousel upload, numbered slides with bold title + body text per slide

**4. Engagement Question** — One comment-bait question to pin as your first reply and drive engagement

**5. Hashtag Pack** — 5 niche + 3 broad hashtags, labeled by specificity

**Output:**
- Saves to `./linkedin-posts/<timestamp>/post.html`
- Opens a LinkedIn-styled browser preview with one-click Copy buttons per section

---

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

![QuickUI gallery showing 3 design variations — Clean & Minimal, Bold & Vibrant, Dark & Sophisticated](assets/quickui-preview.png)

## Installation

### Using `npx skills` (recommended)

Install any skill from this repo with a single command:

```bash
# Install the LinkedIn content kit skill
npx skills add https://github.com/basantpandey/skills --skill linkedin

# Install the QuickUI design variations skill
npx skills add https://github.com/basantpandey/skills --skill quickui
```

This installs the skill into your Claude Code skills directory automatically.

### Manual installation

Create a junction/symlink from your Claude skills directory to the skill folder:

**Windows:**
```powershell
# LinkedIn skill
cmd /c mklink /J "$env:USERPROFILE\.claude\skills\linkedin" "C:\path\to\skills\linkedin"

# QuickUI skill
cmd /c mklink /J "$env:USERPROFILE\.claude\skills\quickui" "C:\path\to\skills\quickui"
```

**macOS / Linux:**
```bash
# LinkedIn skill
ln -s /path/to/skills/linkedin ~/.claude/skills/linkedin

# QuickUI skill
ln -s /path/to/skills/quickui ~/.claude/skills/quickui
```
