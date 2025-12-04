# DeX Pointer Capture - Final Implementation

## Summary
Implemented captured pointer handling as per Android documentation:
https://developer.android.com/develop/ui/views/touch-and-input/gestures/movement#pointer-capture

## Implementation in MainActivity.kt

### 1. OnCapturedPointerListener (Recommended by Android Docs)
```kotlin
private fun togglePointerCapture(enable: Boolean) {
    _pointerCaptureEnabled = enable
    val view = window.decorView
    if (enable) {
        // Set up the captured pointer listener as per Android docs
        view.setOnCapturedPointerListener { v, event ->
            if (event.action == MotionEvent.ACTION_MOVE) {
                val relativeX = event.getAxisValue(MotionEvent.AXIS_RELATIVE_X)
                val relativeY = event.getAxisValue(MotionEvent.AXIS_RELATIVE_Y)
                
                if (relativeX != 0f || relativeY != 0f) {
                    flutterMethodChannel?.invokeMethod("on_relative_mouse_move", mapOf(
                        "dx" to relativeX.toDouble(),
                        "dy" to relativeY.toDouble()
                    ))
                    return@setOnCapturedPointerListener true
                }
            }
            false
        }
        view.requestPointerCapture()
    } else {
        view.setOnCapturedPointerListener(null)
        view.releasePointerCapture()
    }
}
```

### 2. dispatchGenericMotionEvent (Fallback)
Also kept as fallback, now properly checks for SOURCE_MOUSE_RELATIVE:
```kotlin
override fun dispatchGenericMotionEvent(event: MotionEvent): Boolean {
    if (_pointerCaptureEnabled && event.action == MotionEvent.ACTION_MOVE) {
        val source = event.source
        // Check for SOURCE_MOUSE_RELATIVE (when pointer capture is active)
        val isCapturedMouse = (source == InputDevice.SOURCE_MOUSE_RELATIVE) ||
                               ((source and InputDevice.SOURCE_MOUSE) == InputDevice.SOURCE_MOUSE && 
                                window.decorView.hasPointerCapture())
        
        if (isCapturedMouse) {
            // Forward AXIS_RELATIVE_X/Y to Flutter
            ...
        }
    }
    return super.dispatchGenericMotionEvent(event)
}
```

## Data Flow
1. User moves physical mouse with pointer capture enabled
2. Android's `OnCapturedPointerListener` receives the event (primary path)
3. OR `dispatchGenericMotionEvent` intercepts it (fallback)
4. Native code extracts `AXIS_RELATIVE_X` and `AXIS_RELATIVE_Y`
5. Native invokes `on_relative_mouse_move` on MethodChannel
6. `RdPlatformChannel._handleMethodCall` receives the call
7. Calls `_relativeMouseMoveCallback` which is `InputModel._onRelativeMouseMoved`
8. `cursorModel.updatePan(delta, Offset.zero, true)` moves the cursor relatively

## Key Points from Android Docs
- When pointer capture is enabled, events use `SOURCE_MOUSE_RELATIVE`
- `AXIS_RELATIVE_X` and `AXIS_RELATIVE_Y` contain the relative movement
- `OnCapturedPointerListener` is the recommended approach
- `dispatchGenericMotionEvent` can be used as fallback

## Moonlight Reference
They check for both sources:
```java
public boolean eventHasRelativeMouseAxes(MotionEvent event) {
    int eventSource = event.getSource();
    return (eventSource == InputDevice.SOURCE_MOUSE_RELATIVE) ||
           (eventSource == InputDevice.SOURCE_TOUCHPAD && targetView.hasPointerCapture());
}
```
