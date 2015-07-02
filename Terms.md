


# Type #

Type is the template of a geometry, which is like the "class" keyword in C++ language.

A .asmm file can be considered as a Type.

In AnyCAD, Type is not visible in the view. If you want to display the type, you need to create an Instance for it.


# Instance #

Instance is the concrete object of the Type. For example:
```
class Type{};
Type instance;
```

# Element #
Both Type and Instance are called Element.

# Parameter #
Both Type and Instance can have parameters.

## Type Parameter ##
Type parameter is the property of Type.

## Instance Parameter ##
Instance  parameter is the property of Instance.
Instances of the same Type share the same Type parameters.