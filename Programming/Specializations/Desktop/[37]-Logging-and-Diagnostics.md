[Previous](./[36]-Debugging-and-Profiling.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[38]-Building-and-Packaging-for-Release.md)

*Architecture & Best Practices*

# Lesson 37 - Logging & Diagnostics

## 37.1 Why Logging Matters for Desktop Apps

Unlike a server you control, a desktop app runs on machines you can't inspect directly — when a user reports "it crashed" or "it did something weird," logs are often the only window into what actually happened on their system. Good logging is an investment that pays off the first time it saves a support investigation.

---

## 37.2 Log Levels

Structure logs by severity so they can be filtered appropriately: `Trace`/`Debug` for detailed diagnostic output (usually off in release builds), `Info` for normal significant events (app started, document saved), `Warning` for recoverable but unexpected situations, and `Error`/`Fatal` for failures that need attention.

```csharp
logger.LogInformation("Document saved: {Path}", filePath);
logger.LogError(ex, "Failed to connect to database");
```

---

## 37.3 Where Logs Go

Desktop app logs typically write to a rotating file in the app's data directory (see Lesson 20 for platform-specific paths), so old logs don't grow unbounded and disk usage stays bounded. Frameworks like Serilog (.NET), `winston`/`electron-log` (Electron), and `log4rs`/`tracing` (Rust) handle rotation, formatting, and multiple output targets (file, console, remote service) out of the box.

---

## 37.4 Privacy in Logs

Never log sensitive data — passwords, tokens, full file contents, personally identifiable information — even at debug level, since logs may be shared with support or uploaded for crash analysis (Lesson 46). Redact or omit sensitive fields explicitly, and be intentional about what telemetry, if any, leaves the user's machine, disclosing it clearly if it does.

[Previous](./[36]-Debugging-and-Profiling.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[38]-Building-and-Packaging-for-Release.md)
