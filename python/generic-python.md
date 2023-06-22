# Generic python

## itertools

- unpacking nested lists

  ```python
  from itertools import chain

  list(chain.from_iterable(list_of_list))
  ```

## argparse

```python
import argparse

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="TBD")
    parser.add_argument(
        "-s", "--string",
        type="str",
        default="this is a string",
        help="put a string here"
    )
```

`type="str"` is not necessary because str is the default type.

```python
    parser.add_argument(
        "-s", "--string", default="this is a string", help="put a string here"
    )
```
