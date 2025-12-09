# MedVAL Testing

This directory contains the testing infrastructure for the MedVAL pipeline using the MedVAL-Bench dataset.

## Overview

The test runner evaluates the complete MedVAL validation pipeline on the MedVAL-Bench dataset from HuggingFace. It loads test cases with reference inputs, AI-generated candidate outputs, and physician risk assessments, then runs them through the pipeline to validate performance.

## Running Tests

### Basic Usage

```bash
# Run all tests
python tests/run.py

# Quick test with limited cases
python tests/run.py --limit 5

# Use a specific model
python tests/run.py --model openai/gpt-4o
```

### Filtering Options

```bash
# Test specific task type
python tests/run.py --task dialogue2note

# Test specific risk level (1-4)
python tests/run.py --risk-level 4

# Combine filters
python tests/run.py --task dialogue2note --risk-level 3 --limit 10
```

### Output Options

```bash
# Verbose output with detailed error analysis
python tests/run.py --verbose

# Save results to JSON file
python tests/run.py --output results.json --limit 20
```

## Available Tasks

The pipeline supports the following medical NLP tasks (loaded from `utils/task_prompts.json`):

- `dialogue2note` - Summarize patient/doctor dialogue into assessment and plan
- `report2simplified` - Create patient-friendly version of medical text
- `impression2simplified` - Simplify impression sections
- `report2impression` - Summarize radiology findings into impression
- `bhc2spanish` - Translate brief hospital course to Spanish
- `query2question` - Summarize patient query into concise question
- `medication2answer` - Answer medication-related questions

## Environment Setup

### Using conda

```bash
# Activate environment
conda activate medval

# Set API key (if not using .env file)
export OPENAI_API_KEY="your-openai-api-key"

# Run tests
python tests/run.py --limit 5
```

### Using .env file

Create a `.env` file in the project root:

```
OPENAI_API_KEY=your-openai-api-key
```

## Understanding Test Results

The test runner evaluates:

1. **Risk Level Accuracy**: Compares predicted risk levels (1-4) with physician assessments
   - **EXACT**: Risk level matches exactly
   - **NEAR**: Risk level within ±1
   - **MISMATCH**: Risk level differs by 2+

2. **Error Detection**: Lists specific errors identified in candidate outputs

### Sample Output

```
[1/5] Testing case_001...
✓ EXACT case_001 | Task: dialogue2note
    Expected Risk: 3/4  |  Actual Risk: 3/4

    REFERENCE INPUT (Original Medical Text):
      [Patient dialogue...]

    CANDIDATE OUTPUT (AI-Generated Response):
      [Assessment and plan...]

    ERRORS DETECTED (2 total):
      1. Missing critical medication dosage
      2. Incomplete differential diagnosis
```

## Performance Metrics

After running tests, you'll see summary statistics:

- **Total Tests**: Number of test cases run
- **Successful/Failed**: Cases that completed successfully vs. errored
- **Risk Level Accuracy**:
  - EXACT Match: Percentage of exact risk level matches
  - NEAR ±1: Percentage within one risk level
- **By Risk Level**: Breakdown of accuracy for each risk level (1-4)

## References

- [MedVAL Paper](https://arxiv.org/abs/2507.03152) - Original research paper
- [MedVAL-Bench Dataset](https://huggingface.co/datasets/stanfordmimi/MedVAL-Bench) - HuggingFace dataset
- [MedVAL-4B Model](https://huggingface.co/stanfordmimi/MedVAL-4B) - Fine-tuned model
