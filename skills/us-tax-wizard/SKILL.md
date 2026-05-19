---
name: us-tax-wizard
description: Interactive US tax filing wizard for W-2 employees. Asks 3 quick questions then generates a personalized HTML checklist/wizard covering document gathering, filing method selection, key decisions, step-by-step walkthrough, and post-filing actions. Includes IRS.gov links, progress tracking, and green fintech design. Trigger when user invokes /us-tax-wizard or asks for help filing US taxes, IRS Form 1040, or W-2 employee tax return guidance.
---

Guide a W-2 employee through their US federal tax return. Follow every step below exactly.

## Step 1 — Determine the active tax year

Use today's date to determine which tax year is active:
- If today is between Jan 1 and Oct 15 of any year Y: tax year = Y-1 (filing season for prior year)
- If today is between Oct 16 and Dec 31 of any year Y: tax year = Y (preparing for upcoming filing)
- Standard deadline: April 15 of Y+1
- Extension deadline: October 15 of Y+1

Set these variables for use throughout the skill:
- TAX_YEAR = the active tax year (e.g. 2025)
- FILING_DEADLINE = April 15, [TAX_YEAR+1]
- EXTENSION_DEADLINE = October 15, [TAX_YEAR+1]
- TODAY = current date

Determine if TODAY is:
- **Before** FILING_DEADLINE → "Filing season is open"
- **Between** FILING_DEADLINE and EXTENSION_DEADLINE → "You are in the extension period — original deadline has passed"
- **After** EXTENSION_DEADLINE → "The [TAX_YEAR] filing deadline has passed — you may need to file a late return"

---

## Step 2 — Ask 3 chat questions

Ask the user these three questions one at a time (wait for each answer before asking the next):

**Question 1:**
```
What is your filing status for tax year [TAX_YEAR]?

  1. Single
  2. Married Filing Jointly
  3. Married Filing Separately
  4. Head of Household

Type the number or the name.
```

**Question 2:**
```
Do you have any dependents (children or other qualifying people you support)?

  1. Yes
  2. No
```

**Question 3:**
```
Do any of the following apply to you? (Select all that apply, or type "none")

  A. I pay a mortgage (home loan interest)
  B. I have student loan interest payments
  C. Both A and B
  D. None of the above
```

Store the answers as:
- FILING_STATUS = (Single | MFJ | MFS | HoH)
- HAS_DEPENDENTS = (true | false)
- HAS_MORTGAGE = (true | false)
- HAS_STUDENT_LOANS = (true | false)

---

## Step 3 — Determine standard deduction and key credits

Based on TAX_YEAR and FILING_STATUS, set STANDARD_DEDUCTION:
- Single or MFS: $14,600 (2024), $15,000 (2025)
- MFJ: $29,200 (2024), $30,000 (2025)
- HoH: $21,900 (2024), $22,500 (2025)
- For years beyond 2025, note: "Check IRS.gov for the current standard deduction amount"

Determine applicable credits/deductions to highlight:
- HAS_DEPENDENTS = true → include Child Tax Credit (up to $2,000/child), Child & Dependent Care Credit, EITC eligibility check
- HAS_MORTGAGE = true → include Mortgage Interest Deduction note, Form 1098 required
- HAS_STUDENT_LOANS = true → include Student Loan Interest Deduction (up to $2,500), Form 1098-E required

---

## Step 4 — Create the output file

Create a timestamped output folder and HTML file:
```
./us-tax-wizard/YYYY-MM-DD-HHmmss/wizard.html
```

Write a single self-contained `wizard.html` with the full structure and design below.

---

### Visual design system

- Page background: `#f0fdf4`
- Primary green: `#16a34a`
- Dark green (hover): `#15803d`
- Light green accent: `#dcfce7`
- White card background: `#ffffff`
- Text primary: `#111827`
- Text secondary: `#6b7280`
- Border color: `#e5e7eb`
- Card border-radius: `16px`
- Card box-shadow: `0 1px 3px rgba(0,0,0,0.08), 0 4px 12px rgba(0,0,0,0.04)`
- Font: Google Font `Inter` loaded via CDN
- Max content width: `800px`, centered
- Body padding: `24px`

---

### HTML page structure

```
<head>
  Google Fonts: Inter (300,400,500,600,700)
  All CSS embedded in <style> block

<body>
  <header>           ← fixed top bar
  <div.progress-bar> ← percentage fill bar (below header)
  <nav.step-nav>     ← horizontal step indicators
  <main>
    <section#step1>  Documents to Gather
    <section#step2>  Choose Your Filing Method
    <section#step3>  Key Decisions
    <section#step4>  Step-by-Step Filing Walkthrough
    <section#step5>  After You File
    <section#resources> Useful Resources
  </main>
  <footer>
```

