---

---

Setup local [[LLM]] in a [[Samsung]] phone.

Hardware: Note 20 Ultra
Memory: 10.6GB (12GB with [[swap memory]])
CPU: 1?

Model: [[Llama]]

# Hardware Constraints & Model Scaling (10.6GB RAM Ceiling)

- **System Baseline:** ~4GB ([[Android OS]], background services, SSH).
- **1.5B Model:** Uses ~5-5.5GB total peak RAM. Very snappy and safe.
- **7B Model:** Pushed system usage to ~7.8GB/10.6GB, causing noticeable generation lag, thermal throttling, and leaving dangerously low headroom.
- **3B Model (`llama3.2:3b` / `qwen2.5-coder:3b`):** The ideal sweet spot (~6.5GB total usage), balancing code generation capability with stable mobile performance and over 4GB of free headroom.


# Multi-Model Best Practices

- Running multiple models simultaneously on mobile causes excessive RAM pressure and risks Out-Of-Memory (OOM) Android crashes.
- Best practice for mobile hardware is to run **one active model at a time**. [[Ollama]] automatically unloads models after a period of inactivity.

# Manual Model Management & APIs

- **Unload a model instantly:**
  curl http://localhost:11434/api/generate -d '{"model": "<model_name>", "keep_alive": 0}'
- **Track token usage:** Use `ollama run <model> --verbose` in the [[CLI]].
- **OpenAI Compatibility:** [[Ollama]] provides native [[OpenAI-compatible endpoints]] at `http://localhost:11434/v1/` for drop-in integration with standard [[SDKs]] and tools.

# Log

- Quite ez to do. Just need [[Termux]] and setup [[Termux boot]] to start the app if phone restarted
- Install [[Termux service]].
	- Termux-services writes `runit` (`sv`) stdout/stderr directly to log files.
	- View live logs: `tail -f $PREFIX/var/log/sv/<service>/current`
- Host with [[Ollama]]
- Enable [[SSH server]] and [[ssh]] to it to [[Termux]] session.
