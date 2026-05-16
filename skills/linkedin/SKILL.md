---
name: linkedin
description: Generate a full LinkedIn content kit (hook trio, full post, carousel script, engagement question, hashtag pack) from a topic or story. Opens a LinkedIn-branded HTML preview with one-click copy buttons per section. Trigger when user invokes /linkedin or asks to create LinkedIn content, a post kit, or a content strategy for LinkedIn.
---

Generate a complete LinkedIn content kit from the user's input. Follow every step below exactly.

## Step 1 — Parse the input

Extract from the user's message:
- **Topic / story**: the main subject (required)
- **[tone: x]** tag (optional): `raw`, `bold`, `mentor`, `story`. Default: `confident practitioner`
- **[audience: x]** tag (optional): e.g. `CTOs`, `mid-career engineers`, `founders`. Default: `professionals in the field`

Example inputs:
```
/linkedin "I just shipped a feature that reduced API latency by 40%"
/linkedin "I got promoted after 3 years of being overlooked" [tone: raw] [audience: mid-career engineers]
```

## Step 2 — Generate the 5-piece content kit

Use the parsed topic, tone, and audience to write all five pieces. Apply the tone voice consistently across all pieces.

### Tone voice guide

**confident practitioner** (default): Direct sentences, earned insight, no filler phrases. Never use "In today's fast-paced world", "Unpopular opinion:", or "Let that sink in." Sounds like a senior practitioner who respects the reader's time.

**raw**: Vulnerable, personal, honest. Short sentences. Allow pauses with line breaks. Let emotion show without overdoing it.

**bold**: Contrarian, declarative, provocative. Make a strong claim and defend it. No hedging.

**mentor**: Teaching-focused, structured, clear frameworks. Use numbered steps, clear principles, actionable takeaways.

**story**: Narrative arc. Scene-setting opening, tension in the middle, resolution at the end. Slow burn.

---

### Piece 1 — Hook Trio

Write **3 distinct opening lines** for the post. Each hook uses a different approach:

- **Hook A — Curiosity gap**: Tease the payoff without revealing it. Makes the reader need to click "see more".
- **Hook B — Bold claim**: Lead with a declarative, slightly provocative statement related to the topic.
- **Hook C — Story opener**: Drop the reader into a specific moment or scene.

Format rules (LinkedIn has no markdown):
- One line each. No punctuation gimmicks. No ellipsis abuse.
- Never start with "I" — start with the situation, the insight, or the tension.
- Under 140 characters each.

---

### Piece 2 — Full Post

Write the **complete LinkedIn post** (~200–300 words) using Hook A by default.

Format rules (LinkedIn renders plain text only — no markdown, no tables):
- Use blank lines between paragraphs for breathing room.
- Use ALL CAPS sparingly for a single emphasis word (not sentences).
- Never use bullet dashes inside the post — write in flowing prose or numbered points.
- End with a single question or call to action on its own line.
- Do not include hashtags in the post body — they go in Piece 5.

---

### Piece 3 — Carousel Script

Write a **6-slide carousel script** formatted for a LinkedIn PDF carousel upload.

Format rules (no tables — LinkedIn carousels are slide-by-slide):
- Number each slide clearly.
- Each slide has: a SHORT BOLD TITLE (under 8 words, all caps) and 1–2 lines of body text.
- Slide 1 is the cover — use the Hook B text as the title.
- Slide 6 is the CTA — end with a follow prompt or question.
- Keep body text punchy. One idea per slide.

Example format:
```
Slide 1 — Cover
Title: YOUR HOOK B TEXT HERE
Body: (optional subtitle or leave blank for visual impact)

Slide 2
Title: THE PROBLEM
Body: Most people don't realize X until it's too late.

...

Slide 6 — CTA
Title: WHAT'S YOUR TAKE?
Body: Follow for more posts like this. Drop your answer below.
```

---

### Piece 4 — Engagement Question

Write **one standalone question** designed to be posted as the first comment on your own post to drive replies.

Rules:
- Under 100 characters.
- Must be answerable in one sentence — low friction for the reader.
- Directly related to the topic.
- Never rhetorical. Must invite a real answer.

---

### Piece 5 — Hashtag Pack

Provide **8 hashtags** split into two groups (no table — use labeled lists):

**Niche (5):** Specific to the topic, industry, or audience. Lower volume, higher relevance.
**Broad (3):** Large general hashtags with millions of followers (e.g. #Leadership, #CareerGrowth, #Tech).

Format: one line per group, comma-separated.

---

## Step 3 — Create the output file

Create a timestamped output folder and HTML file:
```
./linkedin-posts/YYYY-MM-DD-HHmmss/post.html
```

Write a single `post.html` file with this structure and style:

### Visual design

- Page background: `#1b1b1b`
- Font: Google Font `Inter` (load via CDN)
- LinkedIn blue accent: `#0077B5`
- Card background: `#ffffff`
- Card border-radius: `12px`
- Card padding: `24px`
- Max content width: `760px`, centered

### Page layout (HTML structure)

```
<header>  LinkedIn Content Kit  +  topic subtitle  (blue bar)
<section> Post Preview Card  (mimics LinkedIn post UI)
<section> Hook Trio Card
<section> Carousel Script Card
<section> Engagement Question Card
<section> Hashtag Pack Card
<footer>  generated timestamp
```

### Post Preview Card

Mimic the LinkedIn post layout:
- Avatar circle placeholder (initials or generic icon, blue background)
- Name: "You" | Subtext: "Your headline here • 1st"
- Post body text in a readable `16px` font, `line-height: 1.7`
- A faint border around the card

### Content cards (Pieces 1, 3, 4, 5)

Each card has:
- A **colored header bar** with the piece name (e.g. "Hook Trio", "Carousel Script") in white text on `#0077B5` background
- The generated content in a `<pre>` or `<div>` with `white-space: pre-wrap` so line breaks render correctly
- A **"Copy" button** (top-right of each card) that copies the card's text content to clipboard using `navigator.clipboard.writeText()`
- Copy button style: outlined, `#0077B5` border and text, turns to "Copied!" with a green checkmark for 2 seconds on click

### No tables anywhere in the HTML

Use `<ul>`, `<ol>`, `<p>`, `<strong>`, and `<pre>` elements only. Never `<table>`.

### Entrance animation

CSS keyframe `fadeUp`: `opacity: 0 → 1, transform: translateY(16px) → translateY(0)` over `0.4s ease-out`. Stagger each card with `animation-delay` increments of `0.1s`.

---

## Step 4 — Open in browser

After writing the file, open it:

**Windows (PowerShell):**
```powershell
Start-Process (Resolve-Path "./linkedin-posts/<timestamp>/post.html")
```

**macOS:**
```bash
open ./linkedin-posts/<timestamp>/post.html
```

**Linux:**
```bash
xdg-open ./linkedin-posts/<timestamp>/post.html
```

---

## Step 5 — Report to user

After opening, print:
```
LinkedIn content kit generated for: "<topic>"
Tone: <detected tone>  |  Audience: <detected audience>
Saved to: ./linkedin-posts/<timestamp>/post.html

  1. Hook Trio          → 3 opening lines (curiosity, bold, story)
  2. Full Post          → ~250 words, ready to paste
  3. Carousel Script    → 6 slides for PDF carousel
  4. Engagement Question → pin as first comment to drive replies
  5. Hashtag Pack       → 5 niche + 3 broad hashtags

Preview opened in browser. Use the Copy buttons to grab each piece.
```
