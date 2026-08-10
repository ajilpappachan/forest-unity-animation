# Forest Animation

A rendered cinematic built in Unity: a man walks through a forest at night with a flashlight, an owl
watches from a branch, a lighthouse sweeps its beam across the treeline, and something finds him.
There is no gameplay — the whole thing is a Timeline sequence shot with virtual cameras and rendered
out to video.

Built in January 2022 as a task submission.

## How it works

**The sequence is a Timeline.** `Assets/Cutscenes/CutsceneTimeline.playable` drives everything —
character animation, the Cinemachine camera cuts, audio, and the shader changes on the monster. The
render was captured straight out of the editor with Unity Recorder rather than a runtime build.

**A custom Timeline track animates shader properties.** Timeline can keyframe transforms and
animator parameters out of the box, but not arbitrary material properties, which is what the monster
reveal needed. `Assets/Custom Playable/` adds a track type that does:

- **`ShaderTrack`** — a `TrackAsset` bound to a `Renderer`, configured with the shader property name
  and whether it is a float, vector or colour.
- **`ShaderControlAsset`** / **`ShaderPlayable`** — the clip you drag onto the track, holding the
  target value.
- **`ShaderMixer`** — the track mixer. It walks every input clip, scales each one's value by that
  clip's weight and sums them, so overlapping clips blend instead of snapping. Below a total weight
  of 0.5 it leaves the material alone, which keeps the property from being written when no clip is
  active. It writes to `sharedMaterial` in the editor and `material` in play mode, so scrubbing the
  timeline previews correctly without permanently editing the asset.

The monster's timeline uses two of these tracks: `_AlphaScale` dissolves it in, and `_Lerp` blends
its diffuse map across to the red one as it turns.

**Three Shader Graphs** carry the look:

- `Monster/Materials/Monster_Shader` — gradient-noise dissolve driven by `_AlphaScale`, plus a lerp
  between two diffuse maps for the colour shift.
- `Water/Water_Shader` — scene-depth sampling to blend a shallow and a deep water colour, two
  scrolling normal maps for the surface, and vertex displacement for swell.
- `Lighthouse/Light/VolumetricT` — the visible cone of the lighthouse beam.

**The environment** is eight terrain tiles sculpted with the Terrain Tools package, textured with
five terrain layers, planted with SpeedTree conifers and grass billboards, and lit with baked
lightmaps and a reflection probe. Fireflies are a particle system; the flashlight is a spot light
parented to the character rig.

## Built with

Unity 2020.3.15f2 · Universal Render Pipeline 10.5 · Timeline · Cinemachine · Unity Recorder ·
Animation Rigging · Terrain Tools · Shader Graph · C#

## Opening the project

Clone the repo and open the root folder as a Unity project. It was built against Unity
**2020.3.15f2**; a newer editor will trigger a one-way project upgrade.

The scene is `Assets/Scenes/Main.unity`. Open `Assets/Cutscenes/CutsceneTimeline.playable` in the
Timeline window to scrub the sequence.

## About the assets

Everything in `Assets/Samples/` and `Assets/Standard Assets/` is Unity's own sample content, not
mine. Only what the scene actually references is committed — the terrain layers and their textures,
and the conifer. The Terrain Tools *sculpting brush* set is editor-only tooling that nothing in the
scene references, so it is not vendored here; re-import it from Package Manager if you want to
reshape the terrain.

Third-party assets used:

| | |
|---|---|
| Lighthouse model | [Sketchfab](https://sketchfab.com/3d-models/fectar-lighthouse-6fd7ceda588348dcb0562d1e5b2f1ade) |
| Owl model | [Sketchfab](https://sketchfab.com/3d-models/eastern-screech-owl-1b34de1b8208495abb37b4b629c1f8a3) |
| Forest night ambience | [freesound](https://freesound.org/people/felix.blume/sounds/466955/) |
| Footsteps | [freesound](https://freesound.org/people/Cinetony/sounds/556791/) |
| Owl hoot | [freesound](https://freesound.org/people/Breviceps/sounds/465697/) |
| Monster roar | [freesound](https://freesound.org/people/jacobalcook/sounds/195568/) |
| Breaking branch 1 | [freesound](https://freesound.org/people/ceberation/sounds/235508/) |
| Breaking branch 2 | [freesound](https://freesound.org/people/Bini_trns/sounds/353536/) |
| Music | [freepd](https://freepd.com/misc.php) |

Character models and animations are from Mixamo.
