# Description
A test was applied on a device based on the impl we made before. the log is available at rustdesk_log_.*.txt

Notably, the following is obtained:
```
12-01 10:10:57.258 16043 16043 D SamsungDexUtils: DeX Meta Key Capture set to: true
12-01 10:10:57.261 16043 16043 D mMainActivity: Pointer capture enabled
12-01 10:10:58.843 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:10:58.915 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1 (Keyboard Activity)
**SILENCE. Moving mouse doesn't provide log**
12-01 10:10:27.869 16043 16043 I BLASTBufferQueue_Java: update, w= 2800 h= 1752 mName = VRI[MainActivity]@629de70 mNativeObject= 0xb400007ba97a92b0 sc.mNativeObject= 0xb400007c097c2890 format= -3 caller= android.view.ViewRootImpl.updateBlastSurfaceIfNeeded:3386 android.view.ViewRootImpl.relayoutWindow:11361 android.view.ViewRootImpl.performTraversals:4544 android.view.ViewRootImpl.doTraversal:3708 android.view.ViewRootImpl$TraversalRunnable.run:12542 android.view.Choreographer$CallbackRecord.run:1751 # I think this is me changing the settings
12-01 10:10:27.869 16043 16043 I VRI[MainActivity]@629de70: Relayout returned: old=(0,0,2800,1752) new=(0,0,2800,1752) relayoutAsync=true req=(2800,1752)0 dur=3 res=0x0 s={true 0xb400007c997d3190} ch=false seqId=0
12-01 10:10:27.870 16043 16043 I VRI[MainActivity]@629de70: updateBoundsLayer: t=android.view.SurfaceControl$Transaction@2975aeb sc=Surface(name=Bounds for - com.carriez.flutter_hbb/com.carriez.flutter_hbb.MainActivity@1)/@0x11e489e frame=145
12-01 10:10:27.870 16043 16043 I VRI[MainActivity]@629de70: registerCallbackForPendingTransactions
12-01 10:10:27.871 16043 16113 I VRI[MainActivity]@629de70: mWNT: t=0xb400007c39805fd0 mBlastBufferQueue=0xb400007ba97a92b0 fn= 145 HdrRenderState mRenderHdrSdrRatio=1.0 caller= android.view.ViewRootImpl$9.onFrameDraw:6276 android.view.ViewRootImpl$3.onFrameDraw:2440 android.view.ThreadedRenderer$1.onFrameDraw:761 
12-01 10:10:30.856 16043 16043 I VRI[MainActivity]@629de70: call setFrameRateCategory for touch hint category=no preference, reason=boost timeout, vri=VRI[MainActivity]@629de70
12-01 10:10:54.941 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:10:54.945 16043 16043 I VRI[MainActivity]@629de70: call setFrameRateCategory for touch hint category=high hint, reason=touch, vri=VRI[MainActivity]@629de70
12-01 10:10:55.038 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:10:55.224 16043 16043 I BLASTBufferQueue_Java: update, w= 2800 h= 1752 mName = VRI[MainActivity]@629de70 mNativeObject= 0xb400007ba97a92b0 sc.mNativeObject= 0xb400007c097c2890 format= -3 caller= android.view.ViewRootImpl.updateBlastSurfaceIfNeeded:3386 android.view.ViewRootImpl.relayoutWindow:11361 android.view.ViewRootImpl.performTraversals:4544 android.view.ViewRootImpl.doTraversal:3708 android.view.ViewRootImpl$TraversalRunnable.run:12542 android.view.Choreographer$CallbackRecord.run:1751 
12-01 10:10:55.224 16043 16043 I VRI[MainActivity]@629de70: Relayout returned: old=(0,0,2800,1752) new=(0,0,2800,1752) relayoutAsync=true req=(2800,1752)0 dur=0 res=0x0 s={true 0xb400007c997d3190} ch=false seqId=0
12-01 10:10:55.225 16043 16043 I VRI[MainActivity]@629de70: updateBoundsLayer: t=android.view.SurfaceControl$Transaction@2975aeb sc=Surface(name=Bounds for - com.carriez.flutter_hbb/com.carriez.flutter_hbb.MainActivity@1)/@0x11e489e frame=146
12-01 10:10:55.225 16043 16043 D InputMethodManagerUtils: startInputInner - Id : 0
12-01 10:10:55.226 16043 16043 I InputMethodManager: startInputInner - IInputMethodManagerGlobalInvoker.startInputOrWindowGainedFocus
12-01 10:10:55.229 16043 16043 I VRI[MainActivity]@629de70: registerCallbackForPendingTransactions
12-01 10:10:55.230 16043 16112 I VRI[MainActivity]@629de70: mWNT: t=0xb400007c39813a90 mBlastBufferQueue=0xb400007ba97a92b0 fn= 146 HdrRenderState mRenderHdrSdrRatio=1.0 caller= android.view.ViewRootImpl$9.onFrameDraw:6276 android.view.ViewRootImpl$3.onFrameDraw:2440 android.view.ThreadedRenderer$1.onFrameDraw:761 
12-01 10:10:55.233 16043 16056 D InputTransport: Input channel constructed: 'ClientS', fd=213
12-01 10:10:55.681 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:10:55.780 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:10:56.443 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:10:56.492 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:10:56.532 16043 16043 D SamsungDexUtils: DeX Meta Key Capture set to: false
12-01 10:10:56.534 16043 16043 D mMainActivity: Pointer capture released
```

