# 03 - XR Input, Haptics & Interaction

## OpenXR Input Model

### 1. What are "Action Sets" and "Actions"?
**Answer:**
OpenXR uses an abstract input model:
- **Action:** A specific intent (e.g., "Grab", "Teleport").
- **Action Set:** A collection of actions for a specific context (e.g., "InMenu", "Gameplay").
- **Binding:** Mapping a physical button (e.g., Quest Trigger) to an Action.
- **Benefit:** Allows developers to write code for "Grab" without caring if the user has a Valve Index controller or a Quest controller.

### 2. How do you handle "Hand Tracking" in OpenXR?
**Answer:**
Using the `XR_EXT_hand_tracking` extension. It provides a skeletal model with 26 joints per hand. You use `xrLocateHandJointsEXT` to get the position and orientation of each joint.

### 3. What is "Haptic Feedback"?
**Answer:**
Providing tactile sensation to the user. OpenXR uses `XrHapticVibration` to set the frequency and amplitude of the controller's vibration motor.

## Interaction Patterns

### 4. What is "Raycasting" in XR?
**Answer:**
Projecting a line from a controller or the user's gaze to detect collisions with virtual objects. Essential for "Point and Click" interactions.

### 5. How do you implement "Physics-based Grabbing"?
**Answer:**
Instead of just parenting the object to the hand, you use a **Physics Joint** (e.g., FixedJoint or ConfigurableJoint). This allows the object to interact with the environment (e.g., hitting a wall) while being held.

### 6. Importance of "Teleportation" vs. "Smooth Locomotion"?
**Answer:**
- **Teleportation:** Reduces motion sickness by instantly moving the user.
- **Smooth Locomotion:** More immersive but can cause nausea if the "Comfort Vignet" (shrinking field of view during movement) isn't used.
