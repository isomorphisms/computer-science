# Android touch screen primitives

This is a first-pass catalog of the information Android can expose about touch input, from the Linux input-driver layer through the Android framework and the NDK.

**NDK** is Android's **Native Development Kit**. Its native input API is primarily declared in `android/input.h` and exposes `AInputEvent` plus `AMotionEvent_*` accessors.

The important distinction for this repository is between **information primitives** and **derived interpretations**. A touch event can contain positions, identities, timestamps, pressure, contact geometry, tool type, device capabilities, and so on. A swipe, pinch, fling, tap, or palm-rejection decision is normally computed from those observations rather than being a fundamental touchscreen measurement.

Not every touchscreen reports every possible axis. Android explicitly supports device-dependent motion ranges, calibration, normalization, and optional axes.

## 1. Event identity and state

At the Android app/NDK level, a motion event can expose:

- input event type
- input source, including `SOURCE_TOUCHSCREEN` / `AINPUT_SOURCE_TOUCHSCREEN`
- input device id
- motion action
  - down
  - up
  - move
  - cancel
  - outside
  - pointer down
  - pointer up
  - hover enter/move/exit
  - scroll
  - button press/release
- action pointer index for multi-pointer transitions
- flags
- edge flags
- modifier/meta-key state
- pressed-button state
- action button

The NDK represents the action and pointer index together in `AMotionEvent_getAction`; masks separate the action code from the pointer index.

References:

- Android `MotionEvent`: https://developer.android.com/reference/android/view/MotionEvent
- Android NDK input API: https://developer.android.com/ndk/reference/group/input
- Android `InputDevice`: https://developer.android.com/reference/android/view/InputDevice

## 2. Time and sampling

A motion stream exposes time as data:

- **down time** — when the current contact stream began
- **event time** — when the current event sample was generated
- **history size** — number of coalesced movement samples bundled with the event
- **historical sample time**
- **historical per-pointer axis values**

The historical samples matter: one delivered `MOVE` event can contain measurements that occurred between it and the preceding delivered event. Throwing them away loses input information and can make velocity or curve estimates worse.

Relevant native functions include:

- `AMotionEvent_getDownTime`
- `AMotionEvent_getEventTime`
- `AMotionEvent_getHistorySize`
- `AMotionEvent_getHistoricalEventTime`
- `AMotionEvent_getHistoricalAxisValue`

Reference: https://developer.android.com/ndk/reference/group/input

## 3. Pointer identity

A single event may contain several simultaneous contacts.

Primitives:

- pointer count
- pointer index
- pointer id
- tool type

**Pointer index and pointer id are different.** The index identifies a slot in the particular `MotionEvent`; it can change as fingers are added and removed. The pointer id follows a particular contact through the current gesture stream.

Tool types include finger, stylus, mouse, eraser, palm, or unknown where supported.

References:

- https://developer.android.com/reference/android/view/MotionEvent
- https://developer.android.com/ndk/reference/group/input

## 4. Per-pointer coordinates and axes

The basic coordinates are:

- `X`
- `Y`
- raw/display `X`
- raw/display `Y`

Android can additionally expose device-dependent motion axes. The touchscreen-relevant ones include:

- `PRESSURE`
- `SIZE`
- `TOUCH_MAJOR`
- `TOUCH_MINOR`
- `TOOL_MAJOR`
- `TOOL_MINOR`
- `ORIENTATION`
- `DISTANCE`
- `TILT`

The generic accessors are important because the set of axes is extensible:

- Java/Kotlin: `MotionEvent.getAxisValue(...)`
- NDK: `AMotionEvent_getAxisValue(...)`

There are also convenience accessors such as `getPressure`, `getTouchMajor`, `getToolMajor`, and their NDK equivalents.

### Contact ellipse versus tool ellipse

Android preserves a useful geometric distinction inherited from the Linux multi-touch model:

- **touch major/minor** describe the contact patch on the surface
- **tool major/minor** estimate the size of the approaching finger or stylus itself
- **orientation** describes the orientation of the contact/tool ellipse

These are potentially useful inputs for distinguishing fingertip, side-of-finger, thumb, stylus, or palm-like contacts. Such classifications are derived decisions; the geometry is closer to the primitive observation.

References:

- Android `MotionEvent`: https://developer.android.com/reference/android/view/MotionEvent
- NDK input: https://developer.android.com/ndk/reference/group/input
- Linux multi-touch protocol: https://docs.kernel.org/input/multi-touch-protocol.html

## 5. Coordinate precision and device ranges

The numerical value alone is not the whole primitive. Android also exposes information about what a device claims an axis can mean.

`InputDevice.MotionRange` supplies, per axis and source:

