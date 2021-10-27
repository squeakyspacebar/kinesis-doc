# Basics

## Getting Started

### Installation and Setup

1. Create a new Unity project or open an existing one.
2. [Download and import the Kinesis package](https://docs.unity3d.com/Manual/upm-ui-import.html) into your project. That's it.

### Beginner Orientation

The best place to start is to review the rest of the [Basics](01-basics.md#basics) section and the [Fundamentals](02-fundamentals.md#fundamentals) section to obtain at least a high-level overview of the package and its architecture. After that, the next steps would be to check out the [Tutorial](03-tutorial.md#tutorial) and the [Demo Scene](05-demo-scene.md#demo-scene).

You can find the Doxygen-generated API documentation under `Kinesis/Documentation/html/index.html`, just open the file with a web browser.

## Package Overview

### Package Contents

- `Demo`: Contains all of the additional assets necessary for the demo scene.
  - `Character`: Contains character model assets.
  - `Editor`: Contains custom editor and property drawer scripts for demo components.
  - `Environment`: Contains environment assets.
  - `Prefabs`: Contains prefabs.
  - `Scenes`: Contains the Tutorial and Demo scenes.
  - `Scripts`: Contains demo scripts and components.
- `Documentation`: Contains locally accessible documentation.
- `Editor`: Contains custom editor and property drawer scripts to improve the basic editor experience.
- `Scripts`: Contains the core muscle model implementation as well as some convenience components for working with muscles.
  - `Attributes`: Contains basic non-essential attributes mainly for controlling field display in the Editor.
  - `Components`: Contains components that add non-core functionality either for working from the Editor or via scripting.
  - `Core`: Contains the core scripts that implement the muscle model.
  - `Exceptions`: Contains custom exceptions thrown by Kinesis.
  - `Library`: Contains generic code meant to be reused.

## Code Overview

### Namespaces

All of the code within Kinesis is namespaced.

| Namespace                | Description                                        |
| ------------------------ | -------------------------------------------------- |
| `Kinesis`                | Root namespace.                                    |
| `Kinesis.Core`           | Namespace for core components.                     |
| `Kinesis.Components`     | Namespace for non-core components.                 |
| `Kinesis.Demo`           | Namespace for everything related to the demo.      |
| `Kinesis.UnityInterface` | Namespace for custom editors and property drawers. |

### Components

#### Core

- `Muscle Tendon Unit`: This component holds the muscle model implementation and is what makes an object a muscle.

#### Non-Core

- `Joint Muscle Segments`: This component is meant to be attached to any object with a [**Joint** component](https://docs.unity3d.com/Manual/Joints.html) [spanned by a muscle](#overview-of-the-multi-segment-hill-type-muscle-model-in-Kinesis). It scans and caches a list of the spanning muscle segments for quick lookup. Requires a **Joint** component to exist on the object beforehand.

- `Muscle Group`: This is a minimal component meant as a convenient way to mark container objects for organizing/bundling muscles.

- `Muscle Render Gizmo`: This component visualizes muscle-related data—such as muscle node positions, muscle segment lines, lever arms, and joint torques—in the Scene view, with several options to toggle information and choose which colors are used.

  The component registers a callback triggered by hierarchy changes that scans all muscle objects to keep an updated list. It also implements a singleton pattern, so you're only allowed to have one instance in your project; attempting to attach another instance will fail.

- `Muscle Stimulator`: This component is meant to facilitate muscle testing from the Unity interface. You can use it to manually feed excitation and directly run physics simulation on a specific muscle. Requires a **Muscle Tendon Unit** component to exist on the object beforehand.

### Exceptions

- `MuscleComponentLengthException`: Thrown when a muscle element's length value exceeds the total muscle length. This can happen when the physics are moving too quickly and the muscle gets into a bad state, but should not occur often under normal operation.
- `MuscleNodeMissingBoneException`: Thrown when attempting to access a muscle node's bone object when it has not been set (i.e. is `null`).