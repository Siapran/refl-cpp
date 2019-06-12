# refl-cpp (v0.2.0 beta) ([Documentation](https://veselink1.github.io/refl-cpp/namespacerefl.html))
A static reflection library for modern C++ 🔯🔥

## Synopsis
ReflCpp allows static reflection and inspection of types in C++ with full support for *templated types and functions*! The metadata required for this is specified through the use of macros (but wait!). The macros require the user to only specify the type that is being reflected and only the names of the members that are of interest. ReflCpp has a small and well-organised API surface. 

Macro-based code-gen has been minimized as must as is possible to support all basic requirements. All higher-level operations are implemented as free types and function in one of the utility namespaces.

Minimum language standard: C++17

See examples/ for more usage examples.

Example metadata declaration:
```cpp
struct Point {
  float x, y;
};

REFL_TYPE(Point)
  REFL_FIELD(x)
  REFL_FIELD(y)
REFL_END
```
Basic usage example:
```cpp
  // Printing an object's properties: 
  Point pt{};
  for_each(refl::reflect(pt).members, [&](auto member) {
      std::cout << member.name << "=" << member(pt) << ";";
  }
  // Result: prints x=0;y=0;
  
  // Converting to an std::tuple: (Note the constexpr!)
  constexpr auto values = map_to_tuple(refl::reflect(pt).members, [&](auto member) {
      return member(pt);
  }
  // Result: values == std::tuple<int, int>{ 0, 0 };

```

## Metadata-generation macros 
```cpp

// Starts the declaration of Type's metadata.
// Must be followed by one of: REFL_END, REFL_FIELD, REFL_FUNC.
// Example: REFL_TYPE(Dog, bases<Animal>)
REFL_TYPE(Type, Attributes...)

// Starts the declaration of Type's metadata.
// Must be followed by one of: REFL_END, REFL_FIELD, REFL_FUNC.
// Example: REFL_TEMPLATE((typename K, typename V), (std::pair<K, V>))
REFL_TEMPLATE((typename... Ts), (Type<Ts...>)), Attributes...)

// End the declaration of Type's metadata.
// Does not have an argument list
REFL_END

// Describes a field.
// Example: REFL_FIELD(first_name, property{ "Value" })
REFL_FIELD(Field, Attributes...)

// Describes a function.
// Example: REFL_FUNC(first_name, property{ "Size" })
REFL_FUNC(Function, Attribute...)

// NOTE: None of the macros above need a terminating semi-colon (;)

```

# Changelog

## v0.2.0
  - added refl::util::contains(type_list<...>, [predicate]), refl::util::contains(type_list<...>, const_string<N>)
  - removed refl::attr::is_readable/is_writable (use refl::descriptor::is_readable/is_writable instead; reason: lack of support for fields that lack a property attribute)
  
## v0.1.2
  - refl::runtime::proxy<Derived, Target> can now delegate field 'invocations'. (Methods with names matching those of the reflected fields are created.)
  - refl::attr::property now can take an optional refl::attr::access_type (values: read, write, read_write) to specify whether the property is considered readable or writable (or both). 
  - added refl::descriptor::is_readable/is_writable
  - added refl::attr::is_readable/is_writable