---

### Header (fixed, full-width)

```html
Background: #16a34a
Content (centered, white text):
  Left: "🧾 US Tax Wizard" in 20px 600-weight
  Right: "Tax Year [TAX_YEAR]  |  Filing Status: [FILING_STATUS]"
Height: 60px
z-index: 1000
```

Add `padding-top: 60px` to body to offset fixed header.

---

### Progress percentage bar

Directly below the header (sticky, full-width):
- Background track: `#e5e7eb`, height `8px`
- Fill bar: `#16a34a`, width starts at `0%`, transitions with `transition: width 0.4s ease`
- Label above right: `"Progress: X% complete"` in `#16a34a` `14px` font
- Updates via JavaScript: count total checkboxes vs checked checkboxes across all sections

```javascript
function updateProgress() {
  const total = document.querySelectorAll('input[type=checkbox]').length;
  const checked = document.querySelectorAll('input[type=checkbox]:checked').length;
  const pct = total === 0 ? 0 : Math.round((checked / total) * 100);
  document.getElementById('progress-fill').style.width = pct + '%';
  document.getElementById('progress-label').textContent = 'Progress: ' + pct + '% complete';
}
document.querySelectorAll('input[type=checkbox]').forEach(cb => {
  cb.addEventListener('change', updateProgress);
});
updateProgress();
```

---

### Step navigation bar

Horizontal row of 5 step pills, centered, below the progress bar:

```
[ 1 Documents ] [ 2 Filing Method ] [ 3 Key Decisions ] [ 4 Walkthrough ] [ 5 After Filing ]
```

Each pill:
- Default: white background, `#6b7280` text, `1px solid #e5e7eb` border, `8px 16px` padding, `20px` border-radius
- Active (current section in viewport): `#16a34a` background, white text
- Completed (all checkboxes in that section checked): `#dcfce7` background, `#16a34a` text, `✓` prepended to label
- Clicking a pill smoothly scrolls to that section (`behavior: 'smooth'`)
- Use IntersectionObserver to detect which section is currently in viewport and update active pill

```javascript
const sections = document.querySelectorAll('section[id^="step"]');
const pills = document.querySelectorAll('.step-pill');

const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const idx = Array.from(sections).indexOf(entry.target);
      pills.forEach(p => p.classList.remove('active'));
      if (pills[idx]) pills[idx].classList.add('active');
    }
  });
}, { threshold: 0.3 });

sections.forEach(s => observer.observe(s));
```

Section completion check: a section is "completed" when all its checkboxes are checked. Re-evaluate on every checkbox change.

---

### Section card style

Each `<section>` is a card:
```css
background: #ffffff;
border-radius: 16px;
box-shadow: 0 1px 3px rgba(0,0,0,0.08), 0 4px 12px rgba(0,0,0,0.04);
padding: 32px;
margin-bottom: 24px;
```

Each card has a **colored section header**:
```css
background: linear-gradient(135deg, #16a34a, #15803d);
color: #ffffff;
border-radius: 12px;
padding: 16px 20px;
margin-bottom: 24px;
display: flex;
align-items: center;
gap: 12px;
```
Header includes: emoji icon + section number + section title + completion badge ("✓ Complete" appears when all checkboxes in section are checked)

Checkbox items:
```css
display: flex;
align-items: flex-start;
gap: 12px;
padding: 12px 0;
border-bottom: 1px solid #f3f4f6;
cursor: pointer;
```
Checkbox input: `accent-color: #16a34a; width: 18px; height: 18px; margin-top: 2px; cursor: pointer; flex-shrink: 0`
Label: `font-size: 15px; line-height: 1.5; color: #374151; cursor: pointer`
When checked: label gets `text-decoration: line-through; color: #9ca3af`

Info boxes (tips, warnings, notes):
```css
background: #f0fdf4;
border-left: 4px solid #16a34a;
border-radius: 0 8px 8px 0;
padding: 12px 16px;
margin: 16px 0;
font-size: 14px;
color: #374151;
```

Warning boxes (deadlines, penalties):
```css
background: #fefce8;
border-left: 4px solid #eab308;
```

---

### Section 1 — Documents to Gather

Icon: 📋 | Title: "Step 1: Documents to Gather"

**Intro text:** "Before you start filing, gather all documents below. Check each one off as you collect it."

