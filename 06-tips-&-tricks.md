# Tips & Tricks

## Tips

- Since you can select and edit multiple objects with **Muscle Stimulator** components at once, you can simultaneously control excitation to multiple muscles by selecting them in the hierarchy and using the *Excitation* field slider.
- While working with specific muscles, it helps to pin **Rigidbody** components you're not working with by setting their *Is Kinematic* fields to `true`.

## Caveats

- Muscles still exert passive effects even without activation, so joint torques still need be calculated for all muscles regardless of activation level to get the full effect.
- The `muscleNodes` field in `MuscleTendonUnit` expects muscle nodes to be defined *in order from muscle origin to insertion*. This ordering implicitly encodes the direction of muscle contraction (which begins with the insertion and moves toward the origin).
- For those from a more traditional academic background who might be unfamiliar, Unity uses a left-handed coordinate system. Keep in mind that rotations therefore follow the same principle.
- Try not to assign too much meaning to any particular paramater value or put too much stock in finding real-world reference values. The parameters and configurations you use are what they are and will be what they need to be.

## Troubleshooting Muscle Movement

This is a big topics since Unity physics has a lot of parts to it. **Rigidbody**, **Joint**,  **Muscle Node**, and **Muscle Tendon Unit** configurations all affect how bodies and muscles behave, and there are myriad reasons why muscles may fail to behave as anticipated (beyond your own misconceptions). I've created an initial checklist from issues that I've encountered:

- Make sure that each muscle node `Bone` field references the actual `GameObject` that you want to attach to. Errors can easily be made if you use the Object Picker interface and have objects that share the same name.

- Following from the above, do a check in the Scene View from all angles that your muscle nodes are in their intended positions. As mentioned in [(Geijtenbeek, et al., 2013, p. 4)](/10-bibliography.md), *the muscle torques generated can be extremely sensitive to muscle node location.*

- In addition, a configuration of muscle nodes that looks acceptable in one body position can produce a completely unintended movement once rotated far enough along into another body position. You can of course simply run simulations in Play Mode to observe motion behavior, but it can help to rotate limbs around in the Scene View first to check that the muscle nodes won't enter an undesirable configuration.

- Check the Unity [Joint and ragdoll stability page](https://docs.unity3d.com/Manual/RagdollStability.html).

- Ensure that the global 3D physics settings are set as you want them, such as *Default Max Angular Speed*. To find these settings, navigate to *Edit* > *Project Settings* > *Physics*.

- If your rigid bodies seem a bit too sensitive or a bit too resistive to movement under muscle activation, you may need to manually adjust their [inertia tensors (which affect rotation).
  
  A quick-and-dirty way to scale all of your Rigidbody components for diagnosis:
  
  ```
  float scalingCoefficient = 10.0f; // Use whatever value you want here.
  Rigidbody[] rigidBodies = GameObject.FindObjectsOfType<Rigidbody>();
  foreach (Rigidbody rb in rigidBodies) {
    rb.inertiaTensor *= scalingCoefficient;
  }
  ```
  
- Check the configurations on the Rigidbody components that are undergoing motion (or supposed to be). In particular, make sure that:
  - The value of the *Mass* field isn't set too high relative to the maximum isometric force settings of the muscles acting on the rigid body.
  - The values of the *Drag* and *Angular Drag* fields aren't set too high. Otherwise, the muscles won't be able to overcome the movement resistance.
  - [*Is Kinematic*](https://docs.unity3d.com/ScriptReference/Rigidbody-isKinematic.html) is not checked. If it is, physics (and therefore muscles) won't affect the rigid body.
  - *Use Gravity* is on the setting you intend. Muscles can and often will behave very differently under the influence of gravity versus without. Turning off gravity for select rigid bodies can be useful for debugging movements, but please be aware that the results must be taken in context.
  - The movement constraints are set as intended. Make sure you didn't leave any of the *Freeze Position* or *Freeze Rotation* coordinate-axes flags on if you didn't intend to.
  
- Check the configurations of the **Joint** components involved in the motion. In particular:
  - Make sure that the angular limits allow or restrict the movements that you expect to be allowed or restricted.
  - Double-check that the *Connected Body* field is properly set.
  
- If you're setting the *Is Kinematic* flag on some rigid bodies to pin the model while you test certain muscles and movements, please be aware that the motion you're intending may involve a broader combination of muscles, bodies, and joints than you first think. Restricting movement in a body that you're not focused on can have anywhere from subtle to drastic effects on movements in adjacent areas.

- On your muscles, check the values for the following fields:
  - *Normalized SEE Slack Length* affects the length ratio of the "passive" serial elastic element to the "active" contractile element. Setting this value higher makes the muscle more elastic and less responsive, potentially getting more pronounced oscillatory behavior. This also makes the muscle's passive effects more pronounced, so a muscle with a high normalized SEE slack length value can powerfully inhibit the movement of antagonist muscles.
  - *Max Isometric Force* affects the overall magnitude of the torques generated by the muscle. If this value is set too low, the muscle will be ineffective in driving motion. If this value is set too high, it's usually pretty obvious.
