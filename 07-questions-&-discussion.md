# Questions & Discussion

## Questions

***Why create Kinesis?***

Kinesis was created mostly as a byproduct of attempting to create a base for further AI experiments in embodied cognition. I wanted an agent with a complete physical presence that could navigate and interact with a broader physical environment in an organic and "non-scripted" way. Although I didn't think it would be a cakewalk, I simply misjudged the amount of work it would take to both implement and work with such a muscle-based system (at least, for someone like me without a graduate background in biodynamics, robotics, or mathematics). In hindsight, I could've picked a better approach for what I wanted to accomplish, but working on Kinesis was rewarding in its own right.

The reason I decided to clean things up and release it on the Unity Asset Store was because I haven't really seen anything like it available for Unity, and I wanted to help jump-start the process for others who might want to follow down the same path. It definitely would've been nice to have something like Kinesis starting out.

***What does the muscle model in Kinesis ignore and what is it missing?***

The muscle model in Kinesis is a somewhat simpler version of Hill-type muscle model. While it does handle segmented pathing, features that were not included in the initial implementation include:

- Serial damping effects.
- Modeling with muscle fiber pennation angles.
- Handling for more complex muscles such as those with multiple heads, insertions, and sheet-like muscles that extend over large areas.

There are other phenomena that Hill-type muscles models don't typically seem to address:

- The effects of muscle mass, although there has been at least some exploration of this with Hill-type muscle models.
- The effects of recent or extended activity on muscle dynamics.

***The model applies forces using the muscle segments as lines of action. Why use* [`AddTorque()`](https://docs.unity3d.com/ScriptReference/Rigidbody.AddTorque.html) *instead of* [`AddForce()`](https://docs.unity3d.com/ScriptReference/Rigidbody.AddForce.html)?**

I did experiment with `AddForce()` in the beginning, but it quickly became apparent that the approach was inherently unstable, even though one would imagine they should thereotically generate the same results. Not only was using `AddTorque()` more stable, but it also simplified the code required for movement forces.
