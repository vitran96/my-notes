# Import history to [[Zim]]

```bash
awk '{print ": 0:0;"$0}' ~/.bash_history >> ~/.zhistory
```