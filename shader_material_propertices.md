# Shader Material Propertices

MaterialPropertyDrawer
class in UnityEditor
Description

Base class to derive custom material property drawers from.

Use this to create custom UI drawers for your material properties, without having to write custom MaterialEditor classes. This is similar to how PropertyDrawer enables custom UI without writing custom inspectors.

In shader code, C#-like attribute syntax can be used in front of shader properties to add drawers to them. Unity has several built-in drawers, and you can write your own. Here's a shader code snippet demonstrating the syntax:

```
Shader "Custom/Example" 
{
    Properties
    {
        _MainTex("Base (RGB)", 2D) = "white" {}

        // Display a popup with None,Add,Multiply choices,
        // and setup corresponding shader keywords.
        [KeywordEnum(None, Add, Multiply)] _Overlay("Overlay mode", Float) = 0

        _OverlayTex("Overlay", 2D) = "black" {}

        // Display as a toggle.
        [Toggle] _Invert("Invert color?", Float) = 0
    }

        // rest of shader code...
}```
When implementing your own drawers, you should override OnGUI function. You can also optionally override GetPropertyHeight and Apply functions. Here's an example of a property drawer that displays a checkbox for a float property, with the value set to 0 or 1 depending on the state:

```using UnityEngine;
using UnityEditor;
using System;

// The property drawer class should be placed in an editor script, inside a folder called Editor.
// Use with "[MyToggle]" before a float shader property.

public class MyToggleDrawer : MaterialPropertyDrawer
{
    // Draw the property inside the given rect
    public override void OnGUI (Rect position, MaterialProperty prop, String label, MaterialEditor editor)
    {
        // Setup
        bool value = (prop.floatValue != 0.0f);

        EditorGUI.BeginChangeCheck();
        EditorGUI.showMixedValue = prop.hasMixedValue;

        // Show the toggle control
        value = EditorGUI.Toggle(position, label, value);

        EditorGUI.showMixedValue = false;
        if (EditorGUI.EndChangeCheck())
        {
            // Set the new value if it has changed
            prop.floatValue = value ? 1.0f : 0.0f;
        }
    }
}
```
The built-in MaterialPropertyDrawers are: ToggleDrawer, EnumDrawer, KeywordEnumDrawer, PowerSliderDrawer, IntRangeDrawer. In shader code, the "Drawer" suffix of the class name is not written; when Unity searches for the drawer class it adds "Drawer" automatically.

Toggle displays a float as a toggle. The property value will be 0 or 1, depending on the toggle state. When it is on, a shader keyword with the uppercase property name +"_ON" will be set, or an explicitly specified shader keyword.

```
// Will set "_INVERT_ON" shader keyword when set
[Toggle] _Invert ("Invert?", Float) = 0
```

```// Will set "ENABLE_FANCY" shader keyword when set.
[Toggle(ENABLE_FANCY)] _Fancy ("Fancy?", Float) = 0```
Enum displays a popup menu for a float property. You can supply either an enum type name (preferably fully qualified with namespaces, in case there are multiple types), or explicit name/value pairs to display. Up to 7 name/value pairs can be specified.

```
// Blend mode values
[Enum(UnityEngine.Rendering.BlendMode)] _Blend ("Blend mode", Float) = 1
```

```
// A subset of blend mode values, just "One" (value 1) and "SrcAlpha" (value 5).
[Enum(One,1,SrcAlpha,5)] _Blend2 ("Blend mode subset", Float) = 1```
KeywordEnum displays a popup menu for a float property, and enables corresponding shader keyword. This is used with "#pragma multi_compile" in shaders, to enable or disable parts of shader code. Each name will enable "property name" + underscore + "enum name", uppercased, shader keyword. Up to 9 names can be provided.

```// Display a popup with None, Add, Multiply choices.
// Each option will set _OVERLAY_NONE, _OVERLAY_ADD, _OVERLAY_MULTIPLY shader keywords.
[KeywordEnum(None, Add, Multiply)] _Overlay ("Overlay mode", Float) = 0```

```
// ...later on in CGPROGRAM code:

### #pragma multi_compile _OVERLAY_NONE, _OVERLAY_ADD, _OVERLAY_MULTIPLY
// ...
```
PowerSlider displays a slider with a non-linear response for a Range shader property.

```
// A slider with 3.0 response curve
[PowerSlider(3.0)] _Shininess ("Shininess", Range (0.01, 1)) = 0.08
```
IntRange displays an integer slider for a Range shader property.

```
// An integer slider for specified range (0 to 255)
[IntRange] _Alpha ("Alpha", Range (0, 255)) = 100
```
When a property drawer class name ends with "Decorator", that is a property decorator, similar to DecoratorDrawer. They are used to create headings and dividers between properties that don't affect the property itself. A single property can have multiple decorators on it. The built-in decorator drawers are: SpaceDecorator, HeaderDecorator.

Space creates vertical space before the shader property.

```
// Default small amount of space.
[Space] _Prop1 ("Prop1", Float) = 0
```

```
// Large amount of space.
[Space(50)] _Prop2 ("Prop2", Float) = 0
```
Header creates a header text before the shader property.

```
[Header(A group of things)] _Prop1 ("Prop1", Float) = 0
```
Note that for performance reasons, EditorGUILayout functions are not usable with MaterialPropertyDrawers.

See Also: MaterialProperty class.