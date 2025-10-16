# DreamBSD-Mach: A macOS-Inspired OS on FreeBSD

This document outlines the vision, roadmap, and development plan for DreamBSD-Mach, a modern operating system inspired by the aesthetics and user experience of macOS, built upon the solid foundation of FreeBSD. The development process is designed to be AI-assisted, with detailed prompts for each milestone.

---

## 🎨 Project Vision

The primary goal of **DreamBSD-Mach** is to create a beautiful, fluid, and intuitive desktop experience. The project prioritizes the user interface and user experience above all else in its initial phases.

-   **Smooth Desktop UI:** A responsive and visually appealing graphical user interface is the core of the project. We aim for a polished feel, with high-quality font rendering that makes text crisp and easy to read.
-   **Interactive Dock:** The desktop will feature a macOS-style dock with smooth hover animations, icon magnification, and a "jelly" or "glass" translucent effect that blurs the background.
-   **Advanced Window Manager:** The window manager will support managing multiple windows with features like snapping, animated transitions for opening/closing/resizing, and subtle shadow effects to create a sense of depth.
-   **Applications Framework (`libdreamui`):** A lightweight, C-ABI compatible Rust framework will allow developers to build native applications that integrate seamlessly with the DreamBSD desktop environment.
-   **UI-First Approach:** The initial development will focus entirely on the compositor, window manager, and UI framework. Kernel-level features like **Mach IPC** and custom **hardware drivers** are considered optional and can be integrated after the user interface reaches a stable and polished state. We will use dummy interfaces and stubs to simulate these components initially.



---

## 🗺️ Roadmap

The project is broken down into 12 distinct milestones. Each milestone has a clear focus, a minimal deliverable to ensure progress, and a "perfect" or macOS-like goal to strive for.

| Milestone | Focus                       | Minimal Deliverable                                                               | Perfect / macOS-Like Deliverable                                                                       |
| :-------- | :-------------------------- | :-------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------- |
| **T1** | Project Skeleton            | Basic Rust project structure, `Cargo.toml`, README.                               | Complete workspace setup, CI/CD pipeline, build scripts for FreeBSD.                                   |
| **T2** | Compositor Minimal          | A single-window Vulkan/Skia context that can clear to a solid color.              | A double-buffered compositor that can render a static gradient background.                             |
| **T3** | Userland Framework          | `libdreamui` library with dummy `send_draw_request()` and `receive_event()`.        | A functional client-server IPC mechanism (e.g., via Unix sockets) for UI events and draw commands.     |
| **T4** | Font Rendering              | Renders a fixed-size bitmap font string ("Hello, World").                         | HarfBuzz for text shaping and MSDF/SDF for GPU-accelerated, scalable, high-quality font rendering.     |
| **T5** | Layered Compositing         | Compositor can stack two static, colored rectangles (layers).                     | Compositor manages multiple layers with alpha blending and simple fade-in/out animations.              |
| **T6** | Desktop Shell / Dock        | A static, non-interactive bar at the bottom of the screen with placeholder icons. | An interactive dock with icon hover/magnification animations and a translucent, blurred background.      |
| **T7** | Window Manager              | Can draw and move a single, undecorated window.                                   | Manages multiple windows with shadows, snapping, animated transitions (resize/move), and multi-monitor support. |
| **T8** | Advanced UI Effects         | A simple box blur effect applied to a single layer.                               | A real-time, multi-pass Gaussian blur shader for glass/jelly effects, with depth perception.           |
| **T9** | Applications Integration    | A "Hello World" app using `libdreamui` to draw a window with static text.         | Functional Terminal, Text Editor, and Settings apps using `libdreamui` for a cohesive experience.      |
| **T10** | Kernel / Mach IPC (Optional) | Stubbed-out Mach IPC API in the userland.                                         | Kernel-level Mach IPC for message passing, task/thread creation, and memory management between processes. |
| **T11** | GPU & Hardware Drivers      | Basic framebuffer driver support.                                                 | Accelerated Vulkan drivers (DRM/KMS), and drivers for HID (keyboard/mouse), network, and storage.      |
| **T12** | Polishing                   | UI is functional but may have minor visual glitches or performance dips.          | Consistent ≥60 FPS, pixel-perfect font rendering, and fluid, interruptible animations across the system. |

---

## 🤖 AI Prompts for Development

This section contains detailed prompts for an AI code assistant (like Gemini Pro or Claude Code) to generate the necessary code and documentation for each milestone.

