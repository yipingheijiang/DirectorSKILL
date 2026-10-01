# Reference-image fidelity / 沿用原图构图与风格

Use when the user wants an existing image animated with its original look, asks for “保持原图风格 / 和原图一样 / 从这张图开始”, or wants to understand why a prompt preserves a reference. This file owns preservation guidance; [tool adapters](ai-video-tool-adapters.md) still own control surfaces and motion budgets, and [video prompt templates](../assets/video-prompt-template.md) own prompt forms. For an explanation, discuss the relevant mechanism only; for a prompt request, deliver the prompt without a production checklist.

## Establish what the image controls

Inspect the supplied image before naming its visual properties. If only text is available, distinguish the user's description from observed image evidence and keep missing details conditional. Do not claim to have compared a source and result that were not supplied.

Identify each input's role: opening frame, ending frame, identity reference, or style reference. A character sheet or style reference does not automatically establish the opening composition. Use the generator's actual first-frame input when the image must determine the opening; text such as `<Picture 1>` is only a label and cannot upload an image or select that role.

Anchor a first-frame prompt with an instruction equivalent to:

> 视频从这张参考图建立的构图开始，沿用图中的人物位置、遮挡关系和前后景层次。
>
> Begin from the composition established by the supplied first-frame image, preserving the subjects' positions, occlusion, and foreground/background relationships.

This fixes the requested **opening**, while the subsequent camera and action clauses define permitted changes over time. A slow push-in changes framing deliberately; do not also demand an unchanged crop throughout the clip. A requested new angle needs a suitable new keyframe or a supported reference workflow, rather than a false promise that one image shows the unseen side.

## Select invariants from the actual reference

Choose the details that make this particular image recognizable. Keep the prompt's preservation clause compact and tied to visible evidence; a full inventory of the picture is unnecessary.

| Layer | What to preserve when visible and relevant | What it controls |
|---|---|---|
| Composition | Subject scale and screen position, eyeline, foreground occlusion, depth layers | Opening spatial arrangement |
| Identity and objects | Face structure, hair, costume silhouette, distinctive jewelry or prop details | Who and what remains recognizable |
| Light and color | Apparent light direction, soft or hard shadows, warm or cool balance, contrast, palette | The image's light and color relationships |
| Focus and optics | Which subject is sharp, which plane is blurred, bokeh character, diffusion | Separation of subjects and background |
| Rendering and surface | Live-action or illustration, linework, paper grain, brushwork, material texture | The image's medium and surface appearance |

Describe visible effects rather than inventing the lens, aperture, LUT, or lighting equipment that produced them. “Keep the face sharp and foreground softly blurred” is usable without claiming a particular f-stop.

Use preservation verbs: “retain the reference's soft side light and muted palette”, rather than requesting a newly designed lighting setup. Identity consistency and style consistency are separate: the same face can be rendered in a different medium, and the same color grade can accompany a different face.

The user's preservation constraints take precedence over genre defaults and director overlays. Apply a director's methods only to compatible choices, such as timing or performance. If the user explicitly requests a change of medium, color, or framing, change that dimension and preserve the others they specified. Do not add `live-action`, `photorealistic`, `cinematic lighting`, red-and-gold colors, or shallow depth of field unless the reference or requested change supports them. A watercolor input should retain its linework and washes when the user asks for the same style.

## Give the scene motion without redesigning it

When fidelity is the priority and the requested action permits it, choose a continuous shot with a locked camera or one slow, small-amplitude move. State the move's type, range, speed, and stopping point when they matter. Do not add a cut, reverse angle, orbit, focus pull, or lighting change merely to make the clip feel more cinematic.

Keep the requested dramatic event legible: a short eyeline shift, breath, restrained turn, or single gesture can have a clear start and end. Use sound to motivate a reaction when appropriate. Small motion is a directing option, not a universal requirement: preserve a user-requested walk, dance, or larger action and plan the additional reference views it needs.

