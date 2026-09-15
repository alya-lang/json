# alya-lang/json

[![CI](https://github.com/alya-lang/json/actions/workflows/ci.yml/badge.svg)](https://github.com/alya-lang/json/actions/workflows/ci.yml)
[![License](https://img.shields.io/github/license/alya-lang/json?color=blue&label=License)](LICENSE)
[![Alya](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fjson%2Fmain%2Falya.toml&query=%24.package.alya-version&label=Alya&color=orange&prefix=%3E%3D)](https://github.com/alya-lang/alya)
[![Package Version](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fjson%2Fmain%2Falya.toml&query=%24.package.version&label=Version&color=brightgreen)](alya.toml)

High-performance RFC 8259 JSON parser, recursive serializer, JSONPath, JSON Schema validator, NDJSON streaming, and fluent builder for the Alya programming language.

---

## 🌟 Features

- ⚡ **RFC 8259 Compliant Parser**: Full support for objects, arrays, escaped strings, integers, floating-point numbers, booleans, and null.
- 🚀 **Blazing Fast Microsecond Speeds**: Sub-5µs parse times with throughput up to 220,000+ ops/sec.
- 🔍 **JSONPath & JSON Pointer (RFC 6901)**: Query nested structures easily using dot notation (`store.book.price`), wildcard array indexing (`users[*].id`), and pointer paths (`/users/0/name`).
- 🛡️ **JSON Schema Validator**: Draft-07 inspired schema validator supporting `type`, `required`, `properties`, `minimum`, `maximum`, `minLength`, `maxLength`, `enum`, `items`, and `additionalProperties`.
- 🌊 **NDJSON Streaming**: Line-delimited JSON parsing, serialization, and stream counting for log aggregation and big data pipelines.
- 🛠️ **Fluent JSON Builder**: Type-safe programmatically constructed JSON objects and arrays via chaining.
- 🎨 **Formatters & Minifier**: Native `pretty` printing with configurable indentation levels and whitespace-stripping `minify`.

---

## 📁 Project Architecture

```
json/
├── alya.toml               # Package manifest
├── src/
│   ├── lib.alya            # Public API facade
│   ├── types.alya          # Core struct definitions (ValidationResult, JsonBuilder)
│   └── core/
│       ├── utils.alya      # String escaping, unescaping, and character helpers
│       ├── parser.alya     # Recursive descent RFC 8259 tokenizer & parser
│       ├── serializer.alya # Serializer, pretty printer, minifier, and formatters
│       ├── jsonpath.alya   # JSONPath and RFC 6901 JSON Pointer evaluator
│       ├── schema.alya     # Comprehensive JSON Schema validation engine
│       ├── ndjson.alya     # Line-delimited streaming parser and generator
│       └── builder.alya    # Fluent JSON builder implementation
├── examples/
│   └── demo.alya           # Full runnable showcase demo
├── tests/
│   ├── test_basic.alya     # Parser & core types test suite (34 tests)
│   ├── test_serializer.alya# Serializer, minifier & pretty printer test suite (15 tests)
│   ├── test_jsonpath.alya  # JSONPath & JSON Pointer test suite (13 tests)
│   ├── test_schema.alya    # JSON Schema validation test suite (11 tests)
│   ├── test_ndjson.alya    # Streaming NDJSON test suite (10 tests)
│   └── test_builder.alya   # Fluent builder test suite (14 tests)
└── benches/
    └── bench_basic.alya    # Performance micro-benchmarks
```

---

## 📦 Installation

Add `json` to the `[dependencies]` section in your `alya.toml`:

```toml
[dependencies]
json = { git = "https://github.com/alya-lang/json", branch = "main" }
```

Or install it directly using the Alya package CLI:

```bash
alyac add json --git https://github.com/alya-lang/json --branch main
alyac install
```

---

## 🚀 Quick Start

```alya
import "json" as json

function main()
    # 1. Parse JSON payload
    let raw = "{\"id\": 101, \"title\": \"Alya Guide\", \"active\": true}"
    let doc = json::parse(raw)
    say "ID: " + str(doc["id"])
    say "Title: " + str_from_ptr(doc["title"])

    # 2. Query with JSONPath
    let title = json::query(doc, "title")
    say "Queried Title: " + str_from_ptr(title)

    # 3. Validate with JSON Schema
    let schema = json::parse("{\"type\":\"object\",\"required\":[\"id\"]}")
    let res = json::validate(doc, schema)
    say "Valid: " + str(res.res_valid)

    # 4. Pretty Print & Minify
    let formatted = json::pretty(raw, 2)
    say formatted

    let compact = json::minify(formatted)
    say compact
end

main()
```

---

## 📖 API Reference

### Parsing & Inspection
| Function | Arguments | Returns | Description |
|---|---|---|---|
| `parse(text)` | `text: string` | `Map \| Array \| Scalar` | Parses RFC 8259 JSON payload into native Alya types. |
| `parse_object(text)` | `text: string` | `Map` | Parses JSON string guaranteed to be a root object. |
| `parse_array(text)` | `text: string` | `Array` | Parses JSON string guaranteed to be a root array. |
| `is_valid(text)` | `text: string` | `int (0 \| 1)` | Fast syntax check without constructing intermediate structures. |

### Serialization & Formatting
| Function | Arguments | Returns | Description |
|---|---|---|---|
| `stringify(val)` | `val: any` | `string` | Serializes scalar, array, or object into JSON format. |
| `pretty(val, indent)` | `val: string, indent = 2` | `string` | Formats JSON text with visual hierarchy and indentation. |
| `minify(text)` | `text: string` | `string` | Strips extraneous whitespace while preserving string content. |
| `json_object(m)` | `m: Map` | `string` | Serializes a native Map to JSON object string. |
| `json_array(arr)` | `arr: Array` | `string` | Serializes a native Array to JSON array string. |

### Querying (JSONPath & JSON Pointer)
| Function | Arguments | Returns | Description |
|---|---|---|---|
| `query(data, path)` | `data, path: string` | `any` | Evaluates dot notation (`a.b.c`) and wildcard paths (`items[*].id`). |
| `pointer(data, ptr)` | `data, ptr: string` | `any` | Resolves RFC 6901 JSON Pointer path (e.g. `/users/0/name`). |

### JSON Schema Validation
| Function | Arguments | Returns | Description |
|---|---|---|---|
| `validate(data, schema)` | `data, schema` | `ValidationResult` | Validates data against schema returning validity flag and error list. |
| `is_valid_schema(data, schema)` | `data, schema` | `int (0 \| 1)` | Returns 1 if valid, 0 otherwise. |

### NDJSON Streaming
| Function | Arguments | Returns | Description |
|---|---|---|---|
| `ndjson_read(text)` | `text: string` | `Array<Map>` | Parses newline-delimited JSON stream into array of records. |
| `ndjson_write(records)` | `records: Array` | `string` | Serializes record array into newline-delimited JSON stream. |
| `ndjson_count(text)` | `text: string` | `int` | Efficiently counts valid non-empty records in stream. |

### Fluent Builder
| Function | Arguments | Returns | Description |
|---|---|---|---|
| `builder_object()` | - | `JsonBuilder` | Initializes a new fluent JSON object builder. |
| `builder_array()` | - | `JsonBuilder` | Initializes a new fluent JSON array builder. |
| `jb_set_string(b, k, v)` | `b, key, val` | `JsonBuilder` | Sets string property on object builder. |
| `jb_set_int(b, k, v)` | `b, key, val` | `JsonBuilder` | Sets integer property on object builder. |
| `jb_set_bool(b, k, v)` | `b, key, val` | `JsonBuilder` | Sets boolean property on object builder. |
| `jb_build(b)` | `b: JsonBuilder` | `Map \| Array` | Finalizes builder into native Alya data structures. |
| `jb_to_json(b)` | `b: JsonBuilder` | `string` | Finalizes and serializes builder directly to JSON string. |

---

## ⚡ Performance Benchmarks

Measured on Windows 11 (AMD64, release build):

| Method | Mean (ns/op) | Throughput | Description |
|---|---|---|---|
| **JSON Parser** | 4.9 µs | 204,000 ops/s | Full parsing of nested JSON payload |
| **Fast Validation** | 4.5 µs | 219,000 ops/s | `is_valid` structural validation |
| **JSON Serializer** | 100 ns | 10,000,000 ops/s | Fast map/array serialization |
| **JSON Minifier** | 1.7 µs | 574,000 ops/s | Whitespace extraction |
| **JSONPath Query** | 350 ns | 2,850,000 ops/s | Nested dot-notation traversal |
| **Schema Validation** | 2.6 µs | 384,000 ops/s | Schema constraint & type evaluation |
| **NDJSON Parser** | 3.5 µs | 285,000 ops/s | Streaming line-delimited records |

---

## 🧪 Running Tests & Benchmarks

Run the entire automated test suite:

```bash
alyac test
```

Run individual test suites:

```bash
alyac run tests/test_basic.alya
alyac run tests/test_serializer.alya
alyac run tests/test_jsonpath.alya
alyac run tests/test_schema.alya
alyac run tests/test_ndjson.alya
alyac run tests/test_builder.alya
```

Run benchmarks:

```bash
alyac run benches/bench_basic.alya
```

Run the showcase demo:

```bash
alyac run examples/demo.alya
```

Check source code formatting:

```bash
alyac fmt . --check
```

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository and clone it locally
2. Create your feature branch (`git checkout -b feat/my-feature`)
3. Ensure all tests and formatting pass:
   ```bash
   alyac test
   alyac fmt . --check
   ```
4. Commit your changes (`git commit -m "feat: add feature"`)
5. Push to the branch and open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.