this means that the current impl on pointer doesn't work as expected. However, when using finger as trackpad, the following log (1329 - 1379):
```
12-01 10:11:51.406 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:53.344 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:11:53.344 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:53.345 16043 16065 I flutter : onPointDownImage PointerDeviceKind.mouse
12-01 10:11:53.405 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:11:53.407 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:54.287 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=ARROW, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:54.352 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:55.293 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:11:55.295 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:55.297 16043 16065 I flutter : onPointDownImage PointerDeviceKind.mouse
12-01 10:11:55.383 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:11:55.384 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:56.808 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:11:56.809 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:56.812 16043 16065 I flutter : onPointDownImage PointerDeviceKind.mouse
12-01 10:11:57.430 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:11:57.434 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:58.216 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:11:58.216 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:58.217 16043 16065 I flutter : onPointDownImage PointerDeviceKind.mouse
12-01 10:11:58.993 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:11:58.996 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:59.228 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:11:59.228 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:59.229 16043 16065 I flutter : onPointDownImage PointerDeviceKind.mouse
12-01 10:11:59.283 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:11:59.284 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:11:59.949 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:11:59.952 16043 16065 I flutter : onPointDownImage PointerDeviceKind.touch
12-01 10:12:00.381 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:12:00.919 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:12:00.977 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:12:01.052 16043 16065 I flutter : CustomTouchGestureRecognizer init
12-01 10:12:01.706 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:12:01.752 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:12:01.996 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:12:01.998 16043 16065 I flutter : addAllowedPointer
12-01 10:12:01.998 16043 16065 I flutter : onPointDownImage PointerDeviceKind.touch
12-01 10:12:02.087 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:12:02.089 16043 16065 I flutter : PointerUpEvent
12-01 10:12:02.466 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:12:02.469 16043 16065 I flutter : addAllowedPointer
12-01 10:12:02.469 16043 16065 I flutter : onPointDownImage PointerDeviceKind.touch
12-01 10:12:02.520 16043 16065 I flutter : start oneFingerPan
12-01 10:12:02.889 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:12:02.895 16043 16065 I flutter : ScaleGestureRecognizer onEnd
12-01 10:12:02.895 16043 16065 I flutter : OneFingerState.pan onEnd
12-01 10:12:03.544 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:12:03.659 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:12:04.772 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:12:04.775 16043 16065 I flutter : addAllowedPointer
12-01 10:12:04.776 16043 16065 I flutter : onPointDownImage PointerDeviceKind.touch
12-01 10:12:04.888 16043 16065 I flutter : start oneFingerPan
12-01 10:12:06.275 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:12:06.278 16043 16065 I flutter : ScaleGestureRecognizer onEnd
12-01 10:12:06.278 16043 16065 I flutter : OneFingerState.pan onEnd
12-01 10:12:07.091 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:12:07.094 16043 16065 I flutter : addAllowedPointer
12-01 10:12:07.094 16043 16065 I flutter : onPointDownImage PointerDeviceKind.touch
12-01 10:12:07.153 16043 16065 I flutter : start oneFingerPan
12-01 10:12:07.411 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 1
12-01 10:12:07.412 16043 16065 I flutter : ScaleGestureRecognizer onEnd
12-01 10:12:07.412 16043 16065 I flutter : OneFingerState.pan onEnd
12-01 10:12:10.415 16043 16043 I VRI[MainActivity]@629de70: call setFrameRateCategory for touch hint category=no preference, reason=boost timeout, vri=VRI[MainActivity]@629de70
12-01 10:12:13.876 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
12-01 10:12:13.877 16043 16043 I VRI[MainActivity]@629de70: call setFrameRateCategory for touch hint category=high hint, reason=touch, vri=VRI[MainActivity]@629de70
12-01 10:12:13.880 16043 16065 I flutter : addAllowedPointer
12-01 10:12:13.881 16043 16065 I flutter : onPointDownImage PointerDeviceKind.touch
12-01 10:12:13.915 16043 16043 I VRI[MainActivity]@629de70: updatePointerIcon : PointerIcon{type=NULL, hotspotX=0.0, hotspotY=0.0}
12-01 10:12:14.392 16043 16043 I VRI[MainActivity]@629de70: ViewPostIme pointer 0
```
