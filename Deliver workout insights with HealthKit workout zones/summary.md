# Deliver workout insights with HealthKit workout zones

# Session Summary: Deliver workout insights with HealthKit workout zones

## Overview
This session introduces the new **Workout Zones** capability in HealthKit (introduced in iOS 27 and watchOS 27). Workout zones allow developers to track and display intensity levels for activities like running, cycling, and rowing by categorizing raw health data—specifically **Heart Rate** and **Cycling Power**—into distinct, user-personalized zones. By leveraging these APIs, developers can provide actionable training guidance, such as post-workout summaries, real-time intensity coaching, and long-term performance tracking, while ensuring consistency across the Apple health ecosystem.

## Key Concepts and Frameworks
*   **HealthKit (HK):** The central framework for managing workout and health data. 
*   **Workout Zones:** A structured way to segment intensity.
    *   **Heart Rate Zones:** Calculated based on age and resting heart rate.
    *   **Cycling Power Zones:** Based on Functional Threshold Power (FTP).
*   **HKWorkoutZoneGroup:** The core structure containing:
    *   **Configuration:** Describes the set of zones, the source (system-calculated, user-defined, or app-custom), and the boundaries.
    *   **Zone Durations:** An array detailing the exact amount of time spent in each zone.
*   **Zone Dynamics:** Zones are defined as contiguous, non-overlapping ranges. The first zone has no lower bound; the last has no upper bound.

## APIs and Implementation Patterns
### 1. Retrieving Historical Data
Developers can access zone data from a completed `HKWorkout` or `HKWorkoutActivity` object.
*   **Method:** Access the `zoneGroups(for:)` dictionary by passing the appropriate `HKQuantityType` (e.g., `.heartRate()`).
*   **Utility:** Use the `zoneDurations` array to populate visualizations, such as bar charts or histograms, in a post-workout summary screen.

### 2. Live Workout Updates
To build real-time coaching features, implement the `HKLiveWorkoutBuilderDelegate`.
*   **Delegate Method:** Use `didUpdateWorkoutZone(_:to:from:at:)`. 
*   **Behavior:** This method triggers only when the user crosses a threshold (e.g., moving from Zone 2 to Zone 3).
*   **Context:** It provides the current/previous zone, the full `HKWorkoutZoneGroup` configuration, and the timestamp of the last processed sample, which is useful for displaying a running timer of the current effort level.

### 3. Customizing Zone Configurations
While HealthKit defaults to the user's preferred settings, developers can inject custom zones for specialized training.
*   **Pattern:** Create `HKQuantityZoneBoundaries` using custom thresholds and unit types.
*   **Integration:** Add the configuration to the `HKWorkoutBuilder` *before* starting the workout collection (`beginCollection`). 
*   **Persistence Note:** Custom zones are ephemeral to the workout. If your app requires these zones to persist or sync, your app is responsible for saving them.

## Practical Takeaways for Developers
*   **Authorization:** Ensure you request standard HealthKit permissions for workouts, heart rate, and cycling power.
*   **Data Normalization:** If your app compares data across multiple workouts, be aware that the number of zones can vary (e.g., some apps use 5 zones, others 8). Always normalize data based on zone boundaries rather than just the zone index.
*   **Preferred vs. Custom:** Always query for the user’s "Preferred" configuration first to provide a consistent user experience. Only override with "Custom" configurations if your platform provides unique, specialized training models.
*   **User Empowerment:** Use zone transitions to offer haptic or visual feedback, helping users maintain target intensities during intervals or recovery phases.

<!-- resources -->

---

## Resources

- [Tracking heart rate zones for workouts](https://developer.apple.com/documentation/HealthKit/tracking-heart-rate-zones-for-workouts)
- [Accessing workout zone data](https://developer.apple.com/documentation/HealthKit/accessing-workout-zone-data)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/207/5/8627c1d4-7a34-46f2-8491-f0d1c138edd1/downloads/wwdc2026-207_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/207/5/8627c1d4-7a34-46f2-8491-f0d1c138edd1/downloads/wwdc2026-207_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/207/
