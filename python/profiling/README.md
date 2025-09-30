
# Profiling

## Script Profile

cProfile

```bash
python -m cProfile main.py
```

```python
# main.py
import cProfile
import pstats


def main():
    pass


if __name__ == "__main__":
    with cProfile.Profile() as profile:
        main()
        
    results = pstats.Stats(profile)
    results.print_stats()
    # or
    results.dump_stats("[FILE NAME].prof")
```

## Custom Vizualization

```bash
pip install tuna
```

```bash
tuna [FILE NAME].prof
```

It will open a brownser page on http://localhost:8000 with a graph vizualization for the profile result
