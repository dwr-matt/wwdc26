# Find your accessory with Bluetooth Channel Sounding

# WWDC 2026: Find your accessory with Bluetooth Channel Sounding

## Session Overview
This session introduces **Bluetooth Channel Sounding**, a new method for accurately measuring the distance between an iOS device and a Bluetooth-enabled accessory. Moving beyond the unreliable estimations of signal strength (RSSI), Channel Sounding uses phase-based ranging across 2.4 GHz channels to provide precise distance data. The session outlines how to implement this for distance tracking via **Core Bluetooth** or, for both distance and direction, via the **Nearby Interaction** framework.

---

## Key Concepts, APIs, and Frameworks

### Core Concepts
*   **Initiator vs. Reflector:** The iOS device acts as the *Initiator*, sending signals (tones), while the Bluetooth accessory acts as the *Reflector*, sending them back.
*   **Procedure:** The process of measuring phase changes across channels to calculate distance.
*   **Phase-Based Ranging:** The underlying technology that calculates distance based on the rate of signal change across different frequencies.

### Frameworks
*   **Core Bluetooth:** Used for standard Bluetooth connectivity and basic distance-only ranging.
*   **Nearby Interaction (NI):** Used for advanced spatial awareness, providing both distance and direction by fusing Channel Sounding data with camera input.
*   **Accessory Setup Kit:** The mandatory framework for pairing and setting up third-party accessories before they can utilize these APIs.

---

## Implementation Patterns

### For Distance Only (Core Bluetooth)
1.  **Check Compatibility:** Use `CBCentralManager.supportsFeatures()` to ensure the device supports Channel Sounding.
2.  **Initialize:** Start the session by calling `startChannelSoundingSession()` on the `CBPeripheral` object.
3.  **Delegate Handling:** Listen for results via `peripheral(_:didReceiveResults:)`, which returns distance in meters.
4.  **Termination:** Call `cancelChannelSoundingSession()` when finished to stop the ranging.

### For Distance and Direction (Nearby Interaction)
1.  **Configuration:** Create a configuration object using the `CBPeripheral` identifier.
2.  **Motion State:** Improve accuracy by using `updateMotionState(for:state:)`. Pass `.stationary` for fixed accessories (like wall tags) or `.moving` for mobile items.
3.  **Camera Assistance:** Enabling camera assistance is required for direction tracking.
4.  **Session Loop:** Run an `NISession`. The delegate will provide `NINearbyObject` updates containing distance and direction.

---

## Practical Takeaways for Developers

### Hardware Requirements
To support Channel Sounding, your accessory must:
*   Support **Bluetooth 6.3** and the **inline PCT** feature.
*   Support **Mode 0 and Mode 2** as defined by the Bluetooth specification.
*   Support a **TFCS (interspace timing)** of at least **100 microseconds**.

### Development Constraints & Tips
*   **Foreground Only:** Channel Sounding is limited to apps in the foreground; sessions will automatically pause if the app moves to the background.
*   **Adaptive Frequency:** iOS may throttle the frequency of measurements if there is high Wi-Fi or Bluetooth interference in the environment.
*   **Data Validation:** Results can be `nil` if a measurement fails. Always implement robust filtering and smoothing in your UI to account for raw data noise.
*   **Compatibility:** Channel Sounding requires an iPhone equipped with an **N1 chip**.
*   **Best Practice:** Use the `Nearby Interaction` framework if you need direction, as the fusion of Bluetooth data and camera inputs provides a superior user experience compared to raw ranging.

<!-- resources -->

---

## Resources

- [AccessorySetupKit](https://developer.apple.com/documentation/AccessorySetupKit)
- [Nearby Interaction](https://developer.apple.com/documentation/NearbyInteraction)
- [Core Bluetooth](https://developer.apple.com/documentation/CoreBluetooth)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/369/4/fea90204-fd38-4da4-b9e7-5dce37bc87d8/downloads/wwdc2026-369_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/369/4/fea90204-fd38-4da4-b9e7-5dce37bc87d8/downloads/wwdc2026-369_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/369/
