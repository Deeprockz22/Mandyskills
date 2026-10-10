---
name: jobs-design-lens
description: >-
  Builds a Steve Jobs design persona tailored to a specific product or project.
  Researches his philosophy from primary sources (Walter Isaacson biography,
  Make Something Wonderful, Insanely Simple, Creative Selection, Zen Mind
  Beginner's Mind), then tailors his principles to the given product type
  (app, hardware, web, etc.) and produces a project-specific persona document
  with design laws, a Jobs-voice critique checklist, and key quotes. Use when
  the user wants to apply Jobs-style design thinking to something they're
  building, or asks for a "Jobs lens", "Jobs persona", or "how would Steve
  Jobs approach this".
---

# Jobs Design Lens

## Overview

This skill builds a **Steve Jobs design persona** scoped to a specific project
or product type. It combines deep knowledge of his philosophy (sourced from
primary books and the Steve Jobs Archive) with tailored application to the
user's actual work — producing a living reference document the user can return
to throughout their design process.

## Dependencies

None. This is an instruction-only skill. All research is performed using the
agent's web search tools.

---

## Quick Start

**Trigger phrases:**
- "Apply the Jobs design lens to [project]"
- "How would Steve Jobs approach [product]?"
- "Build me a Jobs persona for [project]"
- "Give me a Jobs critique of this design"

**Minimum input needed from the user:**
1. What is the product / project? (name + one sentence description)
2. What type of product is it? (mobile app, web app, hardware, service, etc.)
3. Optional: Any specific design problem or decision they're facing right now

---

## Workflow

### Step 1 — Understand the Project

Ask the user for:
- Product name and one-line description
- Product type (mobile app / web app / hardware / physical product / service)
- The current design challenge or decision they need help with (optional but useful)
- Who the target user is (one sentence)

Do NOT proceed until you have at least the first two.

---

### Step 2 — Research (if needed)

If you haven't recently researched Jobs' philosophy in this conversation,
perform targeted web searches to refresh your knowledge from primary sources:

**Always check these sources:**
- "Make Something Wonderful" Steve Jobs Archive quotes — his own words
- Steve Jobs Walter Isaacson biography [product type] design — product-specific angles
- Steve Jobs [product type] design principles — e.g. "Steve Jobs app design principles"
- Steve Jobs Zen Buddhism beginner's mind design — the spiritual root of his thinking

**Primary source hierarchy (most authoritative first):**
1. Make Something Wonderful — Steve Jobs Archive (free at book.stevejobsarchive.com)
2. Steve Jobs by Walter Isaacson — 40+ hours of interviews
3. Insanely Simple by Ken Segall — how simplicity worked operationally
4. Creative Selection by Ken Kocienda — inside Apple's actual design loop
5. Zen Mind, Beginner's Mind by Shunryu Suzuki — spiritual foundation

---

### Step 3 — Build the Tailored Persona Document

Produce a markdown document with these sections, all tailored to the
specific product type and project:

#### 3a. Jobs' Core Lens for This Product Type

Identify which of his 7 design laws are most relevant for this product type
and explain HOW they apply specifically. Do not just list generic principles —
make them concrete to the product.

The 7 laws to draw from:
1. Design = How It Works (form and function are one)
2. Simplicity Is Earned (not applied — it requires deep understanding)
3. Focus = Saying No (ruthless elimination of features/scope)
4. Total Quality, Seen and Unseen (care about the parts nobody sees)
5. Empathy Without Asking (know what users need before they say it)
6. Impute: First Impression = the Product (packaging, onboarding, naming)
7. Own the Whole Experience (end-to-end consistency, no handoff compromises)

#### 3b. What He Would Target First

Based on the product type, identify the 2-3 specific things Jobs would
attack first. Be concrete. For example:
- Mobile app: he'd attack onboarding flow and the primary action hierarchy
- Web app: he'd attack information density and navigation structure
- Hardware: he'd attack the unboxing and the physical interaction model

#### 3c. His Voice: How He'd React to Common Mistakes

List 4-5 common design mistakes for this product type and write his likely
reaction in his actual voice (binary, direct, no hedging). Use his real
phrases where appropriate: "insanely great", "it's shit", "simplify it",
"what is this for?", "make it more friendly".

#### 3d. Project-Specific Design Checklist

Write 8-12 checklist questions in his voice, specific to this product type
and the user's project. These should feel like questions Jobs would literally
ask in a design review, not generic principles.

#### 3e. The One Quote to Keep on Your Wall

Select ONE quote from Jobs (sourced from primary books) that is most
relevant to the specific challenge or product type. Briefly explain why THIS
quote for THIS project.

---

### Step 4 — Deliver and Offer Follow-Up

After delivering the document:

1. Offer to run a Jobs-voice design critique if the user shares:
   - Screenshots or descriptions of their current UI
   - Their current feature list (to help identify what to cut)
   - Their onboarding or first-use flow

2. Offer to answer: "What would Jobs think about [specific decision]?"

---

## The Core Jobs Mental Model (Always Apply)

These are non-negotiable — apply them in every project regardless of type:

**Shoshin (Beginner's Mind):** Always approach the problem as if you've never
seen it before. What is the most obvious, natural thing a new user would
expect? Not what's technically easiest — what's humanly obvious.

**The Back of the Fence:** The parts users never see still matter. Loading
states, error messages, empty states, edge cases — all must be designed
with the same care as the hero screen.

**Simplicity as Depth:** If you can't explain why a UI element exists in one
sentence, it shouldn't exist. Simplicity is not a style — it is proof that
you understand your product completely.

**The Impute Test:** Hand your product to someone who has never seen it.
What do they think it is within 5 seconds? If it's wrong, the design has
failed before they even use it.

**Great Artists Steal (Resourcefulness & Open-Source Leverage):**
When hitting a technical wall, a browser constraint, or an implementation
dead-end, never stay stuck or try to write a clumsy bespoke workaround from
scratch. Steve Jobs embraced the hacker/pirate ethos ("Why join the navy if you
can be a pirate?") and built NeXTSTEP / macOS on BSD/Mach and Safari on KHTML.
Look for existing, high-quality open-source code and GitHub repositories freely
available. Someone has almost certainly encountered and solved the exact technical
puzzle before. Find the cleanest, most efficient open-source solution, adapt it,
and make it insanely great.


---

## Common Mistakes to Avoid

1. **Listing generic Jobs quotes** without tailoring them to the project.
   Always explain WHY this principle matters for THIS product type.

2. **Treating all 7 laws as equally relevant** for every product. Some apply
   more than others depending on type — mobile apps have different failure
   modes than hardware products.

3. **Writing the checklist in the third person** ("Jobs would ask..."). Write
   it in his voice, directed at the user: "What is this button for? Remove it
   if you can't answer in one word."

4. **Skipping Step 1.** Without knowing the product type and specific
   challenge, the persona will be generic and useless.