The practical rationale is that limited changes ask the model to invent less unseen geometry and fewer new visual relationships. This can help preserve the reference, but is not a measured guarantee or a reason to freeze the requested action. Use only exclusions relevant to the shot's likely failure, in the tool's supported form. Audio fields arrange sound; they do not lock visual style.

## Paste-ready preservation patterns

Adapt these clauses to observed details, the requested duration and action, and the selected tool. They supplement S1's start-state and continuity slots; they are not a fifth prompt shape. A required platform header goes before the normal slots. Keep the existing short-prompt budget unless the selected interface genuinely needs a structured timeline.

Chinese pattern:

```text
[固定镜头，或一次缓慢、小幅的指定运镜]。视频从参考图建立的构图开始。
[人物]从图中姿态开始，[一个主要动作及停止位置]，结尾停在[明确状态]。
保持参考图中的[身份与服饰特征]、[光线与色彩关系]、[焦点层次或绘制质感]；
画面变化限于上述动作和运镜。
```

English pattern:

```text
[One camera behavior]. Begin from the composition established by the supplied first-frame image.
[Subject] starts in the pictured pose, then [one action with pace and endpoint]. End with [state].
Retain the reference's [identity/costume anchors], [light/color relationships], and [focus or rendering texture].
Limit visual changes to the specified action and camera movement.
```

Worked example — only for a reference that actually shows a sharp woman in red-and-gold costume, a blurred attendant in the foreground, and warm candle bokeh:

```text
镜头缓慢小幅推进。视频从参考图建立的构图开始，保留主角与前景侍女的站位和遮挡关系。
主角保持图中姿态，听到画外一声轻响后，视线轻移向声源并停住，头肩保持稳定。
保留人物面容、发髻、花钿和红金服饰，延续暖烛光、柔和光斑及原图的柔光质感。
主角始终清晰，侍女始终在前景虚化。镜头最后停止推进，停留在主角的反应上，全程一个连续镜头。
```

The red-and-gold palette, candlelight and blur belong to this example, not to the reusable rule. For a flat ink drawing, the preservation clause instead names its visible linework, limited palette and paper texture; it does not introduce optical bokeh or live-action skin.

### H3 first-frame wrapper, when that workflow is selected

H3's documented I2VA prompt structure uses the following opening instruction, followed by `integrated_multimodal_description`, `overall_soundscape`, and `non_diegetic_music`. Use it only with the matching H3 mode and a real image supplied as the first frame; other models may require different syntax.

```text
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.
```

Put the composition, preservation clauses and motion in the visual description. Match timing to the requested duration; the wrapper does not prescribe a 12-second clip, dialogue, or a specific palette. It expresses requested alignment, not proof of an exact match. Sources checked 2026-10-01: [H3 base prompt guide](https://huggingface.co/MiniMaxAI/MiniMax-H3/blob/main/docs/VIDEO_PROMPT_WRITING_GUIDE_base_en.md) and [MiniMax video input guide](https://platform.minimax.io/docs/guides/video-generation). Re-check the selected interface before relying on its controls.

## Verify fidelity against the source

Before generation, check the actual first-frame assignment, opening composition, preservation clauses, and any conflict with a style overlay. If the source has not been inspected or the input role cannot be verified, state that limit instead of marking it checked.

After generation, compare the source with the opening, a middle frame, the ending, and frames around the largest movement; then watch the motion for transient drift. Check identity/objects separately from light/color, depth/focus, rendering texture, and camera changes. Account for intentional movement: compare retained relationships rather than expecting a push-in to keep identical pixels. Frame sampling alone cannot establish that every intermediate frame is correct.

Report what was actually checked and any visible drift. `fully referenced`, `Preserve`, a fixed seed, or a passed prompt-format check cannot establish pixel identity. Without generated output, say that the prompt is designed to preserve the reference and that the visual result remains unverified. Never label a static prompt check as a generated-video fidelity pass.
