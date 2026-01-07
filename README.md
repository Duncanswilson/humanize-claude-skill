# Humanize Text - Claude Code Skill

A Claude Code skill that rewrites AI-generated text to reduce AI detection scores using the Pangram API for feedback.

## What It Does

This skill helps you iteratively rewrite text to pass AI detection tools. It uses Pangram's API to:
1. Detect which parts of your text are flagged as AI-generated
2. Identify specific trigger sentences
3. Automatically apply fixes (hedging, simplification)
4. Verify the rewritten text passes detection

## Requirements

```bash
export PANGRAM_API_KEY="your-api-key"
pip install pangram-sdk
```

## Quick Start

```bash
# Check if text passes detection
python .claude/skills/humanize-text/pangram_detect.py --file essay.txt --both

# Auto-humanize text
python .claude/skills/humanize-text/auto_humanize.py --file essay.txt --output humanized.txt
```

## Key Findings

Through extensive testing, we discovered:

| Finding | Details |
|---------|---------|
| **~180 word threshold** | The full model almost always flags text over 180 words, regardless of style |
| **Aggregate detection** | Individual sentences pass, but combinations fail—the detector sees document-level patterns |
| **What works** | Simpler sentences, more paragraphs, personal admissions ("I dont fully understand") |
| **What doesn't work** | Missing apostrophes alone, em-dash removal, meta-commentary |

## Available Scripts

### `pangram_detect.py` - Detection & Analysis

```bash
--both      # Compare full vs short model (recommended first step)
--flagged   # Show only AI-labeled windows
--verbose   # Full text of problem segments
--json      # Machine-readable output
```

### `auto_humanize.py` - Automated Fixing

Builds text sentence-by-sentence, finds triggers, and applies fixes automatically.

```bash
--file input.txt          # Input file
--output humanized.txt    # Save result
--stats                   # Show detailed trigger/fix info
--quiet                   # Just output the text
```

**Fix strategies tried:**
- Add "I think" / "I guess" at end
- Add "probably" after is/are/was/were
- Convert to question
- Add "Maybe" at start

## Example

**Input (100% AI detected):**
> The implementation of machine learning algorithms requires careful consideration of various factors including data preprocessing and model optimization.

**Output (0% AI detected):**
> So heres the thing about ML. You cant just pick an algorithm and hope for the best. Data cleaning takes forever and optimization is its own rabbit hole. I learned this the hard way.

## Two Detection Models

Pangram has two models that give very different results:

| Model | Behavior | Use Case |
|-------|----------|----------|
| **Full** (`predict`) | Aggressive, flags most formal writing | When you need to pass strict detection |
| **Short** (`predict_short`) | Lenient, often passes formal text | Quick check, less strict requirements |

The same formal essay might score 100% AI on full model but 0% on short model.

## Documentation

See [SKILL.md](.claude/skills/humanize-text/SKILL.md) for comprehensive documentation including:
- Detailed debugging workflow
- Technique effectiveness table
- Troubleshooting guide
- Real working examples

## License

MIT
