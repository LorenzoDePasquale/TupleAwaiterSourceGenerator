# Tuple Awaiter Source Generator

A C# source generator that enables directly awaiting on tuples of `Task<T>` and `ValueTask<T>`.

## Features

- **Direct tuple awaiting**: Await tuples of tasks without explicit `Task.WhenAll()` calls
- **Mixed task types**: Support for both `Task<T>` and `ValueTask<T>` in the same tuple
- **Configurable awaiter**: Support for both `ConfigureAwait(bool)` and `ConfigureAwait(ConfigureAwaitOptions)`
- **Type safety**: Preserves strong typing with proper generic constraints
- **Zero runtime overhead**: Code generation at compile time with no runtime performance impact
- **Automatic detection**: Generates extension methods only for tuple patterns actually used in your code

## Usage

Instead of writing:

```csharp 
var task1 = GetStringAsync(); 
var task2 = GetIntAsync(); 
var task3 = GetBoolAsync();
await Task.WhenAll(task1, task2, task3); 
var result1 = task1.Result; 
var result2 = task2.Result; 
var result3 = task3.Result;
```

You can now write:

```csharp 
var (result1, result2, result3) = await (GetStringAsync(), GetIntAsync(), GetBoolAsync());
```

The generator supports mixing `Task<T>` and `ValueTask<T>` in the same tuple:

```csharp 
var (result1, result2) = await ( /* Task */ Operation1(), /* ValueTask */ Operation2());
```

It's also possible to configure the awaiter:

```csharp 
var (result1, result2) = await (Operation1(), Operation2()).ConfigureAwait(false);
// ConfigureAwaitOptions is supported too
var (result1, result2) = await (Operation1(), Operation2()).ConfigureAwait(ConfigureAwaitOptions.ContinueOnCapturedContext);
```

## How It Works

The source generator:

1. **Analyzes the code** for `await` expressions of tuples
2. **Detects task types** in the tuple elements (`Task<T>` or `ValueTask<T>`)
3. **Generates extension methods** for the specific tuple patterns you use
4. **Creates custom awaiters** that handle the concurrent execution using `Task.WhenAll()`

## Requirements

- .NET 8.0 or higher
- C# 9.0 or higher (for tuple syntax support)
