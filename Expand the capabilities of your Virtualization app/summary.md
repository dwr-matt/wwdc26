# Expand the capabilities of your Virtualization app

# Session Summary: Expand the capabilities of your Virtualization app

## Overview
This session introduces significant enhancements to the Apple Virtualization framework, focusing on making virtualized environments more powerful, automated, and performant. Ronnie Mishra outlines new tools for macOS guest provisioning, USB device passthrough, advanced networking, efficient disk management, and custom device development. These updates are designed to support everything from automated testing and continuous integration to full-scale desktop virtualization and high-performance communication channels.

---

## Key Concepts, APIs, and Frameworks

*   **macOS Guest Provisioning:** Automates the initial setup of macOS virtual machines, bypassing the manual Setup Assistant.
*   **Accessory Access Framework:** A new framework that allows host-connected USB devices to be shared with virtual machines. It emphasizes user privacy by giving the host user explicit control over device attachment and detachment.
*   **VMNet Framework:** Enables complex, custom network topologies. Developers can now define their own networks, configure DHCP settings, and implement host-to-guest port forwarding.
*   **Disk Image Kit:** Introduces efficient disk management using the Apple Sparse Image Format (ASIF). It supports "stacked" disk images with base layers, cache layers (to improve performance on slow storage), and overlay layers (for copy-on-write snapshots).
*   **Custom VIRTIO Devices:** Allows developers to implement high-performance, para-virtualized devices for Linux guests. This is ideal for low-latency communication, specialized co-processors, or hardware-accelerated tasks.

---

## Code Patterns & Techniques

*   **Provisioning Automation:** 
    *   Construct `VZMacGuestProvisioningOptions` with credentials and SSH settings.
    *   Inject these into `VZMacOSVirtualMachineStartOptions` before booting the VM. 
    *   *Note:* These settings are ignored if the guest has already been set up.
*   **Accessory Passthrough:**
    *   Define `AAUSBAcccessoryMatchingCriteria` and register a listener with `AAUSBAcccessoryManager`.
    *   Use `VZUSBPassThroughDevice` to attach the device to the VM's controller within the `didConnect` delegate method.
*   **Network Topology:**
    *   Create a `VMNet` network configuration, define DHCP/port forwarding rules, and initialize a `VMNet` network object.
    *   Attach this object to a `VZVirtIONetworkDeviceConfiguration`.
    *   *Persistence:* Since `VMNet` objects are not persisted on app quit, developers must handle serialization of settings manually using `VMNet`’s copy/create serialization APIs.
*   **Disk Stacking:**
    *   Stack `Disk Image` objects (Base $\rightarrow$ Cache/Overlay).
    *   Attach the resulting stack to a `VZVirtIOBlockDeviceConfiguration`.
    *   *Performance Tip:* Keep disk stacks shallow to avoid performance degradation.
*   **Custom VIRTIO Devices:**
    *   Use `VZCustomVirtIO` to define device identity and VIRTIO queues.
    *   Implement the `VZCustomVirtIO` delegate to process data dequeued from shared memory buffers and trigger guest-side interrupts.

---

## Practical Takeaways for Developers

1.  **Security First:** When implementing automated guest provisioning, never hard-code passwords. Use the macOS Keychain, secure configuration files, or environment variables.
2.  **User Control:** When using the Accessory Access framework, ensure your app gracefully handles "detachment" events, as users can disconnect USB devices from the VM at any time via the menu bar.
3.  **Optimize Disk Space:** Move away from raw disk images to the Apple Sparse Image Format (ASIF). It significantly improves performance for cloning and snapshotting by leveraging copy-on-write capabilities.
4.  **Resource Lifecycle:** Remember that `VMNet` networks are tied to the process lifecycle. If you need a network to persist across app launches, you are responsible for saving and restoring that state.
5.  **Advanced Guest Features:** Leverage modern macOS/Linux features available in newer versions of the framework, such as iCloud integration, EFI Secure Boot, and Metal acceleration (including argument buffers) for guest desktops.

<!-- resources -->

---

## Resources

- [DiskImageKit](https://developer.apple.com/documentation/DiskImageKit)
- [Accessory Access](https://developer.apple.com/documentation/AccessoryAccess)
- [vmnet](https://developer.apple.com/documentation/vmnet)
- [Virtual I/O Device (VIRTIO) Version 1.4](https://docs.oasis-open.org/virtio/virtio/v1.4/virtio-v1.4.html)
- [Virtualization](https://developer.apple.com/documentation/Virtualization)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/224/5/33a91529-8caf-409e-9c54-1b8952744651/downloads/wwdc2026-224_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/224/5/33a91529-8caf-409e-9c54-1b8952744651/downloads/wwdc2026-224_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/224/