### Milestone 1: Project Skeleton

#### AI Prompt: Generate Project Structure
"Generate the file and directory structure for a new Rust project named **DreamBSD-Mach**. Create a Cargo workspace in the root directory. The workspace should contain three initial crates:
1.  `compositor`: The main binary for the desktop compositor.
2.  `libdreamui`: The shared library for the application framework.
3.  `apps`: A directory that will later contain application crates.

In the root `Cargo.toml`, define the workspace members. In each crate's `Cargo.toml`, add placeholder dependencies. Finally, generate a `README.md` file in the root with the project title and a brief 'macOS-inspired OS built on FreeBSD' description."

### Milestone 2: Compositor Minimal

#### AI Prompt: Generate Vulkan/Skia Double-Buffered Compositor
"In the `compositor` crate, write the boilerplate code for a minimal graphical application using **winit** for windowing and **vulkano** for Vulkan context creation. The goal is to open a single, full-screen window and clear it to a solid color (e.g., dark gray `#333333`). Implement a basic render loop. The code should handle window creation, Vulkan instance and device setup, and a main event loop that clears the swapchain image on each frame. Ensure proper resource cleanup on exit. Add comments explaining the Vulkan setup process."

### Milestone 3: Userland Framework (`libdreamui`)

#### AI Prompt: Generate Application Framework API
"In the `libdreamui` crate, define the public API for the application framework. The communication between applications and the compositor will be asynchronous. Create two main functions:
1.  `send_draw_request(request: DrawCommand)`: This function will eventually send drawing commands to the compositor. For now, it can just print the command to the console. Define the `DrawCommand` enum with variants like `DrawRect { rect: Rect, color: Color }` and `DrawText { text: String, pos: Point }`.
2.  `receive_event() -> Option<UIEvent>`: This function will eventually receive events from the compositor. For now, it should be a dummy that always returns `None`. Define the `UIEvent` enum with variants like `MouseDown { pos: Point }` and `KeyPress { key: Key }`.

Define the basic helper structs `Rect`, `Color`, `Point`, and `Key`."

### Milestone 4: Font Rendering

#### AI Prompt: Implement Scalable Font Rendering
"Enhance the `compositor` to render text. Integrate the **rusttype** or **tiny-skia** crate to render a simple string like 'Welcome to DreamBSD' onto the screen. First, create a function to rasterize a glyph into a bitmap in memory. Then, create a Vulkan texture from this bitmap and render it onto a quad.

As a more advanced follow-up, create a prompt to replace this with a GPU-accelerated solution: 'Refactor the font rendering to use **HarfBuzz** for text shaping and a **Multi-channel Signed Distance Field (MSDF)** font texture. Write a GLSL fragment shader that uses the MSDF texture to render smooth, scalable text at any size without pixelation.'"

### Milestone 5: Layered Compositing & Animations

#### AI Prompt: Implement Layer Management and Fade Animations
"Refactor the `compositor`'s rendering logic to support multiple layers. Define a `Layer` struct containing its position, size, z-index, and content (e.g., a texture or solid color). The main render loop should iterate through a list of layers and draw them in order of their z-index with alpha blending enabled.
Then, implement a simple animation system. Add an `opacity` field to the `Layer` struct and create a function that animates this value from `0.0` to `1.0` over a duration of 500ms to create a fade-in effect for new layers."

### Milestone 6: Desktop Shell / Dock

#### AI Prompt: Generate Interactive Dock with Blur Effect
"Create a **Dock** component within the `compositor`. It should be a rounded rectangle positioned at the bottom-center of the screen.
1.  **Layout:** Render 5 placeholder icons (simple colored squares) evenly spaced inside the dock.
2.  **Animation:** Implement a hover effect. When the mouse moves over an icon, it and its immediate neighbors should scale up smoothly. Use a sine curve for the magnification amount based on the cursor's distance from the icon's center.
3.  **Effects:** Implement a 'glass' effect for the dock background. Before drawing the dock, render the desktop content behind it to a separate texture. Apply a **Gaussian blur** shader to this texture and then draw it as the dock's background, giving it a translucent, blurred appearance."

### Milestone 7: Window Manager

#### AI Prompt: Generate Window Manager with Snapping and Shadows
"Create a `WindowManager` module in the `compositor`.
1.  **Window Representation:** Define a `ManagedWindow` struct that holds its ID, title, position, size, and a texture for its content. The window manager should maintain a list of these windows.
2.  **Movement and Resizing:** Implement logic to handle mouse drag events to move and resize windows.
3.  **Shadows:** For each window, render a blurred, semi-transparent black rectangle behind it, offset slightly, to simulate a drop shadow.
4.  **Snapping:** When a window is being moved near the edge of the screen or another window, make it 'snap' into alignment."

