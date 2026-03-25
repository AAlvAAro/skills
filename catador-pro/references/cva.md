# CVA — Coffee Value Assessment Protocol — Detailed Reference

## Overview

The Coffee Value Assessment (CVA) is the SCA's modern evaluation framework, introduced in 2023-2024 to complement (and eventually evolve beyond) the traditional cupping form. It separates coffee evaluation into two independent dimensions: how much you **like** it (Affective) and what it objectively **is** (Descriptive).

CVA is available on two official digital platforms: Tastify Web App and the Catador Pro mobile app.

## Why CVA Exists

The traditional SCA form conflates preference and description — a cupper scoring "Acidity: 8.5" is mixing "I like this acidity" with "this acidity is intense/complex." CVA separates these:

- **Affective**: Pure preference — do I like this coffee? Would I buy it?
- **Descriptive**: Pure description — what are its objective sensory characteristics?
- **Combined**: Both together for a complete picture

This separation makes evaluations more useful across the supply chain. A producer needs descriptive data. A buyer needs affective data. A roaster needs both.

## CVA Affective

### Purpose
Measure hedonic (pleasure-based) response. How much does the evaluator enjoy this coffee?

### Evaluation Format
The evaluator rates the coffee on preference scales:

**Overall Preference** (1-9 hedonic scale):
| Score | Label |
|-------|-------|
| 9 | Like Extremely |
| 8 | Like Very Much |
| 7 | Like Moderately |
| 6 | Like Slightly |
| 5 | Neither Like nor Dislike |
| 4 | Dislike Slightly |
| 3 | Dislike Moderately |
| 2 | Dislike Very Much |
| 1 | Dislike Extremely |

**Attribute-Level Preference** (same 1-9 scale for each):
- Aroma preference
- Flavor preference
- Acidity preference
- Bitterness preference
- Sweetness preference
- Mouthfeel preference
- Aftertaste preference

**Purchase Intent**:
- Would you buy this coffee? (Yes/No/Maybe)
- What would you pay? (price range)

### Scoring
There is no single "final score" like SCA's 100-point scale. The output is a preference profile — useful for understanding market fit, not for ranking coffees against a standard.

### When to Use
- Consumer panels
- Market research
- Understanding target audience preferences
- Comparing coffees for a specific buyer's palate
- Evaluating how a coffee will perform commercially

## CVA Descriptive

### Purpose
Objectively characterize a coffee's sensory attributes. No preference judgment — just "what is this coffee?"

### Evaluation Format

**Intensity Scales** (0-15 for each attribute):
Rate the intensity (not quality) of each attribute.

- **Aroma intensity**: How strong is the aroma? (0 = none, 15 = extremely strong)
- **Acidity intensity**: How pronounced is the acidity?
- **Sweetness intensity**: How sweet is it?
- **Bitterness intensity**: How bitter is it?
- **Saltiness intensity**: How salty is it?
- **Body/Weight intensity**: How heavy/thick does it feel?
- **Aftertaste intensity**: How long does the finish last?

**Quality Attributes** (check all that apply):
- **Acidity quality**: Bright, crisp, tart, sharp, sour, vinegary
- **Body quality**: Silky, creamy, smooth, watery, astringent, chalky
- **Aftertaste quality**: Clean, sweet, dry, lingering, harsh

**Flavor Descriptors** (select from standardized vocabulary):
- Uses the SCA/WCR Sensory Lexicon (more structured than the Flavor Wheel)
- Categories: fruity, floral, sweet, nutty/cocoa, spice, roasted, green, sour/fermented, other
- Multiple selections allowed

**Defects** (if present):
- Type: ferment, phenol, chemical, earthy, musty, Rio, rubber, other
- Intensity: slight, moderate, strong

### Scoring
No single numeric score. The output is a sensory profile — a multi-dimensional description of the coffee. This can be visualized as radar charts, flavor maps, or intensity profiles.

### When to Use
- Quality control (is this lot consistent with previous lots?)
- Communication between supply chain actors ("this lot has X intensity acidity, Y intensity sweetness")
- Research
- Training and calibration
- Building a sensory database

## CVA Combined

### Purpose
Complete evaluation integrating both preference and objective description. Gives the fullest picture of a coffee's value.

### Evaluation Format
The evaluator completes both the Affective and Descriptive assessments for the same coffee. The two evaluations may be done:
- Simultaneously (evaluate each attribute for both preference and intensity)
- Sequentially (complete Descriptive first, then Affective, or vice versa)

### Output
A combined profile that answers both:
- "What is this coffee?" (Descriptive data)
- "How valuable is this coffee to this market/buyer?" (Affective data)

### When to Use
- Premium coffee selection (need both quality data and market fit)
- Comprehensive quality programs
- Buyer-supplier alignment ("here's exactly what this coffee is AND how our team rated it")
- Competition alternatives (some competitions are exploring CVA-based formats)

## CVA vs Traditional SCA Form

| Aspect | Traditional SCA | CVA |
|--------|----------------|-----|
| Scale | 100 points (single score) | Multi-dimensional profiles |
| Preference vs Description | Mixed together | Separated |
| Output | One number (e.g., 87.5) | Sensory profile + preference data |
| Best for | Quick ranking, specialty threshold | Detailed understanding, supply chain communication |
| Training required | Q Grader level | Varies (Affective = any consumer; Descriptive = trained) |
| Digital platforms | Various | Tastify, Catador Pro (official SCA partners) |

## Practical Guidance

When a user asks for CVA evaluation:

1. **Ask which variant**: "CVA has three modes — Affective (your personal preference), Descriptive (objective characteristics), or Combined (both). Which would you like?"

2. **Set expectations**: CVA doesn't produce a single score like the SCA form. It produces a profile. If the user wants a simple number, the traditional SCA form might be more appropriate.

3. **For Affective**: Guide them through the hedonic scale for each attribute. This is intuitive — "On a scale of 1-9, how much did you enjoy the acidity?"

4. **For Descriptive**: Guide them through intensity ratings. Emphasize objectivity — "Don't rate whether you like the acidity. Rate how MUCH acidity there is, from 0 (none) to 15 (extremely intense)."

5. **For Combined**: Do Descriptive first (objective assessment without preference bias), then Affective.

6. **Visualization**: CVA data is best visualized as radar charts with intensity values, paired with preference overlays. The HTML report should reflect this dual-dimension approach.
