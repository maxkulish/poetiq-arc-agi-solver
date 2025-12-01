# Frequently Used Patterns

## Commands

### Development
```bash
# Activate environment
source .venv/bin/activate

# Run solver
python main.py

# Install dependencies
pip install -r requirements.txt
```

### Testing
```bash
# Run on subset of problems
# Modify NUM_PROBLEMS in main.py
```

## Code Templates

### Async API Call Pattern
```python
import asyncio
from asynciolimiter import Limiter

limiter = Limiter(rate=10)  # 10 requests per second

async def call_api():
    async with limiter:
        # Make API call
        pass
```

### Grid Manipulation
```python
import numpy as np

# Create grid
grid = np.array([[0, 1], [1, 0]])

# Transform grid
rotated = np.rot90(grid)
flipped = np.flip(grid, axis=0)
```
