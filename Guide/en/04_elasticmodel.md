# ElasticModel

Making JSON work like ActiveRecord - simple and transparent

ElasticModel allows storing structured data in JSON while manipulating them as classic PHP properties. This technical layer combines JSON flexibility with Yii2 ActiveRecord power, thus offering a data model that can evolve without breaking existing code.

Concretely, ElasticModel solves the classic CMS problem: how to manage different content types without multiplying tables or creating unused columns. The solution rests on three pillars: flexible JSON storage, validation via JSON Schema, and transparent manipulation via ActiveRecord.

> **Important**
> 
> ElasticModel addresses very specific use cases in Blackcube. For more general JSON storage needs, prefer a document-oriented database.

## Why ElasticModel?

### The Problem with Traditional CMS

In a CMS, managing different content types poses an architectural problem:

**Option 1: One table per content type**
- `articles` table, `products` table, `testimonials` table, etc.
- Complex to maintain when you have dozens of types
- Duplicated code for each type

**Option 2: Multiple columns in a single table**
- Columns `field1`, `field2`... `field50`
- Used or not depending on type
- Confusion: which column for which type?
- Space waste

**Option 3: Store HTML**
- Flexible but not structured
- Impossible to validate
- No field search
- Security risks

**Option 4: ElasticModel**
- Single table with structured JSON
- Schema defined by type
- Automatic validation
- Structure maintained

### The Solution: Elastic Model

ElasticModel combines:

1. **JSON** to store data flexibly
2. **JSON Schema** to validate structure according to type
3. **Yii2 ActiveRecord** to manipulate like classic code

Each content type has its own structure, stored consistently, without multiplying tables or columns.

### Concrete Evolution Example

Version 1 - "contact" structure:

```json
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string"
    },
    "email": {
      "type": "string",
      "format": "email"
    }
  },
  "required": ["name", "email"]
}
```

Existing data:

```json
{
  "name": "Marie Dupont",
  "email": "marie@example.com"
}
```

Version 2 - Adding a phone:

```json
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string"
    },
    "email": {
      "type": "string",
      "format": "email"
    },
    "phone": {
      "type": "string"
    }
  },
  "required": ["name", "email"]
}
```

Old data remains valid. The `phone` field is optional. The code continues to work, `$item->phone` returns `null` for old records.

> **Warning**
>
> Backward compatibility only works one way: adding optional fields. If a field is removed from the schema, even if the data still exists in the database, the property is no longer accessible via ElasticModel.
>
> Example: schema v1 with `username` and `email`, schema v2 without `username`. Even if old data still contains `username` in the database, `$bloc->username` will throw an exception because the field is no longer declared in the schema.

To handle these cases, check attribute existence:

```php
if ($bloc->hasAttribute('username')) {
    echo $bloc->username;
} else {
    // The field is no longer in the current schema
}
```

### Concrete Advantages

1. **Elastic model**: Structure can grow without touching code
2. **Backward compatibility**: Old data continues to work
3. **Automatic validation**: Each save checks schema compliance
4. **Flexibility**: Add/remove fields at will

## How Does It Work?

### Schema Defines Structure

A JSON Schema describes the expected data structure:

```json
{
  "type": "object",
  "properties": {
    "title": {
      "type": "string"
    },
    "email": {
      "type": "string",
      "format": "email"
    }
  }
}
```

### Data is Stored in JSON

In the database, a `data` field contains actual values:

```
table_example
├─ id : 1
├─ schemaId : 5
└─ data : {"title": "Contact", "email": "contact@example.com"}
```

### Manipulable as an ActiveRecord

ElasticModel transforms JSON into PHP properties accessible naturally:

```php
$item = Item::findOne(1);

// Reading
echo $item->title;  // "Contact"
echo $item->email;  // "contact@example.com"

// Writing
$item->title = "New title";
$item->email = "new@example.com";
$item->save();
```

Under the hood:

1. `$item->title` reads from `data->title`
2. `$item->title = "..."` modifies `data->title`
3. `$item->save()` validates against JSON Schema then saves

### Queries on JSON Data

ElasticModel allows querying directly in JSON:

