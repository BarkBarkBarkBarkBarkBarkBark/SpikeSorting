when installing packages, the Jetson uses a broken link

flag all pip installations like this

```
pip install pyqtgraph \
  --no-cache-dir \
  --index-url https://pypi.org/simple
```