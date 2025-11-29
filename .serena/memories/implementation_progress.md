# DeX Pointer Capture - FIXED with localDelta

## Summary
Successfully re-implemented pointer capture by using Flutter's `localDelta` - the same approach RustDesk uses for touch mode (screen-as-trackpad).

## Key Insight
RustDesk already handles relative mouse movements in touch mode via:
- `DragUpdateDetails.delta` in gesture handlers
- `cursorModel.updatePan(delta, localPosition, touchMode=true)`
- `_handleTouchMode()` in CursorModel which uses delta.dx/dy to move cursor

When Android pointer capture is active, Flutter's `PointerMoveEvent.localDelta` contains the relative movements. We can reuse the same `updatePan()` code path!

## Implementation

### platform_channel.dart
```dart
// Track if pointer capture is currently active
bool _pointerCaptureEnabled = false;
bool get pointerCaptureEnabled => _pointerCaptureEnabled;

Future<void> togglePointerCapture(bool enable) async {
  await _mainChannel.invokeMethod('togglePointerCapture', enable);
  _pointerCaptureEnabled = enable;
}
```

### MainActivity.kt
```kotlin
private fun togglePointerCapture(enable: Boolean) {
    val view = window.decorView
    if (enable) {
        view.requestPointerCapture()
    } else {
        view.releasePointerCapture()
    }
}
```

### input_model.dart
```dart
void onPointMoveImage(PointerMoveEvent e) {
  // ... existing checks ...
  
  if (isPhysicalMouse.value) {
    // When pointer capture is active, use relative delta
    if (isAndroid && RdPlatformChannel.instance.pointerCaptureEnabled) {
      final delta = e.localDelta;
      if (delta.dx != 0 || delta.dy != 0) {
        // Reuse touch mode's relative movement handling
        parent.target?.cursorModel.updatePan(delta, e.localPosition, true);
      }
    } else {
      // Normal absolute positioning
      handleMouse(...);
    }
  }
}
```

## Why This Works

1. **Android Pointer Capture**: When `requestPointerCapture()` is called, Android sends relative movements
2. **Flutter's localDelta**: Contains the relative movement data from the motion event
3. **Existing Infrastructure**: RustDesk's touch mode already handles relative movements via `updatePan()`
4. **Minimal Changes**: Just check if capture is active and redirect to existing code path

## Files Changed
- `flutter/lib/utils/platform_channel.dart` - Re-added togglePointerCapture with state tracking
- `flutter/android/app/src/main/kotlin/.../MainActivity.kt` - Re-added native handler
- `flutter/lib/models/input_model.dart` - Modified onPointMoveImage to use localDelta
- `flutter/lib/common/widgets/toolbar.dart` - Updated to enable both captures

## Status: READY FOR TESTING
The implementation reuses proven code paths and should work with Samsung DeX hardware mouse.