```php
// Search by email
$items = ElasticModel::find()
    ->andWhere(['email' => 'johndoe@example.com'])
    ->all();

// Search by name
$items = ElasticModel::find()
    ->andWhere(['name' => 'John Doe'])
    ->all();

// Combine multiple conditions
$items = ElasticModel::find()
    ->andWhere(['active' => true])
    ->andWhere(['email' => 'johndoe@example.com'])
    ->all();
```

Unlike raw HTML, data remains queryable even in JSON.

## JSON Schema: Automatic Validation

### What is JSON Schema?

JSON Schema is a standard for describing and validating JSON structures. It defines:

- Expected properties
- Their types (string, number, boolean, etc.)
- Constraints (required, format, length, etc.)

### Complete Schema Example

```json
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string",
      "minLength": 3,
      "maxLength": 100
    },
    "email": {
      "type": "string",
      "format": "email"
    },
    "description": {
      "type": "string"
    },
    "active": {
      "type": "boolean",
      "default": false
    }
  },
  "required": ["name", "email"]
}
```

This schema says:

- `name`: required string, between 3 and 100 characters
- `email`: required email, automatically validated
- `description`: optional string
- `active`: optional boolean, false by default

### Validation at Save

When data is saved, Blackcube:

1. Retrieves the associated JSON Schema
2. Validates entered data
3. If valid: `active = 1`, data is usable
4. If invalid: `active = 0`, data remains draft

Failed validation example:

```json
{
  "name": "AB",
  "email": "contact@example.com"
}
```

Error: `name` is 2 characters, minimum required = 3. Data remains `active = 0` until correction.

## Special Formats

### Formats Supported by JSON Schema

JSON Schema supports several validation formats:

- `"format": "email"`: Email validation
- `"format": "uri"`: Valid URL
- `"format": "date"`: Date in ISO format
- `"format": "date-time"`: Date + time
- `"format": "ipv4"`: IPv4 address
- `"format": "ipv6"`: IPv6 address

Example:

```json
{
  "email": {
    "type": "string",
    "format": "email"
  },
  "website": {
    "type": "string",
    "format": "uri"
  }
}
```

### Blackcube Specific Format: file

Blackcube adds a `file` format for uploaded files:

```json
{
  "avatar": {
    "type": "string",
    "format": "file"
  }
}
```

The file is stored with a special prefix `@blackcubefs/`:

```json
{
  "avatar": "@blackcubefs/photo-2024.jpg"
}
```

## Use Cases

### When to Use ElasticModel?

Perfect for:

1. Variable structured content
2. Frequently evolving data
3. Different structures depending on context
4. Dynamic form validation

Concrete examples:

- Structured editorial content
- Configurable forms
- Application settings
- Dynamic configuration

### When NOT to Use ElasticModel?

Avoid for:

1. Complex relational data
2. Advanced SQL queries on data
3. Search indexes in JSON data
4. Many-to-many relationships in JSON

Examples where classic SQL is preferable:

- User tables
- E-commerce order system
- Product/category relationships
- Logs with advanced search

### Limitations to Know

1. **Performance**: JSON slower than native SQL columns for complex queries
2. **Indexes**: Index possible on some fields but not as performant as native SQL
3. **Relations**: Foreign keys don't work in JSON

ElasticModel uses MySQL/MariaDB JSON functions to make data queryable, which is an advantage over raw HTML.

## Application in Blackcube

ElasticModel is the technical layer that allows Blackcube to manage flexible structured content.

Blackcube uses ElasticModel to store structured data whose schema can evolve. The model automatically adapts when the structure grows.

In practice:

```php
// An element with its schema:
{
  "title": { "type": "string" },
  "description": { "type": "string" },
  "email": { "type": "string", "format": "email" }
}

// Manipulation via ElasticModel:
$item->title = "My element";
$item->description = "Detailed description";
$item->email = "contact@example.com";
$item->save();
```

The model is elastic: adding a `phone` field in the schema doesn't break existing code. `$item->phone` simply returns `null` for old records.

## Key Points to Remember

1. **Elastic model**: Structure can grow without questioning code
2. **JSON Schema**: Automatic structure validation
3. **Backward compatibility**: Old data continues to work
4. **Queryable**: JSON data is queryable like classic Yii2
5. **Transparent**: Developer manipulates PHP properties, not JSON
6. **Used by the bloc system**: Blackcube technical base
7. **Specific cases**: For general JSON storage, prefer a document-oriented database