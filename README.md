# llm-meter

Terminal-based monitoring tool for LLM API usage and costs

```
cargo install llm-meter
```

```rust
use llm_meter::{Meter, Provider};

let mut meter = Meter::new();
meter.track(Provider::OpenAI, "gpt-4", 1500, 450);
meter.track(Provider::Anthropic, "claude-3-opus", 2000, 600);

println!("{}", meter.summary());
```

## notes

Watches API calls in real time, calculates spend based on token counts and current pricing. Supports OpenAI, Anthropic, Cohere, and custom providers.

Config lives in `~/.config/llm-meter/config.toml`. Add your own pricing or override defaults there.

```toml
[providers.openai]
gpt-4 = { input = 0.03, output = 0.06 }
gpt-3.5-turbo = { input = 0.0005, output = 0.0015 }

[providers.custom]
my-model = { input = 0.01, output = 0.02 }
```

Run as daemon to monitor all API traffic:

```rust
// Hook into HTTP client
use llm_meter::intercept::HttpInterceptor;

let interceptor = HttpInterceptor::new()
    .openai_key("sk-...")
    .anthropic_key("sk-ant-...")
    .start_daemon()?;

// Automatically tracks all requests
client.post("https://api.openai.com/v1/chat/completions")
    .send()?;

// View stats anytime
interceptor.meter().display();
```

Or use CLI directly:

```
llm-meter start              # start daemon
llm-meter stats              # show current usage
llm-meter stats --daily      # daily breakdown
llm-meter export costs.csv   # export data
llm-meter set-budget 100     # alert at $100
```

MIT
<!-- fix cleanup -->