- axis identifier
- source
- minimum
- maximum
- range/span
- flat region
- fuzz/error tolerance
- resolution

`MotionEvent` also exposes X/Y precision values.

These capability values are exactly the sort of metadata that should accompany an algorithm catalog. An algorithm should not merely say “needs pressure”; it can say what axes, resolution, contact count, sampling behavior, or error bounds it expects.

References:

- `InputDevice`: https://developer.android.com/reference/android/view/InputDevice
- `InputDevice.MotionRange`: https://developer.android.com/reference/android/view/InputDevice.MotionRange

## 6. Device identity and capabilities

Useful device-level information includes:

- device id
- device name
- stable descriptor where available
- vendor id
- product id
- input sources/source classes
- supported motion ranges/axes

An event's device id is not itself a permanent hardware identity; Android documents nonzero device ids as arbitrary. Device metadata should therefore be kept separate from assumptions about stable identity.

Reference: https://developer.android.com/reference/android/view/InputDevice

## 7. Linux `evdev` / driver-layer primitives

Below Android's normalized `MotionEvent` representation, Linux input drivers report `evdev` events. For modern multi-touch devices, the important vocabulary includes:

- `EV_ABS`
- `ABS_X`, `ABS_Y` for single-touch-style absolute position
- `ABS_MT_SLOT`
- `ABS_MT_TRACKING_ID`
- `ABS_MT_POSITION_X`
- `ABS_MT_POSITION_Y`
- `ABS_MT_PRESSURE`
- `ABS_MT_TOUCH_MAJOR`
- `ABS_MT_TOUCH_MINOR`
- `ABS_MT_WIDTH_MAJOR`
- `ABS_MT_WIDTH_MINOR`
- `ABS_MT_ORIENTATION`
- `ABS_MT_DISTANCE`
- `ABS_MT_TOOL_X`
- `ABS_MT_TOOL_Y`
- `ABS_MT_TOOL_TYPE`
- `ABS_TILT_X`
- `ABS_TILT_Y`
- `BTN_TOUCH`
- `BTN_TOOL_*`
- `INPUT_PROP_DIRECT`
- `EV_SYN` / `SYN_REPORT`

For the current type-B Linux multi-touch protocol, `ABS_MT_SLOT` selects a contact slot and `ABS_MT_TRACKING_ID` identifies the life of a contact. A negative tracking id marks a slot as no longer occupied.

Android's system path is roughly:

`evdev` → `EventHub` → `InputReader` → calibration/normalization → `InputDispatcher` → app/framework or native input consumer.

Android's AOSP documentation states that `EventHub` reads raw `evdev` events, `InputReader` maintains tool/contact state and performs normalization or gesture work as appropriate, and `InputDispatcher` routes the resulting events to applications.

References:

- AOSP touch devices: https://source.android.com/docs/core/interaction/input/touch-devices
- AOSP `getevent`: https://source.android.com/docs/core/interaction/input/getevent
- Linux multi-touch protocol: https://docs.kernel.org/input/multi-touch-protocol.html

## 8. Inspecting what a real phone actually reports

The theoretical API is larger than the hardware on any one phone. Android's `getevent` utility can show both advertised input capabilities and a live kernel event stream.

Examples from the AOSP documentation:

```text
adb shell su -- getevent -p
adb shell su -- getevent -lt /dev/input/eventN
```

The first is useful for discovering which axes/ranges a device advertises. The second shows the actual event stream and timestamps.

Reference: https://source.android.com/docs/core/interaction/input/getevent

## 9. Derived quantities and interpretations

These should generally be treated as computations over the primitives rather than as the primitive touchscreen data itself:

- tap
- double tap
- long press
- velocity
- acceleration
- fling
- swipe
- drag
- pinch scale
- rotation angle
- trajectory curvature
- gesture classification
- palm rejection
- scroll intent
- handwriting/stroke segmentation

Android sometimes exposes higher-level classifications or gesture-specific axes, especially for richer pointing devices. Those are still useful observations, but they belong in a separate layer from the raw contact/time/axis inventory.

## 10. Why this catalog belongs in the computer-science repository

For architectural selection, a touch algorithm can be described by the information it consumes rather than by one hard-coded implementation choice. Examples:

- a one-finger pan needs pointer identity, X/Y, action, and time
- a velocity estimator additionally benefits from historical samples and precision/range information
- pinch needs two persistent pointer ids and their coordinates
- contact-shape heuristics can consume touch/tool major/minor and orientation
- stylus logic can additionally consume tool type, pressure, distance, tilt, and buttons

That makes the input side of the problem enumerable: **what information exists, which devices actually provide it, at what precision/rate, and which candidate algorithms require which subset?**
