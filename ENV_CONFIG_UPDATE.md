# Environment Configuration Update

This document outlines updates to the implementation plan regarding environment configuration and folder structure.

## Updated Folder Structure

The original plan specified separate `input/` and `output/` directories. This has been updated to use a single `data/` directory for both input documents and processed output, including the vector database.

## Environment Variables

The system now uses environment variables for configuration:

1. API Keys:
   - `OPENAI_API_KEY`: For OpenAI services (embeddings, LLM completions)
   - `ANTHROPIC_API_KEY`: For Anthropic Claude services

2. Folder Configuration:
   - `INPUT_FOLDER`: Set to "data" by default
   - `OUTPUT_FOLDER`: Set to "data" by default

## Implementation Changes

The following code elements should be updated across implementation files:

### 1. Environment Variable Loading

Add to system initialization:

```python
import os
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

# Access API keys
openai_api_key = os.getenv("OPENAI_API_KEY")
anthropic_api_key = os.getenv("ANTHROPIC_API_KEY")

# Access folder configuration
input_folder = os.getenv("INPUT_FOLDER", "data")
output_folder = os.getenv("OUTPUT_FOLDER", "data")
```

### 2. Updated Function Signatures

Change function signatures in system_infrastructure/Part19_SystemIntegration.txt:

```python
async def build_and_initialize_system(
    input_folder=os.getenv("INPUT_FOLDER", "data"),
    output_folder=os.getenv("OUTPUT_FOLDER", "data")
):
    # ... existing implementation
```

And in interfaces/Part7_WebInterface.txt:

```python
async def initialize_and_run_system(
    input_folder=os.getenv("INPUT_FOLDER", "data"),
    output_folder=os.getenv("OUTPUT_FOLDER", "data")
):
    # ... existing implementation
```

### 3. LLM Service Configuration

Update LLMService initialization to use environment variables:

```python
def __init__(self, resource_monitor=None):
    self.openai_api_key = os.getenv("OPENAI_API_KEY")
    self.anthropic_api_key = os.getenv("ANTHROPIC_API_KEY")
    self.resource_monitor = resource_monitor
    # ... rest of implementation
```

## Security Considerations

- The `.env` file containing actual API keys is excluded from Git via `.gitignore`
- Only the `.env.example` template is committed to the repository
- Users must create their own `.env` file with their actual API keys
- API keys should never be hardcoded in any implementation files