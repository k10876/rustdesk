# DeX Pointer Capture - Final Analysis with Moonlight Reference

## Summary
Analyzed both termux-x11 and moonlight-android to understand proper pointer capture implementation.

## Moonlight-Android Approach (Game.java + AndroidNativePointerCaptureProvider.java)

### How They Handle Pointer Capture
1. **InputCaptureProvider abstraction** with multiple providers:
   - `AndroidNativePointerCaptureProvider` - Uses Android O+ native pointer capture
   - `ShieldCaptureProvider` - NVIDIA-specific
   - `EvdevCaptureProvider` - Root-level device access
   - `AndroidPointerIconCaptureProvider` - Just hides cursor, no actual capture

2. **Event Processing (Game.java lines 1828-1842)**:
```java
if (inputCaptureProvider.eventHasRelativeMouseAxes(event)) {
    // Send the deltas straight from the motion event
    short deltaX = (short)inputCaptureProvider.getRelativeAxisX(event);
    short deltaY = (short)inputCaptureProvider.getRelativeAxisY(event);
    conn.sendMouseMove(deltaX, deltaY);
}
```

3. **Relative Axis Detection (AndroidNativePointerCaptureProvider.java)**:
```java
public boolean eventHasRelativeMouseAxes(MotionEvent event) {
    int eventSource = event.getSource();
    return (eventSource == InputDevice.SOURCE_MOUSE_RELATIVE) ||
           (eventSource == InputDevice.SOURCE_TOUCHPAD && targetView.hasPointerCapture());
}

public float getRelativeAxisX(MotionEvent event) {
    int axis = (event.getSource() == InputDevice.SOURCE_MOUSE_RELATIVE) ?
            MotionEvent.AXIS_X : MotionEvent.AXIS_RELATIVE_X;
    return event.getAxisValue(axis);
}
```

### Meta Key Capture (Game.java lines 675-702)
```java
public void setMetaKeyCaptureState(boolean enabled) {
    Class<?> semWindowManager = Class.forName("com.samsung.android.view.SemWindowManager");
    Method getInstanceMethod = semWindowManager.getMethod("getInstance");
    Object manager = getInstanceMethod.invoke(null);
    Method requestMetaKeyEventMethod = semWindowManager.getDeclaredMethod("requestMetaKeyEvent", ...);
    requestMetaKeyEventMethod.invoke(manager, this.getComponentName(), enabled);
}
```

### Key Insight from Moonlight
They call BOTH together (line 1146 + 1159):
```java
private void setInputGrabState(boolean grab) {
    if (grab) {
        inputCaptureProvider.enableCapture();  // Pointer capture
        // ...
    }
    setMetaKeyCaptureState(grab);  // Meta key capture - SEPARATE call
}
```

## Why Flutter Can't Do Pointer Capture Like Moonlight/Termux-X11

### The Problem
1. **Moonlight**: Native Java → MotionEvent → check source/axes → handle relative/absolute
2. **RustDesk**: Native Java → Flutter Engine → Dart → Listener widget expects absolute coords

### What Would Be Required for Flutter Pointer Capture
1. Intercept MotionEvents in MainActivity BEFORE Flutter gets them
2. Convert AXIS_RELATIVE_X/Y to Flutter-compatible format
3. Send through custom MethodChannel to Flutter
4. Modify InputModel to handle relative movements
5. Track cursor position manually in Dart

This is a significant undertaking - essentially reimplementing Flutter's mouse handling.

## Current Fix (Already Applied)
Removed pointer capture, kept only Meta key capture:
- ✅ Meta/Windows key captured and sent to remote
- ✅ All keyboard keys work normally
- ✅ Mouse works with standard absolute positioning

## Future Enhancement Path (If Needed)
If pointer capture is truly needed in future:

1. **Option A**: Native mouse event interception
   - Override `dispatchGenericMotionEvent()` in MainActivity
   - When in capture mode, extract relative coords
   - Send to Flutter via MethodChannel
   - InputModel handles as relative movements

2. **Option B**: Switch to native Android UI for remote session
   - Like Moonlight, use SurfaceView for rendering
   - Handle all input natively
   - Major architectural change

## Files for Reference
- `/tmp/moonlight-android-ref/app/src/main/java/com/limelight/Game.java`
- `/tmp/moonlight-android-ref/app/src/main/java/com/limelight/binding/input/capture/AndroidNativePointerCaptureProvider.java`
- `/tmp/termux-x11-ref/app/src/main/java/com/termux/x11/input/TouchInputHandler.java`