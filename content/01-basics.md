# Basics

## Getting Started

### Installation and Setup

1. Create a new Unity project or open an existing one.
2. [Download and import the Kinesis package](https://docs.unity3d.com/Manual/upm-ui-import.html) into your project. That's it.

### Beginner Orientation

The recommended way to start is to review the rest of the [Basics](01-basics#basics) and [Fundamentals](02-fundamentals#fundamentals) sections for a high-level overview of the package and its architecture. After that, check out the [Tutorial](03-tutorial#tutorial) and the [Demo](05-demo-scene#demo-scene) scenes included with the package, and read the [Tip & Tricks](06-tips-&-tricks#tips-&-tricks) section when you're ready to start experimenting.

If you're reading a local version of this documentation, you can find the latest version online at [https://squeakyspacebar.github.io/kinesis-doc](https://squeakyspacebar.github.io/kinesis-doc). You can also find the Doxygen-generated API documentation in the package directory under `Kinesis/Documentation/html/index.html` or online at [https://squeakyspacebar.github.io/kinesis-doc/api/](https://squeakyspacebar.github.io/kinesis-doc/api/).

## Package Overview

### Package Contents

- `Demo`: Additional assets for the tutorial and demo scenes.
  - `Character`: Character model assets.
  - `Editor`: Custom editor and property drawer scripts for demo components.
  - `Environment`: Environment assets.
  - `Prefabs`: Prefabs for the Tutorial and Demo scenes.
  - `Scenes`: The Tutorial and Demo scenes.
  - `Scripts`: Demo scripts and components.
- `Documentation`: Locally packaged copy of documentation.
- `Editor`: Custom editor and property drawer scripts to improve the basic editor experience.
- `Scripts`: The core muscle model implementation, additional components, and supporting code.
  - `Attributes`: Basic attributes, mainly for controlling the display of fields in the Editor.
  - `Components`: Non-core components.
  - `Core`: Core scripts that implement the muscle model.
  - `Exceptions`: Custom exceptions thrown by Kinesis.
  - `Library`: Generic reusable code.

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

- **Muscle Tendon Unit**: This component is the core of the muscle model implementation and is how you give an object muscle behavior.

#### Non-Core

- **Joint Data**: Acts as a data container for conveniently referencing data related to muscle manipulation. As part of that responsibility, it scans and caches a list of joint-spanning muscle segments for quick lookup. Requires a [**Joint**](https://docs.unity3d.com/Manual/Joints.html) component on any GameObject it is attached to.
- **Muscle Group**: This is a (currently) minimal component meant as a convenient way to mark a GameObject as a container for organizing/grouping muscles.
- **Muscle Render Gizmo**: This component visualizes muscle-related data in the Scene view such as muscle node positions, muscle segment lines, lever arms, and joint torques, with several options to customize the display of information. The component also registers a callbackin the Scene view—triggered by hierarchy changes—that scans all muscle objects to keep an updated list.
- **Muscle Rig**: Meant to be attached to a skeleton's root GameObject. It scans child GameObjects and attaches a **Joint Data** component if a corresponding **Joint** component is detected. The component performs the inverse when removed and removes any **Joint Data** components attached to child GameObjects.
- **Muscle Stimulator**: This component provides the ability to interact directly with muscles from the Unity interface, and can be used to manually set excitation and run physics simulation for specific muscles. Requires a **Muscle Tendon Unit** component on any GameObject it is attached to.

### Exceptions

- **MuscleComponentLengthException**: Thrown when a muscle element's length value exceeds the total muscle length. This can happen when the physics are moving too quickly and the muscle gets into a bad state, but should not occur often under normal operation.
- **MuscleNodeMissingBoneException**: Thrown when attempting to reference the bone object of a muscle node when it has not been set (i.e. is `null`).
