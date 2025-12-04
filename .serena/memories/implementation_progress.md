# DeX Pointer Capture - FIXED with Native Event Interception

## Problem
The previous implementation using Flutter's `e.localDelta` didn't work because:
- When pointer capture is enabled via `requestPointerCapture()`, Android sends mouse events with `SOURCE_MOUSE_RELATIVE`
- Flutter's Listener widget does NOT receive `ACTION_MOVE` events with this source
- This causes "silence" in the logs - no mouse movement events reach Flutter

## Solution
Intercept mouse events in the Android native layer and forward them via MethodChannel:

### MainActivity.kt
1. Added `_pointerCaptureEnabled` flag to track pointer capture state
2. Override `dispatchGenericMotionEvent` to intercept captured mouse events:
```kotlin
override fun dispatchGenericMotionEvent(event: MotionEvent): Boolean {
    if (_pointerCaptureEnabled && 
        event.action == MotionEvent.ACTION_MOVE &&
        (event.source and InputDevice.SOURCE_MOUSE) == InputDevice.SOURCE_MOUSE) {
        
        val relativeX = event.getAxisValue(MotionEvent.AXIS_RELATIVE_X)
        val relativeY = event.getAxisValue(MotionEvent.AXIS_RELATIVE_Y)
        
        if (relativeX != 0f || relativeY != 0f) {
            flutterMethodChannel?.invokeMethod("on_relative_mouse_move", mapOf(
                "dx" to relativeX.toDouble(),
                "dy" to relativeY.toDouble()
            ))
            return true  // Consume the event
        }
    }
    return super.dispatchGenericMotionEvent(event)
}
```

### platform_channel.dart
1. Added `RelativeMouseMoveCallback` typedef
2. Added `_relativeMouseMoveCallback` property
3. Added method call handler to receive `on_relative_mouse_move` from Android
4. Constructor now sets up the method call handler

### input_model.dart
1. Added `_onRelativeMouseMoved(double dx, double dy)` method to handle native events
2. Constructor registers callback with `RdPlatformChannel.instance.relativeMouseMoveCallback`
3. Removed ineffective `e.localDelta` logic in `onPointMoveImage`
4. `onPointMoveImage` now returns early when pointer capture is active (movement handled by native layer)

## Data Flow
1. User moves physical mouse with pointer capture enabled
2. Android sends `ACTION_MOVE` event with `AXIS_RELATIVE_X/Y`
3. `dispatchGenericMotionEvent` intercepts the event in MainActivity
4. Native code invokes `on_relative_mouse_move` on MethodChannel
5. `RdPlatformChannel._handleMethodCall` receives the call
6. Callback `_onRelativeMouseMoved` is invoked in InputModel
7. `cursorModel.updatePan(delta, Offset.zero, true)` moves the cursor

## Button Clicks
Mouse button clicks still work via Flutter's Listener:
- `onPointDownImage` and `onPointUpImage` continue to use `cursorModel.offset` for position
- Buttons are not affected by pointer capture

## Files Changed
1. **MainActivity.kt** - Added `dispatchGenericMotionEvent` override and `_pointerCaptureEnabled` flag
2. **platform_channel.dart** - Added method call handler and callback mechanism
3. **input_model.dart** - Added `_onRelativeMouseMoved`, removed `e.localDelta` logic

## Status: READY FOR TESTING
Implementation uses native event interception as recommended in PROBLEM_DESC.md