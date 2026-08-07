---
url: https://docs.gorules.io
---

Decision engine

# What is GoRules?

- Despite the name, **not written in [[Go]]** — the core engine (**[[ZEN Engine]]**) is written in **[[Rust]]**.
- It's a **Business Rules Engine / Decision Model** system: define rules as a [[JSON]] graph ([[JDM|JDM — JSON Decision Model]]), evaluate them against input data.
- SDKs available for [[Java]], [[NodeJS]], [[Python]], [[Go]], [[C#]], [[Kotlin]], [[Swift]], [[Rust]]  all wrapping the same [[Rust]] core via native bindings ([[JNI]] for [[Java]]) or [[WASM]] (for browser).
- Free hosted visual editor: https://editor.gorules.io
- Self-hosted React editor component: `@gorules/jdm-editor`

---
 
# Architecture (what talks to what)
 
```
React (jdm-editor) --REST--> Spring Boot --JNI--> ZEN Engine (Rust core)
        |
        +--(optional) local WASM eval for pure-client simulation
```
 
- **No separate service/daemon to install.** The [[Java]] [[SDK]] (`io.gorules:zen-engine`) ships prebuilt native binaries inside the jar — [[Maven]]/[[Gradle]] just downloads it like any dependency.
- Rules ([[JDM]] [[JSON]]) can be **stored dynamically** (e.g. in [[Postgres]], or in-memory for prototyping) and edited by end users via the `jdm-editor` React component — this is a first-class use case, not a hack.
---
 
# Local Setup — [[mise]].toml
 
```toml
[tools]
java = "25"
node = "22"
gradle = "8.11"
```
 
# Backend scaffold ([[Spring-boot]] 4, [[Gradle]]/[[Groovy]], [[Java]] 25)
 
```bash
spring init \
  --dependencies=web \
  --type=gradle-project \
  --build=gradle \
  --java-version=25 \
  --boot-version=4.0.0 \
  --language=java \
  --group-id=com.example \
  --artifact-id=myapp-backend \
  --name=myapp-backend \
  .
```

## build.gradle dependency

```groovy
dependencies {
    implementation 'io.gorules:zen-engine:0.7.2'
}
```
 
## Import dependency

```java
import io.gorules.zen_engine.ZenEngine
```

## [[ZEN Engine]] bean

```java
@Configuration
public class ZenEngineConfig {
    @Bean
    public ZenEngine zenEngine() {
        return new ZenEngine(null, null);
    }
}
```
 
## In-memory Rule CRUD (index = ID, empty by default)

```java
@RestController
@RequestMapping("/api/rules")
public class RuleController {
 
    private final ZenEngine zenEngine;
    private final List<Rule> rules = new ArrayList<>(); // empty by default
 
    public RuleController(ZenEngine zenEngine) {
        this.zenEngine = zenEngine;
    }
 
    @GetMapping
    public List<Rule> getAll() { return rules; }
 
    @PostMapping
    public Rule create(@RequestBody Rule rule) {
        rule.setId((long) rules.size());
        rules.add(rule);
        return rule;
    }
 
    @PutMapping("/{id}")
    public Rule update(@PathVariable long id, @RequestBody Rule updated) {
        Rule existing = findByIdOrThrow(id);
        existing.setName(updated.getName());
        existing.setDecision(updated.getDecision());
        return existing;
    }
 
    @DeleteMapping("/{id}")
    public void delete(@PathVariable long id) {
        rules.remove(findByIdOrThrow(id));
    }
 
    private Rule findByIdOrThrow(long id) {
        return rules.stream().filter(r -> r.getId() == id).findFirst()
                .orElseThrow(() -> new RuntimeException("Rule not found: " + id));
    }
}
```
`Rule` = simple POJO: `id (Long)`, `name (String)`, `decision (String — raw JDM JSON)`.
 
## Simulate endpoint ([[async]] — evaluate returns a `CompletableFuture`)

```java
@PostMapping("/{id}/simulate")
public CompletableFuture<SimulationRes> simulate(@PathVariable long id, @RequestBody String inputJson) {
    Rule rule = findByIdOrThrow(id);
    ZenDecision decision = zenEngine.createDecision(rule.getDecision());
 
    JsonBuffer context = inputJson.toString();
    ZenEvaluateOptions options = new ZenEvaluateOptions((byte) 10, true); // trace = true
 
    return decision.evaluate(context, options)
            .thenApply(response -> simulationResMapper.toSimulationRes(
                    response.performance(),
                    response.result().toString(),
                    response.trace()
            ));
}
```
 
## Mapping raw ZEN response → clean DTO ([[Spring Boot]] 4 / [[Jackson]] 3)

> [[Spring-boot]] 4 defaults to **[[Jackson]] 3**: `ObjectMapper` → immutable `JsonMapper`, package moves from `com.fasterxml.jackson` → `tools.jackson`. `JsonMapper` autowires as a Spring bean — no manual config needed.
 
```java
public record SimulationRes(
    String performance,
    JsonNode result,
    Map<String, TraceEntry> trace
) {
    public record TraceEntry(
        String id, String name, JsonNode input, JsonNode output,
        String performance, JsonNode traceData, Integer order
    ) {}
}
 
@Component
public class SimulationResMapper {
    private final JsonMapper jsonMapper; // tools.jackson.databind.json.JsonMapper
 
    public SimulationResMapper(JsonMapper jsonMapper) { this.jsonMapper = jsonMapper; }
 
    public SimulationRes toSimulationRes(String performance, String resultJson, Map<String, ZenEngineTrace> trace) {
        JsonNode resultNode = jsonMapper.readTree(resultJson); // unchecked exception in Jackson 3, no try/catch needed
        Map<String, SimulationRes.TraceEntry> traceMap = new LinkedHashMap<>();
        if (trace != null) {
            trace.forEach((key, t) -> traceMap.put(key, new SimulationRes.TraceEntry(
                    t.id(), t.name(), toJsonNode(t.input()), toJsonNode(t.output()),
                    t.performance(), toJsonNode(t.traceData()), t.order()
            )));
        }
        return new SimulationRes(performance, resultNode, traceMap);
    }
 
    private JsonNode toJsonNode(JsonBuffer buffer) {
        return buffer == null ? null : jsonMapper.readTree(buffer.toString());
    }
}
```
 
---
 
# Frontend scaffold ([[Vite]] + [[React]] + [[Typescript]])
 
```bash
npm create vite@latest . -- --template react-ts
npm install @gorules/jdm-editor antd @ant-design/icons
```
- `antd` is a **required peer dependency** of jdm-editor (used internally for panels/UI).

## vite.config.ts — proxy to backend

```ts
export default defineConfig({
  plugins: [react()],
  server: {
    proxy: { '/api': { target: 'http://localhost:8080', changeOrigin: true } },
  },
});
```
 
## Minimal usage — editor + Run/Simulate panel

```tsx
import { DecisionGraph, GraphSimulator, JdmConfigProvider } from '@gorules/jdm-editor';
import { PlayCircleOutlined } from '@ant-design/icons';
import '@gorules/jdm-editor/dist/style.css';
import 'antd/dist/reset.css';
 
<JdmConfigProvider>
  <DecisionGraph
    value={graph}
    onChange={setGraph}
    simulate={simulation}
    // defaultActivePanel="simulator"
    panels={[{
      id: 'simulator',
      title: 'Simulator',
      icon: <PlayCircleOutlined />,
      renderPanel: () => (
        <GraphSimulator
          onRun={async ({ graph: runGraph }) => {
            // 1. save rule first, 2. call /simulate, 3. feed result back in
            const res = await fetch(`/api/rules/${savedId}/simulate`, {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: contextInputJsonString, // user-provided input JSON
            });
            const result = await res.json();
            setSimulation({ result: { ...result, snapshot: runGraph } });
          }}
          onClear={() => setSimulation(undefined)}
        />
      ),
    }]}
  />
</JdmConfigProvider>
```
 
**Key insight on "live" simulation:** it's **not** streaming/socket-based. One request → one full evaluation → full trace comes back → editor renders the trace as a static overlay on the graph. No true node-by-node live execution hook exists in the SDK.
 
## Manual Upload / Download [[JSON]] (bypass built-in upload — it fails silently)

```tsx
import { decisionModelSchema } from '@gorules/jdm-editor/dist/schema';
 
// Upload
const handleUploadClick = () => {
  const input = document.createElement('input');
  input.type = 'file';
  input.accept = 'application/json';
  input.onchange = async () => {
    const file = input.files?.[0];
    if (!file) return;
    const content = await file.text();
    let parsed: unknown;
    try { parsed = JSON.parse(content); }
    catch (e) { console.error('Invalid JSON syntax:', e); return; }
 
    const result = decisionModelSchema.safeParse(parsed);
    if (!result.success) { console.error('Invalid decision file:', result.error); return; }
    setGraph(result.data);
  };
  input.click();
};
 
// Download
const handleDownload = () => {
  const blob = new Blob([JSON.stringify(graph, null, 2)], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `${name || 'decision'}.json`;
  a.click();
  URL.revokeObjectURL(url);
};
```
 
---
 
# Key gotchas / lessons learned

1. **[[async]] evaluate:** `ZenDecision.evaluate()` returns `CompletableFuture<ZenEngineResponse>` — [[Spring MVC]] handles `CompletableFuture` return types from `@RequestBody` controllers natively, no extra config.
2. **[[Jackson]] 3 in [[Spring-boot]] 4:** inject `JsonMapper` (not `ObjectMapper`), package is `tools.jackson.*` not `com.fasterxml.jackson.*` for the databind/core parts (annotations like `@JsonProperty` stay in `com.fasterxml` for compat).
3. **[[JDM]] Editor's built-in file upload gives no error feedback on failure** — build your own with `decisionModelSchema.safeParse()` + `console.error` for visibility.
4. **Simulator ≠ live/streaming** — it's request/response; the graph trace is rendered after the full evaluation completes.
5. **`configurable={false}`** is the documented prop most likely controlling the left toolbar/node-palette — not 100% confirmed at time of writing, verify behavior (may also disable editing).