**Always show (all filers):**
- [ ] **W-2 form** — From your employer, shows wages earned and taxes withheld. Employers must send by January 31.
- [ ] **Social Security Number (SSN)** — For you and every person on your return.
- [ ] **Prior year tax return** — Useful for reference; AGI from last year may be needed for e-filing identity verification.
- [ ] **Bank account info for direct deposit** — Routing number + account number to receive your refund faster.
- [ ] **Identity documents** — Government-issued ID (driver's license or passport).

**If HAS_DEPENDENTS = true, add:**
- [ ] **Dependent SSNs** — Social Security Numbers for each dependent you're claiming.
- [ ] **Birth certificates or adoption papers** — May be needed to verify dependent relationship.
- [ ] **Form 2441 records** — Receipts for child care / daycare expenses if claiming Child & Dependent Care Credit.
- [ ] **School records** — If claiming a student as dependent.

**If HAS_MORTGAGE = true, add:**
- [ ] **Form 1098 (Mortgage Interest Statement)** — From your lender, shows mortgage interest paid. Needed if itemizing deductions.
- [ ] **Property tax records** — Amount of real estate taxes paid during [TAX_YEAR].

**If HAS_STUDENT_LOANS = true, add:**
- [ ] **Form 1098-E (Student Loan Interest Statement)** — From your loan servicer. Up to $2,500 may be deductible.

**Always show at end of section:**
Info box: "💡 Missing a W-2? If your employer hasn't sent it by mid-February, contact them first. If still missing, you can contact the IRS at 1-800-829-1040 or use Form 4852 as a substitute."

---

### Section 2 — Choose Your Filing Method

Icon: 💻 | Title: "Step 2: Choose Your Filing Method"

**Intro text:** "W-2 employees have several free and paid options. Compare them below and check the one you'll use."

**Filing method options (radio-button style visual cards, not actual radio inputs — use styled divs with checkboxes):**

**Option A — IRS Free File**
- [ ] I will use IRS Free File
- Who it's for: Filers with Adjusted Gross Income (AGI) of $84,000 or less
- Cost: Free (guided tax software from IRS partners)
- Link: [IRS Free File →](https://www.irs.gov/filing/free-file-do-your-federal-taxes-for-free)
- Best for: Simple returns, single filers, straightforward W-2 income

**Option B — IRS Free File Fillable Forms**
- [ ] I will use Free File Fillable Forms
- Who it's for: Any income level — you fill out the forms directly (no guidance)
- Cost: Free
- Link: [Free File Fillable Forms →](https://www.irs.gov/filing/free-file-fillable-forms)
- Best for: Confident filers who know what they're doing

**Option C — Commercial Tax Software**
- [ ] I will use commercial tax software (TurboTax, H&R Block, TaxAct, etc.)
- Cost: Free tier available for simple returns; paid tiers $30–$120 for complex situations
- Best for: Guided experience, maximizing deductions, state return bundled
- Note: Compare free tier eligibility before paying

**Option D — Tax Professional (CPA or Enrolled Agent)**
- [ ] I will hire a tax professional
- Cost: Typically $150–$400+ depending on complexity
- Best for: Complex situations, multiple income sources, life changes (marriage, home purchase, new dependents)
- Find one: [IRS Directory of Federal Tax Return Preparers →](https://irs.treasury.gov/rpo/rpo.jsf)

Info box: "💡 If HAS_DEPENDENTS = true or HAS_MORTGAGE = true: Your situation has itemizable deductions and credits. Commercial software or a tax professional may help you maximize your refund."

---

### Section 3 — Key Decisions

Icon: ⚖️ | Title: "Step 3: Key Decisions"

**Decision 1 — Standard vs. Itemized Deduction**

Info box:
```
Your standard deduction for [TAX_YEAR] ([FILING_STATUS]):
$[STANDARD_DEDUCTION]

You should itemize ONLY if your deductible expenses exceed this amount.
Common itemized deductions: mortgage interest, state/local taxes (SALT cap: $10,000),
charitable contributions, large medical expenses (>7.5% of AGI).
```

- [ ] I have reviewed my deductible expenses and chosen: **Standard Deduction** (most W-2 employees choose this)
- [ ] I have reviewed my deductible expenses and chosen: **Itemized Deduction** (use Schedule A)

If HAS_MORTGAGE = true, add note: "⚠️ If you paid significant mortgage interest in [TAX_YEAR], compare your total itemizable expenses vs. your $[STANDARD_DEDUCTION] standard deduction before deciding."

**Decision 2 — Filing Deadline**

Show deadline status based on TODAY vs. FILING_DEADLINE vs. EXTENSION_DEADLINE:

Warning box if in extension period: "⚠️ The standard April 15 deadline has passed. If you filed Form 4868 for an extension, your deadline is [EXTENSION_DEADLINE]. Note: an extension gives more time to FILE, not more time to PAY — interest may accrue on unpaid tax."

Info box if before deadline: "✅ Filing season is open. Your deadline is [FILING_DEADLINE]. File early to get your refund sooner and reduce identity theft risk."

- [ ] I understand my filing deadline: [FILING_DEADLINE] (or [EXTENSION_DEADLINE] if on extension)
- [ ] I know that if I owe taxes, I should pay by April 15 even if I file an extension

**Decision 3 — Credits to claim (personalized)**

Show only the relevant credits:

If HAS_DEPENDENTS = true:
- [ ] I have reviewed the **Child Tax Credit** (up to $2,000 per qualifying child under 17) — [IRS Child Tax Credit →](https://www.irs.gov/credits-deductions/individuals/child-tax-credit)
- [ ] I have checked my eligibility for **Earned Income Tax Credit (EITC)** — [EITC Assistant →](https://www.irs.gov/credits-deductions/individuals/earned-income-tax-credit/use-the-eitc-assistant)
- [ ] I have documented childcare expenses for the **Child & Dependent Care Credit** (Form 2441)

If HAS_STUDENT_LOANS = true:
- [ ] I have collected Form 1098-E for the **Student Loan Interest Deduction** (up to $2,500, subject to income phase-out)

Always show:
- [ ] I have reviewed the **Saver's Credit** if I contributed to a 401(k) or IRA — [IRS Saver's Credit →](https://www.irs.gov/retirement-plans/plan-participant-employee/retirement-savings-contributions-savers-credit)

---

### Section 4 — Step-by-Step Filing Walkthrough

Icon: 📝 | Title: "Step 4: File Your Return"

**Intro text:** "Follow these steps in order to complete and submit your [TAX_YEAR] federal tax return."

- [ ] **1. Create or log in to your IRS account** — Set up at [IRS Online Account →](https://www.irs.gov/payments/your-online-account) to view prior returns, transcripts, and payment history.
- [ ] **2. Gather all documents from Step 1** — Have your W-2, SSN, and supporting documents ready before starting.
- [ ] **3. Open your chosen filing method** — Log in to IRS Free File, your tax software, or meet with your tax professional.
- [ ] **4. Enter your personal information** — Name, SSN, address, and filing status ([FILING_STATUS]).
- [ ] **5. Enter W-2 income** — Input wages, federal tax withheld, and state tax withheld exactly as shown on your W-2. Check Box 1 (Wages), Box 2 (Federal income tax withheld), Box 17 (State tax withheld).
- [ ] **6. Add any other income** — 1099-INT (bank interest), 1099-DIV (dividends), 1099-B (investments), 1099-NEC (side income) if applicable.

If HAS_DEPENDENTS = true:
- [ ] **7. Add dependents** — Enter each dependent's name, SSN, relationship, and months lived with you. Claim Child Tax Credit and EITC if eligible.

If HAS_MORTGAGE = true:
- [ ] **8. Enter mortgage interest from Form 1098** — Go to Schedule A if itemizing. Enter Box 1 (mortgage interest paid).

If HAS_STUDENT_LOANS = true:
- [ ] **9. Enter student loan interest from Form 1098-E** — Deduct up to $2,500 (phase-out applies above certain income levels).

Always show:
- [ ] **10. Choose standard or itemized deduction** — The software will recommend the one that saves you more.
- [ ] **11. Review your return** — Check every number against your documents. Review your refund or amount owed.
- [ ] **12. Enter bank account for direct deposit** — Routing number and account number for fastest refund (typically 21 days for e-filed returns).
- [ ] **13. E-sign and submit** — Use your prior year AGI or IRS Identity Protection PIN to verify identity. Submit electronically.
- [ ] **14. Save your confirmation** — Save the submission confirmation number and a copy of your filed return (download as PDF).

Info box: "💡 E-filing is faster and more accurate than mailing a paper return. The IRS typically processes e-filed returns within 21 days."

---

### Section 5 — After You File

Icon: ✅ | Title: "Step 5: After You File"

- [ ] **Track your refund** — Use the IRS "Where's My Refund?" tool starting 24 hours after e-filing: [Where's My Refund? →](https://www.irs.gov/refunds)
- [ ] **Save your return** — Store your filed return PDF and all supporting documents (W-2, 1098s, etc.) for at least 3 years.
- [ ] **Check state filing requirement** — Most states require a separate state income tax return. Check your state's tax agency website.
- [ ] **Review withholding for next year** — If you owed a large amount or got a very large refund, consider adjusting your W-4 with your employer: [IRS Withholding Estimator →](https://www.irs.gov/individuals/tax-withholding-estimator)
- [ ] **Set up IRS Identity Protection PIN** — Prevents others from filing a return using your SSN: [IP PIN Program →](https://www.irs.gov/identity-theft-fraud-scams/get-an-identity-protection-pin)
- [ ] **Note next year's deadlines** — Mark April 15, [TAX_YEAR+2] as your next filing deadline.

Info box: "💡 Keep records for at least 3 years (7 years if you claimed a loss on worthless securities or bad debt). The IRS generally has 3 years to audit a return."

---

### Resources panel

Below all sections, a dedicated resources card:

Icon: 🔗 | Title: "Useful IRS Resources"

Organized in two columns (use flexbox, not table):

**Filing Tools:**
- [IRS Free File](https://www.irs.gov/filing/free-file-do-your-federal-taxes-for-free) — Free guided tax software (AGI ≤ $84,000)
- [Free File Fillable Forms](https://www.irs.gov/filing/free-file-fillable-forms) — Fill Form 1040 directly online
- [Where's My Refund?](https://www.irs.gov/refunds) — Track your federal refund status
- [IRS Online Account](https://www.irs.gov/payments/your-online-account) — View transcripts, payments, notices

**Forms & Instructions:**
- [Form 1040 Instructions](https://www.irs.gov/forms-pubs/about-form-1040) — Official Form 1040 and instructions
- [Form 4868 — Extension of Time to File](https://www.irs.gov/forms-pubs/about-form-4868) — Request a 6-month extension
- [IRS Publication 17](https://www.irs.gov/publications/p17) — Comprehensive guide to individual taxes

**Credits & Deductions:**
- [Child Tax Credit](https://www.irs.gov/credits-deductions/individuals/child-tax-credit) — Up to $2,000 per qualifying child
- [EITC Assistant](https://www.irs.gov/credits-deductions/individuals/earned-income-tax-credit/use-the-eitc-assistant) — Check EITC eligibility
- [Withholding Estimator](https://www.irs.gov/individuals/tax-withholding-estimator) — Adjust paycheck withholding

**Help:**
- [IRS Tax Help](https://www.irs.gov/help) — IRS help center
- [VITA — Free In-Person Help](https://www.irs.gov/individuals/free-tax-return-preparation-for-qualifying-taxpayers) — Volunteer Income Tax Assistance (free help for income ≤ $67,000)
- [Find a Tax Professional](https://irs.treasury.gov/rpo/rpo.jsf) — IRS directory of credentialed preparers

---

### Footer

```
background: #f9fafb
border-top: 1px solid #e5e7eb
padding: 24px
text-align: center
color: #9ca3af
font-size: 13px

Text:
"US Tax Wizard  •  Tax Year [TAX_YEAR]  •  Generated [TODAY]
This wizard is for informational guidance only. It is not tax advice.
Always verify information at IRS.gov or consult a qualified tax professional."
```

---

### Entrance animation

CSS keyframe `fadeUp`: `opacity: 0 → 1, transform: translateY(20px) → translateY(0)` over `0.5s ease-out`.
Stagger each section card with `animation-delay` of `0.1s` increments.

---

## Step 5 — Open in browser

After writing the file, open it immediately:

**Windows (PowerShell):**
```powershell
Start-Process (Resolve-Path "./us-tax-wizard/<timestamp>/wizard.html")
```

**macOS:**
```bash
open ./us-tax-wizard/<timestamp>/wizard.html
```

**Linux:**
```bash
xdg-open ./us-tax-wizard/<timestamp>/wizard.html
```

---

## Step 6 — Report to user

After opening, print:

```
US Tax Wizard generated for Tax Year [TAX_YEAR]

  Filing Status : [FILING_STATUS]
  Dependents    : [Yes / No]
  Home/Loans    : [Mortgage: Yes/No  |  Student Loans: Yes/No]
  Deadline      : [FILING_DEADLINE] [+ extension note if applicable]

  Wizard sections:
    1. Documents to Gather     → personalized checklist ([N] items)
    2. Choose Filing Method    → 4 options with IRS links
    3. Key Decisions           → deduction: $[STANDARD_DEDUCTION] standard + [N] credits
    4. Filing Walkthrough      → [N] step-by-step actions
    5. After You File          → refund tracking, records, next steps

  Saved to: ./us-tax-wizard/<timestamp>/wizard.html
  Preview opened in browser. Check off items as you complete them.
```
