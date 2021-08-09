# Translator

Translator library with default `Json` and `Memory` driver.

## Requirements

### Translatable Structures

By default all translations read from config driver (json files or memory). Structures translations can resolved by structure itself and override global translations. For making structure translatable you must implement `Translatable` interface.

**Caution:** use non-pointer implemantation for struct!

```go
type Person struct {
    Name string
    Age  string
}

func (p Person) GetTranslation(locale string, key string) string {
    switch key {
    case "Name":
        if locale == "fa" {
            return "نام"
        } else {
            return "Name"
        }
    case "Age":
        if locale == "fa" {
            return "سن"
        } else {
            return "Age"
        }
    }
    return ""
}
```

## Create New Translator Driver

Translator library contains two different driver by default.

### Json Driver

JSON driver use json file for managing translations.

```go
// Signature:
NewJSONTranslator(fallbackLocale string, dir string) (Translator, error)

// Example:
import "github.com/bopher/translator"
jTrans, err := translator.NewJSONTranslator("en", "trans")
```

### Memory Driver

Use in-memory array for keep translations.

```go
// Signature:
NewMemoryTranslator(fallbackLocale string) Translator

// Example:
import "github.com/bopher/translator"
mTrans := translator.NewMemoryTranslator("en")
```

## Usage

Translator interface contains following methods:

### Register

Register new translation message for locale. Use placeholder in message for field name.

```go
// Signature:
Register(locale string, key string, message string)

// Example:
t.Register("en", "welcome", "Hello {name}, welcome!")
```

### Resolve

Resolve find translation for locale. If no translation found for locale return fallback translation or `""`.

```go
// Signature:
Resolve(locale string, key string) string

// Example:
trans := t.Resolve("en", "welcome") // Hello {name}, welcome!
```

### ResolveStruct

Find translation from translatable. If empty string returned from translatable or struct not translatable, default translation will resolved.

```go
// Signature:
ResolveStruct(s interface{}, locale string, key string) string

// Example:
type Person struct {
    Name string
    Age  string
}

func (p Person) GetTranslation(locale string, key string) string {
    if key == "greeting" {
        if locale == "fa" {
            return "روزبخیر {name}"
        }else{
            return "Greeting {name}"
        }
    }
    return ""
}


p := &Person{
    Name: "John",
    Age: 25,
}
t.ResolveStruct(p, "en", "greeting") // Greeting {name}
t.ResolveStruct(p, "en", "welcome") // Hello {name}, welcome!
t.ResolveStruct(p, "en", "non_exists") // ""
```

### Translate

Translate get translation for locale.

```go
// Signature:
Translate(locale string, key string, placeholders map[string]string) string

// Example:
t.Translate("en", "welcome", map[string]string{ "name": "John" }) // Hello John, welcome!
```

### TranslateStruct

Translate using translatable interface. if empty string returned from translatable or struct not translatable, default translation will resolved.

```go
// Signature:
TranslateStruct(s interface{}, locale string, key string, placeholders map[string]string) string

// Example
t.TranslateStruct(p, "en", "greeting", map[string]string{ "name": "John" }) // Greeting John
```
