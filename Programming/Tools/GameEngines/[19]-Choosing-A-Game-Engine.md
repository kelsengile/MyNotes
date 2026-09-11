[Previous](./[18]-Building-And-Deploying-Games.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md)

*Next Steps*

# Lesson 19 - Choosing A Game Engine

## 19.1 Matching Engines To Project Needs

With the fundamentals from this Topic in hand — scenes and GameObjects, rendering, physics, input, animation, audio, and the production concerns around performance and shipping — the natural next question is practical: *which engine should you actually use?* There's rarely a single objectively "best" engine; the right choice depends on matching an engine's strengths to a specific project's needs.

A few questions worth asking before committing to one:

- **What kind of game is it?** A 2D puzzle game, a large open-world 3D game, and a mobile hyper-casual game have very different technical demands, and engines vary in how well they support each (Section 19.2).
- **What's the team's size and background?** A solo developer with programming experience might value a scriptable, code-first workflow, while a larger team with dedicated designers might lean toward heavier use of visual tools like Unreal's Blueprints (Lesson 3).
- **What platforms does it need to reach?** Nearly all major engines support the most common platforms today, but export quality and ease can still vary meaningfully by target (Lesson 18), especially for less common ones.
- **What's the budget?** Licensing terms differ significantly between engines — some are entirely free and open-source, others take a percentage of revenue past a certain threshold, and custom engines (Lesson 3) carry substantial ongoing engineering costs.

No single engine wins on every axis simultaneously, which is exactly why the three major general-purpose engines from Lesson 3 continue to coexist and thrive rather than one simply replacing the others.

## 19.2 2D-Focused vs 3D-Focused Engines

While most major modern engines support both 2D and 3D to some degree, they don't all support both equally well, and the distinction is worth weighing seriously for a specific project:

- Engines with strong **2D-first tooling** (Godot is frequently cited here) tend to offer more convenient built-in support for things like tilemaps, 2D-specific physics and lighting, and sprite-based animation workflows, without needing to work around tools primarily designed for 3D.
- Engines with strong **3D-first tooling** (Unreal is frequently cited here) tend to offer more advanced rendering features out of the box — dynamic global illumination, advanced material systems, high-fidelity lighting — since 3D visual fidelity is where their core engineering investment has gone.
- General-purpose engines that support both reasonably well (Unity is frequently cited here) offer flexibility at the cost of sometimes feeling like neither 2D nor 3D workflows are quite as polished as a tool built specifically around one.

This isn't a hard rule — every major engine has shipped excellent games outside its "strongest" category — but it's a reasonable heuristic when narrowing down options, and worth validating directly by building a very small prototype in a candidate engine before committing a full project to it.

## 19.3 Learning Curve And Community

Beyond raw technical capability, two practical factors heavily influence how quickly a team (or an individual learner) can actually become productive in a given engine:

- **Learning curve** — how much there is to learn before an engine feels usable, and how gently it introduces that complexity. A gentler learning curve gets a beginner to a playable prototype faster, though it can sometimes come at the cost of the deeper flexibility a more complex engine offers once mastered.
- **Community and documentation** — the size and activity of an engine's community directly affects how easy it is to get unstuck. A large community means existing tutorials, forum answers, and third-party assets for almost any problem a developer runs into; a smaller community means more time spent solving problems from first principles or reading official documentation and source code directly.

These factors matter enormously for solo developers and small teams in particular, since they often don't have a more experienced colleague to ask when they get stuck — for these developers, an engine's community can end up mattering as much as its technical feature set.

## 19.4 Where To Go Next

This Topic deliberately stayed tool-agnostic, focusing on the concepts that transfer across every engine rather than the specific menus and syntax of any one of them — a scene graph is a scene graph, a rigidbody is a rigidbody, and a state machine is a state machine, whether it's called that in Unity, Unreal, Godot, or a custom in-house engine. That foundation is what makes the next step tractable: picking a specific engine and mapping the vocabulary you already know onto its particular tools.

From here, a reasonable path forward is:

1. Pick one general-purpose engine (Lesson 3) based on the factors from this lesson, rather than trying to learn several at once.
2. Work through that engine's official beginner tutorial, actively noticing where its interface and terminology map onto the concepts from this Topic — its version of a "GameObject," its way of writing a script's `update()` method, its physics and collider components.
3. Build something small and complete rather than something large and unfinished — a tiny, fully working game teaches far more than an ambitious prototype that never gets past its first system.

The specific engine you choose matters far less than actually finishing something with it — the concepts from this Topic will still be there, under whatever names a given engine happens to use for them.

---

[Previous](./[18]-Building-And-Deploying-Games.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md)
