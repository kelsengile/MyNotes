[Previous](./[20]-Application-Settings-and-Configuration.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[22]-Background-Tasks-and-Threading.md)

*Data & Storage*

# Lesson 21 - Working with JSON & Serialization

## 21.1 Why Serialize

Serialization converts an in-memory object into a storable/transmittable format (usually text), and deserialization converts it back. Desktop apps serialize constantly: saving settings, writing a document format, sending data through IPC (Lesson 29), or caching data fetched from a web API.

---

## 21.2 JSON Basics

JSON (JavaScript Object Notation) is the most common serialization format thanks to its readability and wide library support:

```json
{
  "title": "My Document",
  "wordCount": 1520,
  "tags": ["draft", "personal"],
  "lastEdited": "2026-08-20T10:00:00Z"
}
```

```csharp
string json = JsonSerializer.Serialize(document);
Document doc = JsonSerializer.Deserialize<Document>(json);
```

---

## 21.3 Handling Schema Evolution

A document format saved by version 1.0 of your app must still be readable by version 2.0. Common strategies: give new fields sensible defaults so old files without them still deserialize; include a `version` field in the saved data and branch your loading logic on it; avoid renaming existing fields — add new ones and deprecate old ones gradually instead of breaking compatibility outright.

---

## 21.4 Beyond JSON

JSON isn't always the right choice — binary formats (Protocol Buffers, MessagePack) are more compact and faster to parse for performance-sensitive IPC or large datasets, while XML remains common for legacy or document-heavy formats. Choose based on whether you need human readability (JSON/XML) or raw performance and size (binary formats).

[Previous](./[20]-Application-Settings-and-Configuration.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[22]-Background-Tasks-and-Threading.md)