### Milestone 8: Advanced UI Effects

#### AI Prompt: Implement Real-Time Gaussian Blur for Glass Effects
"Write a performant, two-pass Gaussian blur implementation in GLSL shaders for the compositor.
-   **Pass 1 (Horizontal):** A fragment shader that takes the scene texture as input and samples pixels to its left and right, weighted by a Gaussian function, to produce a horizontally blurred intermediate texture.
-   **Pass 2 (Vertical):** A second fragment shader that takes the intermediate texture as input and samples pixels above and below it to produce the final, fully blurred image.
Integrate this into the compositor as a post-processing effect that can be selectively applied to regions behind UI elements like the dock or application windows."

### Milestone 9: Applications Integration

#### AI Prompt: Generate Sample Applications Using `libdreamui`
"In the `apps` directory, create a new binary crate named `terminal`. This application should link against `libdreamui`. In its `main` function, it should use the `libdreamui` API to:
1.  Request the creation of a new window with the title 'Terminal'.
2.  In a loop, send a draw request to render a black rectangle covering the window and some static white text (e.g., `user@dreambsd:~$`).
This will test the end-to-end pipeline from an application to the compositor, using the dummy IPC for now."

### Milestone 10: Kernel / Mach IPC (Optional)

#### AI Prompt: Implement Mach IPC Stubs
"Create a new crate `libmach` with a C-style FFI API mirroring the core Mach IPC interface.
Define opaque structs for `mach_port_t` and `mach_msg_header_t`.
Create stub functions like:
-   `kern_return_t mach_port_allocate(ipc_space_t task, mach_port_right_t right, mach_port_name_t *name)`
-   `mach_msg_return_t mach_msg(mach_msg_header_t *msg, mach_msg_option_t option, ...)`
The function bodies should simply log the function call and its arguments to the console and return a success code (e.g., `KERN_SUCCESS`). This allows the UI components to be built against a Mach-like API without needing a custom kernel yet."

### Milestone 11: GPU & Hardware Driver Integration

#### AI Prompt: Design a Driver Abstraction and GPU Integration Plan
"Design a hardware driver abstraction layer in Rust for DreamBSD-Mach.
1.  Define a generic `Driver` trait with methods like `probe(&self, device_info: &DeviceInfo) -> bool` and `initialize(&self) -> Result<(), DriverError>`.
2.  Create a dummy `KeyboardDriver` that implements this trait. Its `initialize` method should start a new thread that simulates key presses by printing messages to the console every few seconds.
3.  Outline the steps required to replace the `winit` dependency in the compositor with direct rendering via the kernel's **DRM/KMS** subsystems, using a crate like `drm-rs` to manage GPU resources and modesetting."

### Milestone 12: Polishing

#### AI Prompt: Implement Frame Timing and Animation Smoothing
"Refactor the main render loop in the `compositor` to improve performance and smoothness.
1.  **Frame Timing:** Use `std::time::Instant` to calculate the delta time between each frame. Display the current FPS in a corner of the screen.
2.  **Animation Smoothing:** Modify all animation logic (fades, movement, scaling) to be based on this delta time instead of being tied to the frame rate. This ensures animations run at the same speed regardless of whether the system is running at 60 FPS or 30 FPS. For example, instead of `opacity += 0.01`, use `opacity += 0.5 * delta_time_seconds`."

---

## 💡 Notes & Tips for AI-Driven Development

-   **Pace Yourself:** Tackle one prompt per day or per week. Focus on getting the minimal deliverable working before asking the AI for the "perfect" implementation.
-   **Iterate and Refine:** The AI's first output may not be perfect. Use follow-up prompts to refine the code, fix bugs, or ask for explanations. For example: "This code panics, can you add error handling?" or "Explain how the Vulkan render pass is structured."
-   **Dummy Implementations First:** Use dummy implementations (stubs, mock objects, logging calls) for components that are not the focus of the current milestone (e.g., IPC, kernel drivers). This allows the UI to be developed independently and rapidly.
-   **Pipeline First, Effects Last:** Focus on building a stable rendering pipeline: `App -> libdreamui -> IPC -> Compositor -> GPU`. Once data flows correctly, you can add more complex animations, shaders, and effects.
