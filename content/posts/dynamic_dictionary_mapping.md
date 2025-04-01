+++
title = '🔄 Simplifying Python Code with Dynamic Dictionary Lookups 🐍'
date = 2025-04-02T00:20:04+05:30
draft = false
tags = ['python', 'dictionary']
+++


## 🌟 Introduction
To simplify Python code by replacing conditional logic with dictionary lookups, you can use dictionaries to map keys to functions or values. Here's how to implement this technique:

## 📊 Basic Example

### ❌ Before (using if/else):
```python
def handle_status(code):
    if code == 200:
        return "OK"
    elif code == 404:
        return "Not Found"
    elif code == 500:
        return "Server Error"
    else:
        return "Unknown Status"
```

### ✅ After (using dictionary lookup):
```python
def handle_status(code):
    status_mapping = {
        200: "OK",
        404: "Not Found",
        500: "Server Error"
    }
    return status_mapping.get(code, "Unknown Status")
```

## 🔄 Function Dispatch Example

### ❌ Before (using if/else):
```python
def process_command(command):
    if command == "start":
        start_engine()
    elif command == "stop":
        stop_engine()
    elif command == "restart":
        restart_engine()
    else:
        print("Unknown command")
```

### ✅ After (using dictionary of functions):
```python
def process_command(command):
    command_handlers = {
        "start": start_engine,
        "stop": stop_engine,
        "restart": restart_engine
    }
    
    handler = command_handlers.get(command)
    if handler:
        handler()
    else:
        print("Unknown command")
```

## 🧩 More Advanced Example with Parameters

### ❌ Before (match/case):
```python
def calculate(operator, x, y):
    match operator:
        case "+":
            return x + y
        case "-":
            return x - y
        case "*":
            return x * y
        case "/":
            return x / y
        case _:
            raise ValueError("Unknown operator")
```

### ✅ After (dictionary with lambdas):
```python
def calculate(operator, x, y):
    operations = {
        "+": lambda a, b: a + b,
        "-": lambda a, b: a - b,
        "*": lambda a, b: a * b,
        "/": lambda a, b: a / b
    }
    
    if operator not in operations:
        raise ValueError("Unknown operator")
    
    return operations[operator](x, y)
```

## 💡 Benefits of This Approach
- 📝 **Cleaner code**: Removes repetitive if/else structures
- 🛠️ **Easier maintenance**: All mappings are in one place
- 🔌 **Extensibility**: New cases can be added by simply updating the dictionary
- ⚡ **Performance**: Dictionary lookups are typically faster than chains of if/else

## 🤔 When to Use This Pattern
- 🎯 When you have multiple conditions mapping to simple values or functions
- 🟰 When the conditions are based on equality checks (not ranges or complex logic)
- ⚙️ When you want to make the mapping easily configurable or modifiable

---

💭 **Remember** that this approach works best for simple mappings. Complex conditional logic with different conditions (like ranges, multiple variables, etc.) may still require if/else statements.


